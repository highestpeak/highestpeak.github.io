# Fusion Music


This product is originally a course project but represents a relatively complete and well-developed system. Personally, I liked the core idea of this project so much that I decided to showcase it here.

## Introduction

We all love music, and music is undeniably cool. Moreover, we want many people to create music together, which is why we made this system collaborative.

In a nutshell, Fusion Music is a real-time collaborative music editing platform where multiple users can work together on music projects simultaneously. It is composed of three main components designed to deliver a seamless collaborative experience.

To be honest, since this started as a course project, it sometimes has a playful and relatively simple nature. However, it embodies many ideas we want to expand upon. A course project is one thing, industrial-grade software another. Here, I simply present what this project currently achieves.

And, while I had a team, I personally handled about 80% of the work—including technical design, establishing a workable CI/CD workflow, collaboration features, fixing all frontend bugs, integration, about 90% of unit testing, deployment, and team management. The contributions from teammates were valuable, but I was able to do their parts more quickly. Considering participation grades and fairness, we had to balance team members' interests. Despite these constraints, this was not an such enjoyable journey, but I’m grateful for the lessons learned. Thank you, God!

## Main User Workflow

The user workflow is straightforward: users enter the editor, invite friends to join, and start collaborating. Users drag and drop notes onto the musical staff and then play back the sound. To keep things manageable, we introduced a music document list page similar to Google Docs, where auto-generated preview images enhance visual browsing.

All features require users to log in via Google OAuth first. The path is simply: Google login → document list page → music staff editor page. That’s it. Very simple. A few screenshots below illustrate the user experience clearly.

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250727194432910.png 'Main User Workflow')

## Tech Design

As the technical lead, I adopted domain-driven design (DDD) as the core architectural philosophy from the outset. It was my first time personally using DDD as the main guiding approach, and I found it extremely effective. There were two key benefits: firstly, it simplified task allocation across team members; secondly, it ensured a highly modular system design, facilitating coding and maintenance. Tasks were assigned according to these domain boundaries and individual expertise, which fostered strong ownership, minimized merge conflicts, and enabled parallel development. This approach ensured high cohesion within modules and accelerated onboarding of new components. Also, it is good and perfect align with the agile process.

> About domain-driven design:
Though not my first mechanical exposure to DDD, this is the first time I consciously chose it for overall architecture design. Reflecting on my 2021–2024 work, I realized our collaborative document systems naturally embrace clear domain responsibilities, but back then I was a coder, not an architect.

I divided the system into four domains as follows:

| Domain                        | Responsibilities                                                                                      |
|-------------------------------|-------------------------------------------------------------------------------------------------------|
| Collaboration & Storage       | User presence tracking, note synchronization via WebSocket, and score persistence in MongoDB.         |
| Canvas Rendering & Dragging   | Staff layout, drag-and-drop note placement, spatial mapping, and drawing loop control.                |
| Wrapper UI Interaction        | Manages outer layout: navigation, avatars, toolbars, and page transitions.                            |
| Note Semantics & Playback     | Note typing, duration handling, rhythm mapping, and audio rendering.                                  |

The canvas and collaboration modules are the most challenging parts, which we will explore further.

### System Architecture

Fusion Music employs a layered, service-oriented architecture. Interaction logic, data flow, and synchronization are split cleanly between frontend, backend, and WebSocket subsystems. Figure~\ref{fig:architecture} depicts the main components and their interrelations.

On the client side, two primary UI modules drive user actions: the **wrapper UI** (navigation, avatars, controls) and the **canvas** (musical staff interaction). These coordinate through a shared **frontend data layer** managing document state, edit buffers, and socket session metadata. This abstraction ensures real-time changes reflect instantly across all visual elements, supporting replay, history tracking, and local validation.

Backend consists of two main endpoints:

- A **RESTful API server** (Express.js) handling user login, document CRUD, save/restore operations, and file attachments (e.g., music samples stored in an object storage like S3).
- A **WebSocket collaboration server** enabling low-latency note synchronization and broadcasts. It validates commands, assigns version numbers, and distributes updates to all clients.

Persistent data—including musical commands, user metadata, and document history—resides in MongoDB, chosen for its schema-less flexibility to accommodate evolving score structures and diverse command formats.

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250727195328044.png 'System architecture of Fusion Music: UI interaction, data layer, backend services, and database persistence')

**Why this design?**

This structure enforces separation of concerns: business logic, real-time sync, and document editing operate in specialized services. WebSocket was selected for bidirectional, event-driven communication ensuring near-instant visual updates, while REST APIs provide stateless, testable persistence endpoints. The layered frontend enables clean state management and modular UI. Combined, this supports scalable collaboration, flexible development, and robust synchronization guarantees.

### Canvas and Sound

