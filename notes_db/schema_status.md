# NoteMaster DB Schema Status

This file indicates that the PostgreSQL schema for the NoteMaster app has been initialized.

## Connection
Use the canonical connection command in:
- `notes_db/db_connection.txt`

## Objects created

### Tables
- `notes`
  - `id` BIGINT identity primary key
  - `title` TEXT not null default ''
  - `content` TEXT not null default ''
  - `is_archived` BOOLEAN not null default false
  - `created_at` TIMESTAMPTZ default now()
  - `updated_at` TIMESTAMPTZ default now()

- `tags`
  - `id` BIGINT identity primary key
  - `name` TEXT not null
  - `created_at` TIMESTAMPTZ default now()
  - `updated_at` TIMESTAMPTZ default now()
  - CHECK: `length(trim(name)) > 0`

- `note_tags` (many-to-many)
  - `note_id` BIGINT references `notes(id)` on delete cascade
  - `tag_id` BIGINT references `tags(id)` on delete cascade
  - PRIMARY KEY (`note_id`, `tag_id`)

### Indexes
- `tags_name_uq_idx` UNIQUE on `lower(name)` (case-insensitive uniqueness)
- `notes_updated_at_idx` on `notes(updated_at desc)`
- `note_tags_tag_id_idx` on `note_tags(tag_id)`
- `notes_title_trgm_idx` (GIN trigram)
- `notes_content_trgm_idx` (GIN trigram)

### Extensions
- `pg_trgm`

### Triggers
- `set_updated_at()` function (plpgsql)
- `trg_notes_set_updated_at` on `notes`
- `trg_tags_set_updated_at` on `tags`

## Seed data (development convenience)
- A welcome note
- Tags: `inbox`, `retro`
- Join rows linking welcome note to both tags
