# API Contract: POST /api/notes

**Endpoint**: `POST /api/notes`  
**Purpose**: Create a new note with title and content  
**Method**: POST  
**Authentication**: None required

## Request

### Headers
```
Content-Type: application/json
Accept: application/json
```

### Request Body
```json
{
  "title": "string (required)",
  "content": "string (required)"
}
```

#### Validation Rules
- **title**: Required, non-empty, non-whitespace, max 200 characters
- **content**: Required, non-empty, non-whitespace, max 10,000 characters
- Both fields must contain at least one non-whitespace character

## Response

### Success Response (201 Created)
```json
{
  "success": true,
  "data": {
    "id": "note_1695456789123_0.456",
    "title": "Shopping List",
    "content": "Milk, Bread, Eggs, Cheese",
    "createdAt": "2025-09-23T10:33:09.123Z",
    "updatedAt": "2025-09-23T10:33:09.123Z"
  }
}
```

### Validation Error (400 Bad Request)
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Note validation failed",
    "details": {
      "title": "Title is required and cannot be empty",
      "content": "Content is required and cannot be empty"
    }
  }
}
```

### Duplicate Error (409 Conflict)
```json
{
  "success": false,
  "error": {
    "code": "DUPLICATE_NOTE",
    "message": "A note with identical title and content already exists",
    "details": "Please modify the title or content to create a unique note"
  }
}
```

### Storage Error (500 Internal Server Error)
```json
{
  "success": false,
  "error": {
    "code": "STORAGE_ERROR",
    "message": "Unable to save note. Please try again.",
    "details": "Internal server error occurred while saving note"
  }
}
```

### Storage Limit Error (413 Payload Too Large)
```json
{
  "success": false,
  "error": {
    "code": "STORAGE_LIMIT",
    "message": "Maximum number of notes reached (1000 limit)",
    "details": "Please delete some notes before creating new ones"
  }
}
```

## Behavior Specifications

### Note Creation Process
1. **Validate** request body against schema rules
2. **Check** for duplicate title+content combination
3. **Generate** unique ID using timestamp and random component
4. **Create** timestamps (createdAt, updatedAt)
5. **Save** to notes.json file with atomic write operation
6. **Return** created note object

### ID Generation
- Format: `note_${timestamp}_${randomFloat}`
- Example: `note_1695456789123_0.456789`
- Guarantees uniqueness across concurrent requests

### Performance Requirements
- Response time MUST be <500ms including file I/O
- Memory usage MUST be <5MB during note creation
- File write operation MUST complete within 300ms

## Edge Cases & Error Handling

### Input Validation Errors
1. **Missing title**: Return 400 with specific field error
2. **Empty content**: Return 400 with validation details  
3. **Whitespace only**: Treat as empty, return validation error
4. **Oversized input**: Return 400 with size limit information
5. **Invalid JSON**: Return 400 with JSON parsing error

### System Errors
1. **File write failure**: Return 500, maintain existing data integrity
2. **Disk space full**: Return 500 with storage error message
3. **Permission denied**: Return 500 with file access error
4. **Concurrent access**: Handle gracefully with retry mechanism

### Business Logic Errors
1. **Duplicate detection**: Compare normalized title+content (trimmed, case-sensitive)
2. **Storage limits**: Check note count before creation
3. **Data corruption**: Validate existing data before adding new note

## Test Scenarios

### Successful Creation
1. **Given** valid title and content
2. **When** POST /api/notes is called
3. **Then** note is created with generated ID and timestamps
4. **And** response contains complete note object
5. **And** note appears in subsequent GET requests

### Validation Failures
1. **Given** empty title or content
2. **When** POST /api/notes is called
3. **Then** response is 400 Bad Request
4. **And** error details specify which fields are invalid
5. **And** no note is created

### Duplicate Prevention
1. **Given** existing note with title "Test" and content "Content"
2. **When** POST with identical title and content
3. **Then** response is 409 Conflict
4. **And** duplicate error message is returned
5. **And** no additional note is created

### Performance Validation
1. **Given** request with valid data
2. **When** POST /api/notes is called
3. **Then** response time is <500ms
4. **And** file write completes successfully
5. **And** memory usage remains within limits

## Implementation Notes

### Atomic Operations
- Use temporary file + rename for atomic writes
- Maintain backup of previous state during updates
- Ensure data consistency even during failures

### Error Recovery
- Implement retry logic for transient failures
- Provide clear error messages for user action
- Log detailed errors for debugging while keeping user messages simple

### Monitoring
- Track creation success/failure rates
- Monitor response times for performance regression
- Alert on storage approaching limits

## Constitutional Compliance

✅ **Performance**: <500ms response time with file I/O  
✅ **UX Consistency**: Clear validation messages and error feedback  
✅ **Code Quality**: Comprehensive error handling and validation  
✅ **Testing**: Full test coverage for all scenarios and edge cases