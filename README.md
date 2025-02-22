# godot_tokio

This was made to prevent re-typing out the boilerplate for creating a tokio runtime godot object.

## Example Use snippets

You can simply create an engine singleton that makes the Tokio runtime accessable by all godot objects.

```rs
#[gdextension]
unsafe impl ExtensionLibrary for Metaphy {
    fn on_level_init(level: InitLevel) {
        match level {
            InitLevel::Scene => {
                Engine::singleton().register_singleton(AsyncRuntime::SINGLETON, &AsyncRuntime::new_alloc());
            }
            _ => (),
        }
    }

    fn on_level_deinit(level: InitLevel) {
        match level {
            InitLevel::Scene => {
                let mut engine = Engine::singleton();

                if let Some(async_singleton) = engine.get_singleton(AsyncRuntime::SINGLETON) {
                    engine.unregister_singleton(AsyncRuntime::SINGLETON);
                    async_singleton.free();
                } else {
                    godot_warn!(
                        "Failed to find & free singleton -> {}",
                        AsyncRuntime::SINGLETON
                    );
                }
            }
            _ => (),
        }
    }
}
```

Then you can access the runtime/singleton like other non-builtin engine singletons.

```rs
let singleton_access = AsyncRuntime::singleton(); // returns an Option<Gd<AsyncRuntime>> if you need this.
let runtime_access = AsyncRuntime::runtime(); // returns an Option<Rc<Runtime>> if you want to get to the point.
```
