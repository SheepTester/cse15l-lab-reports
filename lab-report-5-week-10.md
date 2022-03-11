---
layout: default
title: Lab report 5
image: courtroom
---

To find the tests with different results, I did use `diff` on the results of
running a bash `for` loop.

```sh
for file in test-files/*.md; do echo "$file $(java -cp lib/*:. MarkdownParse $file)"; done > results.txt
```

```sh
diff --color=always results-mine.txt results-ucsd.txt
```

This produced the following output, which I searched through manually.

```diff
46c46
< test-files/14.md []
---
> test-files/14.md [/foo]
106c106
< test-files/194.md []
---
> test-files/194.md [url]
115c115
< test-files/201.md []
---
> test-files/201.md [baz]
271c271
< test-files/342.md []
---
> test-files/342.md [/foo`]
458,460c458,460
< test-files/510.md []
< test-files/511.md []
< test-files/512.md []
---
> test-files/510.md [/uri]
> test-files/511.md [/uri]
> test-files/512.md [/uri]
467c467
< test-files/519.md []
---
> test-files/519.md [uri1]
473c473
< test-files/524.md []
---
> test-files/524.md [/uri]
527,528c527,528
< test-files/573.md []
< test-files/574.md []
---
> test-files/573.md [/url]
> test-files/574.md [/url]
531c531
< test-files/577.md []
---
> test-files/577.md [train.jpg]
533c533
< test-files/579.md []
---
> test-files/579.md [<url>]
535c535
< test-files/580.md []
---
> test-files/580.md [/url]
```

I shall choose tests 14 and .

## [Test 14](https://github.com/SheepTester-forks/markdown-parse/blob/main/test-files/14.md)

<!-- prettier-ignore -->
```md
\*not emphasized*
\<br/> not a tag
\[not a link](/foo)
\`not code`
1\. not a list
\* not a list
\# not a heading
\[foo]: /url "not a reference"
\&ouml; not a character entity
```

> \*not emphasized\* > \<br/> not a tag
> \[not a link](/foo)
> \`not code`
> 1\. not a list \* not a list
> \# not a heading
> \[foo]: /url "not a reference"
> \&ouml; not a character entity

To describe which implementation is correct, I shall show the actual outputs and
the expected output:

- My implementation: `[]` (correct).
- The provided implementation: `[/foo]` (incorrect).
- The expected output: `[]` because there are no links in the test.

For the [provided
implementation](https://github.com/ucsd-cse15l-w22/markdown-parse), which is not
correct, I shall describe the bug. The bug is because the implementation does
not check for a backslash before the open bracket of the link. Here is the code
that should be fixed ([line
57](https://github.com/ucsd-cse15l-w22/markdown-parse/blob/44a15db11dbadaa0053f1308fa4f9a6336153b31/MarkdownParse.java#L57)).

```java
int nextOpenBracket = markdown.indexOf("[", currentIndex);
```

## [Test 342](https://github.com/SheepTester-forks/markdown-parse/blob/main/test-files/342.md)

<!-- prettier-ignore -->
```md
[not a `link](/foo`)
```

> [not a `link](/foo`)

To describe which implementation is correct, I shall show the actual outputs and
the expected output:

- My implementation: `[]` (correct).
- The provided implementation: `` [/foo`] `` (incorrect).
- The expected output: `[]` because there are no links in the test.

For the [provided
implementation](https://github.com/ucsd-cse15l-w22/markdown-parse), which is not
correct, I shall describe the bug. The bug is because the implementation does
not consider single backticks for preformatted text. Here is the code that
should be fixed ([lines
58–63](https://github.com/ucsd-cse15l-w22/markdown-parse/blob/main/MarkdownParse.java#L58-L63)).

````java
int nextCodeBlock = markdown.indexOf("\n```");
if(nextCodeBlock < nextOpenBracket && nextCodeBlock != -1) {
    int endOfCodeBlock = markdown.indexOf("\n```");
    currentIndex = endOfCodeBlock + 1;
    continue;
}
````
