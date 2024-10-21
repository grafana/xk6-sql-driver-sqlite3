# xk6-sql-driver-sqlite3

Database driver extension for [xk6-sql](https://github.com/grafana/xk6-sql) k6 extension to support SQLite v3 database.

## Example

```JavaScript file=examples/example.js
import sql from "k6/x/sql";
import driver from "k6/x/sql/driver/sqlite3";

const db = sql.open(driver, "./test.db");

export function setup() {
  db.exec(`
    CREATE TABLE IF NOT EXISTS roster
      (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        given_name VARCHAR NOT NULL,
        family_name VARCHAR NOT NULL
      );
  `);
}

export function teardown() {
  db.close();
}

export default function () {
  let result = db.exec(`
    INSERT INTO roster
      (given_name, family_name)
    VALUES
      ('Peter', 'Pan'),
      ('Wendy', 'Darling'),
      ('Tinker', 'Bell'),
      ('James', 'Hook');
  `);
  console.log(`${result.rowsAffected()} rows inserted`);

  let rows = db.query("SELECT * FROM roster WHERE given_name = $1;", "Peter");
  for (const row of rows) {
    console.log(`${row.family_name}, ${row.given_name}`);
  }
}
```

## Usage

Check the [xk6-sql documentation](https://github.com/grafana/xk6-sql) on how to use this database driver.

## Build

Since the sqlite3 driver uses a native shared library, the build requirements are slightly different from other drivers.

**Prerequisites**

- [Go toolchain](https://go101.org/article/go-toolchain.html)
- A build toolchain for your system that includes `gcc` or  another C compiler. On Debian and derivatives install the `build-essential` package. On Windows you can use [tdm-gcc](https://jmeubank.github.io/tdm-gcc/). Make sure that `gcc` is in your `PATH`.
- Git
- Set `CGO_ENABLED=1` in the environment

**Linux**

```bash
CGO_ENABLED=1 xk6 build --with github.com/grafana/xk6-sql-driver-sqlite3@latest --with github.com/grafana/xk6-sql@latest
```

**Windows**

```
set CGO_ENABLED=1
xk6 build --with github.com/grafana/xk6-sql-driver-sqlite3@latest --with github.com/grafana/xk6-sql@latest
```
