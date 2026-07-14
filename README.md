This is a small Rust library for injecting the `.note.package` ELF section into programs built with Rust. The primary target is making such programs legible to SBOM-generating software like [syft](https://github.com/anchore/syft). This library only has any effect on Linux at this time.

The `.note.package` section is a [Fedora Project](https://fedoraproject.org/wiki/Changes/Package_information_on_ELF_objects#New_system:_.note.package)-originated feature for annotating software.

## Usage

Add the project to the `[build-dependencies]` section of `Cargo.toml`:

```toml
[build-dependencies]
rust-executable-metadata = "*"
```

Then add a `build.rs` to your project with the following contents:


```rust
fn main() {
    rust_executable_metadata::PackageMetadataBuilder::new_from_cargo("your-cpe-vendor-name")
            .expect("Should be able to parse cargo environment variables")
            .maintainer("you@example.com")
            .copyright("2026 YOUR CO")
            .build()
            .unwrap()
            .write_linker_script_and_inject_argument();
}
```

If you build this on a Linux system, you can read the associated metadata with

```sh
readelf -p .note.package target/release/your-binary
```

It should also be legible to SBOM tools.
