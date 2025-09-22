
# Melody Studio - Python + Angular Project Structure

## Root Directory Structure
```
melody_studio/
├── README.md
├── ARCHITECTURE_PYTHON_ANGULAR.md
├── TECHNOLOGY_STACK.md
├── API_SPECIFICATION.md
├── PROJECT_STRUCTURE_PYTHON_ANGULAR.md
├── DEVELOPMENT_ROADMAP_PYTHON_ANGULAR.md
├── docker-compose.yml
├── docker-compose.dev.yml
├── docker-compose.prod.yml
├── .env.example
├── .gitignore
├── pyproject.toml              # Python project configuration
├── backend/                    # FastAPI Python Backend
├── frontend/                   # Angular PWA Frontend
├── shared/                     # Shared types and utilities
├── docs/                       # Additional documentation
├── scripts/                    # Build and deployment scripts
├── infrastructure/             # Docker, K8s, and deployment configs
├── tests/                      # Integration and E2E tests
└── .github/                    # GitHub Actions workflows
```

## Backend Structure (FastAPI Python)
```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI application entry point
│   ├── core/                   # Core application configuration
│   │   ├── __init__.py
│   │   ├── config.py           # Application settings
│   │   ├── database.py         # Database connection and session
│   │   ├── security.py         # JWT and authentication utilities
│   │   ├── exceptions.py       # Custom exception handlers
│   │   └── middleware.py       # Custom middleware
│   ├── api/                    # API routes and endpoints
│   │   ├── __init__.py
│   │   ├── deps.py             # Dependency injection
│   │   ├── v1/                 # API version 1
│   │   │   ├── __init__.py
│   │   │   ├── router.py       # Main API router
│   │   │   └── endpoints/      # Individual endpoint modules
│   │   │       ├── __init__.py
│   │   │       ├── auth.py     # Authentication endpoints
│   │   │       ├── music.py    # Music search endpoints
│   │   │       ├── library.py  # User library endpoints
│   │   │       ├── audio.py    # Audio processing endpoints
│   │   │       ├── users.py    # User management endpoints
│   │   │       ├── system.py   # System health endpoints
│   │   │       └── websocket.py # WebSocket endpoints
│   ├── models/                 # SQLAlchemy database models
│   │   ├── __init__.py
│   │   ├── base.py             # Base model class
│   │   ├── user.py             # User model
│   │   ├── song.py             # Song model
│   │   ├── library.py          # User library model
│   │   ├── conversion_job.py   # Audio conversion job model
│   │   └── notification.py     # Notification model
│   ├── schemas/                # Pydantic request/response schemas
│   │   ├── __init__.py
│   │   ├── auth.py             # Authentication schemas
│   │   ├── music.py            # Music-related schemas
│   │   ├── library.py          # Library schemas
│   │   ├── audio.py            # Audio processing schemas
│   │   ├── user.py             # User schemas
│   │   └── common.py           # Common/shared schemas
│   ├── services/               # Business logic services
│   │   ├── __init__.py
│   │   ├── auth_service.py     # Authentication business logic
│   │   ├── music_service.py    # Music search and aggregation
│   │   ├── library_service.py  # User library management
│   │   ├── audio_service.py    # Audio processing coordination
│   │   ├── user_service.py     # User management
│   │   ├── notification_service.py # Notification handling
│   │   └── cache_service.py    # Caching utilities
│   ├── integrations/           # External API integrations
│   │   ├── __init__.py
│   │   ├── spotify/
│   │   │   ├── __init__.py
│   │   │   ├── client.py       # Spotify API client
│   │   │   ├── schemas.py      # Spotify response schemas
│   │   │   └── utils.py        # Spotify utilities
│   │   ├── youtube/
│   │   │   ├── __init__.py
│   │   │   ├── client.py       # YouTube API client
│   │   │   ├── schemas.py      # YouTube response schemas
│   │   │   └── utils.py        # YouTube utilities
│   │   ├── lastfm/
│   │   │   ├── __init__.py
│   │   │   ├── client.py       # Last.fm API client
│   │   │   ├── schemas.py      # Last.fm response schemas
│   │   │   └── utils.py        # Last.fm utilities
│   │   ├── lalal/
│   │   │   ├── __init__.py
│   │   │   ├── client.py       # LALAL.AI API client
│   │   │   ├── schemas.py      # LALAL.AI response schemas
│   │   │   └── utils.py        # LALAL.AI utilities
│   │   └── msg91/
│   │       ├── __init__.py
│   │       ├── client.py       # MSG91 SMS client
│   │       ├── schemas.py      # MSG91 response schemas
│   │       └── utils.py        # MSG91 utilities
│   ├── tasks/                  # Celery background tasks
│   │   ├── __init__.py
│   │   ├── celery_app.py       # Celery application setup
│   │   ├── audio_tasks.py      # Audio processing tasks
│   │   ├── notification_tasks.py # Notification tasks
│   │   └── cleanup_tasks.py    # Cleanup and maintenance tasks
│   ├── utils/                  # Utility functions
│   │   ├── __init__.py
│   │   ├── constants.py        # Application constants
│   │   ├── helpers.py          # General helper functions
│   │   ├── validators.py       # Custom validators
│   │   ├── formatters.py       # Data formatting utilities
│   │   ├── encryption.py       # Encryption utilities
│   │   ├── logger.py           # Logging configuration
│   │   └── storage.py          # File storage utilities
│   └── websocket/              # WebSocket handlers
│       ├── __init__.py
│       ├── connection_manager.py # WebSocket connection management
│       ├── handlers/
│       │   ├── __init__.py
│       │   ├── conversion_handler.py # Conversion progress handler
│       │   └── notification_handler.py # Notification handler
│       └── middleware.py       # WebSocket middleware
├── alembic/                    # Database migrations
│   ├── versions/               # Migration files
│   ├── env.py                  # Alembic environment
│   ├── script.py.mako          # Migration template
│   └── alembic.ini             # Alembic configuration
├── tests/                      # Backend tests
│   ├── __init__.py
│   ├── conftest.py             # Pytest configuration
│   ├── unit/                   # Unit tests
│   │   ├── __init__.py
│   │   ├── test_auth.py
│   │   ├── test_music.py
│   │   ├── test_library.py
│   │   └── test_audio.py
│   ├── integration/            # Integration tests
│   │   ├── __init__.py
│   │   ├── test_api.py
│   │   ├── test_database.py
│   │   └── test_external_apis.py
│   └── fixtures/               # Test fixtures and data
│       ├── __init__.py
│       ├── users.py
│       ├── songs.py
│       └── mock_responses.py
├── requirements/               # Python dependencies
│   ├── base.txt                # Base requirements
│   ├── dev.txt                 # Development requirements
│   ├── prod.txt                # Production requirements
│   └── test.txt                # Testing requirements
├── pyproject.toml              # Python project configuration
├── Dockerfile                  # Docker configuration
├── .env.example                # Environment variables template
└── README.md                   # Backend documentation
```

