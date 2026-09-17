# CRATE — Development Process

## 1. Starting Point

CRATE began as a focused vinyl collection manager built for an OutSystems ODC course project. The first model centered on two concepts: **Records** and physical **Shelves/Crates**.

The initial goal was a practical MVP with CRUD, search, filtering, shelf organization, ratings and notes.

## 2. Moving Musical Metadata to Track Level

One of the most important changes came from testing the original domain model against real DJ use.

Genre, BPM and Energy were initially associated with the Record. That works for a simple catalog, but not for many vinyl releases: the tracks on one record may have different genres, tempos and energy levels.

The model was therefore expanded with a dedicated **Track** entity. Position, Title, Genre, BPM, Energy, Rating and Notes could then describe each track individually.

This made Track the correct source of truth for musical metadata and made the application more representative of a real DJ collection.

## 3. Physical Collection Semantics

The next important design problem was distinguishing records that had just been added from records that were deliberately not assigned to a physical shelf.

The final model uses shelf assignment together with `IsUnsorted`:

- assigned Shelf → Shelved;
- no Shelf and not Unsorted → eligible for Recently Added;
- no Shelf and explicitly Unsorted → Unsorted.

Shelf deletion was also designed as a safe operation. Deleting a Shelf clears the affected records' Shelf assignment and marks them Unsorted instead of deleting the records.

## 4. Core Application Flows

The application was built out around the domain model with:

- Record create/edit/delete
- Track create/edit/delete
- Shelf create/edit/delete
- Search
- Collection-state filtering
- Shelf-specific navigation
- Cover artwork
- Pagination
- Configurable page size

A **Go to Crate** action was added so the digital collection maps naturally to the user's physical organization.

## 5. Record Detail Experience

The Record Details view evolved into two clear areas:

1. Record Information
2. Tracks

The final view includes optional artwork, release information, shelf context and track-derived musical information. The track list exposes the metadata that matters while browsing a record: Position, Title, BPM, Energy, Rating and Notes.

## 6. Overview Dashboard

After the core collection flows were stable, an Overview screen was added to make the collection understandable at a glance.

It includes:
- Total Records
- Total Tracks
- Shelves
- Recently Added in the last 30 days
- Track Energy distribution
- Top Genres
- BPM ranges
- Recently Added records

This extended CRATE from CRUD-only collection management into a useful collection overview without crossing into automated DJ recommendations.

## 7. Agentic AI Extension

After the base application was complete, CRATE was extended with an ODC Agentic App rather than adding a generic chatbot disconnected from application logic.

The first iteration focused on read-only natural-language access to real collection data. Selected Web App logic was exposed through Service Actions and wrapped by local Agentic App actions for Action Calling. The Assistant could answer collection questions such as statistics, unsorted records, shelf contents, BPM ranges, Energy and missing metadata using application data rather than model guesses.

The architecture evolved through several important lessons:

- internal Server Actions and cross-asset Service Actions serve different roles in ODC;
- imported Service Actions are exposed to the Agent through thin local adapters;
- system instructions, current user messages, conversational memory and live action results must remain conceptually separate;
- current collection state should come from fresh application actions rather than old conversational memory.

## 8. From Read-Only Assistant to Constrained Agency

The Assistant was later extended beyond read-only queries to demonstrate controlled automation.

Two initial write capabilities were introduced:

- create a Shelf/Crate;
- move a Record to a Shelf.

Rather than allow unrestricted writes, the Agent uses a confirmation protocol. It resolves Record/Shelf identifiers through dedicated lookup actions, proposes the exact operation and waits for explicit confirmation before calling the write action.

`AssistantMoveRecordToShelf` also performs a post-update read to verify the destination Shelf before returning success. Runtime QA confirmed persisted Record movement through both subsequent Assistant reads and the normal Shelf UI.

This changed the project from a conversational reporting layer into a small example of **bounded agency with human control**.

## 9. Agent Data-Minimization Fix

A significant Agentic QA issue involved legacy Record-level metadata appearing in shelf-list responses even though Energy had moved conceptually to Track level.

