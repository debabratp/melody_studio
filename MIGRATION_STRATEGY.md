
# Melody Studio - Migration Strategy: Node.js + React → Python + Angular

## Migration Overview

This document outlines the comprehensive strategy for migrating Melody Studio from Node.js + React to Python + Angular, ensuring minimal downtime, data integrity, and feature parity.

### Migration Goals
- **Zero Data Loss**: Preserve all user data, songs, and library information
- **Minimal Downtime**: < 4 hours total downtime during cutover
- **Feature Parity**: Maintain all existing functionality
- **Performance Improvement**: Achieve better performance with new stack
- **Scalability Enhancement**: Improve system scalability and maintainability

## Migration Phases

### Phase 1: Preparation & Setup (Week 1-2)

#### 1.1 Environment Setup
```bash
# Set up new development environment
git checkout -b migration/python-angular
mkdir -p backend frontend shared

# Initialize Python backend
cd backend
poetry init
poetry add fastapi uvicorn sqlalchemy psycopg2-binary alembic redis celery

# Initialize Angular frontend
cd ../frontend
ng new melody-studio-angular --routing --style=scss --package-manager=npm
ng add @angular/material @ngrx/store @ngrx/effects @angular/pwa
```

#### 1.2 Database Migration Planning
```sql
-- Create migration mapping table
CREATE TABLE migration_mapping (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    old_collection VARCHAR(50) NOT NULL,
    old_id VARCHAR(100) NOT NULL,
    new_table VARCHAR(50) NOT NULL,
    new_id UUID NOT NULL,
    migrated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(old_collection, old_id)
);

-- Index for fast lookups during migration
CREATE INDEX idx_migration_mapping_old ON migration_mapping(old_collection, old_id);
CREATE INDEX idx_migration_mapping_new ON migration_mapping(new_table, new_id);
```

#### 1.3 Data Analysis & Mapping
```python
# MongoDB to PostgreSQL data mapping script
import pymongo
import asyncpg
import asyncio
from typing import Dict, Any

class DataMigrationAnalyzer:
    def __init__(self, mongo_uri: str, postgres_uri: str):
        self.mongo_client = pymongo.MongoClient(mongo_uri)
        self.postgres_uri = postgres_uri
        
    async def analyze_collections(self):
        """Analyze MongoDB collections and create mapping strategy"""
        db = self.mongo_client.melody_studio
        
        # Analyze Users collection
        users_count = db.users.count_documents({})
        users_sample = list(db.users.find().limit(10))
        
        # Analyze Songs collection
        songs_count = db.songs.count_documents({})
        songs_sample = list(db.songs.find().limit(10))
        
        # Analyze UserLibrary collection
        library_count = db.user_library.count_documents({})
        library_sample = list(db.user_library.find().limit(10))
        
        return {
            'users': {'count': users_count, 'sample': users_sample},
            'songs': {'count': songs_count, 'sample': songs_sample},
            'library': {'count': library_count, 'sample': library_sample}
        }
```

### Phase 2: Backend Migration (Week 3-6)

#### 2.1 Database Schema Creation
```python
# SQLAlchemy models for new schema
from sqlalchemy import Column, String, Integer, Boolean, DateTime, Text, ARRAY, JSON
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.ext.declarative import declarative_base
import uuid

Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    
    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    phone_number = Column(String(15), nullable=False, unique=True)
    country_code = Column(String(5), nullable=False)
    is_verified = Column(Boolean, default=False)
    name = Column(String(255))
    avatar_url = Column(Text)
    preferences = Column(JSON, default={})
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), server_default=func.now(), onupdate=func.now())
    last_login_at = Column(DateTime(timezone=True))

class Song(Base):
    __tablename__ = "songs"
    
    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    title = Column(String(500), nullable=False)
    artist = Column(String(500), nullable=False)
    album = Column(String(500))
    language = Column(String(10))
    duration = Column(Integer)  # in seconds
    genre = Column(ARRAY(String))
    release_year = Column(Integer)
    popularity = Column(Integer, default=0)
    explicit = Column(Boolean, default=False)
    thumbnail_url = Column(Text)
    external_ids = Column(JSON, default={})
    audio_files = Column(JSON, default={})
    metadata = Column(JSON, default={})
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), server_default=func.now(), onupdate=func.now())
```

