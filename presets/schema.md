# Schema

Preset for database schemas, migrations, and data models. Auto-detects database and ORM.

## Detection Signals

Identify schema artifacts from:
- Migration files: `migrations/`, `db/migrate/`, `alembic/`
- Schema definitions: `schema.prisma`, `*.sql`, `models.py`
- ORM configurations: Entity definitions, model classes

Identify database/ORM from:
- Prisma: `schema.prisma`, `@prisma/client` in dependencies
- TypeORM: `ormconfig.json`, entity decorators
- Sequelize: `sequelize` in dependencies, model definitions
- SQLAlchemy: `alembic/`, SQLAlchemy imports
- Django ORM: `models.py` with Django model classes
- Drizzle: `drizzle.config.ts`, schema files
- Knex: `knexfile.js`, migration files
- Rails ActiveRecord: `db/migrate/`, `db/schema.rb`
- Raw SQL: `*.sql` files, migration scripts

Identify database type from:
- PostgreSQL: `psql`, `pg_` prefixes, PostgreSQL-specific types
- MySQL: `mysql` configs, MySQL-specific syntax
- SQLite: `*.sqlite`, `sqlite3` imports
- MongoDB: `mongoose` schemas, MongoDB connection strings

## Search Keywords

Based on detected ORM:
- "[ORM] best practices [year]"
- "[ORM] migration patterns"
- "[ORM] schema design"

Based on database:
- "[database] schema design best practices"
- "[database] indexing strategies"
- "[database] normalization patterns"

General schema patterns:
- "database migration best practices"
- "schema versioning patterns"
- "database rollback strategies"

## Investigation Focus

When analyzing local schema code, examine:
- **Migration organization** — Naming conventions, ordering, timestamps
- **Rollback strategy** — Are down migrations defined? Tested?
- **Seeding** — How is test/development data managed?
- **Indexing** — What indexes exist? Performance considerations?
- **Constraints** — Foreign keys, unique constraints, check constraints
- **Naming conventions** — Table names, column names, key names
- **Data types** — Type choices, nullable fields, defaults
- **Soft deletes** — How are deletions handled?
- **Audit fields** — created_at, updated_at, versioning

## Policy Considerations

Common sections for schema policies:
- Naming conventions (tables, columns, indexes)
- Migration file naming
- Rollback requirements
- Index strategy
- Constraint requirements
- Data type standards
- Audit field requirements
- Seed data management
- Review process for schema changes

## CI/CD Detection

Look for database pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml` with migration steps
- Flyway: `flyway.conf`
- Liquibase: `liquibase.properties`
- Database CI: Schema validation, migration testing

## Security Investigation

Examine security-related patterns:
- **Sensitive data** — PII columns, encryption requirements
- **Access control** — Row-level security, column permissions
- **Audit logging** — Change tracking, audit tables
- **Backup strategy** — Backup scripts, retention policies

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns**: `**/migrations/**/*`, `**/schema.*`, `**/models/**/*`, `db/**/*`