Our canvas rendering and audio playback adopt a hybrid approach combining **abcjs** for staff rendering and **Tone.js** for audio output. Unlike monolithic frameworks such as VexFlow, we separated concerns: abcjs handles static layout, while all interactive behaviors and visual feedback are implemented via custom logic.

The rendering pipeline converts internal music models (MusicStaff, MusicMeasure, MusicNote) into ABC notation strings passed to `abcjs.renderAbc` which draws the staff on a resizable canvas, supporting responsive scaling, measure wrapping, and note click handlers. On top of this, drag-and-drop interactions and coordinate-to-pitch conversions are custom-built.

For playback, models map to rhythmic durations (e.g., "4n" for quarter notes) scheduled via `Tone.Part`. Playback timing and visual cursor feedback synchronize through `Tone.getDraw().schedule`, supporting real-time tracking.

This layered design decouples score layout from interaction, retains fine control over playback, and permits future extensibility — like instrument selection and advanced rhythm editing.

### Collaboration

Fusion Music implements a custom command-based collaboration protocol over WebSocket tailored for real-time music score editing. Unlike generic CRDT libraries like Yjs or Automerge, our protocol defines versioned operations respecting the structural and rhythmic constraints of music notation.

Every user action (e.g., note replacement, measure addition) is encoded as a typed command object containing metadata (`documentId`, `timestamp`, `version`) and semantic targeting (`measureIndex`, `noteIndex`). Commands are first validated and versioned by a central collaboration API, then broadcast to all connected clients via WebSocket, ensuring session-wide consistency.

Clients maintain a local lock queue, awaiting ACKs before issuing new commands, preserving operation order and low latency.

Beyond score edits, the protocol covers ephemeral signals like cursor movement and user presence via lightweight messages (e.g., `cursor_position`) using semantic identifiers instead of raw coordinates, enabling resolution-independent rendering.

The following figures `command-structure` and `cursor-message` illustrate the command structure and cursor broadcast format; 

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250727231805108.png 'Versioned command structure & Cursor broadcast message format')


Figures `sync-architecture` and `cursor-broadcast` show the synchronization pipeline and throttled cursor updates.

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250727231927204.png 'End-to-end command flow: client → API → WebSocket → peers')

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250727232005521.png 'Throttled cursor synchronization between clients')


**Why this design?**  

Musical notation demands structurally and semantically accurate updates. Our command-based approach sidesteps OT/CRDT complexity, achieving deterministic replication and undo support. The architecture cleanly separates persistent edits from transient presence, enhancing clarity, debuggability, and long-term evolution.

## Testing

To guarantee correctness and stability, we designed a layered test strategy spanning client, server, and real-time infrastructure.

### Test Coverage

We cover:
- Unit tests: authentication, music model validation, command creation.
- Integration tests: client-server communications, WebSocket broadcasting, version syncing.
- Resilience tests: network loss, reconnection, delayed delivery.
- End-to-end tests: multi-user collaborative editing scenarios.

Implemented in TypeScript using Jest, the suite is organized by functionality as visualized in Figure~\ref{fig:test-files}.

### Automation & CI

Tests run automatically on pull requests and main branch pushes via GitHub Actions. The `MR-Check.yml` workflow enforces linting and test passes pre-merge. Results push to a Discord channel via webhook for immediate team visibility.

Complementing automated tests, manual validation was performed for complex real-time features like cursor sync, multi-user drag-and-drop, and playback accuracy under high latency.

**Summary:** This test mix ensures Fusion Music’s stability and consistency throughout development.

## Deployment

### Deployment Strategy

For flexibility, reliability, and full control, all Fusion Music services run on a single DigitalOcean droplet. Early on, AWS and Google Cloud were excluded due to intricate configuration (IAM, VPCs) and uncertain billing. Personal servers were avoided to prevent permissions issues and scaling bottlenecks.

Front and backend services—including Next.js frontend, Express REST API, and WebSocket server—cohabit the same VM behind an Nginx reverse proxy managing routing, load buffering, and TLS termination. Cloudflare fronts Nginx, offering global CDN, SSL, and DDoS protection.

Though initially considering Vercel for frontend and separate WebSocket hosting, consolidation facilitated deployment, log access, and environment control. A shared shell script performs update, build, and sequential startup.

This setup avoids serverless limitations, crucial for stateful WebSocket sessions.

Deployment architecture is depicted in the following figure.

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250727232047323.png 'Deployment architecture: unified service stack with Nginx proxy and Cloudflare firewall on a single DigitalOcean droplet')

## Looking Ahead

Future plans include:
- Richer music semantics (additional rhythms, accidentals, tuplets)
- Advanced playback controls and multi-instrument synthesis
- Real-time collaborative performance modes (e.g., piano jam sessions)
- Integrated sharing and public gallery features