#### 2.2 Data Migration Scripts
```python
# Comprehensive data migration script
import asyncio
import asyncpg
import pymongo
from datetime import datetime
from typing import List, Dict, Any
import uuid

class DataMigrator:
    def __init__(self, mongo_uri: str, postgres_uri: str):
        self.mongo_client = pymongo.MongoClient(mongo_uri)
        self.postgres_uri = postgres_uri
        self.migration_log = []
        
    async def migrate_users(self) -> Dict[str, int]:
        """Migrate users from MongoDB to PostgreSQL"""
        conn = await asyncpg.connect(self.postgres_uri)
        db = self.mongo_client.melody_studio
        
        users_cursor = db.users.find({})
        migrated_count = 0
        error_count = 0
        
        async with conn.transaction():
            for user_doc in users_cursor:
                try:
                    new_user_id = uuid.uuid4()
                    
                    # Transform MongoDB document to PostgreSQL row
                    user_data = {
                        'id': new_user_id,
                        'phone_number': user_doc['phoneNumber'],
                        'country_code': user_doc['countryCode'],
                        'is_verified': user_doc.get('isVerified', False),
                        'name': user_doc.get('profile', {}).get('name'),
                        'avatar_url': user_doc.get('profile', {}).get('avatar'),
                        'preferences': user_doc.get('profile', {}).get('preferences', {}),
                        'created_at': user_doc.get('createdAt', datetime.utcnow()),
                        'last_login_at': user_doc.get('lastLoginAt')
                    }
                    
                    # Insert into PostgreSQL
                    await conn.execute("""
                        INSERT INTO users (id, phone_number, country_code, is_verified, 
                                         name, avatar_url, preferences, created_at, last_login_at)
                        VALUES ($1, $2, $3, $4, $5, $6, $7, $8, $9)
                    """, *user_data.values())
                    
                    # Record migration mapping
                    await conn.execute("""
                        INSERT INTO migration_mapping (old_collection, old_id, new_table, new_id)
                        VALUES ($1, $2, $3, $4)
                    """, 'users', str(user_doc['_id']), 'users', new_user_id)
                    
                    migrated_count += 1
                    
                except Exception as e:
                    error_count += 1
                    self.migration_log.append(f"User migration error: {str(e)}")
                    
        await conn.close()
        return {'migrated': migrated_count, 'errors': error_count}
    
    async def migrate_songs(self) -> Dict[str, int]:
        """Migrate songs from MongoDB to PostgreSQL"""
        conn = await asyncpg.connect(self.postgres_uri)
        db = self.mongo_client.melody_studio
        
        songs_cursor = db.songs.find({})
        migrated_count = 0
        error_count = 0
        
        async with conn.transaction():
            for song_doc in songs_cursor:
                try:
                    new_song_id = uuid.uuid4()
                    
                    song_data = {
                        'id': new_song_id,
                        'title': song_doc['title'],
                        'artist': song_doc['artist'],
                        'album': song_doc.get('album'),
                        'language': song_doc.get('language'),
                        'duration': song_doc.get('duration'),
                        'genre': song_doc.get('genre', []),
                        'release_year': song_doc.get('metadata', {}).get('releaseYear'),
                        'popularity': song_doc.get('metadata', {}).get('popularity', 0),
                        'explicit': song_doc.get('metadata', {}).get('explicit', False),
                        'thumbnail_url': song_doc.get('thumbnail'),
                        'external_ids': song_doc.get('externalIds', {}),
                        'audio_files': song_doc.get('audioFiles', {}),
                        'metadata': song_doc.get('metadata', {}),
                        'created_at': song_doc.get('createdAt', datetime.utcnow())
                    }
                    
                    await conn.execute("""
                        INSERT INTO songs (id, title, artist, album, language, duration,
                                         genre, release_year, popularity, explicit,
                                         thumbnail_url, external_ids, audio_files, metadata, created_at)
                        VALUES ($1, $2, $3, $4, $5, $6, $7, $8, $9, $10, $11, $12, $13, $14, $15)
                    """, *song_data.values())
                    
                    await conn.execute("""
                        INSERT INTO migration_mapping (old_collection, old_id, new_table, new_id)
                        VALUES ($1, $2, $3, $4)
                    """, 'songs', str(song_doc['_id']), 'songs', new_song_id)
                    
                    migrated_count += 1
                    
                except Exception as e:
                    error_count += 1
                    self.migration_log.append(f"Song migration error: {str(e)}")
                    
        await conn.close()
        return {'migrated': migrated_count, 'errors': error_count}
```

