# Melody Studio - API Specification

## Base URL
- **Development**: `http://localhost:3000/api/v1`
- **Production**: `https://api.melodystudio.com/v1`

## Authentication
All protected endpoints require a Bearer token in the Authorization header:
```
Authorization: Bearer <jwt_token>
```

## Response Format
All API responses follow this standard format:
```json
{
  "success": boolean,
  "data": object | array | null,
  "message": string,
  "error": {
    "code": string,
    "details": object
  } | null,
  "pagination": {
    "page": number,
    "limit": number,
    "total": number,
    "totalPages": number
  } | null
}
```

## Error Codes
- `AUTH_REQUIRED` - Authentication required
- `INVALID_TOKEN` - Invalid or expired token
- `INVALID_OTP` - Invalid OTP provided
- `RATE_LIMIT_EXCEEDED` - Too many requests
- `VALIDATION_ERROR` - Request validation failed
- `RESOURCE_NOT_FOUND` - Requested resource not found
- `EXTERNAL_API_ERROR` - External service error
- `PROCESSING_ERROR` - Audio processing error
- `STORAGE_ERROR` - File storage error

---

## Authentication Endpoints

### POST /auth/request-otp
Request OTP for phone number verification.

**Request Body:**
```json
{
  "phoneNumber": "9876543210",
  "countryCode": "+91"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "otpId": "otp_12345",
    "expiresIn": 300
  },
  "message": "OTP sent successfully"
}
```

**Rate Limit:** 3 requests per hour per phone number

### POST /auth/verify-otp
Verify OTP and authenticate user.

**Request Body:**
```json
{
  "otpId": "otp_12345",
  "otp": "123456",
  "phoneNumber": "9876543210",
  "countryCode": "+91"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_12345",
      "phoneNumber": "9876543210",
      "countryCode": "+91",
      "isVerified": true,
      "profile": {
        "name": null,
        "avatar": null
      }
    },
    "tokens": {
      "accessToken": "jwt_access_token",
      "refreshToken": "jwt_refresh_token",
      "expiresIn": 900
    }
  },
  "message": "Authentication successful"
}
```

### POST /auth/refresh-token
Refresh access token using refresh token.

**Request Body:**
```json
{
  "refreshToken": "jwt_refresh_token"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "accessToken": "new_jwt_access_token",
    "expiresIn": 900
  },
  "message": "Token refreshed successfully"
}
```

### POST /auth/logout
Logout user and invalidate tokens.

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

## Music Search Endpoints

### GET /music/search
Search for songs across multiple platforms.

**Query Parameters:**
- `q` (required): Search query
- `type`: `track` | `artist` | `album` (default: `track`)
- `limit`: Number of results (default: 20, max: 50)
- `offset`: Pagination offset (default: 0)
- `language`: Filter by language code (e.g., `hi`, `en`, `ta`)
- `source`: `spotify` | `youtube` | `lastfm` | `all` (default: `all`)

**Example Request:**
```
GET /music/search?q=tum hi ho&limit=10&language=hi
```

**Response:**
```json
{
  "success": true,
  "data": {
    "tracks": [
      {
        "id": "track_12345",
        "title": "Tum Hi Ho",
        "artist": "Arijit Singh",
        "album": "Aashiqui 2",
        "language": "hi",
        "duration": 262,
        "genre": ["Bollywood", "Romance"],
        "releaseYear": 2013,
        "popularity": 95,
        "explicit": false,
        "thumbnail": "https://cdn.example.com/thumb.jpg",
        "sources": {
          "spotify": {
            "id": "spotify_track_id",
            "previewUrl": "https://spotify.com/preview.mp3",
            "available": true
          },
          "youtube": {
            "id": "youtube_video_id",
            "url": "https://youtube.com/watch?v=xyz",
            "available": true
          }
        }
      }
    ]
  },
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 150,
    "totalPages": 15
  },
  "message": "Search completed successfully"
}
```

### GET /music/track/:trackId
Get detailed information about a specific track.

**Response:**
```json
{
  "success": true,
  "data": {
    "track": {
      "id": "track_12345",
      "title": "Tum Hi Ho",
      "artist": "Arijit Singh",
      "album": "Aashiqui 2",
      "language": "hi",
      "duration": 262,
      "genre": ["Bollywood", "Romance"],
      "releaseYear": 2013,
      "popularity": 95,
      "explicit": false,
      "thumbnail": "https://cdn.example.com/thumb.jpg",
      "lyrics": "Tum hi ho bandhu...",
      "sources": {
        "spotify": {
          "id": "spotify_track_id",
          "previewUrl": "https://spotify.com/preview.mp3",
          "available": true
        },
        "youtube": {
          "id": "youtube_video_id",
          "url": "https://youtube.com/watch?v=xyz",
          "available": true
        }
      },
      "metadata": {
        "bpm": 120,
        "key": "C major",
        "energy": 0.7,
        "danceability": 0.6
      }
    }
  },
  "message": "Track details retrieved successfully"
}
```

