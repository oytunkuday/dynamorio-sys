# simple_parse

[![crates.io](https://img.shields.io/crates/v/simple_parse.svg)](https://crates.io/crates/simple_parse)
[![mio](https://docs.rs/simple_parse/badge.svg)](https://docs.rs/simple_parse/)
![Lines of Code](https://tokei.rs/b1/github/elast0ny/simple_parse)

A declarative converter for Rust type to and from binary.

It provides basic implementations for most [standard Rust types](https://docs.rs/simple_parse/latest/simple_parse/trait.SpReadRawMut.html#foreign-impls) and also provides a derive macro to automatically implement the trait on your own types !

For bit level control or more advanced features, take a look at [deku](https://github.com/sharksforarms/deku).

## Usage

```Rust
use ::simple_parse::{SpRead, SpWrite};

#[derive(SpRead, SpWrite)]
pub struct SomeStruct {
    pub some_field: u8,
    #[sp(endian="big")]
    pub items: Vec<u32>,
}

let mut cursor: &[u8] = &[
    1,                      // some_field
    2,0,0,0,0,0,0,0,        // items.len()
    0xDE,0xAD,0xBE,0xEF,    // items[0]
    0xBA,0xDC,0x0F,0xFE     // items[1]
];

// Decode bytes into a struct
let mut my_struct = SomeStruct::from_reader(&mut cursor)?;

/// Modify the struct
my_struct.items.push(0xFFFFFFFF);

/// Encode modified struct into bytes
let mut dst_buf: Vec<u8> = Vec::new();
my_struct.to_writer(&mut dst_buf)?;
//dst_buf == [1,3,0,0,0,0,0,0,0,0xDE,0xAD,0xBE,0xEF,0xBA,0xDC,0x0F,0xFE,0xFF,0xFF,0xFF,0xFF]
```

For complete examples see : [examples](examples/)


