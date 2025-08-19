# Melody Studio - Project Structure

## Root Directory Structure
```
melody_studio/
├── README.md
├── ARCHITECTURE.md
├── API_SPECIFICATION.md
├── PROJECT_STRUCTURE.md
├── DEVELOPMENT_ROADMAP.md
├── docker-compose.yml
├── .env.example
├── .gitignore
├── package.json
├── frontend/                 # React PWA Frontend
├── backend/                  # Node.js API Backend
├── shared/                   # Shared types and utilities
├── docs/                     # Additional documentation
├── scripts/                  # Build and deployment scripts
└── infrastructure/           # Docker, K8s, and deployment configs
```

## Frontend Structure (React PWA)
```
frontend/
├── public/
│   ├── index.html
│   ├── manifest.json         # PWA manifest
│   ├── sw.js                 # Service worker
│   ├── icons/                # PWA icons (various sizes)
│   └── offline.html          # Offline fallback page
├── src/
│   ├── components/           # Reusable UI components
│   │   ├── common/
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Modal/
│   │   │   ├── Loading/
│   │   │   └── ErrorBoundary/
│   │   ├── audio/
│   │   │   ├── AudioPlayer/
│   │   │   ├── PlaylistControls/
│   │   │   ├── VolumeControl/
│   │   │   └── ProgressBar/
│   │   ├── search/
│   │   │   ├── SearchBar/
│   │   │   ├── SearchResults/
│   │   │   ├── SearchFilters/
│   │   │   └── TrendingSection/
│   │   ├── library/
│   │   │   ├── SongCard/
│   │   │   ├── LibraryGrid/
│   │   │   ├── PlaylistManager/
│   │   │   └── ConversionStatus/
│   │   ├── auth/
│   │   │   ├── LoginForm/
│   │   │   ├── OTPInput/
│   │   │   └── PhoneInput/
│   │   └── profile/
│   │       ├── ProfileCard/
│   │       ├── PreferencesForm/
│   │       └── StatsDisplay/
│   ├── pages/                # Page components
│   │   ├── Home/
│   │   ├── Search/
│   │   ├── Library/
│   │   ├── Profile/
│   │   ├── Login/
│   │   ├── Settings/
│   │   └── NotFound/
│   ├── hooks/                # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useAudioPlayer.ts
│   │   ├── useSearch.ts
│   │   ├── useLibrary.ts
│   │   ├── useOffline.ts
│   │   └── useNotifications.ts
│   ├── store/                # Redux store
│   │   ├── index.ts
│   │   ├── slices/
│   │   │   ├── authSlice.ts
│   │   │   ├── musicSlice.ts
│   │   │   ├── librarySlice.ts
│   │   │   ├── playerSlice.ts
│   │   │   └── uiSlice.ts
│   │   └── api/              # RTK Query API slices
│   │       ├── authApi.ts
│   │       ├── musicApi.ts
│   │       ├── libraryApi.ts
│   │       └── audioApi.ts
│   ├── services/             # External service integrations
│   │   ├── audioService.ts
│   │   ├── notificationService.ts
│   │   ├── cacheService.ts
│   │   └── analyticsService.ts
│   ├── utils/                # Utility functions
│   │   ├── constants.ts
│   │   ├── helpers.ts
│   │   ├── validators.ts
│   │   ├── formatters.ts
│   │   └── storage.ts
│   ├── styles/               # Global styles and themes
│   │   ├── globals.css
│   │   ├── theme.ts
│   │   ├── variables.css
│   │   └── components.css
│   ├── types/                # TypeScript type definitions
│   │   ├── api.ts
│   │   ├── music.ts
│   │   ├── user.ts
│   │   └── common.ts
│   ├── App.tsx
│   ├── index.tsx
│   └── setupTests.ts
├── package.json
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.js        # If using Tailwind CSS
└── .env.local
```

