# newtype-uuid

[![newtype-uuid on crates.io](https://img.shields.io/crates/v/newtype-uuid)](https://crates.io/crates/newtype-uuid)
[![Documentation (latest release)](https://img.shields.io/badge/docs-latest%20version-brightgreen.svg)](https://docs.rs/newtype-uuid)
[![Documentation (main)](https://img.shields.io/badge/docs-main-brightgreen)](https://oxidecomputer.github.io/newtype-uuid/rustdoc/newtype_uuid/)
[![License](https://img.shields.io/badge/license-Apache-green.svg)](LICENSE-APACHE)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE-MIT)

A newtype wrapper around `Uuid`.

## Motivation

Many large systems use UUIDs as unique identifiers for various entities. However, the `Uuid`
type does not carry information about the kind of entity it identifies, which can lead to mixing
up different types of UUIDs at runtime.

This crate provides a wrapper type around `Uuid` that allows you to specify the kind of entity
the UUID identifies.

## Example

```rust
use newtype_uuid::{GenericUuid, TypedUuid, TypedUuidKind, TypedUuidTag};

// First, define a type that represents the kind of UUID this is.
enum MyKind {}

impl TypedUuidKind for MyKind {
    fn tag() -> TypedUuidTag {
        TypedUuidTag::new("my_kind")
    }
}

// Now, a UUID can be created with this kind.
let uuid: TypedUuid<MyKind> = "dffc3068-1cd6-47d5-b2f3-636b41b07084".parse().unwrap();

// The Display (and therefore ToString) impls still show the same value.
assert_eq!(uuid.to_string(), "dffc3068-1cd6-47d5-b2f3-636b41b07084");

// The Debug impl will show the tag as well.
assert_eq!(format!("{:?}", uuid), "dffc3068-1cd6-47d5-b2f3-636b41b07084 (my_kind)");
```

## Implementations

In general, `TypedUuid` uses the same wire and serialization formats as `Uuid`. This means
that data on the wire does not change; `TypedUuid` is intended to be helpful within Rust code,
not across serialization boundaries.

- The `Display` and `FromStr` impls are forwarded to the underlying `Uuid`.
- If the `serde` feature is enabled, `TypedUuid` will serialize and deserialize using the same
  format as `Uuid`.
- If the `schemars08` feature is enabled, `TypedUuid` will implement `JsonSchema` if the
  corresponding `TypedUuidKind` implements `JsonSchema`.

To abstract over typed and untyped UUIDs, the `GenericUuid` trait is provided. This trait also
permits conversions between typed and untyped UUIDs.

## Dependencies

- The only required dependency is the `uuid` crate. Optional features may add further
  dependencies.

## Features

- `default`: Enables default features in the uuid crate.
- `std`: Enables the use of the standard library. *Enabled by default.*
- `serde`: Enables serialization and deserialization support via Serde. *Not enabled by
  default.*
- `v4`: Enables the `new_v4` method for generating UUIDs. *Not enabled by default.*
- `schemars08`: Enables support for generating JSON schemas via schemars 0.8. *Not enabled by
  default.*

## Minimum supported Rust version (MSRV)

The MSRV of this crate is **Rust 1.60.** In general, this crate will follow the MSRV of the
underlying `uuid` crate.

## License

This project is available under the terms of either the [Apache 2.0 license](LICENSE-APACHE) or the [MIT
license](LICENSE-MIT).

<!--
README.md is generated from README.tpl by cargo readme. To regenerate:

cargo install cargo-readme
./scripts/regenerate-readmes.sh
-->
