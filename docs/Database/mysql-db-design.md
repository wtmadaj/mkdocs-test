# MySQL Database Design
---

## Description


## Tables
### Users Table

- **user_id (Primary Key)**: UUID. A unique identifier for each user.
- **username or email**: Unique identifier for login.
- Other user-related information like name, profile picture, etc.

### Notes Table

- **note_id (Primary Key)**: UUID. A unique identifier for each note.
- **user_id (Foreign Key)**: References the ```user_id``` in the Users table to establish a relationship between users and their notes.
- **title**: Title of the note.
- **content**: The actual content of the note.
- **tag_count**: number of tags associated with the note.
- **attachment_count**: If using attachment table.
- **created_at**: Timestamp indicating when the note was created.
- **updated_at**: Timestamp indicating when the note was last updated.

### Attachments Table

- **attachment_id (Primary Key)**: A unique identifier for each attachment.
- **note_id (Foreign Key)**: References the ```note_id``` in the Notes table to associate attachments with specific notes.
- **file_name**: Name of the attached file.
- **file_path**: Path to the stored file on your server or cloud storage.
- **file_type**: MIME type or file extension to identify the type of file.
- **created_at**: Timestamp indicating when the attachment was added.

#### Attachments Application Logic
When a user adds an attachment to a note, your application logic should:
1. Store the file on your server or cloud storage.
2. Create a record in the Attachments table with information about the attachment and its association with the corresponding note.
3. Update the attachment_count column in the Notes table for the respective note.
4. When displaying notes, retrieve attachments associated with each note and provide appropriate links or previews for users to access them.

**Security Considerations**: Ensure proper validation and sanitization of file uploads to prevent security vulnerabilities like file injection attacks.
Implement access controls to ensure that users can only access attachments associated with their own notes.

**Scalability and Performance**: Consider scalability and performance implications, especially if your application allows large file uploads. You may need to optimize storage and retrieval mechanisms, and possibly implement caching strategies for frequently accessed attachments.

### Tags Table
- tag_id (Primary Key): UUID. A unique identifier for each tag.
- tag_name: The name of the tag.
- Optionally, add additional columns such as created_at to track when the tag was created.

### NoteTags Table
Create an intermediate table to establish a many-to-many relationship between notes and tags.

- note_id (Foreign Key): References the note_id in the Notes table.
- tag_id (Foreign Key): References the tag_id in the Tags table.
- Optionally, additional columns like created_at to track when the tag was added to the note.

#### Tags Application Logic
When a user adds tags to a note, your application logic should:
1. Create records in the Tags table for any new tags that don't already exist.
2. Create records in the NoteTags table to establish associations between the note and the tags.
3. Update the tag_count column in the Notes table for the respective note.
4. When displaying notes, retrieve tags associated with each note and display them accordingly.

##### Search and Filter Functionality
Implement search and filter functionality based on tags, allowing users to easily find notes with specific tags.

##### Ensure Data Integrity
Implement constraints or validations to ensure that tags are unique and properly formatted.
Implement cascading deletes or updates to maintain data integrity when notes or tags are deleted or modified.

---

## Attachments as separate table || Attachments within Notes Table
Using a separate attachments table instead of inserting attachments directly into the notes table offers several benefits:

- **Normalization**: Separating attachments into their own table follows the principles of database normalization, specifically the Third Normal Form (3NF). Each table in the database represents a single entity (in this case, either notes or attachments), which reduces data redundancy and ensures efficient storage.
- **Scalability**: Storing attachments in a separate table can improve the scalability of your database. If notes can have multiple attachments, storing them directly in the notes table could lead to bloating and increased storage requirements as the number of attachments grows. With a separate table, you can more efficiently manage and scale the storage of attachments independently of notes.
- **Flexibility**: Separating attachments into their own table allows for more flexibility in managing attachments. You can implement features like versioning, access controls, and different storage mechanisms (e.g., local file system, cloud storage) more effectively when attachments are stored separately.
- **Performance**: Retrieving notes without attachments can be faster when attachments are stored in a separate table, especially if attachments are large or if there are many of them. This separation allows the database to optimize queries for retrieving notes without having to deal with the overhead of fetching potentially large binary data.
- **Security**: Storing attachments separately can enhance security by allowing you to implement different access controls and encryption mechanisms specifically tailored to attachment storage. It can also help mitigate risks associated with SQL injection attacks targeting attachment data.

In summary, using a separate attachments table provides better data organization, scalability, flexibility, performance, and security compared to storing attachments directly within the notes table.

---

## Tags as separate table || Tags within Notes Table
If tags are an enumeration, meaning they have a predefined set of values and don't change frequently, you might wonder about the necessity of using separate Tag and NoteTag tables. While it might seem simpler to just have a column for tags directly within the Notes table, there are still benefits to using separate tables:

- **Consistency and Data Integrity**: By using a separate Tag table, you enforce consistency in your data. This ensures that all tags are uniformly represented and eliminates the risk of typographical errors or inconsistencies in tag names. It also facilitates easier management if changes to the tag set are required in the future.
- **Scalability and Flexibility**: Even if tags are currently an enumeration, future requirements might change. For example, you might want to add additional attributes to tags (e.g., descriptions, colors) or allow users to create custom tags. Using a separate Tag table provides flexibility to accommodate such changes without needing to modify the structure of the Notes table.
- **Normalization**: Using separate tables follows the principles of database normalization, specifically the Third Normal Form (3NF). Each table represents a distinct entity (tags and notes), which helps reduce data redundancy and improves database efficiency.
- **Efficient Queries**: Separating tags into their own table allows for more efficient querying, especially if you need to perform operations like searching for notes based on tags or aggregating data by tag. It also facilitates easier maintenance and optimization of database indexes for tag-related queries.
- **Integration with Other Features**: Having a separate Tag table provides a foundation for implementing additional features related to tags, such as tag-based search, tag suggestions, or tag analytics. It also makes it easier to integrate with external systems or APIs that might use standardized tag formats.

While using separate Tag and NoteTag tables might introduce some additional complexity compared to storing tags directly within the Notes table, the benefits in terms of data consistency, scalability, and flexibility often outweigh the overhead. Additionally, modern database systems are well-equipped to efficiently handle joins between tables, making the performance impact of using separate tables minimal in most cases.

## Schemas