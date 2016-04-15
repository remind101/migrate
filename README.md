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
                ID: "1",
                Up: func(tx *sql.Tx) error {
                        return tx.Exec(`CREATE TABLE foo`)
                },
                Down: func(tx *sql.Tx) error {
                        return tx.Exec(`DROP TABLE foo`)
                },
        },
        {
                ID: "2",
                Up: migrate.Queries([]string{
                        "ALTER TABLE foo ADD COLUMN bar text",
                }),
                Down: migrate.Queries([]string{
                        "ALTER TABLE foo DROP COLUMN bar",
                }),
        },
}

err := migrate.Exec(db, migrations, migrate.Up)
```
