# Embedded Analytics Documentation

This file provides detail around our embedded analytics offering, including:

- The API endpoints to send the request to (list CASPERs, list runs, and get run details)
- How to authenticate your request
- Response structures (list CASPERs, list runs, and get run details)

# API Endpoints

## List CASPERs

**Method**: GET

**URL**: `https://api.anova.ai/v1/caspers`

## List Runs

**Method**: GET

**URL**: `https://api.anova.ai/v1/caspers/<casper_id>/runs`

**Params**: Must provide valid `<casper_id>`

## Get Run Details

**Method**: GET

**URL**: `https://api.anova.ai/v1/caspers/<casper_id>/runs/<run_id>`

**Params**: Must provide valid `<casper_id>` and `<run_id>`

# Authentication

You must provide a valid API key in the `X-Api-Key` header. Please request one by reaching out to <support@anova.ai>.

# Response Structures

## List CASPERs

You can see an example here: https://github.com/anova-ai/public_docs/blob/main/Embedded_Analytics/responses/get_caspers.json

### `root`

**Type**: Object[]

**Description**: An array of objects, each containing the details of a CASPER.

### `id`

**Type**: String

**Example**: cspr_BCDfghJKLmnp678

**Description**: The unique ID of the CASPER, which is sent to other endpoints.

### `name`

**Type**: String

**Description**: The name you gave your CASPER.

### `created_at`

**Type**: String (ISO 8601)

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the CASPER was created in UTC/GMT.

### `updated_at`

**Type**: String (ISO 8601) | null

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the CASPER was last updated in UTC/GMT, if any. If never updated, the value will be null.

## List Runs

You can see an example here: https://github.com/anova-ai/public_docs/blob/main/Embedded_Analytics/responses/get_runs.json

### `root`

**Type**: Object[]

**Description**: An array of objects, each containing the details of a run.

### `id`

**Type**: String

**Example**: run_BCDfghJKLmnp678

**Description**: The unique ID of the run, which is sent to other endpoints.

### `casper_id`

**Type**: String

**Example**: cspr_BCDfghJKLmnp678

**Description**: The ID of the CASPER that this run belongs to, which is sent to other endpoints.

### `name`

**Type**: String

**Description**: The AI-generated name for the run.

### `started_at`

**Type**: String (ISO 8601)

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the run started in UTC/GMT.

### `ended_at`

**Type**: String (ISO 8601)

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the run ended in UTC/GMT.

## Get Run Details

You can see an example here: https://github.com/anova-ai/public_docs/blob/main/Embedded_Analytics/responses/get_run_details.json

### `root`

**Type**: Object

**Description**: An object with the details of the run.

### `id`

**Type**: String

**Example**: run_BCDfghJKLmnp678

**Description**: The unique ID of the run, which will match what you requested.

### `casper_id`

**Type**: String

**Example**: cspr_BCDfghJKLmnp678

**Description**: The ID of the CASPER that this run belongs to, which will match what you requested.

### `name`

**Type**: String

**Description**: The AI-generated name for the run.

### `status`

**Type**: Enum

**Enum**:
```json
[
    "COMPLETED",
    "FAILED"
]
```

**Description**: The status of the run. If successful, it will be `COMPLETED`. Otherwise, it will be `FAILED` with more details in the `error` field.

### `meta_data`

**Type**: Object

**Description**: Any metadata for the run. This is an ever-evolving field.

#### `iterations`

**Type**: Integer

**Description**: The number of iterations the AI took. Each iteration consists of a thought, data request, SQL, and micro analysis.

### `context`

**Type**: Object

**Description**: A snapshot of the context for this run that the AI used.

#### `objective`

**Type**: String

**Description**: The most recent objective for the CASPER.

#### `initial_date_range`

**Type**: String

**Description**: The most recent initial date range for the CASPER.

#### `analysis_instructions`

**Type**: String

**Description**: The most recent analysis instructions for the CASPER.

#### `additional_context`

**Type**: String

**Description**: The most recent additional context for the stream.

#### `benchmark_against`

**Type**: String

**Description**: The most recent benchmark against for the stream.

#### `guardrails`

**Type**: String

**Description**: The most recent guardrails for the stream.

#### `additional_filter_rules`

**Type**: String

**Description**: The most recent additional filter rules for the stream.

### `nodes`

**Type**: Object[]

**Description**: An array of objects, each containing the details of an iteration.

#### `thinking`

**Type**: String

**Description**: The AI's reasoning and thought process behind the data request being made to help remove the black box behind what the AI is doing and why.

#### `data_request`

**Type**: String

**Description**: The AI's data request.

#### `sql`

**Type**: String

**Description**: The SQL generated for the data request and run against the data warehouse for the AI to do its analysis. Anova does not store the actual data, so to get it, you will need to run this same SQL against your data warehouse. Any dates in the SQL are static, so the results should be the same as what the AI used in its analysis.

#### `micro_analysis`

**Type**: String

**Description**: The AI's micro analysis of the data returned from the SQL.

### `macro_analysis`

**Type**: String

**Description**: The AI's macro analysis of the entire run, which includes what happened, why it happened, and what to do next broken out by priority.

### `completion_tokens`

**Type**: Integer

**Description**: The total number of AI output tokens across all agents for the run.

### `prompt_tokens`

**Type**: Integer

**Description**: The total number of prompt input tokens across all agents for the run.

### `total_tokens`

**Type**: Integer

**Description**: The total number of tokens (completion + prompt) across all agents for the run.

### `error`

**Type**: Object | null

**Description**: If the `status` of the run is `FAILED`, this field will contain more details around the error. This is an ever-evolving field.

### `started_at`

**Type**: String (ISO 8601)

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the run started in UTC/GMT.

### `ended_at`

**Type**: String (ISO 8601)

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the run ended in UTC/GMT.

### `created_at`

**Type**: String (ISO 8601)

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the run was created in UTC/GMT.

### `updated_at`

**Type**: String (ISO 8601) | null

**Example**: 2025-01-31 13:57:12.345678+00

**Description**: The date and time the run was last updated in UTC/GMT, if any. If never updated, the value will be null.