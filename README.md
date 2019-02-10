# crossbeam-channel-select

An experimental `dynamic_select!` macro implemented as a procedural macro that supports selecting
over iterators of `Sender`s and `Receiver`s;

### Example:

```rust
#![feature(proc_macro_hygiene)]

use crossbeam_channel as channel;
use crossbeam_channel_select::dynamic_select;

fn main() {
    let (s0, r0) = channel::unbounded();

    let (s1, r1) = channel::unbounded();
    let (s2, r2) = channel::unbounded();
    let receivers = vec![r1, r2];

    s0.send("test").unwrap();
    s1.send("foo").unwrap();
    s2.send("bar").unwrap();

    for _ in 0..3 {
        dynamic_select! {
            recv(&r0) -> msg => {
                println!("Received {:?} from s0", msg);
            },
            recv_any(receivers) -> msg, i => {
                println!("Received {:?} from receivers[{}]", msg, i);
            }
        }
    }
}
```
