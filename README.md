# DbProof capture

Captures your production schema and planner statistics for [DbProof](https://dbproof.dev), which checks every pull request's migrations against them. It never reads a row of your data.

It connects with the connection string your migrations already use, from your own runner, and reads:

- **The catalog:** tables, columns, indexes, constraints, views, functions, triggers, policies and grants.
- **Planner estimates:** each table's row count, and each column's null fraction, distinct count and average width. Never the value samples in `pg_stats`.
- **Your migration history table,** with anything that could name a person or quote data blanked first.

Then it uploads them with an upload-only token. If it can't run, it says why in a warning and never fails your deploy.

## Usage

Connect your repository at [app.dbproof.dev](https://app.dbproof.dev): DbProof opens a pull request with a scheduled capture, and shows the steps for your deploy job. Around your migration step:

```yaml
- uses: dbproof/capture-action@v1
  with:
    kind: pre
    capture-token: ${{ secrets.DBPROOF_CAPTURE_TOKEN }}
    capture-dsn: ${{ secrets.DBPROOF_CAPTURE_DSN }}

# ... your migration step ...

- uses: dbproof/capture-action@v1
  with:
    kind: post
    capture-token: ${{ secrets.DBPROOF_CAPTURE_TOKEN }}
    capture-dsn: ${{ secrets.DBPROOF_CAPTURE_DSN }}
```

## Inputs

| Input | Default | |
| --- | --- | --- |
| `kind` | | `pre` before your migrations, `post` after, or `scheduled`. |
| `capture-token` | | The project's upload-only capture token. |
| `capture-dsn` | | Connection string for a role that can read the migration history, such as your migrations'. |
| `dbproof-url` | `https://app.dbproof.dev` | DbProof's address. |
| `expect-version` | | After a deploy, the migration version to wait for in the history. |

Every query capture runs is in the open-source agent: [dbproof/agent](https://github.com/dbproof/agent). Pull requests are checked by [DbProof check](https://github.com/dbproof/check-action).

## License

[Apache-2.0](LICENSE)
