# Data Model: Simple Notes App

**Phase**: 1 - Data Architecture  
**Generated**: 2025-09-23  
**Dependencies**: research.md technical decisions

## Core Entities

### Note Entity
**Purpose**: Represents a user-created note with metadata
**Storage**: JSON file (`backend/notes.json`)
**Lifecycle**: Create → Read → (No update/delete in MVP)

#### Schema Definition
```json
{
  "id": "string",           // Unique identifier (timestamp-based)
  "title": "string",        // User-provided title (required)
  "content": "string",      // User-provided content (required)
  "createdAt": "string",    // ISO 8601 timestamp
  "updatedAt": "string"     // ISO 8601 timestamp (same as createdAt for MVP)
}
```

#### Validation Rules
- **id**: Auto-generated, format: `note_${Date.now()}_${Math.random()}`
- **title**: Required, non-empty, non-whitespace, max 200 characters
- **content**: Required, non-empty, non-whitespace, max 10,000 characters
- **createdAt**: Auto-generated, ISO 8601 format
- **updatedAt**: Auto-generated, same as createdAt (future extensibility)

#### Business Rules
- **Uniqueness**: Prevent duplicate notes with identical title+content combination
- **Ordering**: Default sort by createdAt descending (newest first)
- **Persistence**: All notes persist between application sessions
- **Limits**: Maximum 1000 notes to prevent performance degradation

## Data Storage Strategy

### File Structure
```
backend/
└── notes.json           # Primary data file
    ├── notes: []        # Array of note objects
    ├── metadata: {}     # File metadata (count, lastUpdated)
    └── version: "1.0"   # Schema version for future migrations
```

### Storage Operations
1. **Read All Notes**: Load entire JSON file, sort by createdAt desc
2. **Create Note**: Validate → Generate ID → Append to array → Save file
3. **Duplicate Check**: Compare title+content against existing notes
4. **Backup Strategy**: Keep `.backup` copy before each write operation

### Error Scenarios
- **File not found**: Create new empty notes.json with default structure
- **Invalid JSON**: Restore from backup, log error for investigation
- **Write failure**: Return error to client, maintain in-memory state
- **Storage full**: Graceful error message to user

## API Data Contracts

### Get All Notes
**Endpoint**: `GET /api/notes`
**Response**: Array of note objects sorted by createdAt (descending)
**Performance**: <500ms for up to 1000 notes

### Create Note
**Endpoint**: `POST /api/notes`
**Request Body**: `{ title: string, content: string }`
**Response**: Created note object with generated id and timestamps
**Validation**: Server-side validation with detailed error responses

## Frontend Data Flow

### State Management
- **In-Memory**: Current notes array for UI rendering
- **Local Operations**: Optimistic UI updates with server confirmation
- **Error Handling**: Revert optimistic updates on server errors
- **Loading States**: Visual feedback during API operations

### UI Data Binding
- **Notes List**: Direct array rendering with reverse chronological order
- **Form State**: Temporary state until successful save
- **Validation**: Real-time client-side validation with server confirmation

## Performance Considerations

### Memory Usage
- **Expected**: ~1KB per note × 1000 notes = ~1MB maximum
- **File I/O**: Synchronous operations acceptable for expected data size
- **Caching**: Keep current notes array in memory, refresh on mutations

### Scalability Boundaries
- **Note Count**: Hard limit at 1000 notes with user notification
- **Content Size**: Limit individual notes to 10KB total content
- **File Size**: Monitor notes.json size, implement archiving if needed

## Security & Validation

### Input Sanitization
- **XSS Prevention**: HTML escape all user content for display
- **Length Limits**: Enforce maximum lengths client and server-side
- **Character Encoding**: UTF-8 support for international characters
- **SQL Injection**: N/A (file-based storage)

### Data Integrity
- **Atomic Writes**: Write to temp file, then rename for atomicity
- **Backup Strategy**: Maintain backup before destructive operations
- **Validation**: Strict schema validation on all data operations
- **Error Recovery**: Graceful handling of corrupted data scenarios

## Future Extensibility

### Schema Evolution
- **Version Field**: Enable future migrations
- **Backward Compatibility**: Maintain read compatibility for older formats
- **Migration Strategy**: Automatic schema updates on application start

### Feature Preparation
- **Update Capability**: updatedAt field ready for edit functionality
- **Categories**: Schema extensible for future tagging/categorization
- **Search**: Content indexed for future search implementations
- **Export**: JSON format enables easy data export/import

## Constitutional Compliance

✅ **Code Quality**: Clear data structures with comprehensive validation  
✅ **Testing**: All data operations unit-tested with edge cases  
✅ **UX Consistency**: Predictable data behavior across all interactions  
✅ **Performance**: Data model optimized for <500ms API responses  

**Ready for**: Contract definition and quickstart documentation