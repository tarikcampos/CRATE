# CRATE — Vinyl Collection Manager

CRATE is a responsive vinyl collection manager for DJs, built with **OutSystems Developer Cloud (ODC)**.

It is designed around the way DJs physically organize and explore record collections: records live inside Shelves/Crates, while musical metadata such as BPM, genre, energy and rating can be stored at **track level**.

The goal is not to automate DJ selection. CRATE helps users understand and navigate their collection while leaving the creative process of choosing and combining records to the DJ.

## Live Demo

**CRATE:** https://personal-wpsz0rzh-dev.outsystems.app/CRATEVinylCollectionManager/Overview

## Project Context

CRATE was developed as the final application project for an OutSystems AI Developer learning path. The portfolio version is hosted in OutSystems ODC; this repository documents the product, architecture, development decisions and QA process rather than presenting CRATE as a conventional source-code repository.

## Core Features

- Create, edit and delete records
- Create, edit and safely delete Shelves/Crates
- Add and manage individual tracks inside each record
- Track-level metadata: position, title, genre, BPM, energy, rating and notes
- Optional record cover artwork
- Search and collection filtering
- All / Recently Added / Unsorted record views
- Physical crate navigation with **Go to Crate**
- Pagination with 10 / 25 / 50 / 100 page-size options
- Responsive desktop and mobile interface
- Collection Overview with record, track, shelf and recently-added totals
- Energy distribution, top genres and BPM-range summaries

## Data Model

The project evolved from a record-only catalog into a more accurate model for DJ collections.

### Shelf
Represents a physical shelf or crate used to organize records.

### Record
Represents a physical vinyl release. It contains release-level information such as artist, title, cover artwork, notes, physical position and shelf assignment.

### Track
Represents an individual track belonging to a record. Track-specific musical information is stored here because one record can contain tracks with very different characteristics.

Key track attributes include Position (for example A1, A2, B1), Title, Genre, BPM, Energy, Rating and Notes.

### Energy
A static track metadata classification: **Warm**, **Rolling** and **Peak**.

## Key Design Decision

The initial model stored Genre, BPM and Energy at Record level. During development this was revised because a multi-track record may contain tracks with different genres, tempos and energy levels.

A dedicated **Track** entity was introduced and those attributes — together with Rating — became track-level metadata. This was one of the main data-modeling decisions in the project.

## Collection States

Records can exist in three practical states:

- **Shelved** — assigned to a Shelf/Crate
- **Recently Added** — not yet shelved and recently entered into the collection
- **Unsorted** — explicitly removed from a shelf or awaiting physical organization

Deleting a shelf does **not** delete its records. Affected records are preserved and become Unsorted.

## UX and Interface

CRATE includes dedicated Overview, Records and Shelves navigation, responsive record/track layouts, mobile drawer navigation, cover artwork, track energy color coding, star ratings, search/filter controls and responsive pagination.

## Technology and Skills Demonstrated

- OutSystems Developer Cloud (ODC)
- Reactive web application development
- Relational data modeling
- CRUD workflows
- Client and server actions
- Aggregates, search and filtering
- Pagination and UI state
- Binary image upload
- Responsive UI/UX
- Custom CSS refinement
- Debugging, iterative development and functional QA

## Documentation

- [Project Documentation](docs/PROJECT_DOCUMENTATION.md)
- [Development Process](docs/DEVELOPMENT_PROCESS.md)

## Screenshots

Screenshots of the final desktop and mobile application will be added to this repository as portfolio material.

## Status

The current portfolio version is **feature-complete, published and functionally tested**.

## Future Improvements

Potential post-submission improvements include richer statistics and charts, metadata suggestions/autocomplete, Discogs integration, printable sleeve labels, native/mobile exploration and customizable Energy types.