### GET /music/trending
Get trending songs by language or globally.

**Query Parameters:**
- `language`: Language code (optional)
- `limit`: Number of results (default: 20, max: 50)
- `timeframe`: `daily` | `weekly` | `monthly` (default: `weekly`)

**Response:**
```json
{
  "success": true,
  "data": {
    "tracks": [
      // Array of track objects similar to search results
    ],
    "timeframe": "weekly",
    "language": "hi"
  },
  "message": "Trending tracks retrieved successfully"
}
```

---

## User Library Endpoints

### GET /library/songs
Get user's saved songs.

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `limit`: Number of results (default: 20, max: 100)
- `offset`: Pagination offset (default: 0)
- `sortBy`: `addedAt` | `title` | `artist` | `playCount` (default: `addedAt`)
- `sortOrder`: `asc` | `desc` (default: `desc`)
- `hasInstrumental`: `true` | `false` (filter by instrumental availability)

**Response:**
```json
{
  "success": true,
  "data": {
    "songs": [
      {
        "id": "library_12345",
        "track": {
          // Track object as defined above
        },
        "addedAt": "2024-01-15T10:30:00Z",
        "playCount": 15,
        "lastPlayedAt": "2024-01-20T14:22:00Z",
        "hasInstrumental": true,
        "instrumentalStatus": "completed",
        "instrumentalUrl": "https://cdn.example.com/instrumental.mp3"
      }
    ]
  },
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "totalPages": 3
  },
  "message": "Library retrieved successfully"
}
```

### POST /library/songs
Add a song to user's library.

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "trackId": "track_12345"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "libraryItem": {
      "id": "library_12345",
      "trackId": "track_12345",
      "addedAt": "2024-01-15T10:30:00Z",
      "playCount": 0,
      "hasInstrumental": false,
      "instrumentalStatus": null
    }
  },
  "message": "Song added to library successfully"
}
```

### DELETE /library/songs/:libraryId
Remove a song from user's library.

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "message": "Song removed from library successfully"
}
```

### POST /library/songs/:libraryId/play
Record a play event for analytics.

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "duration": 180,
  "completed": true
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "playCount": 16,
    "lastPlayedAt": "2024-01-20T14:22:00Z"
  },
  "message": "Play event recorded successfully"
}
```

---

## Audio Processing Endpoints

### POST /audio/convert-to-instrumental
Request instrumental conversion for a song.

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "libraryId": "library_12345",
  "audioUrl": "https://example.com/original-song.mp3",
  "priority": "normal"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "jobId": "job_12345",
    "status": "queued",
    "estimatedTime": 120,
    "queuePosition": 3
  },
  "message": "Conversion job created successfully"
}
```

### GET /audio/conversion-status/:jobId
Get status of instrumental conversion job.

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "data": {
    "jobId": "job_12345",
    "status": "processing",
    "progress": 65,
    "estimatedTimeRemaining": 45,
    "startedAt": "2024-01-15T10:35:00Z",
    "completedAt": null,
    "error": null
  },
  "message": "Conversion status retrieved successfully"
}
```

**Status Values:**
- `queued` - Job is in queue
- `processing` - Currently being processed
- `completed` - Successfully completed
- `failed` - Processing failed
- `cancelled` - Job was cancelled

### GET /audio/conversion-history
Get user's conversion history.

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `limit`: Number of results (default: 20, max: 50)
- `offset`: Pagination offset (default: 0)
- `status`: Filter by status

**Response:**
```json
{
  "success": true,
  "data": {
    "conversions": [
      {
        "jobId": "job_12345",
        "libraryId": "library_12345",
        "track": {
          "title": "Tum Hi Ho",
          "artist": "Arijit Singh"
        },
        "status": "completed",
        "createdAt": "2024-01-15T10:35:00Z",
        "completedAt": "2024-01-15T10:37:30Z",
        "instrumentalUrl": "https://cdn.example.com/instrumental.mp3"
      }
    ]
  },
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 12,
    "totalPages": 1
  },
  "message": "Conversion history retrieved successfully"
}
```

---

## User Profile Endpoints

### GET /user/profile
Get user profile information.

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_12345",
      "phoneNumber": "9876543210",
      "countryCode": "+91",
      "isVerified": true,
      "profile": {
        "name": "John Doe",
        "avatar": "https://cdn.example.com/avatar.jpg",
        "preferences": {
          "languages": ["hi", "en", "ta"],
          "genres": ["Bollywood", "Pop", "Rock"],
          "autoConvertToInstrumental": false,
          "notificationsEnabled": true
        }
      },
      "stats": {
        "totalSongs": 45,
        "totalPlaytime": 12450,
        "instrumentalConversions": 8,
        "joinedAt": "2024-01-01T00:00:00Z"
      }
    }
  },
  "message": "Profile retrieved successfully"
}
```

