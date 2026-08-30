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

## 7. Responsive UI/UX Refinement

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

The original filter/page-size controls were rebuilt with Links to provide predictable active-state styling and behavior.

## 8. CSS Audit and Scope Control

Screen-level CSS was reviewed and consolidated across the main application screens. A broader global CSS audit showed that the theme also contained legacy/template styling and duplicate rules.

Rather than perform a high-risk global cleanup immediately before submission, the project deliberately stopped after the current screens were stable. This was a scope-management decision: avoid introducing regressions into a feature-complete application for cosmetic cleanup that did not affect the final user experience.

## 9. Final QA

The final QA pass covered the main functional and responsive flows.

Specific cleanup included:
- removing an unused `OnPageSizeChange` action;
- removing an orphaned `GetRecordByIdAfterFetch` handler;
- verifying pagination;
- fixing desktop page-size wrapping;
- fixing mobile Records horizontal overflow;
- verifying the final published application.

The remaining warnings are non-blocking missing-icon warnings associated with Phosphor 2.0/template UI elements. They were intentionally not chased further before submission because the application publishes and the tested flows work correctly.

## 10. Result

The portfolio version of CRATE reached a feature-complete, published and functionally tested state.

The development process demonstrates not only implementation in OutSystems ODC, but also iterative data modeling, business-rule design, responsive UX refinement, debugging, QA and scope management.

## 11. Post-Submission Direction

Possible future iterations include:
- richer statistics and charts
- metadata suggestions/autocomplete
- Discogs integration
- printable sleeve labels based on track positions
- native/mobile exploration
- customizable Energy types

These ideas are intentionally separated from the completed submission scope.
