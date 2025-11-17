# Semantic Layer Documentation

This file provides detail around our semantic layer, including:

- The overall structure of the JSON template
- Description of attributes
- Usage and examples

This file will serve three key purposes:

1. Prompt foundation for initial schema generation to give our team and customers a starting point
2. Helpful guide for our team and customers during the schema creation phase of the onboarding process
3. Prompt foundation for relevant AI agents so they know how to interpret the data schema

This is an ever-evolving layer. As the template scales, this documentation **must and will** be kept in sync.

# Structure

## `version`

**Required**: Yes

**Type**: String

**Default**: 1.0.0

**Description**: Allows us to define the **major.minor.patch** as we anticipate modifying and scaling the template -- major increments for backward-incompatible changes, minor increments for new backward-compatible features, and patch increments for backward-compatible minor enhancements. This system helps users understand the nature of changes in a new version.

## `metadata`

**Required**: Yes

**Type**: Object

**Description**: Holds any future metadata we want to include in the file. For now, it is an empty object just added for future-proofing.

## `tables`

**Required**: Yes

**Type**: Object

**Description**: The tables that the AI should be aware of. Any irrelevant or unnecessary tables can safely be excluded, which gives the AI focus without the user needing to adjust their actual data schema. This is intentionally an object (instead of an array) with each top-level key being the name of the table so that, in the future if anything needs to be overridden for a specific table, we can target the table by name (instead of an index).

### `__NAME__`

**Required**: Yes

**Type**: String

**Example**: conversions

**Description**: The name of the table.

#### `description`

**Required**: No, but highly recommended

**Type**: String

**Example**: Contains a breakdown of conversions by campaigns, ad groups, and keywords

**Description**: Describes the purpose, underlying data, or usage of the table. This is an ideal place to detail any business-specific nuances that the AI should be aware of and may not be intuitive from just the table's name.

#### `columns`

Refer to the `columns` section of this document for more details.

#### `default_date_column`

**Required**: Yes, if there are multiple date columns in the table

**Type**: String

**Example**: conversion_date

**Description**: If there are multiple date columns in the table, this lets the AI know which one to use for filtering when a column isn't explicitly specified.

#### `primary_key`

**Required**: Yes, if the table has any relevant primary key(s)

**Type**: String | String[]

**Example (String)**: Most common for a single primary key.
```
id
```

**Example (String[])**: An array-style approach for composite primary keys.
```json
[
    "campaign_id",
    "ad_group_id"
]
```

**Description**: The primary key(s) for a specific table.

#### `foreign_keys`

**Required**: Yes, if the table has any relevant foreign keys

**Type**: Object[]

**Description**: The foreign keys for a specific table, which is important for the AI to understand the relationships between tables and how to join them when generating cross-table SQLs. Each item is a single reference (e.g., if there are two foreign keys, then there will be two items).

##### `from_column`

**Required**: Yes

**Type**: String | String[]

**Example (String)**: Most common for when a single foreign key is referenced.
```
campaign_id
```

**Example (String[])**: An array-style approach for when composite foreign keys are referenced.
```json
[
    "campaign_id",
    "ad_group_id"
]
```

**Description**: The foreign key(s) in this specific table.

##### `to_table`

**Required**: Yes

**Type**: String

**Example**: campaigns

**Description**: The table this relationship references.

##### `to_column`

**Required**: Yes

**Type**: String | String[]

**Example (String)**: Most common for when a single column is referenced.
```
id
```

**Example (String[])**: An array-style approach for when multiple columns are referenced.
```json
[
    "campaign_id",
    "ad_group_id"
]
```

**Description**: The column(s) in the table being referenced.

##### `cardinality`

**Required**: Yes

**Type**: Enum

**Enum**:
```json
[
    "one_to_one",
    "one_to_many",
    "many_to_one",
    "many_to_many"
]
```

**Description**: Describes the number of instances of one entity that can be associated with the number of instances of another entity in the relationship, which helps the AI determine the joins that can be used. This **must** be a single value from the enum list.

##### `default_join`

**Required**: No

**Type**: Enum

**Enum**:
```json
[
    "INNER",
    "LEFT",
    "RIGHT",
    "FULL",
    "CROSS"
]
```

**Default**: INNER

**Description**: Lets the user override the default join type if needed.

#### `filter_rules`

**Required**: No

**Type**: String

**Example**: A natural language explanation of the filter rules to apply. A SQL snippet can even be provided (e.g., a WHERE statement) for a more concrete example.
```
Filter for active campaigns belonging to client 123 or 456
```
```sql
WHERE is_active = true AND (client_id = 123 OR client_id = 456)
```

