# Apply Inverted Pyramid to Debug Messages

When something is important enough to surface to engineers, make sure to communicate
the information efficiently to save engineering time.

One way to accomplish this is by applying the principles of the
[Inverted Pyramid](https://en.wikipedia.org/wiki/Inverted_pyramid_(journalism)).
Surface the most important information first.

Consider the following debug message:
```
1970-01-01 00:00:00.000: Files [abc, bcd, cde, def, efg, fgh, ghi, hij, ijk, jkl, klm, lmn, mno] have been corrupted.
```

This debug message leaves the critical observation, that files have been corrupted, at the
end. Engineers scanning debug messages may be less likely to notice this information.

Now consider the following debug message:
```
1970-01-01 00:00:00.000: The following files have been corrupted: [abc, bcd, cde, def, efg, fgh, ghi, hij, ijk, jkl, klm, lmn, mno].
```

This debug message puts the critical observation at the beginning, increasing the visibility
and discoverability of the critical information.

Consider what information a debug message is trying to convey and seek direct and
efficient ways to convey that information.
