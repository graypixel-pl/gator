# Gator

Gator is a command-line RSS feed aggregator written in Go. It stores users, feeds, feed follows, and posts in PostgreSQL and can continuously collect posts from subscribed RSS feeds.

## Requirements

To run Gator, you need:

- Go installed
- PostgreSQL installed and running

You will also need a PostgreSQL database for Gator, for example:

```sh
createdb gator
```

## Install

Once this repository is available on GitHub, install the CLI with:

```sh
go install github.com/graypixel-pl/gator@latest
```

Make sure your Go binary directory is in your `PATH`. You can check it with:

```sh
go env GOPATH
```

The installed binary is typically placed in:

```text
$GOPATH/bin
```

## Database setup

Run the database migrations using Goose from the `sql/schema` directory:

```sh
cd sql/schema
goose postgres "postgres://postgres:postgres@localhost:5432/gator" up
```

Adjust the connection string to match your local PostgreSQL setup.

If you need Goose:

```sh
go install github.com/pressly/goose/v3/cmd/goose@latest
```

This project also uses SQLC for generated database code:

```sh
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
sqlc generate
```

## Configuration

Create a file named:

```text
~/.gatorconfig.json
```

Example:

```json
{
  "db_url": "postgres://postgres:postgres@localhost:5432/gator?sslmode=disable"
}
```

The application will add `current_user_name` when you register or log in.

## Usage

Register a user:

```sh
gator register alice
```

Log in as an existing user:

```sh
gator login alice
```

List users:

```sh
gator users
```

Add a feed:

```sh
gator addfeed "Boot.dev Blog" "https://blog.boot.dev/index.xml"
```

List available feeds:

```sh
gator feeds
```

Follow an existing feed:

```sh
gator follow "https://blog.boot.dev/index.xml"
```

List feeds followed by the current user:

```sh
gator following
```

Unfollow a feed:

```sh
gator unfollow "https://blog.boot.dev/index.xml"
```

Run the RSS aggregator every minute:

```sh
gator agg 1m
```

The aggregator runs continuously. Stop it with `Ctrl+C`.

Browse the two most recent posts for the current user:

```sh
gator browse
```

Or specify a limit:

```sh
gator browse 10
```

Reset the database users and related data:

```sh
gator reset
```

Because related records use cascading deletes, resetting users also removes dependent feed and follow data where applicable.

## Development

Generate SQLC code after changing SQL queries:

```sh
sqlc generate
```

Build the project:

```sh
go build .
```

Run directly during development:

```sh
go run . <command> [arguments...]
```

