# CRATE — Project Documentation

## 1. Overview

CRATE is a responsive vinyl collection management application for DJs, built in OutSystems Developer Cloud (ODC). Its purpose is to organize a physical record collection while preserving the DJ's own creative selection process.

The application models both physical organization — Shelves/Crates and record position — and musical information at track level. The portfolio version also includes an Agentic AI Collection Assistant for natural-language collection access and constrained, user-confirmed automation.

## 2. Functional Scope

The completed portfolio version provides:

- Record CRUD
- Track CRUD within records
- Shelf/Crate CRUD
- Safe shelf deletion
- Record and shelf search
- Record collection filters
- Pagination and selectable page size
- Cover artwork upload
- Recently Added and Unsorted collection states
- Physical crate navigation
- Collection Overview/dashboard
- Responsive desktop and mobile layouts
- Agentic AI natural-language collection queries
- Agent-assisted Shelf creation
- Agent-assisted Record movement between Shelves
- Explicit confirmation before Agent write operations

## 3. Domain Model

### Shelf

Represents a physical shelf or crate.

Main data:
- Name
- Description
- User ownership

A Shelf can contain multiple Records.

### Record

Represents a physical vinyl release.

Main data:
- Artist
- Title (stored by the application model under the original Release attribute)
- Genre (optional legacy/release-level field)
- BPM (optional legacy/release-level field)
- Rating
- Notes
- Shelf reference (optional)
- Physical Position
- IsUnsorted
- CoverImage
- DateAdded

Record-level Energy remains as a legacy model attribute but is not used as the musical source of truth and is not exposed by the Assistant's record-listing payloads.

### Track

Represents an individual track belonging to a Record.

Main data:
- Position — required text, e.g. A1, A2, B1
- Title — required
- Genre — optional
- BPM — optional integer
- Energy — optional reference to Energy
- Rating — optional, 1–5
- Notes — optional
- RecordId — required

Track is the source of truth for track-specific musical metadata.

### Energy

Static metadata values:
- Warm
- Rolling
- Peak

In the interface these are visually distinguished with green, yellow and red treatments respectively.

## 4. Relationships

- One Shelf can contain many Records.
- A Record may belong to zero or one Shelf.
- One Record can contain many Tracks.
- Every Track belongs to one Record.
- A Track may reference one Energy value.

## 5. Collection-State Rules

The final model distinguishes physical storage state from recent entry into the catalog.

- `ShelfId` set → the Record is shelved.
- `ShelfId` null and `IsUnsorted = False` → the Record can appear as Recently Added when it meets the date rule.
- `ShelfId` null and `IsUnsorted = True` → the Record is Unsorted.

Deleting a Shelf never deletes the records that were stored in it. Their shelf reference is cleared and they become Unsorted.

## 6. Main Screens

### Overview

Collection landing page containing:
- Total Records
- Total Tracks
- Shelves
- Recently Added (30 days)
- Track Energy summary
- Top Genres
- BPM ranges
- Recently Added records

### Records

Global collection view with:
- Search
- All / Recently Added / Unsorted filters
- Page-size controls: 10 / 25 / 50 / 100
- Pagination
- Shelf information
- Rating
- Added date
- Navigation to record details

### Record Details

Displays record-level information, optional cover artwork and the track list. Tracks can be added, edited and deleted from this context.

### Record Edit/Add

Used to create and maintain release-level data, shelf assignment, physical position, notes and optional cover artwork.

### Shelves

Lists physical Shelves/Crates and supports search, creation, editing, deletion and navigation into a selected crate.

### Shelf Records

Shows only records assigned to the selected Shelf/Crate.

### Track Add/Edit

Maintains track Position, Title, Genre, BPM, Energy, Rating and Notes.

### Collection Assistant

Provides a conversational interface to collection data. The current Agent can perform real collection reads and a deliberately constrained set of write operations. It can create a Shelf and move a Record to a Shelf after resolving the required data and receiving explicit user confirmation.

## 7. Agentic AI Architecture

The Collection Assistant is implemented as a separate ODC Agentic App connected to the CRATE Web App through public Service Actions.

The architecture separates responsibilities:

- **Web App Server Actions** contain collection/business logic.
- **Service Actions** expose only selected operations across assets.
- **Agentic App local actions** act as adapters for Agent Action Calling.
- **Agent Flow** prepares context/messages, invokes the configured AI model, supports tool/action execution, stores conversational memory and returns the response to the Web App.
- **SessionId** maintains conversational continuity without making previous model output the source of truth for current collection state.

