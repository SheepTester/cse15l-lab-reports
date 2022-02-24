---
layout: default
title: Week 8 lab report
image: geisel
---

A link to [my markdown-parse
repository](https://github.com/SheepTester-forks/markdown-parse) and a link to
the [one I reviewed](https://github.com/ericwpei/markdown-parse).

## Snippet 1

I shall decide on what it should produce by using the VScode preview.

- `` `google.com``
- `google.com`
- `ucsd.edu`

I shall show the code in `MarkdownParseTest.java` for how I turned it into a
test.

```java
@Test
public void testSnippet1() throws IOException {
    String markdown = Files.readString(Path.of("snippet1.md"));
    assertEquals(List.of(""), MarkdownParse.getLinks(markdown));
}
```

For my implementation, the corresponding output when running the tests. Since it
didn't pass, I shall show the specific part of the JUnit output that shows the
test failure.

```java
1) testSnippet1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet1(MarkdownParseTest.java:135)
```

For the implementation I reviewed, the corresponding output when running the
tests. Since it didn't pass, I shall show the specific part of the JUnit output
that shows the test failure.

```java
2) testSnippet1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com, ucsd.edu]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet1(MarkdownParseTest.java:168)
```

I do not think there is a small (<10 lines) code change that will make my
program work for snippet 1 and all related cases that use inline code with
backticks. It would be a more involved change because while I could just find
the next backtick whenever I encounter a backtick, it could be an inline code
with two backticks, so I would have to check for that. Also, if the backtick
isn't closed properly, the VS Code markdown preview probably has strange rules
for whether newlines are allowed between backticks. It might also encounter code
blocks and escaped backticks, which would require more checks.

Here's some code that makes snippet 1 pass:

After `if (nextOpenBracket == -1) { break; }`,

```java
int nextBacktick = markdown.indexOf("`", currentIndex);
if (nextBacktick != -1 && nextBacktick < nextOpenBracket) {
    int nextNextBacktick = markdown.indexOf("`", nextBacktick + 1);
    // Ensure that there is no paragraph break in between the
    // backticks
    if (nextNextBacktick != -1
            && !markdown.substring(nextBacktick, nextNextBacktick).contains("\n\n")) {
        // Skip to the next backtick
        currentIndex = nextNextBacktick + 1;
        continue;
    }
    // Otherwise, the file does not have a matching backtick, so can
    // ignore this backtick.
}
```

After `int nextCloseBracket = markdown.indexOf("]", nextOpenBracket);`,

```java
if (nextBacktick != -1 && nextBacktick < nextCloseBracket) {
    // The backtick at this point is between [ and ], so we have to
    // skip to the next backtick before looking for the actual
    // closing ]
    int nextNextBacktick = markdown.indexOf("`", nextBacktick + 1);
    if (nextNextBacktick != -1) {
        nextCloseBracket = markdown.indexOf("]", nextNextBacktick);
    }
}
```

## Snippet 2

I shall decide on what it should produce by using the VScode preview.

- `a.com`
- `a.com(())`
- `example.com`

I shall show the code in `MarkdownParseTest.java` for how I turned it into a
test.

```java
@Test
public void testSnippet2() throws IOException {
    String markdown = Files.readString(Path.of("snippet2.md"));
    assertEquals(List.of(""), MarkdownParse.getLinks(markdown));
}
```

For my implementation, the corresponding output when running the tests. Since it
didn't pass, I shall show the specific part of the JUnit output that shows the
test failure.

```java
2) testSnippet2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com(())]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet2(MarkdownParseTest.java:141)
```

For the implementation I reviewed, the corresponding output when running the
tests. Since it didn't pass, I shall show the specific part of the JUnit output
that shows the test failure.

```java
3) testSnippet2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, example.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet2(MarkdownParseTest.java:174)
```

I do not think there is a small (<10 lines) code change that will make my
program work for snippet 2 and all related cases that nest parentheses,
brackets, and escaped brackets because I would have to check for an odd number
of backslashes before the bracket, I think. However, it only takes three lines to make the program work for snippet 2.

After `int nextCloseBracket = markdown.indexOf("]", nextOpenBracket);`,

```java
while (nextCloseBracket != -1 && markdown.charAt(nextCloseBracket - 1) == '\\') {
    nextCloseBracket = markdown.indexOf("]", nextCloseBracket + 1);
}
```

## Snippet 3

I shall decide on what it should produce by using the VScode preview.

- `https://www.twitter.com`
- `https://ucsd-cse15l-w22.github.io/`
- `https://cse.ucsd.edu/`

I shall show the code in `MarkdownParseTest.java` for how I turned it into a
test.

```java
@Test
public void testSnippet3() throws IOException {
    String markdown = Files.readString(Path.of("snippet3.md"));
    assertEquals(List.of(""), MarkdownParse.getLinks(markdown));
}
```

For my implementation, the corresponding output when running the tests. Since it
didn't pass, I shall show the specific part of the JUnit output that shows the
test failure.

```java
3) testSnippet3(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://www.twitter.com, https://ucsd-cse15l-w22.github.io/, https://cse.ucsd.edu/]> but was:<[https://www.twitter.com, https://ucsd-cse15l-w22.github.io/]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet3(MarkdownParseTest.java:149)
```

For the implementation I reviewed, the corresponding output when running the
tests. Since it passed, I shall say so.

It passed.

I do not think there is a small (<10 lines) code change that will make my
program work for snippet 3 and all related cases that have newlines in brackets
and parentheses because I think my code already does handle these cases.
However, I counted `https://www.twitter.com` as a URL because markdown renderers
tend to detect and link-ify text that looks like a URL in plain text, and
detecting plain URLs using the knowledge we are expected to have would likely
require much more than 10 lines of code.