**Description**: Table-level filter rules that are uncircumventable. These are applied using the WHERE clause before data is grouped or aggregated. This is useful when there's a requirement to ensure data is always filtered a specific way anytime this schema is used. Database-level filter rules, that are applied using the HAVING clause after data is grouped or aggregated, are also available. Any optional filters that the user or AI can apply if needed should **not** be defined here.

## `columns`

**Required**: Yes

**Type**: Object

**Description**: The columns within a specific table. Any irrelevant or unnecessary columns can safely be excluded, which gives the AI focus without the user needing to adjust their actual data schema. This is intentionally an object (instead of an array) to break up the columns into dimensions and metrics.

### `dimensions`

**Required**: Yes, if the table has any relevant dimensions

**Type**: Object

**Description**: The dimensions within a specific table. This is intentionally an object (instead of an array) with each top-level key being the name of the dimension so that, in the future if anything needs to be overridden for a specific dimension, we can target the dimension by name (instead of an index).

#### `__NAME__`

**Required**: Yes

**Type**: String

**Example**: utm_campaign

**Description**: The name of the dimension.

##### `alias`

**Required**: Yes

**Type**: String

**Example**: campaign

**Description**: The alias provides a way to map columns across tables that may be named differently but should be treated the same when the data is grouped. For example, there may be one table that has a _utm_campaign_ column and another table that has a _campaign_ column, but both serve the same purpose, have the same values, and should be grouped together when aggregating data. In this case, both can be aliased as _campaign_ and the AI will treat them as the same when generating SQLs.

##### `type`

**Required**: No, but highly recommended

**Type**: String

**Example**: VARCHAR

**Description**: The data type for the dimension. This helps the AI generate more accurate SQLs, especially when calling functions or doing more complex data transformations. This **must** reflect the actual type in their database.

##### `display_format`

**Required**: No, but recommended for better display in tables and charts

**Type**: Enum

**Enum**:
```json
[
    "text",            // Brand Awareness
    "date_YYYY-MM-DD"  // 2025-01-31
]
```

**Description**: Determines how data is formatted when displayed in tables and charts. This **must** be a single value from the enum list. If additional options need to be added, please email <support@anova.ai>.

##### `date_format`

**Required**: Yes, if the dimension is a date-type

**Type**: String

**Example**: YYYY-MM-DD

**Description**: Defines the date format for any date-type dimensions, especially helpful when the date is manipulated in the generated SQL (e.g., extracting the month).

##### `description`

**Required**: No, but highly recommended

**Type**: String

**Example**: The campaign responsible for the conversion

**Description**: Describes the purpose, underlying data, or usage of the dimension. This is an ideal place to detail any business-specific nuances that the AI should be aware of and may not be intuitive from just the dimension's name.

##### `values`

**Required**: No

**Type**: String | String[] | Object[]

**Example (String)**: A comma-separated list in natural language.
```
google, microsoft, instagram
```

**Example (String[])**: A more defined array-style approach.
```json
[
    "google",
    "microsoft",
    "instagram"
]
```

**Example (Object[])**: Advanced, long-form syntax that allows users to not only define the value, but the nomenclature that users may use to refer to each value. For example, if a user asks for _"breakdown of performance for paid search"_, the AI will know that they want to filter for _google_ and _microsoft_.
```json
[
    {
        "value": "google",
        "nomenclature": "paid search, sem"
    },
    {
        "value": "microsoft",
        "nomenclature": "bing, paid search, sem"
    },
    {
        "value": "instagram",
        "nomenclature": "paid social, meta"
    }
]
```

**Description**: Helpful for the AI to know what it can filter on when a dimension has a finite list of potential values that can exist. A general description of values that can exist here (e.g., _U.S. holidays_) can also be provided and the AI will look here if it needs to filter by values (e.g., _Christmas_, _Thanksgiving_, etc.), though this is not as accurate.

##### `nomenclature`

**Required**: No

**Type**: String | String[]

**Example (String)**: A comma-separated list in natural language.
```
site, publisher, partner, vendor
```

**Example (String[])**: A more defined array-style approach.
```json
[
    "site",
    "publisher",
    "partner",
    "vendor"
]
```

**Description**: Allows the user to define other terminology for how this dimension can be referenced, which is useful for when departments or users within their organization may refer to the dimension in different ways.

##### `transformation_rules`

**Required**: No

**Type**: String | String[] | Object[]

**Example (String)**: A natural language explanation of the transformation rule to apply. A SQL snippet can even be provided (e.g., a CASE statement) for a more concrete example.
```
Rename meta ads, facebook ads, and instagram ads to meta ads
```
```sql
CASE utm_source
    WHEN 'facebook ads' THEN 'meta ads'
    WHEN 'instagram ads' THEN 'meta ads'
    ELSE 'meta ads'
END AS final_source
```

