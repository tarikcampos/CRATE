# CRATE — Project Documentation

## 1. Overview

CRATE is a responsive vinyl collection management application for DJs, built in OutSystems Developer Cloud (ODC). Its purpose is to organize a physical record collection while preserving the DJ's own creative selection process.

The application models both physical organization — Shelves/Crates and record position — and musical information at track level.

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

## 3. Domain Model

### Shelf

Represents a physical shelf or crate.

Main data:
- Name
- Description

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

Record-level Energy remains as a legacy model attribute but is not used as the musical source of truth in the final UI.

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

## 7. Search, Filtering and Pagination

The Records experience supports text search, collection-state filters and configurable page size. Pagination was functionally and visually verified in the final QA pass on desktop and mobile.

The final filter/page-size implementation uses explicit link-based controls so active state can be controlled reliably.

## 8. Responsive Design

The application was refined for desktop and mobile use. Important responsive work included:

- Mobile navigation drawer
- Responsive record-detail hero
- Mobile-friendly cards and tables
- Long artist/title wrapping
- Table containment to prevent horizontal viewport overflow
- Single-line desktop page-size controls
- Responsive Overview content

## 9. QA and Technical State

The final application was published and functionally tested without application errors.

Two cleanup issues found during QA were fixed:
- an unused Records action was removed;
- an orphaned Aggregate On After Fetch handler was removed.

The remaining development warnings are non-blocking missing-icon warnings originating from Phosphor 2.0/template UI elements. They were deliberately left outside the submission scope because they do not block publication or the tested application flows, and replacing global/template icon dependencies immediately before submission would add unnecessary regression risk.

## 10. Deployment

Live application:

https://personal-wpsz0rzh-dev.outsystems.app/CRATEVinylCollectionManager/Overview

## 11. Known Scope Boundaries

CRATE intentionally does not recommend which records or tracks a DJ should play or automate artistic track selection. Its role is collection organization, navigation and metadata management.

Potential future work is documented as post-submission scope rather than part of the completed portfolio version.
