# Expert Review Analysis System V2 - AI Agent Instructions

**Version:** 2.1.1 | **Stack:** Python 3.11+ Flask backend + Vanilla JS ES6 frontend | **Deployment:** Self-hosted + Docker

---

> **🤖 AI Development Acknowledgment**  
> This codebase was developed collaboratively with AI assistants (GitHub Copilot, Claude). These instructions guide AI agents to maintain consistency with established patterns. All AI contributions are reviewed and validated by human developers.

---

## 🎯 System Overview

A web scraping + NLP analysis engine that extracts reviews from IMDb/Steam/Metacritic, performs theme detection (20 vocabularies), sentiment alignment, and generates personalized media recommendations. Built as monorepo with dual deployment strategies.

### Architecture Pattern: Split Self-Hosted/Containerized

```
self-hosted/
  backend/         ← Primary development location
    api_server.py  ← Flask REST API (MUST sync to root/)
    expert_review_system.py  ← Analysis pipeline (scraping, filtering, summarization)
    preference_store.py      ← JSON-based CRUD persistence
  frontend/        ← Vanilla JS ES6 modules
    app.js         ← Main app (ExpertReviewApp class, AppState management)
    api.js         ← APIClient + MockAPIClient classes
    utils.js       ← XSS-safe DOM helpers (getElement, sanitizeHTML, showLoading)
    config.js      ← CONFIG constants (API_BASE_URL, CLASSES, RECOMMENDATION_LEVELS)
containerized/     ← Docker deployment (multi-stage Dockerfile, gunicorn WSGI)
api_server.py      ← SYNCED COPY from self-hosted/backend/ (sync before commits!)
```

**Critical File Sync:** `self-hosted/backend/api_server.py` ↔️ `api_server.py` (root)

```powershell
Copy-Item "self-hosted\backend\api_server.py" "." -Force  # Run before committing
```

---

## ⚠️ Pre-Commit Checklist (MANDATORY)

```powershell
# 1. Security scan (NEVER commit secrets)
git grep -i "password\|secret\|api_key\|token" -- '*.py' '*.js' '*.json'

# 2. Update CHANGELOG.md [Unreleased] section
# 3. Sync api_server.py if modified
Copy-Item "self-hosted\backend\api_server.py" "." -Force

# 4. Use conventional commits: <type>(<scope>): <description>
#    Types: feat|fix|docs|chore|refactor|test|style|perf
git commit -m "feat(backend): add theme caching layer"
```

---

## 🔧 Development Workflows

### Local Development (Self-Hosted)

```powershell
cd self-hosted
.\scripts\setup.bat           # Create venv, install deps, copy .env.example
.\scripts\start_all.bat       # Backend (5000) + Frontend (8000) in new windows
# OR start individually:
.\scripts\start_server.bat    # Backend only
.\scripts\start_frontend.bat  # Frontend only
```

**Configuration:** Edit `self-hosted/.env` (never commit!). Placeholders in `.env.example`.

### Docker Deployment

```powershell
cd containerized
docker-compose up --build  # Gunicorn (5000) + http.server (8000)
```

**Health Check:** `GET /api/health` returns `{'status': 'healthy', 'version': '2.1.1'}`

---

## 📐 Code Conventions & Patterns

### Backend (Python)

**Structured Logging (JSON):**

```python
from api_server import log_structured
log_structured('info', 'Analysis complete',
               analysis_id=result.analysis_id,
               duration_ms=duration,
               recommendation=result.recommendation)
```

**Config from Environment:**

```python
from expert_review_system import Config
Config.MODEL_NAME  # nlptown/bert-base-multilingual-uncased-sentiment
Config.RATE_LIMIT_MAX  # 100 requests
Config.LOG_LEVEL  # INFO
```

**Rate Limiting Pattern:**

```python
from expert_review_system import RateLimiter
limiter = RateLimiter(max_requests=100, window_seconds=60)
if not limiter.can_proceed():
    return jsonify({'error': 'Rate limit exceeded', 'retry_after': limiter.wait_time()}), 429
```

**Input Validation:**

```python
from expert_review_system import InputValidator
is_valid, error_msg = InputValidator.validate_title_info(data)
```

### Frontend (JavaScript)

**XSS-Safe DOM Manipulation (ALWAYS use utils.js):**

```javascript
import {
  getElement,
  sanitizeHTML,
  escapeHTML,
  showLoading,
  hideLoading,
} from "./utils.js";

const card = getElement("results-card"); // Throws if missing
const html = sanitizeHTML(userInput); // DOMPurify sanitization
const text = escapeHTML(userInput); // Entity encoding for text
```

**Loading States:**

