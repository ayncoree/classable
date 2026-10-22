# ClassAble — Ideathon MVP

## Implementation choices

Keep the existing React/Vite frontend, Express/tRPC API, managed MySQL/Drizzle integration, ClassAble branding, route manifest, and working Three.js/GSAP hero. Do not migrate to Supabase: no Supabase SDK or runtime key is present. Avoid new schema changes and destructive migrations.

Use browser `SpeechRecognition`/`webkitSpeechRecognition` first where supported. Keep microphone permission explicit, interim/final results honest, controls recoverable, and capture released after stop, end, or unmount. Log the actual browser error code without speech content; distinguish `network`, `not-allowed`, `service-not-allowed`, `no-speech`, unsupported browser, and other failures. A network failure terminates that recognizer instance instead of rapid-loop restarts.

Use the existing server-only transcription provider as a real alternative. The audio-only endpoint is available to demo users without sign-in, accepts WAV, MP3, WebM, Ogg, MP4 or M4A up to 10 MB, and applies per-process limits per authenticated account or source IP (six requests per 15 minutes, at most two concurrent requests for that caller). These in-memory counters are not global across multiple server instances. It receives no class data and never returns provider credentials. File/short-recorder results appear only after actual API text is returned; if the provider is not configured, show a setup message. Uploaded audio is not persisted by ClassAble.

Keep the local demo store and `BroadcastChannel` for teacher/student tabs in one browser. Continue to label this accurately as local-only. The optional signed-in workspace uses existing MySQL classes, memberships, captions, transcripts, role checks and short-interval polling for separate devices. Retain server restrictions on member access, draft/unpublished transcript access and teacher-private notes. Do not expand that infrastructure for this speech-fix task.

The local transcript workspace saves actual returned captions, supports editing/export, extractive summaries grounded in transcript sentences, and editable notes. Do not call a summary AI-generated or invent lecture facts. Keep secondary assignments/analytics deferred, and do not imply automatic ISL translation. The resource shelf stays optional; only represent a sign-language video as verified when its source has actually been checked.

## Code responsibilities

- `client/src/features/captions/useSpeechRecognition.ts`: browser engine lifecycle, errors, deduplication and cleanup; `shared/speechErrors.ts` owns testable error copy.
- `client/src/features/portal/LiveClassroomView.tsx`: actual local captions, MediaRecorder fallback, audio import and truthful status.
- `client/src/features/portal/SettingsView.tsx`: browser diagnostics, actual last error code and server fallback readiness.
- `client/src/lib/demoStore.ts`: browser-local sessions/transcripts, tab synchronization and notes; merges sample lecture records idempotently for new and existing demos.
- `client/src/lib/sampleLectures.ts`: four clearly marked example lecture bundles with transcripts, structured notes, summaries, concepts, questions and flashcards; stable IDs preserve user edits and other saved state.
- `server/_core/transcribeBuffer.ts` and `server/routers.ts`: server-only provider use, bounded audio input, readiness response and per-caller limits.
- `server/classroomRouter.ts`: preserve existing authenticated class/session/transcript permissions.

## First-visit sample lectures

Seed four clearly identified sample lectures into the existing browser-local demo store: Newton's Laws, Cell Biology, SQL Basics and Photosynthesis. Each fixture has a saved sample transcript, a structured editable notes document containing its sample summary and key concepts, plus editable revision questions and flashcards. Use stable IDs and an idempotent merge when loading both new and already-populated browser demo state; append missing sample records only, preserving real recordings, user edits and other saved data. Mark sample provenance in the data and UI as demo content that is not a recording. Reuse the existing Transcript Library, Notes dashboard, study material editor, persistence and visual system; make no new routes, migrations or design restyle.

## Lecture documents and hero enhancement

Keep the existing React/Vite portal routes and browser-local demo store; extend the compatible state shape with reusable transcript templates, study templates, generated/editable study documents, and saved versions. Preserve existing original transcript text, corrected text, summaries, captions, teacher/student separation and visibility rules. A transcript detail becomes a document-style lecture workspace with Transcript, Notes, Summary, Flashcards and Questions tabs; searching also considers class/course, date and body text. Templates have editable names/section prompts and can be created, duplicated, renamed and deleted. Study drafts are derived only from actual saved transcript text, visibly identified as transcript-derived, editable, versioned and downloadable as TXT/Markdown; too-short source text gets an explanatory state. Timestamps and speaker names are displayed only if stored by recognition. No external AI is required; procedural source-grounded draft generation avoids invented lecture content.

Reuse the current lazy-loaded Three.js canvas rather than adding a model dependency. Enrich its speech-to-learning composition with a central layered glass/paper knowledge sculpture, small connected sound nodes and restrained gold/sage accents, soft lighting/shadows, and refined document panels. Use one coordinated entrance/slow idle motion, subtle desktop pointer response, and honor reduced motion, low pixel ratio, mobile camera framing and the existing static WebGL fallback. Keep navigation, copy and controls in accessible HTML.

Project structure additions: `client/src/features/portal/TranscriptLibraryView.tsx` owns library search and lecture tabs; a small `StudyMaterialsPanel`/template manager handles template CRUD, source-grounded material generation, editing, version selection and export; `shared/classroom.ts` defines persisted local document/template records; `client/src/lib/demoStore.ts` persists and broadcasts those records. `client/src/features/hero/AccessFlowScene.tsx` remains the lazy 3D scene; `client/src/index.css` adds responsive document/workspace and scene styling. No database schema or independent-device transport changes are required for these local-demo materials.

## Design

Keep the warm ivory `#F5F4EE`, paper white `#FFFEFA`, deep forest `#174B3A`, muted sage `#DCE8DF`, charcoal `#252C28`, sparing amber `#C49A45`, roomy editorial layout, keyboard-readable controls and reduced-motion behavior. Keep the existing dimensional speech-to-paper 3D/GSAP hero; do not spend time polishing it over caption reliability.

## Run and verify

Use the existing configured dev service and `client/public/manus-routes.json`. TypeScript checking, production build and Vitest run are required. The real WAV test must travel through the browser audio-import control, server provider and local caption/transcript store. Live capture still needs a microphone-capable browser to verify on the user's device. Cross-device auth/session delivery also needs two signed-in test devices; local demo tabs are not cross-device sync.