#### 2.3 API Endpoint Migration
```python
# FastAPI endpoint migration example
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import HTTPBearer
from sqlalchemy.ext.asyncio import AsyncSession
from typing import List, Optional

app = FastAPI(title="Melody Studio API v2", version="2.0.0")
security = HTTPBearer()

# Migrate authentication endpoints
@app.post("/api/v1/auth/request-otp")
async def request_otp(
    request: OTPRequest,
    db: AsyncSession = Depends(get_db)
) -> OTPResponse:
    """Migrated from Node.js Express endpoint"""
    try:
        # Validate phone number
        if not validate_phone_number(request.phone_number, request.country_code):
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST,
                detail="Invalid phone number"
            )
        
        # Generate OTP
        otp_service = OTPService(db)
        otp_data = await otp_service.generate_otp(
            request.phone_number, 
            request.country_code
        )
        
        # Send SMS via MSG91
        sms_service = SMSService()
        await sms_service.send_otp(
            request.phone_number,
            request.country_code,
            otp_data.otp
        )
        
        return OTPResponse(
            success=True,
            data={
                "otp_id": otp_data.id,
                "expires_in": 300
            },
            message="OTP sent successfully"
        )
        
    except Exception as e:
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail=str(e)
        )

# Migrate music search endpoints
@app.get("/api/v1/music/search")
async def search_music(
    q: str,
    limit: int = 20,
    offset: int = 0,
    language: Optional[str] = None,
    source: Optional[str] = "all",
    db: AsyncSession = Depends(get_db)
) -> SearchResponse:
    """Migrated music search with improved performance"""
    try:
        music_service = MusicService(db)
        
        # Use async aggregation for better performance
        search_results = await music_service.search_tracks(
            query=q,
            limit=limit,
            offset=offset,
            language=language,
            source=source
        )
        
        return SearchResponse(
            success=True,
            data={"tracks": search_results.tracks},
            pagination={
                "page": offset // limit + 1,
                "limit": limit,
                "total": search_results.total,
                "total_pages": (search_results.total + limit - 1) // limit
            },
            message="Search completed successfully"
        )
        
    except Exception as e:
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail=str(e)
        )
```

### Phase 3: Frontend Migration (Week 7-10)

#### 3.1 Angular Application Setup
```typescript
// Angular 17+ standalone components setup
import { bootstrapApplication } from '@angular/platform-browser';
import { provideRouter } from '@angular/router';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { provideStore, provideEffects } from '@ngrx/store';
import { provideServiceWorker } from '@angular/service-worker';

import { AppComponent } from './app/app.component';
import { routes } from './app/app.routes';
import { authInterceptor } from './app/core/interceptors/auth.interceptor';
import { reducers } from './app/store/app.reducer';
import { AppEffects } from './app/store/app.effects';

bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient(withInterceptors([authInterceptor])),
    provideStore(reducers),
    provideEffects([AppEffects]),
    provideServiceWorker('ngsw-worker.js', {
      enabled: environment.production
    })
  ]
});
```