```javascript
showLoading(submitBtn, "button"); // Adds .btn--loading, sets aria-busy="true", disabled
hideLoading(submitBtn, "button"); // Removes loading state
// Types: 'button' | 'form' | 'card' | 'overlay'
```

**API Client Usage:**

```javascript
import { APIClient, MockAPIClient } from "./api.js";
import { CONFIG } from "./config.js";

const api = CONFIG.ENABLE_MOCK_DATA ? new MockAPIClient() : new APIClient();
const result = await api.analyzeTitle(titleData); // Returns analysis result
```

**CSS BEM Methodology:**

```css
.analysis-card {
}
.analysis-card__title {
}
.analysis-card__content {
}
.analysis-card--loading {
} /* Modifier */
```

---

## 🚀 Version Management (SemVer)

**Update ALL 6 files when releasing:**

1. `README.md` line 7: `version-X.Y.Z` badge
2. `README.md` ~line 340: Version history
3. `CHANGELOG.md`: Move `[Unreleased]` → `[X.Y.Z] - YYYY-MM-DD`
4. `self-hosted/backend/api_server.py` line 43: `'version': 'X.Y.Z'`
5. `api_server.py` (root, synced from #4)
6. `ToDos.md` line 59: `**Version:** X.Y.Z`

**Find all version strings:**

```powershell
git grep -E "version.*[0-9]+\.[0-9]+\.[0-9]+" -- '*.md' '*.py' '*.json'
```

**Release workflow:**

```powershell
git tag -a v2.2.0 -m "Release version 2.2.0"
git push origin main --tags
```

---

## 🔍 Key Integration Points

### Web Scraping (expert_review_system.py)

- **IMDb:** BeautifulSoup with User-Agent rotation, `.review-container` selector
- **Steam:** Official API via `requests`, requires `STEAM_API_KEY` in .env
- **Metacritic:** BeautifulSoup with `.review_content` selector, rate limiting

**Graceful Degradation:** Falls back to mock data if scraping fails (logged as warning).

### Theme Analysis

20 predefined themes with keyword dictionaries in `ExpertReviewAnalyst.THEME_VOCABULARIES`:

```python
'action': ['explosion', 'chase', 'fight', 'intense', ...],
'drama': ['emotional', 'character development', 'deep', ...],
# ... 18 more themes
```

**Similarity Scoring:** TF-IDF cosine similarity between review keywords and theme vocabularies.

### API Endpoints

```
POST /api/analyze        ← Main analysis (rate limited, structured logging)
GET  /api/metrics        ← System performance stats
GET  /api/preferences    ← List all user preferences
POST /api/preferences    ← Create preference
GET  /api/preferences/<id>   ← Get single preference
PUT  /api/preferences/<id>   ← Update preference
DELETE /api/preferences/<id> ← Delete preference
GET  /api/health         ← Health check (returns version)
```

---

## 🧪 Testing Strategy (Future Implementation)

**Unit Tests (pytest):**

```python
# tests/test_expert_review_system.py
def test_filter_reviews_deduplication():
    reviews = [{'text': 'Great!', 'author': 'A'}, {'text': 'Great!', 'author': 'B'}]
    filtered = filter_reviews(reviews)
    assert len(filtered) == 1
```

**Integration Tests (Flask test client):**

```python
# tests/test_api.py
def test_analyze_endpoint(client):
    response = client.post('/api/analyze', json={'name': 'Test', 'media_type': 'movie'})
    assert response.status_code == 200
```

---

## 📚 Reference Documentation

- **Full Guidelines:** See `.ai-instructions/GUIDELINES.md` for detailed code style, accessibility requirements, error handling
- **Open Tasks:** `ToDos.md` tracks remaining features (dark mode, unit tests, ARIA improvements)
- **Changelog:** `CHANGELOG.md` (Keep a Changelog format, update `[Unreleased]` before commits)

---

## Quick Reference

**Security scan:**

```powershell
git grep -i "password\|secret\|api_key\|token" -- '*.py' '*.js' '*.json'
```

**Version consistency check:**

```powershell
git grep -E "version.*[0-9]+\.[0-9]+\.[0-9]+" -- '*.md' '*.py' '*.json'
```

**Sync api_server.py:**

```powershell
Copy-Item "self-hosted\backend\api_server.py" "." -Force
```

**Conventional commit examples:**

- `feat(backend): add caching layer for theme analysis`
- `fix(frontend): resolve XSS vulnerability in user input`
- `docs(readme): update installation instructions`
- `chore(release): bump version to 2.2.0`

---

**For comprehensive documentation, see `.ai-instructions/GUIDELINES.md` (878 lines) - this file is a condensed reference for quick onboarding.**
