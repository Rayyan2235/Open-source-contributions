# Contribution [#9850]: [Add alternative Status Badge with first letter capitalized
 #9850]

**Contribution Number:** [1 / 2 / 3]  
**Student:** Rayyan Syed
**Issue:** https://github.com/WeblateOrg/weblate/issues/9850
**Status:** [Phase II  Complete]

---

## Why I Chose This Issue

I chose this because this seems begineer friendly, choosing 2 languages I am familar with just to avoid any confusion. The Author already pointed to the exact file and line to change, and even suggested the implementation approach (adding a URL parameter). The fix is almost entirely Python, which is my primary language, and it's a real feature that actual users have requested

---

## Understanding the Issue

### Problem Description

The status badges in Weblate only display text in lowercase, and this issue adds an option to capitalize the first letter via a URL parameter.

### Expected Behavior

Should have an option to capitalize the first letter of the status badge and be consistent compared to other repo's like https://github.com/flathub/website 

### Current Behavior

When opening a project and checking the status badge, it displays the status badge with all the first letter as lowercase 'translated 76%'. Whereas, we want to give the user an option to toggle the first letter to be uppercase or not.

### Affected Components

1. **`weblate/trans/widgets.py`**
    - **`SVGBadgeWidget.render()`** (around the `gettext("translated")` line you called out)
    - Where you’ll:
        - add an `extra_parameters` entry for the new `capitalize` option (following the existing pattern)
        - read the parameter inside `render()` and apply capitalization to the label text
2. **`weblate/trans/widgets.py` (same file) — inheritance impact**
    - **`PNGBadgeWidget`** inherits from `SVGBadgeWidget`
    - Meaning: fixing `SVGBadgeWidget` should automatically fix the PNG badge output too (unless PNG overrides the relevant part of rendering)

---

## Reproduction Process

### Environment Setup
- OS: Windows 11 with WSL2 (Ubuntu 24.04)
- Docker Desktop with WSL integration enabled
- Key fix required: Added `UV_NO_CACHE: "1"` to docker-compose.yml to resolve uv cache corruption on Windows filesystem
- Key fix required: Changed data volume from `$PWD/data:/app/data` to `weblate-data:/app/data` to resolve Linux permission errors on Windows-mounted paths

### Steps to Reproduce
1. Run `./rundev.sh` from WSL terminal inside the repo
2. Navigate to `http://localhost:8080`
3. Run `docker exec dev-docker-weblate-1 weblate import_demo` to create demo data
4. Go to `http://localhost:8080/widgets/demo/`
5. Observe the Live Preview shows "translated 96%" — first letter is lowercase

### Branch Link
https://github.com/Rayyan2235/weblate/tree/feature/capitalize-status-badge
### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** <img width="1840" height="868" alt="image" src="https://github.com/user-attachments/assets/dc4cc200-b4f2-42d9-8ede-0237a69613e9" />

- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):
### Implementation Plan (UMPIRE)

**Understand:** The SVG status badge displays "translated X%" with a lowercase first letter. Some repositories style all badges with an uppercase first letter. There is no option to capitalize it. The fix should add an optional URL parameter so users can opt into capitalized text without changing the default behavior.

**Match:** `LanguageBadgeWidget` in `weblate/trans/widgets.py` already uses an `extra_parameters` system to add a `threshold` parameter. This is the exact pattern to follow — it handles both the URL parameter parsing and the Widget Settings UI dropdown automatically.

**Plan:**
1. In `weblate/trans/widgets.py`, add an `extra_parameters` entry to `SVGBadgeWidget` for a `capitalize` option (boolean/checkbox type)
2. In `SVGBadgeWidget.render()` around line 342, read the parameter and apply `.capitalize()` to `translated_text` if set
3. Add a test in `weblate/trans/tests/test_widgets.py` verifying the capitalized output

**Review:** Check Weblate's CONTRIBUTING.md for commit message format and PR conventions before submitting

**Evaluate:** Run `docker exec dev-docker-weblate-1 pytest weblate/trans/tests/test_widgets.py` and visually verify the Live Preview on the widgets page shows "Translated 96%" when the parameter is enabled
---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes
## Implementation Notes

### Week 3 Progress
Built the core fix for issue #9850. Added a `capitalize` URL parameter to `SVGBadgeWidget` 
in `weblate/trans/widgets.py` following the existing `extra_parameters` pattern used by 
`LanguageBadgeWidget` for its `threshold` parameter (as directed by maintainer nijel).

**What was built:**
- Added `extra_parameters` entry to `SVGBadgeWidget` with `capitalize` as a number 
  type (min: 0, max: 1, step: 1)
- Added logic in `render()` to read the parameter from `request.GET` and apply 
  `.capitalize()` to the badge text if set
- Added error handling for invalid parameter values using `messages.error()`

**Challenges faced:**
- Considered using a `"checkbox"` type for better UX but confirmed `ExtraParametersDict` 
  only supports `"number"` — switching would require changes to the type system, 
  `widgets.js`, and value parsing. Kept `"number"` to match existing patterns and 
  minimize review friction.
- Significant time spent on Windows/WSL2 Docker setup before being able to reproduce 
  the bug locally (see Environment Setup section)

**Decisions made:**
- Used `"number"` type (0/1) over a custom checkbox to stay consistent with 
  `LanguageBadgeWidget.threshold` pattern
- Added proper error handling rather than silently defaulting to 0

## Code Changes

- **Files modified:** `weblate/trans/widgets.py`
- **Key commits:** [feat(widgets): add capitalize parameter to SVGBadgeWidget](https://github.com/Rayyan2235/weblate/commit/fe2018d95707033dbc16e8478bd1ea7654aaf782)
- **Approach decisions:** Followed `LanguageBadgeWidget.extra_parameters` pattern 
  as suggested by maintainer nijel. Used `"number"` type constrained to 0/1 rather 
  than introducing a new checkbox type to avoid expanding scope into the frontend 
  and type system.

### Week 4 Plan
- Write tests in `weblate/trans/tests/test_widgets.py`
- Run full widget test suite to verify no regressions
- Submit PR

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
