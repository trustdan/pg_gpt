# Postgres <> ChatGPT

Experimental PostgreSQL extension that enables the use of OpenAI GPT API inside PostgreSQL, allowing for queries to be written using natural language.

## Demo

https://user-images.githubusercontent.com/1121616/228234378-40c796d3-0a38-465a-92da-9370fb21b93b.mp4

(This demo uses data from the [Hacker News](https://www.cloudquery.io/integrations/hackernews/postgresql) and [Azure](https://www.cloudquery.io/integrations/azure/postgresql) CloudQuery plugins)

## How does it work?

The extension sends a subset of the database schema to ChatGPT and asks it to generate a query based on this and the user input.

## Before you start

- **Note**: This plugins sends schema (without the data) to OpenAI GPT API, so it is not recommended to use it on production databases.
- **Note**: This is an experimental plugin and not officially supported by CloudQuery.

## Installation

Requires [pgrx](https://github.com/tcdi/pgrx). Install this first:

```bash
cargo install --locked cargo-pgrx
cargo pgrx init
```

Now you can install the extension:

```bash
git clone https://github.com/cloudquery/pg_gpt
cd pg_gpt
export OPENAI_KEY=<YOUR_KEY>
cargo pgrx run
# will drop into psql shell
```

```sql
create extension pg_gpt;
set openai.key = '<YOUR OPENAPI API KEY HERE>'; -- set your key
select gpt('show me all open aws s3 buckets');
-- will output the following query, so you can execute it
-- select * from aws_s3_bucket;
```

## Available Functions

- `gpt(text)` - Generates a query based on the user input and the full database schema. This works fine for databases with small schemas.
- `gpt_tables(table_pattern, text)` - Similar to gpt, but only uses the tables that match the pattern. The pattern is passed to a `table_name LIKE` query, so `%` can be used as wildcard.

## Installing the extension on an existing Postgres instance

First run:

```bash
cargo pgrx install
```

This places the extension in the postgres extensions directory. Then, in your postgres instance, run:

```sql
create extension pg_gpt;
set openai.key = '<YOUR OPENAPI API KEY HERE>';
-- proceed to use the extension
```

## Limitations

* Schema Size - Currently we use gpt-3.5-turbo, which is limited to 4096 tokens. Use `gpt_tables` to narrow down the set of tables.
