i18n
====

Library for internationalization (i18n)
and conventions for localization (L10n).

PRINCIPLES AND EXAMPLES
-----------------------

The first step in the internationalization process is
to remove static text messages from the source code:

    print( "Hi there!" );

In this code, the string `"Hi there!"` is in English,
and must be extracted to be replaced with a suitable
translation in user's language:

    print( getText( "greeting" ) );

The key is a unique identifier for the message.
Like a variable name, it should be explicit,
but it is not intended for end-users,
only for developers and translators.

The message that you extracted from the code
shall be filed with the key, in a spreadsheet,
a database, or a simple text file:

    // en.txt
    greeting: "Hi there!"

Translators can then use the messages in the source language
as the basis for translations in a target language.
Each translation can be saved in a separate text file,
named after the [language tag][] of the target language:

    // fr.txt
    greeting: "Salut toi !"

As seen in the above example, only the message is translated,
the key which identifies the message must be left untouched.

Some difficulty arises when the message is built from the
concatenation of separate parts with variable values:

    print( "Hi " + getFirstName() + "!" );

Since the order of grammatical structures changes depending on the language,
it is not enough in this case to translate the fixed parts of the message,
`"Hi "` and `"!"`; the message must be translated as a whole:

    print( getText( "greeting", getFirstName ) );

In the message string, a placeholder is included to mark the position
of the variable value. Although numbers can be used in these placeholders,
`{0}`, `{1}`, etc.,  names are more explicit:

    // en.txt
    greeting: "Hi {firstName}!"

    // fr.txt
    greeting: "Salut {firstName} !"

Each time a parameter is found in a message string, the function provided
as second parameter of `getText()` is called to retrieve the value, with the
name of the parameter as argument:

    getFirstName( "firstName" );

Unlike in the example above, where the parameter has no use,
the next example shows how A single function can return values
for several parameters to be replaced within the same message:

    print( getText( "greeting", getUserInfo ) );

    // en.txt
    greeting: "Hello {firstName} {LastName}!"

    // fr.txt
    greeting: "Bonjour {firstName} {LastName} !"

The translation of the above message results in two calls to `getUserInfo()`:

    getUserInfo( "firstName" );
    getUserInfo( "lastName" );

CONVENTIONS FOR LOCALIZATION (L10N)
-----------------------------------

TODO: start with simple example:
key -> value

The key name should be in English. It should be only ASCII a-z, A-Z,
and separators '.' '-' '\_'.

The start and end separators of parameters can be customized.

In some implementations, regular expressions may be used instead
to specify the start/end separators and the range of characters
allowed for the parameter name.

TODO: add examples with different separators:
- same start and end: #
- different start and end, single character { }
- different start and end, two characters {{ }}

Add examples of advanced use cases, with separate voidable blocks:
- plural
- agreement with genre
- parameters not found, not replaced,
  and end of unreplaced parameters correctly used as start of next one

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

    Function getValue(String name) -> String

TODO: add example 3, the value of the parameter is a template itself

The name parameter identifies the parameter to replace.

TODO: show successive calls in an example with recursion

REFERENCES
----------

* [RFC-4646 - Tags for Identifying Languages][language tag]
[language tag]: https://tools.ietf.org/html/rfc4646


