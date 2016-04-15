# Migrate

Migrate is a Go library for doing migrations. It's stupidly simple and gets out of your way.

## Features

* It doesn't try to support multiple databases. It's only dependency is `database/sql`.
* It supports any type of migration you want to run (e.g. raw sql, or Go code).
* It doesn't provide a command. It's designed to be embedded in projects.

## Usage

```go
migrations := []migrate.Migration{
	{
		ID: 1,
		Up: func(tx *sql.Tx) error {
			_, err := tx.Exec("CREATE TABLE people (id int)")
			return err
		},
		Down: func(tx *sql.Tx) error {
			_, err := tx.Exec("DROP TABLE people")
			return err
		},
	},
	{
		ID: 2,
		// For simple sql migrations, you can use the migrate.Queries
		// helper.
		Up: migrate.Queries([]string{
			"ALTER TABLE people ADD COLUMN first_name text",
		}),
		Down: func(tx *sql.Tx) error {
			// It's not possible to remove a column with
			// sqlite.
			_, err := tx.Exec("SELECT 1 FROM people")
			return err
		},
	},
}

db, _ := sql.Open("sqlite3", ":memory:")
_ = migrate.Exec(db, migrate.Up, migrations...)
```
