# Feature Specification: Exercise Route Tracker

**Feature Branch**: `001-exercise-route-tracker`
**Created**: 2026-05-22
**Status**: Draft
**Input**: User description: "This is a Single Page Application that allows people to track the distance they made by exercises (walking, running, biking, etc) and shows that distance as progress along a pre-configured route between two points on Google Maps. The page is split into two side-by-side parts. On the left side, the map is displayed that is zoomable, draggable, and it shows the selected route between the start and end locations. On the right side, there is a table that shows the completed exercises. Above the table there are buttons for filtering the table be exercise type or date. The map shows the configured route as a red line, and the combined distance of all the exercises show in the table show up as a green part of the route line on the map."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Log a Completed Exercise (Priority: P1)

A user has just finished a walk, run, or bike ride and wants to record it. They open the app and add a new exercise entry with the type of activity, the date it was completed, and the distance covered. After saving, the entry appears in the exercise table and the green progress indicator on the map immediately reflects the updated cumulative distance.

**Why this priority**: Without the ability to add exercises, there is no data to display and no progress to track. This is the foundational action that makes everything else meaningful.

**Independent Test**: Can be fully tested by adding a new exercise and confirming the entry appears in the exercise table with correct data, delivering immediate feedback that data was recorded.

**Acceptance Scenarios**:

1. **Given** the user is on the main page, **When** they add a new exercise with type "Running", a date, and a distance, **Then** the new exercise appears as a new row in the exercise table with all entered details correctly shown.
2. **Given** the exercise table has at least one entry, **When** the user adds another exercise, **Then** the total cumulative distance reflected by the green line on the map increases accordingly.
3. **Given** the user is adding an exercise, **When** they submit incomplete or invalid data (e.g., negative distance or a missing date), **Then** the system displays a clear error message and does not save the entry.

---

### User Story 2 - View Route Progress on the Map (Priority: P2)

A user opens the app to see how far along their configured route their cumulative exercise distance has taken them. The full route is shown as a red line on an interactive map. The portion of the route covered by the total logged exercise distance is highlighted as a green line starting from the route's beginning. The user can zoom and pan the map to explore the route in detail.

**Why this priority**: The map visualization is the app's defining feature — the reason a user chooses this app over a simple exercise log. Without it, the product lacks its core differentiator.

**Independent Test**: Can be fully tested by verifying that the map displays both the full red route and a green segment proportional to the sum of all logged exercise distances, and that the map responds to zoom and pan interactions.

**Acceptance Scenarios**:

1. **Given** exercises have been logged, **When** the user views the map, **Then** a red line shows the full configured route from start to end, and a green line overlays the beginning of the route for the distance equal to the total logged exercise distance.
2. **Given** the user is viewing the map, **When** they scroll or use pinch-to-zoom, **Then** the map zooms in or out smoothly and all route lines remain visible and correctly positioned.
3. **Given** no exercises have been logged yet, **When** the user views the map, **Then** the full route is displayed as a red line with no green overlay.
4. **Given** the cumulative exercise distance equals or exceeds the total route length, **When** the user views the map, **Then** the entire route is displayed in green, indicating completion.

---

### User Story 3 - Filter the Exercise Table (Priority: P3)

A user wants to review only a subset of their exercise history — for example, only their cycling sessions from last month. They use the filter controls above the exercise table to narrow the view by exercise type (e.g., Walking, Running, Biking) or by date range. The map's green progress segment updates to reflect only the cumulative distance of the exercises currently shown in the filtered table.

**Why this priority**: Filtering is a quality-of-life enhancement that becomes essential as the exercise log grows. It lets users answer questions like "how far have I biked this month?" without changing any persistent data.

**Independent Test**: Can be fully tested by applying a filter, verifying the table shows only matching exercises, and confirming the map's green segment changes to match only the filtered exercises' cumulative distance.

**Acceptance Scenarios**:

