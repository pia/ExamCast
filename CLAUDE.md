# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**ExamCast (考试广播系统)** is a single-file HTML application for school exam broadcast management. It provides automatic voice announcements at scheduled times during examinations.

## Architecture

### Single-File Structure
The entire application is contained in `index.html` (~1600 lines), which includes:
- CSS styles (in `<style>` tag)
- HTML structure
- JavaScript code (in `<script>` tag)

### Data Model
- `exams[]`: Array of exam objects `{date, grade, subject, start_time, end_time}`
- `plan[]`: Array of broadcast tasks `{id, date, time, type, content, done, ...}`
- `gradeLocations{}`: Object mapping grades (1-6年级) to broadcast locations
- `templates{}`: Broadcast message templates with variable substitution
- `customTemplates`: User-customized templates (persisted)

### Key Features
1. **Image Recognition**: Uses GLM-4.1V API (dmxapi.cn) to extract exam schedules from images
2. **Voice Broadcast**: Uses Web Speech API with male voice synthesis (pitch 0.8)
3. **Auto-execution**: Background interval checks every second for scheduled broadcasts
4. **Data Persistence**: LocalStorage for all data; JSON import/export for backup/transfer

### Page Sections (via showSection())
1. `import` (section-import): Upload images, AI recognition of exam schedules
2. `schedule` (section-schedule): Edit exam timetable
3. `plan` (section-plan): Broadcast plan with live countdown, auto-scroll to next task
4. `settings` (section-settings): Customize broadcast message templates

## Common Commands

### Deployment
The app is deployed via GitHub Pages:
```bash
# Push changes to deploy
git add index.html
git commit -m "Description"
git push
```

### Data Management
Users export/import data via the UI:
- **Export Data**: Downloads `考试广播数据_YYYY-MM-DD.json` (exams, plan, templates, gradeLocations)
- **Export Plan**: Downloads Word document (`.doc`) with broadcast schedule
- **Import Data**: Upload JSON file to restore all data

### Development
No build system required. To test locally:
```bash
# Simply open index.html in browser, or:
python -m http.server 8000
# Then open http://localhost:8000
```

## Key Implementation Details

### Auto-Save Behavior
- Grade locations: Auto-save on `onchange` event
- Broadcast content: Auto-save on `onblur` (textarea loses focus)
- Templates: Manual save via "保存模板" button

### Broadcast Plan Rendering
- `renderPlanList(shouldAutoScroll)`: Renders the full plan list
- `updatePlanCountdown()`: Updates only countdown text (every second) without re-rendering to preserve expanded task state
- Auto-scroll only happens on first entry to plan section (controlled by `hasAutoScrolled` flag)

### Voice Synthesis
- Uses `window.speechSynthesis` with voice selection logic for Chinese male voice
- Pitch set to 0.8 for deeper voice
- Calls `cancel()` before speaking to avoid queue buildup

### API Configuration
```javascript
const API_KEY = 'sk-...';  // Current key (may need rotation)
const API_URL = 'https://www.dmxapi.cn/v1/chat/completions';
const MODEL = 'GLM-4.1V-Thinking-Flash';
```

## File Structure

```
ExamCast/
├── index.html          # Main application (single file)
├── .gitignore          # Excludes *.docx files
└── （01-13-14）考试播报监考.docx  # Reference template (not tracked by git)
```

## Important Notes

1. **GitHub Pages URL**: https://pia.github.io/ExamCast/
2. **No tests**: This is a simple utility application without automated tests
3. **No build step**: Direct HTML editing, browser loads file as-is
4. **Data storage**: All data in browser LocalStorage; JSON export for backup is essential
5. **CNAME**: If custom domain needed, create CNAME file with domain name