## Frontend Structure (Angular PWA)
```
frontend/
├── src/
│   ├── app/
│   │   ├── core/               # Core singleton services
│   │   │   ├── guards/
│   │   │   │   ├── auth.guard.ts
│   │   │   │   └── no-auth.guard.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── auth.interceptor.ts
│   │   │   │   ├── error.interceptor.ts
│   │   │   │   └── loading.interceptor.ts
│   │   │   ├── services/
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── api.service.ts
│   │   │   │   ├── storage.service.ts
│   │   │   │   ├── notification.service.ts
│   │   │   │   └── websocket.service.ts
│   │   │   └── core.module.ts
│   │   ├── shared/             # Shared components and utilities
│   │   │   ├── components/
│   │   │   │   ├── loading/
│   │   │   │   │   ├── loading.component.ts
│   │   │   │   │   ├── loading.component.html
│   │   │   │   │   └── loading.component.scss
│   │   │   │   ├── error-message/
│   │   │   │   │   ├── error-message.component.ts
│   │   │   │   │   ├── error-message.component.html
│   │   │   │   │   └── error-message.component.scss
│   │   │   │   ├── confirm-dialog/
│   │   │   │   │   ├── confirm-dialog.component.ts
│   │   │   │   │   ├── confirm-dialog.component.html
│   │   │   │   │   └── confirm-dialog.component.scss
│   │   │   │   └── pagination/
│   │   │   │       ├── pagination.component.ts
│   │   │   │       ├── pagination.component.html
│   │   │   │       └── pagination.component.scss
│   │   │   ├── directives/
│   │   │   │   ├── lazy-load.directive.ts
│   │   │   │   └── infinite-scroll.directive.ts
│   │   │   ├── pipes/
│   │   │   │   ├── duration.pipe.ts
│   │   │   │   ├── file-size.pipe.ts
│   │   │   │   └── time-ago.pipe.ts
│   │   │   ├── utils/
│   │   │   │   ├── constants.ts
│   │   │   │   ├── helpers.ts
│   │   │   │   ├── validators.ts
│   │   │   │   └── formatters.ts
│   │   │   └── shared.module.ts
│   │   ├── features/           # Feature modules
│   │   │   ├── auth/
│   │   │   │   ├── components/
│   │   │   │   │   ├── login/
│   │   │   │   │   │   ├── login.component.ts
│   │   │   │   │   │   ├── login.component.html
│   │   │   │   │   │   └── login.component.scss
│   │   │   │   │   ├── otp-input/
│   │   │   │   │   │   ├── otp-input.component.ts
│   │   │   │   │   │   ├── otp-input.component.html
│   │   │   │   │   │   └── otp-input.component.scss
│   │   │   │   │   └── phone-input/
│   │   │   │   │       ├── phone-input.component.ts
│   │   │   │   │       ├── phone-input.component.html
│   │   │   │   │       └── phone-input.component.scss
│   │   │   │   ├── services/
│   │   │   │   │   └── auth-api.service.ts
│   │   │   │   ├── store/
│   │   │   │   │   ├── auth.actions.ts
│   │   │   │   │   ├── auth.effects.ts
│   │   │   │   │   ├── auth.reducer.ts
│   │   │   │   │   ├── auth.selectors.ts
│   │   │   │   │   └── auth.state.ts
│   │   │   │   ├── auth-routing.module.ts
│   │   │   │   └── auth.module.ts
│   │   │   ├── music/
│   │   │   │   ├── components/
│   │   │   │   │   ├── search-bar/
│   │   │   │   │   │   ├── search-bar.component.ts
│   │   │   │   │   │   ├── search-bar.component.html
│   │   │   │   │   │   └── search-bar.component.scss
│   │   │   │   │   ├── search-results/
│   │   │   │   │   │   ├── search-results.component.ts
│   │   │   │   │   │   ├── search-results.component.html
│   │   │   │   │   │   └── search-results.component.scss
│   │   │   │   │   ├── track-card/
│   │   │   │   │   │   ├── track-card.component.ts
│   │   │   │   │   │   ├── track-card.component.html
│   │   │   │   │   │   └── track-card.component.scss
│   │   │   │   │   ├── search-filters/
│   │   │   │   │   │   ├── search-filters.component.ts
│   │   │   │   │   │   ├── search-filters.component.html
│   │   │   │   │   │   └── search-filters.component.scss
│   │   │   │   │   └── trending-section/
│   │   │   │   │       ├── trending-section.component.ts
│   │   │   │   │       ├── trending-section.component.html
│   │   │   │   │       └── trending-section.component.scss
│   │   │   │   ├── services/
│   │   │   │   │   └── music-api.service.ts
│   │   │   │   ├── store/
│   │   │   │   │   ├── music.actions.ts
│   │   │   │   │   ├── music.effects.ts
│   │   │   │   │   ├── music.reducer.ts
│   │   │   │   │   ├── music.selectors.ts
│   │   │   │   │   └── music.state.ts
│   │   │   │   ├── music-routing.module.ts
│   │   │   │   └── music.module.ts
│   │   │   ├── audio-player/
│   │   │   │   ├── components/
│   │   │   │   │   ├── player/
│   │   │   │   │   │   ├── player.component.ts
│   │   │   │   │   │   ├── player.component.html
│   │   │   │   │   │   └── player.component.scss
│   │   │   │   │   ├── mini-player/
│   │   │   │   │   │   ├── mini-player.component.ts
│   │   │   │   │   │   ├── mini-player.component.html
│   │   │   │   │   │   └── mini-player.component.scss
│   │   │   │   │   ├── progress-bar/
│   │   │   │   │   │   ├── progress-bar.component.ts
│   │   │   │   │   │   ├── progress-bar.component.html
│   │   │   │   │   │   └── progress-bar.component.scss
│   │   │   │   │   ├── volume-control/
│   │   │   │   │   │   ├── volume-control.component.ts
│   │   │   │   │   │   ├── volume-control.component.html
│   │   │   │   │   │   └── volume-control.component.scss
│   │   │   │   │   └── playlist-controls/
│   │   │   │   │       ├── playlist-controls.component.ts
│   │   │   │   │       ├── playlist-controls.component.html
│   │   │   │   │       └── playlist-controls.component.scss
│   │   │   │   ├── services/
│   │   │   │   │   ├── audio-player.service.ts
│   │   │   │   │   └── audio-visualizer.service.ts
│   │   │   │   ├── store/
│   │   │   │   │   ├── player.actions.ts
│   │   │   │   │   ├── player.effects.ts
│   │   │   │   │   ├── player.reducer.ts
│   │   │   │   │   ├── player.selectors.ts
│   │   │   │   │   └── player.state.ts
│   │   │   │   └── audio-player.module.ts
│   │   │   ├── library/
│   │   │   │   ├── components/
│   │   │   │   │   ├── library-grid/
│   │   │   │   │   │   ├── library-grid.component.ts
│   │   │   │   │   │   ├── library-grid.component.html
│   │   │   │   │   │   └── library-grid.component.scss
│   │   │   │   │   ├── song-card/
│   │   │   │   │   │   ├── song-card.component.ts
│   │   │   │   │   │   ├── song-card.component.html
│   │   │   │   │   │   └── song-card.component.scss
│   │   │   │   │   ├── playlist-manager/
│   │   │   │   │   │   ├── playlist-manager.component.ts
│   │   │   │   │   │   ├── playlist-manager.component.html
│   │   │   │   │   │   └── playlist-manager.component.scss
│   │   │   │   │   └── conversion-status/
│   │   │   │   │       ├── conversion-status.component.ts
│   │   │   │   │       ├── conversion-status.component.html
│   │   │   │   │       └── conversion-status.component.scss
│   │   │   │   ├── services/
│   │   │   │   │   └── library-api.service.ts
│   │   │   │   ├── store/
│   │   │   │   │   ├── library.actions.ts
│   │   │   │   │   ├── library.effects.ts
│   │   │   │   │   ├── library.reducer.ts
│   │   │   │   │   ├── library.selectors.ts
│   │   │   │   │   └── library.state.ts
│   │   │   │   ├── library-routing.module.ts
│   │   │   │   └── library.module.ts
│   │   │   ├── audio-processing/
│   │   │   │   ├── components/
│   │   │   │   │   ├── conversion-request/
│   │   │   │   │   │   ├── conversion-request.component.ts
│   │   │   │   │   │   ├── conversion-request.component.html
│   │   │   │   │   │   └── conversion-request.component.scss
│   │   │   │   │   ├── conversion-progress/
│   │   │   │   │   │   ├── conversion-progress.component.ts
│   │   │   │   │   │   ├── conversion-progress.component.html
│   │   │   │   │   │   └── conversion-progress.component.scss
│   │   │   │   │   └── conversion-history/
│   │   │   │   │       ├── conversion-history.component.ts
│   │   │   │   │       ├── conversion-history.component.html
│   │   │   │   │       └── conversion-history.component.scss
│   │   │   │   ├── services/
│   │   │   │   │   └── audio-processing-api.service.ts
│   │   │   │   ├── store/
│   │   │   │   │   ├── audio-processing.actions.ts
│   │   │   │   │   ├── audio-processing.effects.ts
│   │   │   │   │   ├── audio-processing.reducer.ts
│   │   │   │   │   ├── audio-processing.selectors.ts
│   │   │   │   │   └── audio-processing.state.ts
│   │   │   │   └── audio-processing.module.ts
│   │   │   └── user-profile/
│   │   │       ├── components/
│   │   │       │   ├── profile-card/
│   │   │       │   │   ├── profile-card.component.ts
│   │   │       │   │   ├── profile-card.component.html
│   │   │       │   │   └── profile-card.component.scss
│   │   │       │   ├── preferences-form/
│   │   │       │   │   ├── preferences-form.component.ts
│   │   │       │   │   ├── preferences-form.component.html
│   │   │       │   │   └── preferences-form.component.scss
│   │   │       │   └── stats-display/
│   │   │       │       ├── stats-display.component.ts
│   │   │       │       ├── stats-display.component.html
│   │   │       │       └── stats-display.component.scss
│   │   │       ├── services/
│   │   │       │   └── user-api.service.ts
│   │   │       ├── store/
│   │   │       │   ├── user.actions.ts
│   │   │       │   ├── user.effects.ts
│   │   │       │   ├── user.reducer.ts
│   │   │       │   ├── user.selectors.ts
│   │   │       │   └── user.state.ts
│   │   │       ├── user-profile-routing.module.ts
│   │   │       └── user-profile.module.ts
│   │   ├── layout/             # Layout components
│   │   │   ├── header/
│   │   │   │   ├── header.component.ts
│   │   │   │   ├── header.component.html
│   │   │   │   └── header.component.scss
│   │   │   ├── sidebar/
│   │   │   │   ├── sidebar.component.ts
│   │   │   │   ├── sidebar.component.html
│   │   │   │   └── sidebar.component.scss
│   │   │   ├── footer/
│   │   │   │   ├── footer.component.ts
│   │   │   │   ├── footer.component.html
│   │   │   │   └── footer.component.scss
│   │   │   └── main-layout/
│   │   │       ├── main-layout.component.ts
│   │   │       ├── main-layout.component.html
│   │   │       └── main-layout.component.scss
│   │   ├── pages/              # Page components
│   │   │   ├── home/
│   │   │   │   ├── home.component.ts
│   │   │   │   ├── home.component.html
│   │   │   │   └── home.component.scss
│   │   │   ├── search/
│   │   │   │   ├── search.component.ts
│   │   │   │   ├── search.component.html
│   │   │   │   └── search.component.scss
│   │   │   ├── library/
│   │   │   │   ├── library.component.ts
│   │   │   │   ├── library.component.html
│   │   │   │   └── library.component.scss
│   │   │   ├── profile/
│   │   │   │   ├── profile.component.ts
│   │   │   │   ├── profile.component.html
│   │   │   │   └── profile.component.scss
│   │   │   ├── settings/
│   │   │   │   ├── settings.component.ts
│   │   │   │   ├── settings.component.html
│   │   │   │   └── settings.component.scss
│   │   │   └── not-found/
│   │   │       ├── not-found.component.ts
│   │   │       ├── not-found.component.html
│   │   │       └── not-found.component.scss
│   │   ├── store/              # Root NgRx store
│   │   │   ├── app.effects.ts
│   │   │   ├── app.reducer.ts
│   │   │   ├── app.selectors.ts
│   │   │   └── app.state.ts
│   │   ├── app-routing.module.ts
│   │   ├── app.component.ts
│   │   ├── app.component.html
│   │   ├── app.component.scss
│   │   └── app.module.ts
│   ├── assets/                 # Static assets
│   │   ├── icons/              # PWA icons
│   │   ├── images/             # Application images
│   │   ├── fonts/              # Custom fonts
│   │   └── i18n/               # Internationalization files
│   ├── environments/           # Environment configurations
│   │   ├── environment.ts      # Development environment
│   │   ├── environment.prod.ts # Production environment
│   │   └── environment.staging.ts # Staging environment
│   ├── styles/                 # Global styles
│   │   ├── _variables.scss     # SCSS variables
│   │   ├── _mixins.scss        # SCSS mixins
│   │   ├── _themes.scss        # Material themes
│   │   └── styles.scss         # Global styles
│   ├── index.html              # Main HTML file
│   ├── main.ts                 # Application bootstrap
│   ├── polyfills.ts            # Browser polyfills
│   └── test.ts                 # Test configuration
├── public/                     # Public assets
│   ├── manifest.json           # PWA manifest
│   ├── ngsw-config.json        # Service worker configuration
│   └── robots.txt              # SEO robots file
├── e2e/                        # End-to-end tests
│   ├── src/
│   │   ├── app.e2e-spec.ts
│   │   ├── app.po.ts
│   │   └── page-objects/
│   ├── protractor.conf.js
│   └── tsconfig.json
├── angular.json                # Angular CLI configuration
├── package.json                # NPM dependencies
├── tsconfig.json               # TypeScript configuration
├── tsconfig.app.json           # App TypeScript configuration
├── tsconfig.spec.json          # Test TypeScript configuration
├── karma.conf.js               # Karma test configuration
├── .eslintrc.json              # ESLint configuration
├── .prettierrc                 # Prettier configuration
├── Dockerfile                  # Docker configuration
└── README.md                   # Frontend documentation
```

