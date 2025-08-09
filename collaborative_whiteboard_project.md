# Collaborative Whiteboard Project Guide

## Overview
This document outlines the architecture and implementation details for building a collaborative whiteboard application using Ruby on Rails, as discussed in a workspace conversation. The project leverages a Rails monolith, Hotwire, Action Cable, and Yjs for real-time collaboration, with Fabric.js for the drawing layer. Additionally, instructions for committing the project file to GitHub are included.

## Core Architecture
- **Rails Monolith**: All server logic, routing, and persistence in a single Rails app.
- **Hotwire/Stimulus**: Server-side rendering (SSR) with Turbo for dynamic updates and Stimulus for client-side interactivity.
- **Action Cable**: Rails' WebSocket support for real-time events like whiteboard updates and user presence.

### Example Setup
```ruby
# config/cable.yml
development:
  adapter: redis
  url: redis://localhost:6379/1
```

## Whiteboard/Collaboration Design
- **Data Model**:
  - `Board`: Stores serialized Yjs state in a `jsonb` column.
  - `Element`: Represents shapes, lines, text, managed by Fabric.js and synced via Yjs.
  - `Action`: Optional, for storing state changes for audit logs or undo/redo.
- **Drawing Layer**: Use Fabric.js with `y-fabric` for canvas-based rendering and collaboration.
- **Real-Time Synchronization**: Yjs for CRDT-based conflict resolution, integrated with Action Cable via `yrb-actioncable`.

### Example Model
```ruby
# app/models/board.rb
class Board < ApplicationRecord
  store :state, coder: JSON
end
```

## Collaboration Features
- **Presence**: Yjs awareness protocol for real-time user cursors/avatars.
- **Authorization**: Devise for authentication, Pundit for access control.
- **Persistence**: Store board snapshots in PostgreSQL `jsonb` column for recovery and versioning.
- **Versioning & History**: Yjs snapshots or `Action` records for undo/redo and audit logs.

### Example Action Cable Subscription
```javascript
// app/javascript/channels/board_channel.js
import consumer from "./consumer"
consumer.subscriptions.create({ channel: "BoardChannel", id: boardId }, {
  connected() {
    console.log("Connected to board channel");
  },
  received(data) {
    console.log("Received data:", data);
  }
});
```

## Workflow
1. User loads the board: Rails renders with Hotwire, client initializes Yjs.
2. User draws: Fabric.js updates locally, Yjs syncs, Action Cable broadcasts.
3. Clients merge updates via Yjs, redraw canvas.
4. Periodic database saves for persistence.

## Committing to GitHub
To commit the project file (e.g., `collaborative_whiteboard_guide.java`) to GitHub:
1. Navigate to your repository on [github.com](https://github.com).
2. Click **"Add file"** > **"Upload files"**, select the file.
3. Add a commit message, e.g., "Add collaborative_whiteboard_guide.java".
4. Choose the branch (main or new) and click **"Commit changes"**.

### Notes
- **File Size Limit**: 25 MiB for web uploads.
- **Protected Branches**: Create a new branch and pull request if main is protected.
- **Alternative**: Use Git commands for larger files or advanced workflows.

## Resources
- [Fabric.js Documentation](https://fabricjs.com/)
- [Yjs Documentation](https://docs.yjs.dev/)
- [yrb-actioncable](https://github.com/y-crdt/yrb-actioncable)
- [GitHub: Adding a file](https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository)

*Generated on August 7, 2025, based on workspace conversation.*