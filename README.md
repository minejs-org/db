<!-- ╔══════════════════════════════ BEG ══════════════════════════════╗ -->

<br>
<div align="center">
    <p>
        <img src="./assets/img/logo.png" alt="logo" style="" height="60" />
    </p>
</div>

<div align="center">
    <img src="https://img.shields.io/badge/v-0.0.2-black"/>
    <img src="https://img.shields.io/badge/🔥-@minejs-black"/>
    <img src="https://img.shields.io/badge/zero-dependencies-black" alt="Test Coverage" />
    <br>
    <img src="https://img.shields.io/badge/coverage-100%25-brightgreen" alt="Test Coverage" />
    <img src="https://img.shields.io/github/issues/minejs-org/db?style=flat" alt="Github Repo Issues" />
    <img src="https://img.shields.io/github/stars/minejs-org/db?style=social" alt="GitHub Repo stars" />
</div>
<br>

<!-- ╚═════════════════════════════════════════════════════════════════╝ -->



<!-- ╔══════════════════════════════ DOC ══════════════════════════════╗ -->

- ## Quick Start 🔥

    > **_Lightweight SQLite database wrapper with fluent query builder and schema management._**

    - ### Setup

        > install [`space`](https://github.com/solution-lib/space) first.

        ```bash
        space i @minejs/db
        ```

    <div align="center"> <img src="./assets/img/line.png" alt="line" style="display: block; margin-top:20px;margin-bottom:20px;width:500px;"/> <br> </div>

    - ### Usage

        ```ts
        import { DB, table, column, primaryKey, integer, text, notNull, references, index } from '@minejs/db'
        ```

        - ### 1. Basic Database Setup

            ```typescript
            // Create in-memory database
            const db = new DB()

            // Or with file path
            const db = new DB('./app.db')

            // Define schema
            db.defineSchema(table('users', [
                primaryKey(integer('id'), true),
                notNull(text('name')),
                notNull(text('email'))
            ]))
            ```

        - ### 2. CRUD Operations

            ```typescript
            // Insert
            const userId = db.insert('users', {
                name: 'John Doe',
                email: 'john@example.com'
            })

            // Read
            const user = db.findById('users', userId)
            const users = db.all('users')
            const admin = db.findOne('users', { name: 'John Doe' })

            // Update
            db.update('users', userId, { email: 'newemail@example.com' })

            // Delete
            db.delete('users', userId)
            ```

        - ### 3. Query Builder

            ```typescript
            const results = db.query()
                .select(['id', 'name', 'email'])
                .from('users')
                .where({ column: 'email', operator: 'LIKE', value: '%@example.com' })
                .orderBy('name', 'ASC')
                .limit(10)
                .execute()
            ```

        - ### 4. Schema with Foreign Keys

            ```typescript
            db.defineSchema(table('posts', [
                primaryKey(integer('id'), true),
                integer('userId'),
                references(
                    column('userId'),
                    'users',
                    'id',
                    { onDelete: 'CASCADE' }
                ),
                text('title'),
                text('content')
            ]))
            ```

        - ### 5. Transactions

            ```typescript
            db.transaction(() => {
                db.insert('users', { name: 'Jane', email: 'jane@example.com' })
                db.insert('posts', { userId: 1, title: 'First Post', content: 'Content' })
                // All succeed or all rollback
            })
            ```

        - ### 6. Indexes

            ```typescript
            db.defineSchema(table('users', [
                primaryKey(integer('id'), true),
                notNull(text('email')),
                index('idx_email', 'email', true)  // unique index
            ]))
            ```

    <br>

- ## API Reference 🔥

    - #### `new DB(path?: string): DB`
        > Create a database instance.

        ```typescript
        // In-memory database (default)
        const db = new DB()

        // Or with file path
        const db = new DB('./data/app.db')
        ```

    - #### `DB.defineSchema(schema: TableSchema): void`

        > Define a table schema.

        ```typescript
        import { table, column, primaryKey, integer, text, notNull } from '@minejs/db'

        db.defineSchema(table('users', [
            primaryKey(integer('id'), true),
            notNull(text('name')),
            notNull(text('email'))
        ]))
        ```

    - #### `DB.getSchema(tableName: string): TableSchema | undefined`

        > Retrieve schema definition for a table.

        ```typescript
        const schema = db.getSchema('users')
        console.log(schema.columns)
        ```

    - #### `DB.listTables(): string[]`

        > Get list of all tables in database.

        ```typescript
        const tables = db.listTables()
        console.log(tables) // ['users', 'posts', ...]
        ```

    - #### `DB.dropTable(tableName: string): void`

        > Drop a table from database.

        ```typescript
        db.dropTable('users')
        ```

    - #### `DB.insert(table: string, data: Record<string, SqlValue>): unknown`

        > Insert a new record.

        ```typescript
        const id = db.insert('users', {
            name: 'John',
            email: 'john@example.com'
        })
        ```

    - #### `DB.findById(table: string, id: number | string): unknown | null`

        > Find record by ID.

        ```typescript
        const user = db.findById('users', 1)
        ```

    - #### `DB.findOne(table: string, conditions: Record<string, SqlValue>): unknown | null`

        > Find first matching record.

        ```typescript
        const user = db.findOne('users', { email: 'john@example.com' })
        ```

    - #### `DB.find(table: string, conditions: Record<string, SqlValue>): unknown[]`

        > Find all matching records.

        ```typescript
        const users = db.find('users', { status: 'active' })
        ```

    - #### `DB.all(table: string): unknown[]`

        > Get all records from table.

        ```typescript
        const allUsers = db.all('users')
        ```

    - #### `DB.update(table: string, id: number | string, data: Record<string, SqlValue>): unknown | null`

        > Update a record by ID.

        ```typescript
        const updated = db.update('users', 1, {
            email: 'newemail@example.com'
        })
        ```

    - #### `DB.delete(table: string, id: number | string): boolean`

        > Delete a record by ID.

        ```typescript
        const success = db.delete('users', 1)
        ```

    - #### `DB.query(): QueryBuilder`

        > Create a query builder for advanced queries.

        ```typescript
        const results = db.query()
            .select(['id', 'name'])
            .from('users')
            .where({ column: 'age', operator: '>', value: 18 })
            .orderBy('name', 'ASC')
            .limit(10)
            .execute()
        ```

    - #### `DB.transaction(callback: (db: DB) => void): void`

        > Execute operations in a transaction.

        ```typescript
        db.transaction(() => {
            db.insert('users', { name: 'Alice' })
            db.insert('posts', { userId: 1, title: 'Welcome' })
        })
        ```

    - #### `DB.exec(sql: string): void`

        > Execute raw SQL statement.

        ```typescript
        db.exec('DELETE FROM users WHERE age < 18')
        ```

    - #### `DB.raw(sql: string, params?: SqlValue[]): unknown[]`

        > Execute raw SQL query with parameters.

        ```typescript
        const users = db.raw(
            'SELECT * FROM users WHERE age > ? ORDER BY name',
            [18]
        )
        ```

    - #### `DB.rawOne(sql: string, params?: SqlValue[]): unknown | null`

        > Execute raw SQL query returning single result.

        ```typescript
        const user = db.rawOne(
            'SELECT * FROM users WHERE email = ?',
            ['john@example.com']
        )
        ```

    - #### `DB.close(): void`

        > Close database connection.

        ```typescript
        db.close()
        ```

    - #### `QueryBuilder`

        > Fluent interface for building queries.

        ```typescript
        interface QueryBuilder {
            select(columns?: string[]): QueryBuilder
            from(table: string): QueryBuilder
            where(condition: WhereCondition | WhereCondition[]): QueryBuilder
            and(condition: WhereCondition): QueryBuilder
            or(condition: WhereCondition): QueryBuilder
            orderBy(column: string, direction?: 'ASC' | 'DESC'): QueryBuilder
            limit(count: number): QueryBuilder
            offset(count: number): QueryBuilder
            insert(table: string, data: Record<string, SqlValue>): QueryBuilder
            update(table: string, data: Record<string, SqlValue>): QueryBuilder
            delete(table: string): QueryBuilder
            execute(): unknown[]
            executeOne(): unknown | null
            raw(sql: string, params?: SqlValue[]): QueryBuilder
        }
        ```

    - #### `table(name: string, columns: ColumnDefinition[]): TableSchema`

        > Create a table schema definition.

        ```typescript
        const usersSchema = table('users', [
            primaryKey(integer('id'), true),
            notNull(text('name'))
        ])
        ```

    - #### `column(name: string, type: ColumnType): ColumnDefinition`

        > Define a column.

        ```typescript
        const nameCol = column('name', 'TEXT')
        ```

    - #### `integer(name: string): ColumnDefinition`

        > Create INTEGER column.

        ```typescript
        const ageCol = integer('age')
        ```

    - #### `text(name: string): ColumnDefinition`

        > Create TEXT column.

        ```typescript
        const nameCol = text('name')
        ```

    - #### `real(name: string): ColumnDefinition`

        > Create REAL (float) column.

        ```typescript
        const priceCol = real('price')
        ```

    - #### `blob(name: string): ColumnDefinition`

        > Create BLOB (binary) column.

        ```typescript
        const dataCol = blob('data')
        ```

    - #### `numeric(name: string): ColumnDefinition`

        > Create NUMERIC column.

        ```typescript
        const idCol = numeric('id')
        ```

    - #### `primaryKey(col: ColumnDefinition, autoIncrement?: boolean): ColumnDefinition`

        > Add primary key constraint.

        ```typescript
        const idCol = primaryKey(integer('id'), true)  // auto-increment
        ```

    - #### `notNull(col: ColumnDefinition): ColumnDefinition`

        > Add NOT NULL constraint.

        ```typescript
        const nameCol = notNull(text('name'))
        ```

    - #### `unique(col: ColumnDefinition): ColumnDefinition`

        > Add UNIQUE constraint to single column.

        ```typescript
        const emailCol = unique(text('email'))
        ```

    - #### `unique(columns: string[]): UniqueConstraint`

        > Create composite UNIQUE constraint.

        ```typescript
        unique(['email', 'provider'])
        ```

    - #### `defaultValue(col: ColumnDefinition, value: SqlValue): ColumnDefinition`

        > Set column default value.

        ```typescript
        const statusCol = defaultValue(text('status'), 'active')
        ```

    - #### `references(col: ColumnDefinition, table: string, column: string, options?: ForeignKeyOptions): ColumnDefinition`

        > Add foreign key constraint.

        ```typescript
        const userIdCol = references(
            integer('userId'),
            'users',
            'id',
            { onDelete: 'CASCADE', onUpdate: 'CASCADE' }
        )
        ```

    - #### `index(name: string, columns: string | string[], unique?: boolean): IndexDefinition`

        > Create index on columns.

        ```typescript
        index('idx_email', 'email')                    // single column
        index('idx_user_post', ['userId', 'postId'])  // composite
        index('idx_email_unique', 'email', true)       // unique index
        ```

<!-- ╚═════════════════════════════════════════════════════════════════╝ -->



<!-- ╔══════════════════════════════ END ══════════════════════════════╗ -->

- ## Real-World Examples

  - #### Blog Application Schema

    ```typescript
    import { DB, table, column, primaryKey, integer, text, notNull, references, unique, index } from '@minejs/db'

    const db = new DB('./blog.db')

    // Users table
    db.defineSchema(table('users', [
        primaryKey(integer('id'), true),
        notNull(unique(text('email'))),
        notNull(text('name')),
        text('avatar'),
        index('idx_email', 'email')
    ]))

    // Posts table
    db.defineSchema(table('posts', [
        primaryKey(integer('id'), true),
        integer('userId'),
        references(
            column('userId'),
            'users',
            'id',
            { onDelete: 'CASCADE' }
        ),
        notNull(text('title')),
        notNull(text('content')),
        text('slug'),
        index('idx_user_posts', ['userId', 'id'])
    ]))

    // Comments table
    db.defineSchema(table('comments', [
        primaryKey(integer('id'), true),
        integer('postId'),
        integer('userId'),
        references(column('postId'), 'posts', 'id', { onDelete: 'CASCADE' }),
        references(column('userId'), 'users', 'id', { onDelete: 'CASCADE' }),
        notNull(text('content'))
    ]))
    ```

  - #### CRUD Operations

    ```typescript
    // Create user
    const userId = db.insert('users', {
        email: 'john@example.com',
        name: 'John Doe'
    })

    // Create post
    const postId = db.insert('posts', {
        userId: userId,
        title: 'My First Post',
        content: 'This is amazing!',
        slug: 'my-first-post'
    })

    // Find user
    const user = db.findById('users', userId)

    // Find user's posts
    const userPosts = db.find('posts', { userId: userId })

    // Update post
    db.update('posts', postId, {
        title: 'Updated Title'
    })

    // Delete comment
    db.delete('comments', commentId)
    ```

  - #### Advanced Queries

    ```typescript
    // Find all posts ordered by creation
    const recentPosts = db.query()
        .select(['id', 'title', 'userId'])
        .from('posts')
        .orderBy('id', 'DESC')
        .limit(10)
        .execute()

    // Find posts by specific user with pagination
    const page1 = db.query()
        .select('*')
        .from('posts')
        .where({ column: 'userId', operator: '=', value: userId })
        .orderBy('id', 'DESC')
        .limit(20)
        .offset(0)
        .execute()

    // Count posts using raw SQL
    const count = db.raw('SELECT COUNT(*) as total FROM posts')[0]
    ```

  - #### Transactions

    ```typescript
    db.transaction(() => {
        // Create user
        const userId = db.insert('users', {
            email: 'jane@example.com',
            name: 'Jane Smith'
        })

        // Create multiple posts
        db.insert('posts', {
            userId: userId,
            title: 'First Post',
            content: 'Welcome!'
        })

        db.insert('posts', {
            userId: userId,
            title: 'Second Post',
            content: 'Another one'
        })

        // If any operation fails, all rollback
    })
    ```

  - #### Schema Inspection

    ```typescript
    // Get all tables
    const tables = db.listTables()
    console.log(tables) // ['users', 'posts', 'comments']

    // Get table schema
    const schema = db.getSchema('users')
    console.log(schema.name)     // 'users'
    console.log(schema.columns)  // [...]

    // Drop table
    db.dropTable('comments')
    ```

  - #### Raw SQL for Complex Queries

    ```typescript
    // Join query
    const postsWithAuthors = db.raw(`
        SELECT 
            p.id,
            p.title,
            u.name as author
        FROM posts p
        JOIN users u ON p.userId = u.id
        WHERE u.id = ?
        ORDER BY p.id DESC
    `, [userId])

    // Aggregation
    const stats = db.raw(`
        SELECT 
            u.name,
            COUNT(p.id) as post_count,
            COUNT(c.id) as comment_count
        FROM users u
        LEFT JOIN posts p ON u.id = p.userId
        LEFT JOIN comments c ON p.id = c.postId
        GROUP BY u.id
        HAVING post_count > ?
    `, [5])
    ```

<br>

---

<div align="center">
    <a href="https://github.com/maysara-elshewehy"><img src="https://img.shields.io/badge/by-Maysara-black"/></a>
</div>

<!-- ╚═════════════════════════════════════════════════════════════════╝ -->
