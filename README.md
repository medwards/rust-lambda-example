# Rust AWS Lambda Example

This is a working example of running a Rust Lambda on AWS based on the
[README.md](https://github.com/awslabs/aws-lambda-rust-runtime/blob/05190efea642243b62ec277a0f2bda9e741d449b/README.md)
of [aws-lambda-rust](https://github.com/awslabs/aws-lambda-rust-runtime).

There are several steps that aren't described or have changed and some
easy quality of life improvements so I'm documenting my example.

## Using Lambda docker to run but not build

You can run your lambda locally out of your `target` folder with the
right configuration.

In `Cargo.toml` you will need to override the name of the final binary:
```
[[bin]]
name = "bootstrap"
path = "src/main.rs"
```

From the project root you can run the lambda:  
`docker run --rm -v \`pwd\`/target/x86_64-unknown-linux-musl/release/:/var/task:ro,delegated lambci/lambda:provided handler '{"firstName": "world"}'`

> Note: `handler '{"json": "event"}'` is required here, the container
> seems to accept input on stdin

## Setting up `x86_64-unknown-linux-musl` (or `'GLIBC_2.18' not found`)

You will definitely need this to use `docker` for testing and I haven't
gotten my system default target to work in AWS so likely this is a
requirement.

1. `rustup target add x86_64-unknown-linux-musl`
2. Make sure you have `musl-gcc`. On Ubuntu `sudo apt install musl-tools`

If you don't want `musl` to be the default target then you'll need to call
`cargo build --release --target x86_64-unknown-linux-gnu`. If you're
comfortable with making it the default you can add a `.cargo/config` file
to make it the default build target.
