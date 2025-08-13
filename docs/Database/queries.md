# Queries
## Read Queries

### GetAllNotes
- My Rmmbrits
- MyRmmbritsListView


Used to display all RmmbrIts on the home page

```sql
Select * from Notes
```

### GetNoteTags


```sql
Select * from NoteTags where
note_id = '${noteId}';
```


### GetAllTags
- Add New RmmbrIt
- AllTagsChoiceChips

```sql
Select * from Tags ORDER BY tag_name;
```


### GetNote
```sql
Select * from Notes where note_id = '${noteId}'
```



### GetTagsForNote
- My Rmmbrits
- TagsRow GridView
- Edit Rmmbrit
- Container

```sql
SELECT *
FROM Tags t
JOIN NoteTags nt ON t.tag_id = nt.tag_id
WHERE nt.note_id = '${noteId}' ORDER BY tag_name;
```


### GetUserInfo
```sql
Select * from Users where user_id = 'new-user-1';
```


### GetAttachmentsForNote
```sql
SELECT *
FROM Attachments
WHERE note_id = '${noteId}';
```


### SearchNotesAndTags

!!! warning "Deprecated"

```sql
SELECT *
FROM Notes n
LEFT JOIN NoteTags nt ON n.note_id = nt.note_id
LEFT JOIN Tags t ON nt.tag_id = t.tag_id
WHERE n.title LIKE '${search}%'
   OR n.content LIKE '${search}%'
   OR t.tag_name LIKE '${search}%';
```


### GetTagIdFromName
```sql
SELECT * FROM Tags WHERE tag_name = '${tagName}';
```


### SearchNotesAndTagsV2
!!! warning "Deprecated"

```sql
SELECT *
FROM Notes n
WHERE n.title LIKE '%${search}%'
   OR n.content LIKE '%${search}%'
   OR NoteTags.tag_name LIKE '%${search}%';
```


### SearchTags
- Tags
- SearchTagsGridView

Searches tag_name based on search query and returns results

```sql
SELECT *
FROM Tags 
WHERE tag_name LIKE '%${search}%' ORDER BY tag_name;
```


### SearchNotesAndTagsV3
```sql
SELECT *
FROM Notes
WHERE title LIKE '%${search}%'
   OR content LIKE '%${search}%'
   OR tags_column LIKE '%${search}%';
```


---
## Update Queries

### AddNote
- My Rmmbrits
- FloatingActionButton
- Action Flow

Creates a new RmmbrIt when the action button is pressed on My RmmbrIts page. This is done in order to prevent attachment null pointers when the AddNewRmmbrit widget loads.
```sql
INSERT INTO Notes (note_id, title, content, created_at, updated_at, tags_column) 
VALUES (
  '${noteId}',
  '${title}', 
  '${content}', 
  date(), 
  date(),
  '${combinedTags}'
 );
```

### DeleteNote
```sql
DELETE FROM Notes WHERE note_id = '${id}';
DELETE FROM Attachments WHERE note_id = '${id}';
DELETE FROM NoteTags WHERE note_id = '${id}';
```


### UpdateNote
```sql
UPDATE Notes
SET 
    title = '${title}',
    content = '${content}',
    updated_at = date(),
    tags_column = '${combinedTags}'
WHERE note_id = '${noteId}';
```


### AddTagsForNewNote
```sql
INSERT INTO NoteTags (note_id, tag_id)
VALUES ('${noteId}', '${tagId}');
```


### UpdateTagsForExistingNote
```sql
DELETE FROM NoteTags
WHERE note_id = '${noteId}';
INSERT INTO NoteTags (note_id, tag_id)
VALUES ('${noteId}', '${tagId}');
```


### UpdateTag
```sql
UPDATE Tags
SET 
    tag_name = '${tagName}'
WHERE tag_id = '${tagId}';
```
- Need to update the tags_column in Notes table



### DeleteTag
```sql
DELETE FROM Tags where tag_id='${tagId}';
DELETE FROM NoteTags WHERE tag_id='${tagId}';
```
- Need to update the tags_column in Notes table



### CreateNewTag
```sql
INSERT into Tags (tag_name, tag_id) 
VALUES ('${tagName}', '${tagId}');
```


### DeleteAttachment
```sql
Delete from Attachments where attachment_id = '${attachmentId}';
```


### AddAttachment
```sql
INSERT into Attachments (attachment_id, attachment, note_id)
VALUES (
  '${attachmentId}',
  '${attachment}',
  '${noteId}'
  );
```


### UpdateUserInfo
```sql
UPDATE Users
SET 
    display_name = '${displayName}',
    email = '${email}',
```


### AddTempNote
```sql
INSERT INTO Notes (note_id, created_at) 
VALUES (
  '${noteId}',
  date()
 );
```


### DeleteTempNote
```sql
DELETE FROM Notes WHERE note_id = '${noteId}'; 
```


### TestAddNoteFromTagName
```sql
INSERT OR IGNORE INTO NoteTags (note_id, tag_id)
VALUES ('${noteId}', (SELECT tag_id FROM Tags WHERE tag_name = '${tagName}'));
```


### DeleteAllTagsForNote
```sql
DELETE FROM NoteTags WHERE note_id = '${noteId}';
```