**Example (String[])**: A more defined array-style approach of the above.
```json
[
    "Rename meta ads, facebook ads, and instagram ads to meta ads",
    "Rename wall street journal, wsj.com, and wsj to wsj"
]
```

**Example (Object[])**: Advanced, long-form syntax that allows users to provide a description of the rule to give the AI more context.
```json
[
    {
        "description": "A rule to standardize all Meta properties so we can get a holistic view",
        "rule": "Rename meta ads, facebook ads, and instagram ads to meta ads"
    },
    {
        "description": "Wall Street Journal was tagged with different values so we need to normalize",
        "rule": "Rename wall street journal, wsj.com, and wsj to wsj"
    }
]
```

**Description**: Allows on-the-fly data transformations on the dimension without changing the underlying raw data. Only SQL-level transformations are supported at this time.

### `metrics`

**Required**: Yes, if the table has any relevant metrics

**Type**: Object

**Description**: The metrics within a specific table. This is intentionally an object (instead of an array) with each top-level key being the name of the metric so that, in the future if anything needs to be overridden for a specific metric, we can target the metric by name (instead of an index).

#### `__NAME__`

**Required**: Yes

**Type**: String

**Example**: viewable_impressions

**Description**: The name of the metric.

##### `alias`

**Required**: Yes

**Type**: String

**Example**: impressions

**Description**: The alias provides a way to map columns across tables that may be named differently but should be aggregated when the data is grouped. For example, there may be one table that has a _viewable_impressions_ column and another table that has a _impressions_ column, but both serve the same purpose. In this case, both can be aliased as _impressions_ and the AI will aggregate them when generating SQLs.

##### `type`

**Required**: No, but highly recommended

**Type**: String

**Example**: INT

**Description**: The data type for the metric. This helps the AI generate more accurate SQLs, especially when calling functions or doing more complex data transformations. This **must** reflect the actual type in their database.

##### `display_format`

**Required**: No, but recommended for better display in tables and charts

**Type**: Enum

**Enum**:
```json
[
    "integer",      // 123
    "float",        // 123.45
    "percent",      // 12.34%
    "currency_usd"  // $1,234.56
]
```

**Description**: Determines how data is formatted when displayed in tables and charts. This **must** be a single value from the enum list. If additional options need to be added, please email <support@anova.ai>.

##### `description`

**Required**: No, but highly recommended

**Type**: String

**Example**: An impression registered when the ad is visible in the browser window

**Description**: Describes the purpose, underlying data, or usage of the metric. This is an ideal place to detail any business-specific nuances that the AI should be aware of and may not be intuitive from just the metric's name.

##### `aggregation`

**Required**: No

**Type**: Enum

**Enum**:
```json
[
    "AVG",
    "COUNT",
    "COUNT_DISTINCT",
    "MAX",
    "MIN",
    "SUM"
]
```

**Default**: SUM

**Description**: Determines how the metric should be aggregated. This **must** be a single value from the enum list. If additional options need to be added, please email <support@anova.ai>.

##### `nomenclature`

**Required**: No

**Type**: String | String[]

**Example (String)**: A comma-separated list in natural language.
```
in-view impressions, billable impressions
```

**Example (String[])**: A more defined array-style approach.
```json
[
    "in-view impressions",
    "billable impressions"
]
```

**Description**: Allows the user to define other terminology for how this metric can be referenced, which is useful for when departments or users within their organization may refer to the metric in different ways.

##### `transformation_rules`

**Required**: No

**Type**: String | String[] | Object[]

**Example (String)**: A natural language explanation of the transformation rules to apply. A SQL snippet can even be provided for a more concrete example.
```
Divide by 100, as the data is incorrectly inflated
```
```sql
SELECT (metric / 100) AS final_metric
```

**Example (String[])**: A more defined array-style approach of the above.
```json
[
    "Divide by 100, as the data is incorrectly inflated"
]
```

**Example (Object[])**: Advanced, long-form syntax that allows users to provide a description of the rule to give the AI more context.
```json
[
    {
        "description": "A rule to calculate the correct number of impressions",
        "rule": "Divide by 100, as the data is incorrectly inflated"
    }
]
```

**Description**: Allows on-the-fly data transformations on the metric without changing the underlying raw data. Only SQL-level transformations are supported at this time.

## `calculated_metrics`

**Required**: Yes, if there are any relevant calculated metrics

**Type**: Object

**Description**: The calculated metrics to compute. This is intentionally at the database-level so both single-table calculated metrics (when all the raw metrics for the calculation exist in a single table) or multi-table calculated metrics (when one or more raw metrics for the calculation exist in multiple tables) can be computed. Also, this is intentionally an object (instead of an array) with each top-level key being the name of the calculated metric so that, in the future if anything needs to be overridden for a specific calculated metric, we can target the calculated metric by name (instead of an index).

