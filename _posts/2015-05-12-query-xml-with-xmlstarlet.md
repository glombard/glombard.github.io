---
layout: post
title: Using XMLStarlet to query XML docs on the command-line
tags: scripting
comments: true
---

If you're a developer or DevOps-type guy, then I bet you like doing things on the command-line and/or in batch files / shell scripts.

This year I've discovered two amazing tools for querying XML and JSON documents from the command-line: [XMLStarlet](http://xmlstar.sourceforge.net/) for querying XML and [jq](http://stedolan.github.io/jq/) for querying JSON from the command-line. Both tools are available on Mac OS X, Windows and Linux. Right now I want to focus on XMLStarlet.

XMLStarlet lets you make XPath style queries and transformations right from the command-line.

This is very handy to query Web APIs or local XML files from scripts.

For example, I often want to parse the results out of [checkstyle](http://checkstyle.sourceforge.net/), [pmd](http://pmd.sourceforge.net/), [findbugs](http://findbugs.sourceforge.net/) or [junit](http://help.catchsoftware.com/display/ET/JUnit+Format) XML report files in an Android project.

In this post I'll show how to use `xmlstarlet` to query the checkstyle XML report output for an Android project.

**Installing xmlstarlet:**

On Mac OS X, install it with [Homebrew](http://brew.sh/):

    brew install xmlstarlet

On Windows, [Chocolatey](https://chocolatey.org/packages?q=xmlstarlet) will probably work for you:

    choco install xmlstarlet

**XMLStarlet select basics:**

Do this first: look at the `sel` command's help (i.e. select data using XPath):

    $ xmlstarlet sel --help

Select command help output:

    XMLStarlet Toolkit: Select from XML document(s)
    Usage: xmlstarlet sel <global-options> {<template>} [ <xml-file> ... ]
    where
      <global-options> - global options for selecting
      <xml-file> - input XML document file name/uri (stdin is used if missing)
      <template> - template for querying XML document with following syntax:

    <global-options> are:
      -Q or --quiet             - do not write anything to standard output.
      -C or --comp              - display generated XSLT
      -R or --root              - print root element <xsl-select>
      -T or --text              - output is text (default is XML)
      -I or --indent            - indent output
      -D or --xml-decl          - do not omit xml declaration line
      -B or --noblanks          - remove insignificant spaces from XML tree
      -E or --encode <encoding> - output in the given encoding (utf-8, unicode...)
      -N <name>=<value>         - predefine namespaces (name without 'xmlns:')
                                  ex: xsql=urn:oracle-xsql
                                  Multiple -N options are allowed.
      --net                     - allow fetch DTDs or entities over network
      --help                    - display help

    Syntax for templates: -t|--template <options>
    where <options>
      -c or --copy-of <xpath>   - print copy of XPATH expression
      -v or --value-of <xpath>  - print value of XPATH expression
      -o or --output <string>   - output string literal
      -n or --nl                - print new line
      -f or --inp-name          - print input file name (or URL)
      -m or --match <xpath>     - match XPATH expression
      --var <name> <value> --break or
      --var <name>=<value>      - declare a variable (referenced by $name)
      -i or --if <test-xpath>   - check condition <xsl:if test="test-xpath">
      --elif <test-xpath>       - check condition if previous conditions failed
      --else                    - check if previous conditions failed
      -e or --elem <name>       - print out element <xsl:element name="name">
      -a or --attr <name>       - add attribute <xsl:attribute name="name">
      -b or --break             - break nesting
      -s or --sort op xpath     - sort in order (used after -m) where
      op is X:Y:Z,
          X is A - for order="ascending"
          X is D - for order="descending"
          Y is N - for data-type="numeric"
          Y is T - for data-type="text"
          Z is U - for case-order="upper-first"
          Z is L - for case-order="lower-first"
    (rest of output omitted for brevity...)

The most important ones to note are the `-v` (value-of) and `-i` (if) expressions. Also useful is `-o` (literal string output) and `-n` (print newline in the output).

**Parsing checkstyle.xml report to list errors:**

Let's break things down. First you need to find all the `checkstyle.xml` report files for your project. This will depend on your Gradle setup, but for me this works (from the project root):

    $ find . -path '*/build/reports/checkstyle/checkstyle.xml'

This lists all the `checkstyle.xml` reports under each Android module in my project.

The contents of the `checkstyle.xml` file has this syntax:

    <?xml version="1.0" encoding="UTF-8"?>
    <checkstyle version="6.2">
    <file name="/Users/glombard/myproject/app/src/main/java/com/codeblast/android/myapp/MyFile.java">
    </file>
    <file name="/Users/glombard/myproject/app/src/main/java/com/codeblast/android/myapp/OtherFile.java">
    <error line="7" severity="error" message="Wrong order for &apos;android.animation.ValueAnimator&apos; import. Order should be: android, com, junit, net, org, java, javax. Each group should be separated by a single blank line." source="com.puppycrawl.tools.checkstyle.checks.imports.ImportOrderCheck"/>
    </file>
    ... rest omitted...

This means that `MyFile.java` has no errors, but the second entry, `OtherFile.java` has one error with the order of the imports at line 7.

I'd like to parse out the errors from this file.

To make it easier, let's first just list all the files checked, regardless of whether they've got errors or not:

    $ xmlstarlet sel -t -m /checkstyle/file -v @name -n library/build/reports/checkstyle/checkstyle.xml

OK, that gives us a taste for the `-m` (match element) and `-v` (print value-of) commands (we're just printing the `name` attribute of the `<file>` element...)

But we're actually only interested in files containing `<error>` child elements. Let's only list files with errors by first matching on `/checkstyle/file` and then also matching on `error`:

    $ xmlstarlet sel -t -m /checkstyle/file -i error -v @name -n library/build/reports/checkstyle/checkstyle.xml

This just prints out the filenames of the `file` elements that also had `error` elements in them.

Now let's put everything together: we'll use `find` to find all the `checkstyle.xml` files in our project, then we want to use `xargs` to pipe the contents of all those `checkstyle.xml` files to `xmlstarlet` and then query for the errors:

    find . -path '*/build/reports/checkstyle/checkstyle.xml' | xargs xmlstarlet sel -t -m /checkstyle/file -i error -v @name -m error -n -v @line -o ': ' -v @message -n -n

I hope this helps you to see the possibilities of being able to query XML from scripts (CI etc). If so, let me know how. If not, let me know how I can improve this page. :-)

Remember, also check out [jq](http://stedolan.github.io/jq/) for querying JSON files in a similar way.
