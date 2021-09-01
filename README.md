General inspiration: https://en.wikipedia.org/wiki/Social_peer-to-peer_processes

## [__Turbocharger__](https://github.com/trevyn/turbocharger)

provides autogenerated async RPC bindings that instantly connect a JS frontend to a Rust backend service via WebSockets and WASM. Get started by using the turnkey [turbocharger-template](https://github.com/trevyn/turbocharger-template/generate), which adds Turbosql, Svelte, Tailwind, and Snowpack.

Makes a Rust _backend_ function, e.g.:

```rust
#[turbocharger::backend]
async fn get_person(id: i64) -> Person {
 // ... write any async backend code here; ...
 // ... query a remote database, API, etc. ...
 Person { name: "Bob", age: 21 }
}
```

instantly available, with _no additional boilerplate_, to a frontend as

- an async JavaScript function
- with full TypeScript type definitions
- that calls the backend over the network:

```js
// export function get_person(id: number): Promise<Person>;

let person = await backend.get_person(1);
```

## [__Turbosql__](https://github.com/trevyn/turbosql)

works great with Turbocharger; it's an easy local data persistence layer for Rust, backed by SQLite.

```rust
use turbosql::{Turbosql, Blob, select, execute};

#[derive(Turbosql, Default)]
struct Person {
    rowid: Option<i64>, // rowid member required & enforced at compile time
    name: Option<String>,
    age: Option<i64>,
    image_jpg: Option<Blob>
}

fn main() -> Result<(), Box<dyn std::error::Error>> {

    // INSERT a row
    let rowid = Person {
        rowid: None,
        name: Some("Joe".to_string()),
        age: Some(42),
        image_jpg: None
    }.insert()?;

    // SELECT all rows
    let people = select!(Vec<Person>)?;

    // SELECT multiple rows with a predicate
    let people = select!(Vec<Person> "WHERE age > ?", 21)?;

    // SELECT a single row with a predicate
    let person = select!(Person "WHERE name = ?", "Joe")?;

    // UPDATE
    execute!("UPDATE person SET age = ? WHERE name = ?", 18, "Joe")?;

    // DELETE
    execute!("DELETE FROM person WHERE rowid = ?", 1)?;

    Ok(())
}
```

Here's some little app experiments that use Turbocharger and Turbosql:

- https://github.com/trevyn/notesearch

Past movements in this direction include:

- https://github.com/trevyn/equipotent
- https://github.com/trevyn/turbo
- https://github.com/trevyn/turbo-historical