### `__NAME__`

**Required**: Yes

**Type**: String

**Example**: roi

**Description**: The name of the calculated metric.

#### `alias`

**Required**: Yes

**Type**: String

**Example**: roi

**Description**: The alias provides a predictable name for the calculated metric, instead of letting the AI generate one.

#### `formula`

**Required**: Yes

**Type**: String

**Example**: ((revenue - cost) / cost) * 100

**Description**: The formula to calculate the metric. Columns **must** be referenced by their **_alias_**. The alias is needed because calculations are done _after_ the data has been aggregated, which is also why the table is _not_ needed.

#### `display_format`

**Required**: No, but recommended for better display in tables and charts

**Type**: Enum

**Enum**:
```json
[
    "integer",      // 123
    "float",        // 123.45
    "percent",      // 12.34%
    "currency_usd"  // $1,234.56
]
```

**Description**: Determines how data is formatted when displayed in tables and charts. This **must** be a single value from the enum list. If additional options need to be added, please email <support@anova.ai>.

#### `description`

**Required**: No, but highly recommended

**Type**: String

**Example**: The return on investment from our media campaigns

**Description**: Describes the purpose, underlying data, or usage of the calculated metric. This is an ideal place to detail any business-specific nuances that the AI should be aware of and may not be intuitive from just the calculated metric's name.

#### `nomenclature`

**Required**: No

**Type**: String | String[]

**Example (String)**: A comma-separated list in natural language.
```
roas, return on ad spend
```

**Example (String[])**: A more defined array-style approach.
```json
[
    "roas",
    "return on ad spend"
]
```

**Description**: Allows the user to define other terminology for how this calculated metric can be referenced, which is useful for when departments or users within their organization may refer to the calculated metric in different ways.

## `filter_rules`

**Required**: No

**Type**: String

**Example**: A natural language explanation of the filter rules to apply. A SQL snippet can even be provided (e.g., a HAVING statement) for a more concrete example.
```
Filter for roi < 1
```
```sql
HAVING roi < 1
```

**Description**: Database-level filter rules that are uncircumventable. These are applied using the HAVING clause after data is grouped or aggregated. This is useful when there's a requirement to ensure data is always filtered a specific way anytime this schema is used. Table-level filter rules, that are applied using the WHERE clause before data is grouped or aggregated, are also available. Any optional filters that the user or AI can apply if needed should **not** be defined here.

## `drill_down_relationships`

**Required**: No

**Type**: String | String[]

**Example (String)**: When there is one relationship.
```
campaign > ad_group > keyword
```

**Example (String[])**: When there is more than one relationship.
```json
[
    "campaign > ad_group > keyword",
    "country > state > city"
]
```

**Description**: Hierarchical relationship of dimensions that allows the AI to navigate through the data from a general summary to more specific levels of detail. Dimensions **must** be referenced by their **_alias_** and separated by **>**.

## `additional_context`

**Required**: No

**Type**: String | String[]

**Example (String)**: A natural language explanation of any additional context / nuances you want the AI to have.
```
There is a known issue where data for Q1 of 2024 is missing
```

**Example (String[])**: A more defined array-style approach of the above.
```json
[
    "There is a known issue where data for Q1 of 2024 is missing",
    "Facebook data refreshes weekly on Mondays"
]
```

**Description**: Allows the user to define or reinforce any additional context that the AI should be aware of. For more complex nuances, a separate set of instructions **must** be created, please email <support@anova.ai>.

## `sql_examples`

**Required**: Not initially, but highly recommended if any AI knowledge gaps have been identified

**Type**: Object[]

**Description**: Reinforces correct SQL generation if any AI knowledge gaps (e.g., inaccurate or invalid SQLs) have been identified or reported. Both, complete SQLs or SQL snippets, are supported to ensure each example is focused on what is being trained / taught to the AI. This is better to address _after_ the AI has generated some initial SQLs so that the training can be targeted to where the AI actually needs reinforcement. For more complex SQLs, a separate set of instructions **must** be created, please email <support@anova.ai>.

### `sql`

**Required**: Yes

**Type**: String

**Example**:
```sql
SUM(
    CASE
        WHEN client_name = 'Client A' THEN website_leads + phone_calls
        WHEN client_name = 'Client B' THEN meetings_booked + downloads
        ELSE 0
    END
) AS conversions
```

**Description**: The complete SQL or SQL snippet that is addressing the knowledge gap.

### `teaches`

**Required**: Yes

**Type**: String

**Example**: This shows how conversions are properly calculated for Client A and Client B

**Description**: A clear, focused explanation of what the example SQL teaches, so the AI knows exactly what to learn from it.