## Backend Structure (Node.js API)
```
backend/
├── src/
│   ├── controllers/          # Route handlers
│   │   ├── authController.ts
│   │   ├── musicController.ts
│   │   ├── libraryController.ts
│   │   ├── audioController.ts
│   │   ├── userController.ts
│   │   └── systemController.ts
│   ├── middleware/           # Express middleware
│   │   ├── auth.ts
│   │   ├── validation.ts
│   │   ├── rateLimit.ts
│   │   ├── errorHandler.ts
│   │   ├── cors.ts
│   │   └── logging.ts
│   ├── routes/               # API routes
│   │   ├── index.ts
│   │   ├── auth.ts
│   │   ├── music.ts
│   │   ├── library.ts
│   │   ├── audio.ts
│   │   ├── user.ts
│   │   └── system.ts
│   ├── services/             # Business logic services
│   │   ├── authService.ts
│   │   ├── musicService.ts
│   │   ├── libraryService.ts
│   │   ├── audioProcessingService.ts
│   │   ├── userService.ts
│   │   ├── notificationService.ts
│   │   └── cacheService.ts
│   ├── models/               # Database models
│   │   ├── User.ts
│   │   ├── Song.ts
│   │   ├── UserLibrary.ts
│   │   ├── ConversionJob.ts
│   │   └── Notification.ts
│   ├── integrations/         # External API integrations
│   │   ├── spotify/
│   │   │   ├── spotifyClient.ts
│   │   │   └── spotifyTypes.ts
│   │   ├── youtube/
│   │   │   ├── youtubeClient.ts
│   │   │   └── youtubeTypes.ts
│   │   ├── lastfm/
│   │   │   ├── lastfmClient.ts
│   │   │   └── lastfmTypes.ts
│   │   ├── lalal/
│   │   │   ├── lalalClient.ts
│   │   │   └── lalalTypes.ts
│   │   └── msg91/
│   │       ├── msg91Client.ts
│   │       └── msg91Types.ts
│   ├── utils/                # Utility functions
│   │   ├── constants.ts
│   │   ├── helpers.ts
│   │   ├── validators.ts
│   │   ├── encryption.ts
│   │   ├── logger.ts
│   │   └── queue.ts
│   ├── config/               # Configuration files
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   ├── aws.ts
│   │   ├── external-apis.ts
│   │   └── environment.ts
│   ├── types/                # TypeScript type definitions
│   │   ├── express.d.ts
│   │   ├── api.ts
│   │   ├── database.ts
│   │   └── external.ts
│   ├── jobs/                 # Background job processors
│   │   ├── audioConversionJob.ts
│   │   ├── notificationJob.ts
│   │   └── cleanupJob.ts
│   ├── websocket/            # WebSocket handlers
│   │   ├── socketServer.ts
│   │   ├── handlers/
│   │   │   ├── conversionHandler.ts
│   │   │   └── notificationHandler.ts
│   │   └── middleware/
│   │       └── socketAuth.ts
│   ├── app.ts                # Express app setup
│   └── server.ts             # Server entry point
├── tests/                    # Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── package.json
├── tsconfig.json
├── jest.config.js
├── .env.example
└── Dockerfile
```

## Shared Directory
```
shared/
├── types/                    # Shared TypeScript types
│   ├── api.ts
│   ├── music.ts
│   ├── user.ts
│   ├── audio.ts
│   └── common.ts
├── constants/                # Shared constants
│   ├── api.ts
│   ├── music.ts
│   └── errors.ts
├── utils/                    # Shared utility functions
│   ├── validation.ts
│   ├── formatting.ts
│   └── helpers.ts
└── package.json
```

## Documentation Directory
```
docs/
├── api/                      # API documentation
│   ├── authentication.md
│   ├── music-search.md
│   ├── library-management.md
│   └── audio-processing.md
├── deployment/               # Deployment guides
│   ├── aws-deployment.md
│   ├── docker-setup.md
│   └── environment-setup.md
├── development/              # Development guides
│   ├── getting-started.md
│   ├── coding-standards.md
│   ├── testing-guide.md
│   └── contribution-guide.md
├── user/                     # User documentation
│   ├── user-guide.md
│   ├── faq.md
│   └── troubleshooting.md
└── architecture/             # Technical documentation
    ├── system-design.md
    ├── database-schema.md
    └── security-considerations.md
```

## Scripts Directory
```
scripts/
├── build/
│   ├── build-frontend.sh
│   ├── build-backend.sh
│   └── build-all.sh
├── deploy/
│   ├── deploy-staging.sh
│   ├── deploy-production.sh
│   └── rollback.sh
├── database/
│   ├── migrate.js
│   ├── seed.js
│   └── backup.sh
├── development/
│   ├── setup-dev.sh
│   ├── start-services.sh
│   └── reset-db.sh
└── testing/
    ├── run-tests.sh
    ├── e2e-tests.sh
    └── performance-tests.sh
```

## Infrastructure Directory
```
infrastructure/
├── docker/
│   ├── Dockerfile.frontend
│   ├── Dockerfile.backend
│   ├── docker-compose.yml
│   ├── docker-compose.dev.yml
│   └── docker-compose.prod.yml
├── kubernetes/
│   ├── namespace.yaml
│   ├── frontend-deployment.yaml
│   ├── backend-deployment.yaml
│   ├── database-deployment.yaml
│   ├── redis-deployment.yaml
│   ├── ingress.yaml
│   └── secrets.yaml
├── terraform/                # Infrastructure as Code
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── modules/
│   │   ├── vpc/
│   │   ├── ecs/
│   │   ├── rds/
│   │   └── s3/
│   └── environments/
│       ├── dev/
│       ├── staging/
│       └── production/
├── nginx/
│   ├── nginx.conf
│   ├── ssl/
│   └── sites-available/
└── monitoring/
    ├── prometheus.yml
    ├── grafana/
    └── alerts/
```