#### 3.2 Component Migration Strategy
```typescript
// React to Angular component migration example
// Original React component
/*
const MusicSearch: React.FC = () => {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  
  const handleSearch = async () => {
    setLoading(true);
    const response = await searchMusic(query);
    setResults(response.data.tracks);
    setLoading(false);
  };
  
  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <button onClick={handleSearch}>Search</button>
      {loading && <div>Loading...</div>}
      {results.map(track => <TrackCard key={track.id} track={track} />)}
    </div>
  );
};
*/

// Migrated Angular component
@Component({
  selector: 'app-music-search',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule, MatInputModule, MatButtonModule],
  template: `
    <div class="search-container">
      <mat-form-field appearance="outline" class="search-field">
        <mat-label>Search for music</mat-label>
        <input matInput [formControl]="searchControl" placeholder="Enter song, artist, or album">
        <mat-icon matSuffix>search</mat-icon>
      </mat-form-field>
      
      <button mat-raised-button color="primary" (click)="handleSearch()" [disabled]="loading">
        {{ loading ? 'Searching...' : 'Search' }}
      </button>
      
      <div *ngIf="loading" class="loading-spinner">
        <mat-spinner diameter="40"></mat-spinner>
      </div>
      
      <div class="results-container">
        <app-track-card 
          *ngFor="let track of searchResults$ | async; trackBy: trackByFn"
          [track]="track"
          (play)="onPlayTrack($event)"
          (addToLibrary)="onAddToLibrary($event)">
        </app-track-card>
      </div>
    </div>
  `,
  styleUrls: ['./music-search.component.scss']
})
export class MusicSearchComponent implements OnInit {
  searchControl = new FormControl('', [Validators.required, Validators.minLength(2)]);
  searchResults$ = this.store.select(selectSearchResults);
  loading$ = this.store.select(selectSearchLoading);
  loading = false;

  constructor(
    private store: Store,
    private musicService: MusicService
  ) {}

  ngOnInit() {
    // Subscribe to loading state
    this.loading$.subscribe(loading => this.loading = loading);
    
    // Auto-search on input changes with debounce
    this.searchControl.valueChanges.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      filter(query => query && query.length >= 2)
    ).subscribe(query => {
      this.performSearch(query);
    });
  }

  handleSearch() {
    const query = this.searchControl.value;
    if (query && query.trim()) {
      this.performSearch(query.trim());
    }
  }

  private performSearch(query: string) {
    this.store.dispatch(searchMusic({ 
      query, 
      filters: { limit: 20, offset: 0 } 
    }));
  }

  onPlayTrack(track: Track) {
    this.store.dispatch(playTrack({ track }));
  }

  onAddToLibrary(track: Track) {
    this.store.dispatch(addToLibrary({ track }));
  }

  trackByFn(index: number, track: Track): string {
    return track.id;
  }
}
```

#### 3.3 State Management Migration
```typescript
// Redux to NgRx migration
// Original Redux slice
/*
const musicSlice = createSlice({
  name: 'music',
  initialState: {
    searchResults: [],
    currentTrack: null,
    isPlaying: false,
    loading: false,
    error: null
  },
  reducers: {
    searchStart: (state) => {
      state.loading = true;
      state.error = null;
    },
    searchSuccess: (state, action) => {
      state.loading = false;
      state.searchResults = action.payload;
    },
    searchFailure: (state, action) => {
      state.loading = false;
      state.error = action.payload;
    }
  }
});
*/

// Migrated NgRx implementation
export interface MusicState {
  searchResults: Track[];
  currentTrack: Track | null;
  isPlaying: boolean;
  loading: boolean;
  error: string | null;
}

export const initialState: MusicState = {
  searchResults: [],
  currentTrack: null,
  isPlaying: false,
  loading: false,
  error: null
};

// Actions
export const searchMusic = createAction(
  '[Music] Search Music',
  props<{ query: string; filters: SearchFilters }>()
);

export const searchMusicSuccess = createAction(
  '[Music] Search Music Success',
  props<{ results: Track[] }>()
);

export const searchMusicFailure = createAction(
  '[Music] Search Music Failure',
  props<{ error: string }>()
);

// Reducer
export const musicReducer = createReducer(
  initialState,
  on(searchMusic, (state) => ({
    ...state,
    loading: true,
    error: null
  })),
  on(searchMusicSuccess, (state, { results }) => ({
    ...state,
    loading: false,
    searchResults: results
  })),
  on(searchMusicFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  }))
);

// Effects
@Injectable()
export class MusicEffects {
  searchMusic$ = createEffect(() =>
    this.actions$.pipe(
      ofType(searchMusic),
      switchMap(({ query, filters }) =>
        this.musicService.searchMusic(query, filters).pipe(
          map(response => searchMusicSuccess({ results: response.data.tracks })),
          catchError(error => of(searchMusicFailure({ error: error.message })))
        )
      )
    )
  );

  constructor(
    private actions$: Actions,
    private musicService: MusicService
  ) {}
}

// Selectors
export const selectMusicState = createFeatureSelector<MusicState>('music');

export const selectSearchResults = createSelector(
  selectMusicState,
  (state) => state.searchResults
);

export const selectSearchLoading = createSelector(
  selectMusicState,
  (state) => state.loading
);

export const selectCurrentTrack = createSelector(
  selectMusicState,
  (state) => state.currentTrack
);
```