## Shared Directory
```
shared/
├── python/                     # Python shared utilities
│   ├── __init__.py
│   ├── types/                  # Shared type definitions
│   │   ├── __init__.py
│   │   ├── api.py              # API response types
│   │   ├── music.py            # Music-related types
│   │   ├── user.py             # User-related types
│   │   └── common.py           # Common types
│   ├── constants/              # Shared constants
│   │   ├── __init__.py
│   │   ├── api.py              # API constants
│   │   ├── music.py            # Music constants
│   │   └── errors.py           # Error constants
│   └── utils/                  # Shared utility functions
│       ├── __init__.py
│       ├── validation.py       # Validation utilities
│       ├── formatting.py       # Formatting utilities
│       └── helpers.py          # General helpers
├── typescript/                 # TypeScript shared utilities
│   ├── types/                  # Shared type definitions
│   │   ├── api.types.ts        # API response types
│   │   ├── music.types.ts      # Music-related types
│   │   ├── user.types.ts       # User-related types
│   │   └── common.types.ts     # Common types
│   ├── constants/              # Shared constants
│   │   ├── api.constants.ts    # API constants
│   │   ├── music.constants.ts  # Music constants
│   │   └── error.constants.ts  # Error constants
│   └── utils/                  # Shared utility functions
│       ├── validation.utils.ts # Validation utilities
│       ├── formatting.utils.ts # Formatting utilities
│       └── helper.utils.ts     # General helpers
├── package.json                # Shared package configuration
└── README.md                   # Shared utilities documentation
```

## Configuration Files

### Root pyproject.toml
```toml
[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"

[tool.poetry]
name = "melody-studio"
version = "2.0.0"
description = "Multilingual Music PWA with AI-powered instrumental conversion"
authors = ["Melody Studio Team <team@melodystudio.com>"]
readme = "README.md"
packages = [{include = "backend"}]

[tool.poetry.dependencies]
python = "^3.11"
fastapi = "^0.104.0"
uvicorn = {extras = ["standard"], version = "^0