1. **Given** the exercise table contains both Running and Walking entries, **When** the user selects the "Running" filter, **Then** only Running entries are shown in the table and the map's green progress reflects only the total Running distance.
2. **Given** the user has applied a date filter, **When** they clear all filters, **Then** the full exercise history is restored in the table and the map returns to showing total cumulative progress.
3. **Given** the user applies a filter that matches no exercises, **When** the filter is applied, **Then** the table shows an empty state message and the map shows no green progress.
4. **Given** filters are active, **When** the user adds a new exercise that matches the active filter, **Then** the new exercise appears in the filtered table and the map updates accordingly.

---

### Edge Cases

- What happens when the combined exercise distance exceeds the configured route length? (The entire route turns green; excess distance is not represented beyond the route end.)
- How does the system behave when the exercise table is empty? (The map displays the full red route with no green overlay and the table shows an empty-state message.)
- What happens if a user applies both a type filter and a date filter simultaneously? (Both filters apply together; only exercises matching both criteria are shown.)
- What if the configured route has not been set up? (The route is guaranteed to be configured before the app is deployed to users; an unconfigured state is treated as a deployment error, not a user-facing scenario. See Assumptions.)

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST display the full configured route as a red line on an interactive map.
- **FR-002**: The system MUST display a green line overlay starting from the route's beginning, spanning the cumulative distance of all exercises currently shown in the table.
- **FR-003**: The map MUST support zooming and panning via standard user gestures (scroll, drag, pinch).
- **FR-004**: The system MUST display a table of logged exercises, each row showing at minimum: exercise type, date, and distance.
- **FR-005**: Users MUST be able to add a new exercise entry by specifying exercise type, date, and distance.
- **FR-006**: The system MUST validate exercise entries and reject submissions with missing required fields or invalid values (e.g., non-positive distance).
- **FR-007**: Users MUST be able to filter the exercise table by exercise type (e.g., Walking, Running, Biking).
- **FR-008**: Users MUST be able to filter the exercise table by date or date range.
- **FR-009**: The map's green progress segment MUST update in real time whenever the active filter changes or a new exercise is added.
- **FR-010**: The layout MUST present the map and the exercise panel side by side on a single page, with no multi-page navigation required to access either.
- **FR-011**: When cumulative exercise distance equals or exceeds the configured route length, the system MUST display the entire route as green.
- **FR-012**: The system MUST persist exercise data so that progress is retained across page reloads and browser sessions.

### Key Entities *(include if feature involves data)*

- **Exercise**: Represents a single completed physical activity. Key attributes: type (e.g., Walking, Running, Biking), date completed, distance covered (in a consistent unit).
- **Route**: The pre-configured path between a start location and an end location. Key attributes: start point, end point, total length, the geographic path of the route.
- **Progress**: A derived value — the cumulative distance of exercises currently displayed in the table, expressed as a segment along the Route from its start point.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Users can add a new exercise entry and see it reflected in both the table and the map within 2 seconds of submission.
- **SC-002**: Applying a filter updates the exercise table and map within 1 second.
- **SC-003**: The map loads and displays the configured route within 3 seconds on a standard broadband connection.
- **SC-004**: 100% of logged exercise entries persist correctly across page reloads.
- **SC-005**: Users can complete the full core workflow (open app → add exercise → view map progress) without consulting any external help or documentation.
- **SC-006**: The green progress line is visually distinguishable from the red route line under standard screen and lighting conditions.

## Assumptions

- The route (start point, end point, and path) is configured once at the application level and is the same for all users — individual users do not configure their own routes. The route is guaranteed to be present before the app is deployed; an unconfigured route state will not occur in normal use.
- Exercise distance is recorded and displayed in kilometers. Conversion to miles is out of scope for v1.
- The supported exercise types are fixed for v1: Walking, Running, and Biking. User-defined custom types are out of scope.
- Users access the app via a modern desktop web browser; mobile browser support is desirable but not a hard requirement for v1.
- Exercise entries can be deleted but not edited after submission; an edit-in-place feature is out of scope for v1.
- The map platform used is Google Maps, as explicitly specified in the feature description.
- All users share a single combined exercise log; per-user authentication and individual progress tracking are out of scope for v1.
