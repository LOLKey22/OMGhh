# Tasks: Simple Notes App

**Input**: Design documents from `/Users/robiwiiedo/OMGhh/specs/001-i-want-to/`
**Prerequisites**: plan.md (✓), research.md (✓), data-model.md (✓), contracts/ (✓)

## Execution Flow (main)
```
1. Load plan.md from feature directory ✓
   → Tech stack: Node.js + Express.js backend, vanilla HTML/CSS/JS frontend
   → Structure: Web app with backend/ and frontend/ directories
2. Load optional design documents ✓
   → data-model.md: Note entity → model tasks
   → contracts/: GET /api/notes + POST /api/notes → contract test tasks
   → research.md: Architecture decisions → setup tasks
3. Generate tasks by category ✓
   → Setup: project structure, dependencies, linting
   → Tests: contract tests, integration tests
   → Core: Note model, file service, API endpoints, frontend
   → Integration: CORS, error handling, static file serving
   → Polish: unit tests, performance, accessibility
4. Apply task rules ✓
   → Different files = mark [P] for parallel
   → Same file = sequential (no [P])
   → Tests before implementation (TDD)
5. Number tasks sequentially (T001, T002...) ✓
6. Generate dependency graph ✓
7. Create parallel execution examples ✓
8. Validate task completeness ✓
   → All contracts have tests? ✓ (GET + POST covered)
   → All entities have models? ✓ (Note model covered)
   → All endpoints implemented? ✓ (GET + POST endpoints)
9. Return: SUCCESS (tasks ready for execution)
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- Include exact file paths in descriptions

## Path Conventions
- **Web app**: `backend/src/`, `frontend/src/`
- Paths follow plan.md structure for frontend+backend architecture

## Phase 3.1: Setup
- [ ] T001 Create project structure per implementation plan (backend/, frontend/, tests/)
- [ ] T002 Initialize Node.js backend project in backend/package.json with Express.js dependencies
- [ ] T003 [P] Configure ESLint and Prettier in backend/.eslintrc.js and .prettierrc
- [ ] T004 [P] Configure Jest testing framework in backend/jest.config.js
- [ ] T005 [P] Initialize frontend package.json with dev dependencies (live-server, Playwright)
- [ ] T006 [P] Configure Playwright for E2E testing in frontend/playwright.config.js

## Phase 3.2: Tests First (TDD) ⚠️ MUST COMPLETE BEFORE 3.3
**CRITICAL: These tests MUST be written and MUST FAIL before ANY implementation**
- [ ] T007 [P] Contract test GET /api/notes in backend/tests/contract/test_get_notes.test.js
- [ ] T008 [P] Contract test POST /api/notes in backend/tests/contract/test_post_notes.test.js
- [ ] T009 [P] Integration test note creation workflow in backend/tests/integration/test_note_workflow.test.js
- [ ] T010 [P] Integration test note persistence in backend/tests/integration/test_note_persistence.test.js
- [ ] T011 [P] Unit test Note model validation in backend/tests/unit/test_note_model.test.js
- [ ] T012 [P] Unit test file service operations in backend/tests/unit/test_file_service.test.js

## Phase 3.3: Core Implementation (ONLY after tests are failing)
- [ ] T013 [P] Note model with validation in backend/src/models/note.js
- [ ] T014 [P] File service for JSON persistence in backend/src/services/fileService.js
- [ ] T015 Notes service with business logic in backend/src/services/notesService.js
- [ ] T016 GET /api/notes endpoint in backend/src/api/routes/notes.js
- [ ] T017 POST /api/notes endpoint in backend/src/api/routes/notes.js
- [ ] T018 Express server setup in backend/server.js
- [ ] T019 [P] Frontend HTML structure in frontend/index.html
- [ ] T020 [P] Frontend CSS styling in frontend/style.css
- [ ] T021 Frontend JavaScript functionality in frontend/script.js

## Phase 3.4: Integration
- [ ] T022 CORS and security middleware configuration in backend/server.js
- [ ] T023 Static file serving for frontend in backend/server.js
- [ ] T024 Error handling middleware in backend/src/middleware/errorHandler.js
- [ ] T025 Request logging middleware in backend/src/middleware/logger.js
- [ ] T026 Input validation middleware in backend/src/middleware/validation.js

## Phase 3.5: Polish
- [ ] T027 [P] Frontend form validation unit tests in frontend/tests/unit/test_form_validation.test.js
- [ ] T028 [P] API response time performance tests in backend/tests/performance/test_api_performance.test.js
- [ ] T029 [P] E2E user workflow tests in frontend/tests/e2e/test_user_workflows.spec.js
- [ ] T030 [P] Accessibility compliance validation in frontend/tests/e2e/test_accessibility.spec.js
- [ ] T031 [P] Error handling and user feedback tests in frontend/tests/e2e/test_error_handling.spec.js
- [ ] T032 Code quality review and cleanup (remove dead code, add JSDoc comments)
- [ ] T033 [P] Performance optimization (file I/O, memory usage)
- [ ] T034 [P] Update documentation in README.md

## Dependencies
```
Setup Phase (T001-T006):
- All setup tasks can run in parallel after T001 (project structure)