Initial attempts hid attributes in the ODC Aggregate editor. Runtime testing proved that this did not affect JSON serialization because `JSON Serialize` still received the complete compound Aggregate list type.

The final fix introduced explicit projection structures:

- `AssistantShelfRecordOutput` — Artist, Release, Genre, ShelfName
- `AssistantUnsortedRecordOutput` — Artist, Release, Genre, DateAdded

Each action now builds a projected list through a ForEach/ListAppend flow and serializes that structure instead of serializing full Record/Shelf entity rows. Runtime testing confirmed that legacy Record BPM, Energy, Rating, Position, Notes and internal IDs no longer appear in shelf-list responses.

This became an important development lesson: **the data supplied to an AI tool should be explicitly shaped at the serialization boundary rather than relying on design-time visibility settings.**

## 10. Responsive UI/UX Refinement

A significant part of the final development cycle focused on interface quality.

Work included:
- dark CRATE header and navigation
- desktop and mobile navigation behavior
- compact search/filter controls
- rating stars
- energy color coding
- responsive record details
- mobile shelf cards
- mobile table containment
- long-value wrapping
- pagination active states
- desktop page-size alignment
- responsive Collection Assistant composer and conversation layout

The original filter/page-size controls were rebuilt with Links to provide predictable active-state styling and behavior.

## 11. CSS Audit and Scope Control

Screen-level CSS was reviewed and consolidated across the main application screens. A broader global CSS audit showed that the theme also contained legacy/template styling and duplicate rules.

Rather than perform a high-risk global cleanup immediately before submission, the project deliberately stopped after the current screens were stable. This was a scope-management decision: avoid introducing regressions into a feature-complete application for cosmetic cleanup that did not affect the final user experience.

The same principle was later applied to Agent development: once the intended read/write flows and serialization boundaries were validated, additional automation was moved to the roadmap instead of continuing to expand the submission scope.

## 12. Final QA

The final QA pass covered the main functional, responsive and Agentic flows.

Specific cleanup included:
- removing an unused `OnPageSizeChange` action;
- removing an orphaned `GetRecordByIdAfterFetch` handler;
- verifying pagination;
- fixing desktop page-size wrapping;
- fixing mobile Records horizontal overflow;
- validating natural-language read actions;
- validating record and shelf lookup;
- validating the write confirmation protocol;
- validating Shelf creation;
- validating persisted Record movement;
- validating that record-listing Agent payloads expose only intended fields;
- verifying the final published application.

The trial AI model/provider showed intermittent connection failures during QA. Because identical operations could later succeed without application changes, and writes were independently verified against application state, the project did not respond by repeatedly changing otherwise validated business logic.

The remaining 17 warnings are non-blocking icon/template warnings. They were intentionally not chased further before submission because the application publishes and the tested flows work correctly.

## 13. Result

The portfolio version of CRATE reached a feature-complete, published and functionally tested state with both conventional collection-management functionality and an Agentic AI extension.

The development process demonstrates iterative data modeling, business-rule design, responsive UX refinement, ODC Service Action architecture, Agent Action Calling, constrained AI writes, explicit data projection, debugging, QA and scope management.

## 14. Post-Submission Direction

Future development is intentionally focused on **user-configurable agency** rather than unrestricted automation.

A proposed Agent Permissions & Preferences experience would allow the DJ to decide which automations are available, which always require confirmation and which are disabled. The same area could define which metadata attributes the Agent may suggest or help complete — for example Genre, BPM or Energy — while keeping application of changes under user control.

Other possible iterations include:
- controlled missing-metadata completion and metadata enrichment;
- artwork/photo-assisted release identification;
- Discogs or similar external metadata integration;
- a global contextual Assistant pop-up/panel replacing or complementing the dedicated Assistant page;
- richer statistics and charts;
- printable sleeve labels based on track positions;
- native/mobile exploration;
- customizable Energy types.

Track/set recommendations, automated ordering, transitions and other artistic DJ decisions remain intentionally outside the Agent's scope.

These ideas are separated from the completed submission so the current portfolio version remains a stable demonstration of the product and architecture already implemented.