### PUT /user/profile
Update user profile information.

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "John Doe",
  "preferences": {
    "languages": ["hi", "en", "ta"],
    "genres": ["Bollywood", "Pop", "Rock"],
    "autoConvertToInstrumental": false,
    "notificationsEnabled": true
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "user": {
      // Updated user object
    }
  },
  "message": "Profile updated successfully"
}
```

### POST /user/avatar
Upload user avatar image.

**Headers:** `Authorization: Bearer <token>`

**Request:** Multipart form data with `avatar` file field

**Response:**
```json
{
  "success": true,
  "data": {
    "avatarUrl": "https://cdn.example.com/avatar.jpg"
  },
  "message": "Avatar uploaded successfully"
}
```

---

## Notification Endpoints

### GET /notifications
Get user notifications.

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `limit`: Number of results (default: 20, max: 50)
- `offset`: Pagination offset (default: 0)
- `unreadOnly`: `true` | `false` (default: `false`)

**Response:**
```json
{
  "success": true,
  "data": {
    "notifications": [
      {
        "id": "notif_12345",
        "type": "conversion_completed",
        "title": "Instrumental Ready!",
        "message": "Your instrumental version of 'Tum Hi Ho' is ready to play.",
        "data": {
          "jobId": "job_12345",
          "libraryId": "library_12345"
        },
        "read": false,
        "createdAt": "2024-01-15T10:37:30Z"
      }
    ]
  },
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 5,
    "totalPages": 1
  },
  "message": "Notifications retrieved successfully"
}
```

### PUT /notifications/:notificationId/read
Mark notification as read.

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "message": "Notification marked as read"
}
```

### PUT /notifications/read-all
Mark all notifications as read.

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "message": "All notifications marked as read"
}
```

---

## System Endpoints

### GET /health
Health check endpoint (no authentication required).

**Response:**
```json
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2024-01-15T10:30:00Z",
    "version": "1.0.0",
    "services": {
      "database": "healthy",
      "redis": "healthy",
      "spotify": "healthy",
      "youtube": "healthy",
      "lalal": "healthy",
      "msg91": "healthy"
    }
  },
  "message": "System is healthy"
}
```

### GET /version
Get API version information (no authentication required).

**Response:**
```json
{
  "success": true,
  "data": {
    "version": "1.0.0",
    "buildDate": "2024-01-15T00:00:00Z",
    "gitCommit": "abc123def",
    "environment": "production"
  },
  "message": "Version information retrieved"
}
```

---

## WebSocket Events

### Connection
Connect to WebSocket for real-time updates:
```
ws://localhost:3000/ws?token=<jwt_token>
```

### Events

#### conversion_progress
Real-time updates for instrumental conversion progress.
```json
{
  "event": "conversion_progress",
  "data": {
    "jobId": "job_12345",
    "progress": 75,
    "status": "processing",
    "estimatedTimeRemaining": 30
  }
}
```

#### conversion_completed
Notification when conversion is completed.
```json
{
  "event": "conversion_completed",
  "data": {
    "jobId": "job_12345",
    "libraryId": "library_12345",
    "instrumentalUrl": "https://cdn.example.com/instrumental.mp3"
  }
}
```

#### notification
Real-time notification delivery.
```json
{
  "event": "notification",
  "data": {
    "id": "notif_12345",
    "type": "conversion_completed",
    "title": "Instrumental Ready!",
    "message": "Your instrumental version is ready to play."
  }
}
```

---

## Rate Limiting

### Default Limits
- **Authentication**: 3 OTP requests per hour per phone number
- **Search**: 100 requests per minute per user
- **Library Operations**: 200 requests per minute per user
- **Audio Conversion**: 5 requests per hour per user
- **Profile Updates**: 10 requests per minute per user

### Headers
Rate limit information is included in response headers:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1642248000
```

This API specification provides a comprehensive foundation for building Melody Studio with all the required features including authentication, music search, library management, and AI-powered instrumental conversion.