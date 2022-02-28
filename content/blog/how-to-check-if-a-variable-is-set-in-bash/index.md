---
Title: How to check if a variable is set in Bash?
description: How do I know if a variable is set in Bash? 
published: true
comments: true
date: 2022-02-28
tags:
  - bash
---

{{< table_of_contents >}}

## Question

For example, how do I check if the user gave the first parameter to a function?

```bash
function a {
    # if $1 is set ?
}
```

## Answers


### Short Approach

```bash
if [ -z ${var+x} ]; then echo "var is unset"; else echo "var is set to '$var'"; fi
```

### Full explanation

Here's how to test whether a parameter is unset, or empty ("Null") or set with a value:

```txt
+--------------------+----------------------+-----------------+-----------------+
|   Expression       |       parameter      |     parameter   |    parameter    |
|   in script:       |   Set and Not Null   |   Set But Null  |      Unset      |
+--------------------+----------------------+-----------------+-----------------+
| ${parameter:-word} | substitute parameter | substitute word | substitute word |
| ${parameter-word}  | substitute parameter | substitute null | substitute word |
| ${parameter:=word} | substitute parameter | assign word     | assign word     |
| ${parameter=word}  | substitute parameter | substitute null | assign word     |
| ${parameter:?word} | substitute parameter | error, exit     | error, exit     |
| ${parameter?word}  | substitute parameter | substitute null | error, exit     |
| ${parameter:+word} | substitute word      | substitute null | substitute null |
| ${parameter+word}  | substitute word      | substitute word | substitute null |
+--------------------+----------------------+-----------------+-----------------+
```
Source: [POSIX: Parameter Expansion](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_06_02)

> In all cases shown with "substitute", the expression is replaced with the value shown. In all cases shown with "assign", parameter is assigned that value, which also replaces the expression.

To show this in action:

```txt
+--------------------+----------------------+-----------------+-----------------+
|   Expression       |  When FOO="world"    |  When FOO=""    |    unset FOO    |
|   in script:       |  (Set and Not Null)  |  (Set But Null) |     (Unset)     |
+--------------------+----------------------+-----------------+-----------------+
| ${FOO:-hello}      | world                | hello           | hello           |
| ${FOO-hello}       | world                | ""              | hello           |
| ${FOO:=hello}      | world                | FOO=hello       | FOO=hello       |
| ${FOO=hello}       | world                | ""              | FOO=hello       |
| ${FOO:?hello}      | world                | error, exit     | error, exit     |
| ${FOO?hello}       | world                | ""              | error, exit     |
| ${FOO:+hello}      | hello                | ""              | ""              |
| ${FOO+hello}       | hello                | hello           | ""              |
+--------------------+----------------------+-----------------+-----------------+
```

Sources:
* https://stackoverflow.com/questions/3601515/how-to-check-if-a-variable-is-set-in-bash