## Configuration Files

### Root package.json
```json
{
  "name": "melody-studio",
  "version": "1.0.0",
  "description": "Multilingual Music PWA with AI-powered instrumental conversion",
  "private": true,
  "workspaces": [
    "frontend",
    "backend",
    "shared"
  ],
  "scripts": {
    "dev": "concurrently \"npm run dev:backend\" \"npm run dev:frontend\"",
    "dev:frontend": "cd frontend && npm run dev",
    "dev:backend": "cd backend && npm run dev",
    "build": "npm run build:shared && npm run build:backend && npm run build:frontend",
    "build:frontend": "cd frontend && npm run build",
    "build:backend": "cd backend && npm run build",
    "build:shared": "cd shared && npm run build",
    "test": "npm run test:backend && npm run test:frontend",
    "test:backend": "cd backend && npm run test",
    "test:frontend": "cd frontend && npm run test",
    "lint": "npm run lint:backend && npm run lint:frontend",
    "lint:backend": "cd backend && npm run lint",
    "lint:frontend": "cd frontend && npm run lint",
    "docker:build": "docker-compose build",
    "docker:up": "docker-compose up -d",
    "docker:down": "docker-compose down"
  },
  "devDependencies": {
    "concurrently": "^8.2.0",
    "husky": "^8.0.3",
    "lint-staged": "^13.2.0"
  }
}
```

### Docker Compose Configuration
```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://localhost:3001/api/v1
    depends_on:
      - backend

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "3001:3001"
    environment:
      - NODE_ENV=development
      - MONGODB_URI=mongodb://mongo:27017/melody_studio
      - REDIS_URL=redis://redis:6379
    depends_on:
      - mongo
      - redis
    volumes:
      - ./backend:/app
      - /app/node_modules

  mongo:
    image: mongo:6.0
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db
    environment:
      - MONGO_INITDB_DATABASE=melody_studio

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./infrastructure/nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./infrastructure/nginx/ssl:/etc/nginx/ssl
    depends_on:
      - frontend
      - backend

volumes:
  mongo_data:
  redis_data:
```

### Environment Variables (.env.example)
```bash
# Application
NODE_ENV=development
PORT=3001
APP_NAME=Melody Studio
APP_VERSION=1.0.0

# Database
MONGODB_URI=mongodb://localhost:27017/melody_studio
REDIS_URL=redis://localhost:6379

# JWT
JWT_SECRET=your-super-secret-jwt-key
JWT_REFRESH_SECRET=your-super-secret-refresh-key
JWT_EXPIRES_IN=15m
JWT_REFRESH_EXPIRES_IN=7d

# External APIs
SPOTIFY_CLIENT_ID=your-spotify-client-id
SPOTIFY_CLIENT_SECRET=your-spotify-client-secret
YOUTUBE_API_KEY=your-youtube-api-key
LASTFM_API_KEY=your-lastfm-api-key
LALAL_API_KEY=your-lalal-api-key
MSG91_AUTH_KEY=your-msg91-auth-key

# AWS S3
AWS_ACCESS_KEY_ID=your-aws-access-key
AWS_SECRET_ACCESS_KEY=your-aws-secret-key
AWS_REGION=us-east-1
AWS_S3_BUCKET=melody-studio-audio-files

# Cloudflare
CLOUDFLARE_ZONE_ID=your-cloudflare-zone-id
CLOUDFLARE_API_TOKEN=your-cloudflare-api-token

# Monitoring
SENTRY_DSN=your-sentry-dsn
DATADOG_API_KEY=your-datadog-api-key

# Rate Limiting
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
```

## Development Workflow

### 1. Initial Setup
```bash
# Clone repository
git clone <repository-url>
cd melody_studio

# Install dependencies
npm install

# Setup environment
cp .env.example .env
# Edit .env with your configuration

# Start development services
npm run docker:up
npm run dev
```

### 2. Development Commands
```bash
# Start all services
npm run dev

# Run tests
npm run test

# Lint code
npm run lint

# Build for production
npm run build

# Docker operations
npm run docker:build
npm run docker:up
npm run docker:down
```

### 3. Git Workflow
```bash
# Feature development
git checkout -b feature/music-search
git add .
git commit -m "feat: implement music search functionality"
git push origin feature/music-search

# Create pull request for review
```

This project structure provides a solid foundation for building a scalable, maintainable, and well-organized Melody Studio application with clear separation of concerns and modern development practices.