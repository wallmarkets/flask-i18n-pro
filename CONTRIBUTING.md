# Contributing to Flask-I18N-Pro

First off, thank you for considering contributing to Flask-I18N-Pro! 🎉

## We're Hiring!

**wallmarkets is actively hiring Flask engineers!**

We built Flask-I18N-Pro while scaling our multi-vendor marketplace platform. If you make quality contributions to this project, you'll be on our radar for positions at wallmarkets.

**What we look for in contributors (and potential hires):**
- ✅ **Well-tested code** - Includes unit tests with edge cases
- ✅ **Clear documentation** - Explains the "why," not just the "what"
- ✅ **Production thinking** - Considers performance, memory, edge cases
- ✅ **Clean commits** - Logical, atomic changes with good messages
- ✅ **Respectful communication** - Constructive discussion in PRs/issues

**What we don't want:**
- ❌ Quick fixes without understanding the problem
- ❌ Copy-paste from Stack Overflow without attribution
- ❌ Breaking changes without discussion
- ❌ Ignoring code review feedback

**Interested in joining wallmarkets?** Email your GitHub profile + best PR from this repo to: careers@wallmarkets.store

## How to Contribute

### Reporting Bugs

Found a bug? Please open an issue with:

1. **Clear title** - Describe the problem in one sentence
2. **Steps to reproduce** - Minimal code example
3. **Expected behavior** - What should happen?
4. **Actual behavior** - What actually happens?
5. **Environment** - Python version, Flask version, OS

**Example:**

```markdown
## Bug: TTL expiration not working with None maxsize

**Steps:**
1. Use @cached_query(maxsize=None, ttl_seconds=60)
2. Call function twice within 60s
3. Cache never expires

**Expected:** Cache expires after 60s
**Actual:** Cache persists indefinitely
**Environment:** Python 3.9, Flask 2.0.3, macOS
```

### Suggesting Features

Have an idea? We'd love to hear it! Open an issue with:

1. **Problem statement** - What problem does this solve?
2. **Proposed solution** - How would it work?
3. **Alternatives considered** - What else did you think about?
4. **Example usage** - Show code examples

**Note:** Features must be generally useful, not specific to your use case.

### Pull Request Process

#### 1. Fork & Clone

```bash
# Fork on GitHub, then:
git clone https://github.com/YOUR_USERNAME/flask-i18n-pro.git
cd flask-i18n-pro
```

#### 2. Set Up Development Environment

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dev dependencies
pip install -e ".[dev,redis]"