### Phase 4: Testing & Validation (Week 11)

#### 4.1 Data Integrity Validation
```python
# Data validation script
import asyncio
import asyncpg
import pymongo
from typing import Dict, List

class MigrationValidator:
    def __init__(self, mongo_uri: str, postgres_uri: str):
        self.mongo_client = pymongo.MongoClient(mongo_uri)
        self.postgres_uri = postgres_uri
        
    async def validate_user_migration(self) -> Dict[str, any]:
        """Validate user data migration"""
        conn = await asyncpg.connect(self.postgres_uri)
        db = self.mongo_client.melody_studio
        
        # Count validation
        mongo_count = db.users.count_documents({})
        postgres_count = await conn.fetchval("SELECT COUNT(*) FROM users")
        
        # Sample data validation
        mongo_sample = db.users.find_one()
        if mongo_sample:
            mapping = await conn.fetchrow("""
                SELECT new_id FROM migration_mapping 
                WHERE old_collection = 'users' AND old_id = $1
            """, str(mongo_sample['_id']))
            
            if mapping:
                postgres_sample = await conn.fetchrow("""
                    SELECT * FROM users WHERE id = $1
                """, mapping['new_id'])
                
                # Validate data integrity
                data_match = (
                    postgres_sample['phone_number'] == mongo_sample['phoneNumber'] and
                    postgres_sample['country_code'] == mongo_sample['countryCode'] and
                    postgres_sample['is_verified'] == mongo_sample.get('isVerified', False)
                )
            else:
                data_match = False
        else:
            data_match = True
            
        await conn.close()
        
        return {
            'mongo_count': mongo_count,
            'postgres_count': postgres_count,
            'count_match': mongo_count == postgres_count,
            'data_integrity': data_match,
            'success': mongo_count == postgres_count and data_match
        }
```

#### 4.2 API Compatibility Testing
```typescript
// API compatibility test suite
describe('API Migration Compatibility', () => {
  let httpClient: HttpClient;
  let httpTestingController: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [MusicService]
    });
    
    httpClient = TestBed.inject(HttpClient);
    httpTestingController = TestBed.inject(HttpTestingController);
  });

  it('should maintain API response format compatibility', () => {
    const service = TestBed.inject(MusicService);
    const mockResponse = {
      success: true,
      data: {
        tracks: [
          {
            id: 'track_123',
            title: 'Test Song',
            artist: 'Test Artist',
            duration: 180
          }
        ]
      },
      pagination: {
        page: 1,
        limit: 20,
        total: 1,
        totalPages: 1
      },
      message: 'Search completed successfully'
    };

    service.searchMusic('test query', {}).subscribe(response => {
      expect(response.success).toBe(true);
      expect(response.data.tracks).toBeDefined();
      expect(response.data.tracks.length).toBe(1);
      expect(response.pagination).toBeDefined();
    });

    const req = httpTestingController.expectOne('/api/v1/music/search?q=test%20query');
    expect(req.request.method).toBe('GET');
    req.flush(mockResponse);
  });

  it('should handle authentication flow correctly', () => {
    const authService = TestBed.inject(AuthService);
    
    // Test OTP request
    authService.requestOTP('9876543210', '+91').subscribe(response => {
      expect(response.success).toBe(true);
      expect(response.data.otpId).toBeDefined();
    });

    const otpReq = httpTestingController.expectOne('/api/v1/auth/request-otp');
    otpReq.flush({
      success: true,
      data: { otpId: 'otp_123', expiresIn: 300 },
      message: 'OTP sent successfully'
    });
  });
});
```

