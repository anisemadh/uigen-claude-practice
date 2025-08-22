# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

UIGen is an AI-powered React component generator with live preview capabilities. It's a Next.js 15 application that allows users to describe React components through chat and see them generated and rendered in real-time using a virtual file system.

## Development Commands

### Setup and Installation
```bash
npm run setup  # Installs dependencies, generates Prisma client, runs migrations
```

### Development
```bash
npm run dev             # Start development server with Turbopack
npm run dev:daemon      # Start dev server in background, logs to logs.txt
```

### Testing and Quality
```bash
npm test               # Run Vitest tests
npm run lint          # ESLint checking
npm run build         # Production build
```

### Database Management
```bash
npx prisma generate    # Generate Prisma client
npx prisma migrate dev # Run database migrations
npm run db:reset      # Reset database (force)
```

## Architecture Overview

### Core Structure
- **Virtual File System**: The application uses a custom `VirtualFileSystem` class that manages files in memory without writing to disk
- **Dual-Panel Interface**: Chat interface on left for user input, preview/code editor on right
- **AI Integration**: Uses Anthropic Claude via Vercel AI SDK for component generation
- **Project Persistence**: Registered users can save projects; anonymous users work with temporary sessions

### Key Directories
- `src/app/` - Next.js App Router pages and API routes
- `src/components/` - React components organized by feature (auth, chat, editor, preview, ui)
- `src/lib/` - Core utilities, contexts, file system, database, and AI tools
- `src/actions/` - Server actions for project management
- `prisma/` - Database schema and migrations (SQLite)

### Context Architecture
The app uses React Context for state management:
- **FileSystemContext**: Manages virtual file system state, file operations, and tool call handling
- **ChatContext**: Manages chat messages, AI interactions, and project persistence

### Database Schema
- **User**: Authentication (email/password with bcrypt)
- **Project**: Stores project data including serialized messages and file system state
- Projects can be anonymous (userId null) or owned by registered users

### AI Tool System
The chat interface uses structured tools for file manipulation:
- `str_replace_editor`: Create, edit, and replace content in files
- `file_manager`: Rename and delete files
- Tools are handled by the FileSystemContext to update the virtual file system

### Preview System
Components are rendered using:
- Babel Standalone for JSX transformation
- Iframe-based preview with sandboxed execution
- Real-time updates when files change

## Important Implementation Details

### File System Serialization
- File system state is serialized to JSON for database storage
- Deserialization reconstructs the VirtualFileSystem from saved data
- Initial file selection prioritizes `/App.jsx` then falls back to root files

### Authentication Flow
- JWT-based sessions using Jose library
- Middleware handles protected routes
- Anonymous users can create temporary projects

### Development Notes
- Uses Tailwind CSS v4 with custom configuration
- Monaco Editor for code editing with syntax highlighting
- React 19 with concurrent features
- TypeScript throughout with strict configuration
- Add comments sparingly. Only add comments for complex code.