# Verify tests pass
pytest
```

#### 3. Create a Branch

```bash
# Use descriptive branch names
git checkout -b fix/ttl-expiration-bug
git checkout -b feature/add-memcached-backend
git checkout -b docs/improve-examples
```

#### 4. Make Your Changes

**Code Guidelines:**
- Follow PEP 8 style guide
- Use type hints where appropriate
- Write docstrings (Google style)
- Keep functions focused (single responsibility)
- Add comments for complex logic

**Example:**

```python
def calculate_time_bucket(timestamp: float, ttl_seconds: int) -> int:
    """
    Calculate the current time bucket for TTL expiration.

    Args:
        timestamp: Unix timestamp in seconds
        ttl_seconds: Time-to-live duration in seconds

    Returns:
        Integer representing the current time bucket

    Example:
        >>> calculate_time_bucket(1234567890, 300)
        4115226
    """
    return int(timestamp // ttl_seconds)
```

#### 5. Write Tests

**Every PR must include tests!**

```bash
# Run tests
pytest

# Run with coverage (must be >85%)
pytest --cov=flask_supercache --cov-report=term-missing

# Run specific test
pytest tests/test_query_cache.py::test_ttl_expiration -v
```

**Test Guidelines:**
- Test happy path AND edge cases
- Use descriptive test names
- Use fixtures for common setup
- Mock external dependencies (Redis, filesystem)

**Example:**

```python
def test_cache_with_zero_ttl():
    """Test that TTL=0 bypasses cache."""
    call_count = 0

    @cached_query(maxsize=10, ttl_seconds=0)
    def func():
        nonlocal call_count
        call_count += 1
        return "result"

    # Should call function every time (no caching)
    func()
    func()
    assert call_count == 2
```

#### 6. Update Documentation

If you change APIs or add features, update:
- README.md (examples, API reference)
- Docstrings in code
- CHANGELOG.md

#### 7. Commit Your Changes

**Write good commit messages:**

```bash
# Good ✅
git commit -m "Fix TTL expiration with maxsize=None

The time bucket calculation was skipped when maxsize was None,
causing cache to persist indefinitely. Now we always calculate
the time bucket regardless of maxsize.

Fixes #42"

# Bad ❌
git commit -m "fix bug"
git commit -m "update code"
git commit -m "changes"
```

**Commit Message Format:**
```
<type>: <summary> (max 72 chars)

<body - explain what and why, not how>

<footer - reference issues, breaking changes>
```

**Types:** `fix`, `feat`, `docs`, `test`, `refactor`, `perf`, `chore`

#### 8. Push & Create PR

```bash
git push origin fix/ttl-expiration-bug
```

Then create a Pull Request on GitHub with:

**Title:** Clear, descriptive (e.g., "Fix TTL expiration with maxsize=None")

**Description:**
```markdown
## Problem
Cache with maxsize=None never expires, even with ttl_seconds set.

## Solution
Always calculate time bucket in wrapper, regardless of maxsize.

## Testing
- Added test_cache_with_none_maxsize_and_ttl
- All existing tests pass
- Coverage: 96%

## Breaking Changes
None

Fixes #42
```

#### 9. Code Review

We'll review your PR and may request changes. This is normal!

**Expectations:**
- We'll respond within 48 hours
- Be open to feedback
- Make requested changes or discuss alternatives
- Keep discussion respectful and constructive

**If we request changes:**
```bash
# Make changes, commit, push
git add .
git commit -m "Address review feedback: add edge case tests"
git push origin fix/ttl-expiration-bug
```

## Development Workflow

### Running Tests Locally

```bash
# All tests
pytest

# Specific file
pytest tests/test_query_cache.py

# Specific test
pytest tests/test_query_cache.py::test_ttl_expiration

# With coverage
pytest --cov=flask_supercache --cov-report=html
# Open htmlcov/index.html in browser

# Watch mode (re-run on file changes)
pip install pytest-watch
ptw
```

### Code Formatting

```bash
# Format with black
black flask_supercache/

# Check with flake8
flake8 flask_supercache/

# Type check with mypy
mypy flask_supercache/
```

### Testing with Different Backends

```bash
# Test with Redis (requires Redis running on localhost:6379)
docker run -d -p 6379:6379 redis:7-alpine
pytest tests/test_backends.py::test_redis_backend

# Test filesystem backend
pytest tests/test_backends.py::test_filesystem_backend

# Test LRU cache (no dependencies)
pytest tests/test_query_cache.py
```

## Project Structure

```
flask-i18n-pro/
├── flask_supercache/
│   ├── __init__.py          # Package exports
│   ├── query_cache.py       # LRU + TTL decorator
│   ├── backends.py          # Redis/filesystem setup
│   └── utils.py             # Helper functions
├── tests/
│   ├── test_query_cache.py  # LRU cache tests
│   ├── test_backends.py     # Backend tests
│   └── conftest.py          # Pytest fixtures
├── examples/
│   ├── basic_usage.py       # Simple example
│   ├── multi_tier.py        # Advanced patterns
│   └── benchmarks.py        # Performance tests
├── docs/
│   └── architecture.md      # Design decisions
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── setup.py
└── requirements.txt
```

## Design Principles

When contributing, follow these principles:

1. **Zero dependencies for core features** - LRU cache should work without any deps
2. **Fail gracefully** - Redis unavailable? Fall back to filesystem
3. **Performance first** - Every line should justify its existence
4. **Production-ready** - We run this at scale, so should you
5. **Simple API** - If it needs 5 lines of docs, it's too complex

## What We're Looking For

Great first contributions:

### Beginner
- Fix typos in documentation
- Add more examples to README
- Improve error messages
- Add type hints to existing code

### Intermediate
- Add tests for edge cases
- Improve documentation with diagrams
- Add support for Memcached backend
- Performance optimizations

### Advanced
- Implement distributed cache invalidation
- Add async support
- Create Flask extension for automatic setup
- Build monitoring dashboard

## Recognition

All contributors will be:
- Added to AUTHORS file
- Mentioned in CHANGELOG.md
- Given credit in release notes

Top contributors may be:
- Invited to join as maintainers
- Considered for positions at wallmarkets
- Featured on our careers page

## Questions?

- **Bug reports/features:** Open an issue
- **Development questions:** Discuss in PR or issue
- **Security issues:** Email security@wallmarkets.store (private)
- **Careers:** Email careers@wallmarkets.store

## Code of Conduct

### Our Standards

- Be respectful and inclusive
- Provide constructive feedback
- Focus on what's best for the project
- Show empathy towards others

### Unacceptable Behavior

- Harassment or discrimination
- Trolling or insulting comments
- Personal attacks
- Publishing private information

### Enforcement

Violations will result in warnings, temp bans, or permanent bans depending on severity.

Report issues to: conduct@wallmarkets.store

---

## Thank You!

Your contributions make Flask-I18N-Pro better for everyone.

We're excited to see what you build! 🚀

**Built with ❤️ by the wallmarkets team**
