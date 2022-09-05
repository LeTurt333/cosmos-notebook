# External Dependencies

The Rust crate system is awesome. It allows us to easily bring in publically available code that others have written, so we don't have to re-invent the wheel (or in some cases, the steam engine).

However, when using external crates in your CosmWasm smart contracts you may find yourself face-to-face with some nasty errors

Any time I come across one of these, I'll update this page and if I have found a solution/explanation I'll provide that as well


# Wasm-Bindgen

Wasm-Bindgen is a rust crate for working with Rust <> JS. If you want to read more about it here's the [link](https://rustwasm.github.io/wasm-bindgen/)

While I've found Wasm-Bindgen to be very useful when writing front-end Rust (and I'm sure many other things as well), in CosmWasm smart contracts it's *no-bueno*

If you'd like to read more about the issues this can cause, [Github issue #1143](https://github.com/CosmWasm/cosmwasm/issues/1143)

TLDR: You need to make sure that none of the dependencies you're using in your contract have any dependency on Wasm-Bindgen, or else you will not be able to upload your contract to a blockchain using the **CosmWasm VM**

I came across this issue when using the `Chrono` crate in a contract. `Chrono` includes multiple features that depend on the `Wasm-Bindgen` crate. However the features that I needed did not.

To fix this, I added Chrono as a dependency, set `default_features = false`, then cherry-picked the features I wanted to use

If you're going to use Chrono, or any crate that has dependencies on Wasm-Bindgen, you'll need to make sure you **disable** those features before adding them to your contract. Good luck!