Tests Phase (T007-T012):
- All test tasks can run in parallel
- Must complete BEFORE any implementation

Core Implementation (T013-T021):
- T013 (Note model) → T014 (File service) → T015 (Notes service)
- T015 (Notes service) → T016-T017 (API endpoints)
- T016-T017 (API endpoints) → T018 (Express server)
- T019-T020 (Frontend HTML/CSS) can run in parallel
- T021 (Frontend JS) depends on T018 (API availability)

Integration (T022-T026):
- All depend on T018 (Express server)
- T022-T025 can run in parallel
- T026 (validation) can run in parallel

Polish (T027-T034):
- T027-T031 (tests) can run in parallel after core implementation
- T032-T034 depend on all previous tasks being complete
```

## Parallel Execution Examples

### Setup Phase Parallel Tasks
```bash
# After T001 completes, run these in parallel:
Task T002 & Task T003 & Task T004 & Task T005 & Task T006
```

### Tests Phase Parallel Tasks  
```bash
# All test tasks can run simultaneously:
Task T007 & Task T008 & Task T009 & Task T010 & Task T011 & Task T012
```

### Core Implementation Sequence
```bash
# Sequential dependencies:
Task T013 → Task T014 → Task T015 → (Task T016 & Task T017) → Task T018

# Parallel with core backend:
Task T019 & Task T020  # Frontend can develop independently

# Final integration:
Task T021  # Frontend JS needs API endpoints
```

### Polish Phase Parallel Tasks
```bash
# Final testing and optimization:
Task T027 & Task T028 & Task T029 & Task T030 & Task T031 & Task T033 & Task T034
```

## Validation Checklist
- [x] All contracts have tests (GET /api/notes: T007, POST /api/notes: T008)
- [x] All entities have models (Note model: T013)
- [x] All endpoints implemented (GET: T016, POST: T017)
- [x] Frontend user interface complete (HTML: T019, CSS: T020, JS: T021)
- [x] Error handling and validation covered (T024, T025, T026)
- [x] Performance and accessibility testing included (T028, T030)
- [x] TDD workflow enforced (Tests T007-T012 before Implementation T013-T021)

## Constitutional Compliance Verification
- [x] **Code Quality**: Linting and formatting configured (T003)
- [x] **Testing Standards**: TDD workflow with comprehensive coverage (T007-T012, T027-T031)
- [x] **UX Consistency**: Accessibility testing and responsive design (T020, T030)
- [x] **Performance Requirements**: API response time testing (T028, T033)

**Ready for execution**: All 34 tasks defined with clear dependencies and parallel execution guidance.