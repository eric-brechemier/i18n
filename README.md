i18n
====

Library for internationalization (i18n)
and data format for localization (L10n)

I18N API
--------

The first step in the internationalization process
is to remove messages (both fixed messages and
messages built with string concatenation) and call
a function to retrieve localized messages instead.

TODO: add example 1 here, simple use case

The signature for the `getText()` function is:

    Function getText(String key, optional Function getValue) -> String

TODO: add example 2, parameter with a simple value

where the first parameter is a key which identifies the message,
and the optional function `getValue()` allows `getText()` to retrieve values
for parameters to replace in a template string to produce the message.
The `getValue()` function has the following signature:

    Function getValue(String name, optional String[] keys) -> String

TODO: add example 3, the value of the parameter is a template itself

The name parameter identifies the parameter to replace, and the second
parameter is an optional list of key values provided as parameter to
`getText()` in a chain of recursive calls that resulted in the current
call of `getValue()`.

L10N DATA FORMAT
----------------

TODO: start with simple example:
key -> value

The start and end separators of parameters can be customized.

TODO: add examples with different separators:
- same start and end: #
- different start and end, single character { }
- different start and end, two characters {{ }}

Add examples of advanced use cases:
- plural
- agreement with genre

