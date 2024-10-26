# REGEX (REGULAR EXPRESSION)

Sequence of characters that specifies matching pattern in text.

## Character Class or Set (`[]`)

Matches one character out of set. `gr[ae]y` matches "gray" or "grey".

Use hyphen for range. `[0-9]` matches single digit from "0" to "9". `[0-9a-fxA-FX]` matches hex digit or "x", case-insensitive.

Caret (`^`) after opening `[` negates set. `qu[^o]` matches "qui" in "quiz" but nothing in "quote". Must have at least one character after "qu" to match.

### Shorthand

`\d` matches digit, `\w` matches "word character" (alphanumeric characters, and underscore), and `\s` matches whitespace (includes tab and line break). In modern apps, these include non-English letters and numbers.

### Unicode

If app supports Unicode, use `\uFFFF` or `\x{FFFF}` to insert Unicode character. `\u20AC` or `\x{20AC}` matches euro currency sign.

If app does not support Unicode, use `\xFF` to match character by its hexadecimal index in character set. `\xA9` matches copyright symbol in Latin-1.

### Non-Printable Characters

| Identifier | ASCII | Description     |
| ---------- | ----- | --------------- |
| `\t`       | 0x09  | tab             |
| `\r`       | 0x0D  | carriage return |
| `\n`       | 0x0A  | line feed       |
| `\a`       | 0x07  | bell            |
| `\e`       | 0x1B  | escape          |
| `\f`       | 0x0C  | form feed       |
| `\v`       | 0x0B  | vertical tab    |

Windows text files use "\r\n" to terminate lines, UNIX text files use "\n".

## Dot (`.`) Matches (Almost) Any Character

Dot (`.`) matches character, except line break characters. Short for `[^\n]` (UNIX) or `[^\r\n]` (Windows). Most apps have "dot matches all" or "single line" mode that makes dot match any character, including line breaks.

`gr.y` matches "gray", "grey", "gr%y", etc. Use dot sparingly. Often character class or negated character class is faster and more precise.

## Anchor

Anchor matches position, not character. `^` matches start of string, `$` matches end. Most regex engines have *multi-line* mode that makes `^` match after line break and `$` before. `^m` matches only first "m" in "mom".

`\b` matches at word boundary - position between character that can be matched by `\w` and character that cannot. `\b` also matches at start and/or end of string if first and/or last characters are word characters. `\B` matches at every position where `\b` cannot.

## Alternation (`|`)

Equivalent to *or*. `cat|dog` matches "cat" in "cats and dogs". If applied again, matches "dog". Can add as many alternatives as wanted: `cat|dog|mouse|fish`.

Lowest precedence. `cat|dog food` matches "cat" or "dog food". To match "cat food" or "dog food", need to group alternatives: `(cat|dog) food`.

## Repetition

Question mark (`?`) makes preceding token optional. `colou?r` matches "colour" or "color".

Asterisk or star (`*`) matches preceding token zero or more times. Plus (`+`) matches preceding token once or more. `<[A-Za-z][A-Za-z0-9]*>` matches HTML tag without any attribute. `<[A-Za-z0-9]+>` is easier to write but matches invalid tags like "<1>".

Curly braces (`{}`) specifies amount of repetition. `\b[1-9][0-9]{3}\b` matches number between **1000** and **9999**. `\b[1-9][0-9]{2,4}\b` matches number between **100** and **99999**.

### Greedy vs Lazy

Repetition operators or quantifiers are *greedy*; they expand match as far as they can. `<.+>` matches "\<em>first<\/em>" in "This is a \<em>first<\/em> test".

Place `?` after quantifier to make it *lazy*. `<.+?>` matches "\<em>" in above string.

Even better, `<[^<>]+>` matches HTML tag without regard to attributes. Negated character class is more specific therefore more efficient than dot.

## Grouping and Capturing

Group tokens with parentheses (`()`). Can apply quantifier to group. `Set(Value)?` matches "Set" or "SetValue".

Parentheses create capturing group. Above example has one group. After match, group number one contains nothing if "Set" was matched. It contains "Value" if "SetValue" was matched. How to access group's contents depends on software or programming language. Group zero always contains entire regex match.

Use special syntax `Set(?:Value)?` to group tokens without creating capturing group. More efficient if group's contents aren't needed.

### Backreferences

Use backreference `\1` to match same text matched by capturing group. `([abc])=\1` matches "a=a", "b=b", "c=c", and nothing else. If regex has multiple capturing groups, they are numbered counting their opening parentheses from left to right.

### Named Groups and Backreferences

If many groups, easier to name them. `(?<mygroup>[abc])=\k<mygroup>` is identical to `([abc])=\1`.

## Unicode Properties

`\p{L}` matches single character in given Unicode category. L stands for letter. `\P{L}` matches single character not in given Unicode category.

## Lookaround

Special kind of group. Tokens inside group matched normally, but regex engine makes group give up match and keeps result only. Matches position, just like anchors. Does not expand regex match.

`q(?=u)` matches "q" in "question", but not in "Iraq". This is **positive lookahead**. "u" not part of match. Matches at each position in string before "u".

`q(?!u)` matches "q" in "Iraq" but not in "question". This is **negative lookahead**. Tokens inside lookahead are attempted, match discarded, and result inverted.

Can also look backwards. **Positive lookbehind**: `(?<=a)b` matches "b" in "abc". `(?<!a)b`, **negative lookbehind**, fails to match "abc".

Can use full-fledged regex inside lookahead. Most apps only allow fixed-length expressions in lookbehind.

## Free-Spacing Syntax

Many apps have option labeled *free-spacing* or *ignore whitespace or comments* that makes regex engine ignore unescaped spaces and line breaks, and makes `#` character start comment running until end of line. Can use whitespace to format regex easier to read and maintain.

## Metacharacters

12 characters with special meanings: `\`, `^`, `$`, `.`, `|`, `?`, `*`, `+`, `(`, `)`, `[`, `{`.

To match literally, escape with `\`.