### Phase 5: Deployment & Cutover (Week 12)

#### 5.1 Blue-Green Deployment Strategy
```yaml
# Docker Compose for blue-green deployment
version: '3.8'

services:
  # Blue environment (current Node.js)
  backend-blue:
    image: melody-studio-backend:node-latest
    ports:
      - "3001:3001"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=${MONGODB_URI}
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.backend-blue.rule=Host(`api.melodystudio.com`) && PathPrefix(`/blue`)"

  frontend-blue:
    image: melody-studio-frontend:react-latest
    ports:
      - "3000:3000"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.frontend-blue.rule=Host(`melodystudio.com`) && PathPrefix(`/blue`)"

  # Green environment (new Python + Angular)
  backend-green:
    image: melody-studio-backend:python-latest
    ports:
      - "8001:8000"
    environment:
      - ENVIRONMENT=production
      - DATABASE_URL=${POSTGRESQL_URI}
      - REDIS_URL=${REDIS_URI}
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.backend-green.rule=Host(`api.melodystudio.com`) && PathPrefix(`/green`)"

  frontend-green:
    image: melody-studio-frontend:angular-latest
    ports:
      - "4200:80"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.frontend-green.rule=Host(`melodystudio.com`) && PathPrefix(`/green`)"

  # Load balancer
  traefik:
    image: traefik:v2.10
    command:
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

#### 5.2 Cutover Procedure
```bash
#!/bin/bash
# cutover.sh - Production cutover script

set -e

echo "Starting Melody Studio migration cutover..."

# Step 1: Final data sync
echo "Performing final data synchronization..."
python scripts/final_data_sync.py

# Step 2: Enable maintenance mode
echo "Enabling maintenance mode..."
curl -X POST https://api.melodystudio.com/admin/maintenance/enable

# Step 3: Stop write operations to old system
echo "Stopping write operations..."
kubectl scale deployment melody-studio-backend-node --replicas=0

# Step 4: Final data migration
echo "Running final data migration..."
python scripts/final_migration.py

# Step 5: Validate data integrity
echo "Validating data integrity..."
python scripts/validate_migration.py

# Step 6: Switch traffic to new system
echo "Switching traffic to Python + Angular stack..."
kubectl apply -f k8s/production/python-angular-deployment.yaml

# Step 7: Health check
echo "Performing health checks..."
for i in {1..30}; do
  if curl -f https://api.melodystudio.com/health; then
    echo "Health check passed"
    break
  fi
  echo "Health check attempt $i failed, retrying..."
  sleep 10
done

# Step 8: Disable maintenance mode
echo "Disabling maintenance mode..."
curl -X POST https://api.melodystudio.com/admin/maintenance/disable

echo "Cutover completed successfully!"
```

#### 5.3 Rollback Plan
```bash
#!/bin/bash
# rollback.sh - Emergency rollback script

set -e

echo "Initiating emergency rollback..."

# Step 1: Enable maintenance mode
curl -X POST https://api.melodystudio.com/admin/maintenance/enable

# Step 2: Switch traffic back to Node.js system
kubectl apply -f k8s/production/node-react-deployment.yaml

# Step 3: Restore database if needed
if [ "$1" == "--restore-db" ]; then
  echo "Restoring database from backup..."
  pg_restore -h $DB_HOST -U $DB_USER -d melody_studio backup/pre_migration_backup.sql
fi

# Step 4: Health check
for i in {1..30}; do
  if curl -f https://api.melodystudio.com/health; then
    echo "Rollback health check passed"
    break
  fi
  sleep 10
done

# Step 5: Disable maintenance mode
curl -X POST https://api.melodystudio.com/admin/maintenance/disable

echo "Rollback completed successfully!"
```

## Risk Mitigation

### Data Loss Prevention
- **Multiple Backups**: Full database backups before each migration step
- **Incremental Sync**: Continuous data synchronization during migration
- **Validation Scripts**: Comprehensive data integrity validation
- **Rollback Procedures**: Tested rollback procedures for each phase

### Performance Monitoring
- **Load Testing**: Comprehensive load testing before cutover
- **Performance Baselines**: Establish performance bas