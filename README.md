An answer to https://github.com/BurntSushi/fst/issues/160#issuecomment-3490618999
since Andrew Gallant asked for it (after blocking me, ensuring that my answer can't be posted in his issue tracker):

> [@ssvb](https://github.com/ssvb) what are you hoping to achieve here? I find _your_ statement strange. Notice what I said:
> 
> > There may indeed be bugs there.

"may" is just not the right choice of word. At least one bug is definitely there. And that's been known since 2017: https://github.com/BurntSushi/fst/issues/38

Also see https://github.com/Automattic/harper/issues/138#issuecomment-2395298888 for a workaround that people seem to be using (but I haven't tested it myself yet). The `fst-bin` command line tool can probably also take it into use as a quick solution.

> > I personally do not have any time in the short term to look into those bugs. The Levenshtein code was something I whipped together several years ago, and it has not seen much love since then.

Just for the record. I'm not urging you to do anything. And I don't blame you. I'm just trying to confirm that we both realistically understand the current state of affairs.

> I do [see one Unicode test](https://github.com/BurntSushi/fst/blob/5907b4739793b3d5d7061eaa3f85274e09769d6a/tests/test.rs#L45).

Thanks for a constructive feedback. Based on what I can see there, this is just a single test, which certainly doesn't provide enough coverage, and the chosen word even does not share a common prefix/suffix with the other words from the set. Moreover, it doesn't represent real world UTF-8 data, which tends to have many multi-byte UTF-8 characters used back-to-back.

Here's the UTF-8 counterpart of `levenshtein_simple` with Greek words:
```
#[cfg(feature = "levenshtein")]
#[test]
fn levenshtein_simple_unicode() {
    let set = fst_set(vec!["Ἄγγελος", "Ἄγγελοι", "banana"]);
    let q = Levenshtein::new("Ἄγγελοα", 1).unwrap();
    let vs = set.search(&q).into_stream().into_byte_keys();
    assert_eq!(vs, vec!["Ἄγγελος".as_bytes(), "Ἄγγελοι".as_bytes()]);
}
```
And it fails `cargo test --all-features`.

> Folks are welcome to take a dive into the Levenshtein automata code and see if they can fix it. PRs are welcome.

Do you mean that a PR for fixing strictly this bug and nothing else is welcome? Because PRs generally tend to be left ignored (and again, I'm not blaming you for that, just trying to confirm the current state of affairs): https://github.com/BurntSushi/fst/pulls