Current collection capabilities include read actions for unsorted records, records by shelf, tracks by BPM range, tracks by Energy, tracks with missing metadata and collection statistics, plus record/shelf lookup actions used to safely resolve identifiers for writes.

### Constrained Write Protocol

Write operations follow a human-in-the-loop protocol:

1. Resolve the target Record/Shelf using collection actions rather than inventing internal IDs.
2. Describe the exact proposed operation.
3. Ask for explicit confirmation.
4. Stop and wait for the user's next message.
5. Execute only after a clear confirmation.
6. Treat a revised request as a new operation requiring a new confirmation cycle.

The current write surface intentionally remains small: Shelf creation and Record movement. This demonstrates controlled agency without granting unrestricted mutation of the collection.

### Data Exposure and Serialization

During QA, record-listing actions were found to serialize complete ODC entity/aggregate records, unintentionally exposing legacy fields such as Record-level BPM, Energy and Rating to the model.

The final implementation uses dedicated projection structures before JSON serialization:

- `AssistantShelfRecordOutput`: Artist, Release, Genre, ShelfName
- `AssistantUnsortedRecordOutput`: Artist, Release, Genre, DateAdded

The Agent therefore receives only the fields required by those operations. Track-specific actions continue to use Track metadata, including `Track.Energy`, normally.

## 8. Search, Filtering and Pagination

The Records experience supports text search, collection-state filters and configurable page size. Pagination was functionally and visually verified in the final QA pass on desktop and mobile.

The final filter/page-size implementation uses explicit link-based controls so active state can be controlled reliably.

## 9. Responsive Design

The application was refined for desktop and mobile use. Important responsive work included:

- Mobile navigation drawer
- Responsive record-detail hero
- Mobile-friendly cards and tables
- Long artist/title wrapping
- Table containment to prevent horizontal viewport overflow
- Single-line desktop page-size controls
- Responsive Overview content
- Responsive Collection Assistant conversation/composer layout

## 10. QA and Technical State

The final application was published and functionally tested without application errors.

Agentic QA included:
- natural-language collection statistics and filtering queries;
- record and shelf identifier resolution;
- explicit write confirmation behavior;
- Shelf creation;
- Record movement with post-update verification;
- persisted movement confirmed through both Assistant reads and the Shelf UI;
- refusal to make artistic DJ/set-selection decisions;
- validation that record-listing payloads no longer expose legacy Record-level Energy/BPM/Rating.

The trial AI model/provider used by the portfolio environment showed intermittent connection failures during runtime QA. The same operations could succeed on subsequent attempts without application changes, while persisted writes were independently verified through application state. This is documented as an environment/runtime limitation rather than hidden as an application feature.

The remaining 17 development warnings are non-blocking missing-icon/template warnings. They were deliberately left outside the submission scope because they do not block publication or the tested application flows, and replacing global/template icon dependencies immediately before submission would add unnecessary regression risk.

## 11. Deployment

Live application:

https://personal-5npg68ma-dev.outsystems.app/CRATEVinylCollectionManager/Overview

## 12. Known Scope Boundaries

CRATE intentionally does not recommend which records or tracks a DJ should play, build sets/playlists, automate track order, choose transitions or make other artistic DJ decisions.

The current Agent also does not yet provide general-purpose metadata mutation/enrichment. Expanded automation is intentionally treated as future work so it can be introduced with explicit user permissions rather than by simply increasing autonomous access.

## 13. Future Agent Direction

Future development is centered on **configurable agency**.

A proposed **Agent Permissions & Preferences** screen would allow the DJ to choose:

- which Agent operations are enabled or disabled;
- which write operations always require confirmation;
- which metadata attributes may receive AI suggestions;
- whether suggested metadata may be applied after confirmation;
- which capabilities should never be available to the Agent.

Potential future capabilities include controlled missing-metadata completion, metadata enrichment, artwork/photo-assisted release identification and external metadata lookup. A future ingestion workflow could prepare Record/Track drafts for review rather than silently creating collection data.

The current dedicated Collection Assistant page could also evolve into a global contextual pop-up/panel available while browsing Records, Shelves and Record Details, allowing the Agent to assist without forcing the user to leave the current collection context.

These extensions retain the project's central boundary: **administrative and metadata assistance can become more capable; artistic DJ decisions remain with the DJ.**
