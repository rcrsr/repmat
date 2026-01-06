# API Docs

Preset for API documentation and specifications. Auto-detects API style and documentation format.

## Detection Signals

Identify as API docs from:
- Filename patterns: `openapi.yaml`, `swagger.json`, `api.md`, `*-api.md`
- Location: `docs/api/`, `api/`, `specs/`
- Content markers: Endpoint definitions, request/response examples

Identify documentation format from:
- OpenAPI/Swagger: `openapi:`, `swagger:`, YAML/JSON with paths
- AsyncAPI: `asyncapi:` version marker
- API Blueprint: `.apib` files, `FORMAT: 1A`
- RAML: `.raml` files
- GraphQL: `schema.graphql`, `.gql` files
- Markdown: Structured markdown with endpoint sections
- Postman: `.postman_collection.json`

Identify generation tools from:
- Swagger UI: `swagger-ui` in dependencies
- Redoc: `redoc` configuration
- Stoplight: `.stoplight/` directory
- Docusaurus OpenAPI: `docusaurus-plugin-openapi`

## Search Keywords

Based on detected format:
- "[format] best practices [year]"
- "[format] documentation patterns"
- "[format] tooling ecosystem"

General API documentation:
- "API documentation best practices"
- "REST API documentation"
- "API reference structure"
- "API versioning documentation"

## Investigation Focus

When analyzing local API docs, examine:
- **Specification completeness** — All endpoints documented? Request/response schemas?
- **Examples** — Are request/response examples provided?
- **Error documentation** — Error codes, error response formats
- **Authentication** — How is auth documented? Security schemes?
- **Versioning** — How are API versions documented?
- **Changelog** — API change history, deprecation notices
- **Code generation** — Is client code generated from specs?
- **Validation** — Is the spec validated in CI? Linting?
- **Sync with implementation** — How is spec kept in sync with code?

## Policy Considerations

Common sections for API documentation policies:
- Required sections (Overview, Authentication, Endpoints)
- Example requirements
- Error documentation format
- Versioning documentation
- Deprecation notice format
- Changelog requirements
- Validation requirements
- Sync with implementation process

## CI/CD Detection

Look for API documentation pipelines:
- GitHub Actions: `.github/workflows/*.yml` with spec validation
- Spectral: `.spectral.yaml` (OpenAPI linting)
- Swagger validation: swagger-cli, openapi-generator validate
- Documentation deployment: Swagger UI, Redoc hosting

## Defaults

- **meta_type**: status
- **states**: [Draft, Published, Deprecated]
- **author_role**: editor
- **typical_patterns**: `**/openapi.yaml`, `**/swagger.json`, `**/asyncapi.yaml`, `docs/api/**/*.md`
