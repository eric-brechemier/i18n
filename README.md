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

Each language is identified by a [language tag][] and is associated with
a set of key/value pairs for the messages translated in this language.
A language tag is made of one or several subtags, separated with '-'.

The case of language tag is not relevant for comparison; language tags
should be put in lower-case before comparison. Also, the '\_' character
which appears sometimes as subtag separator must be replaced with '-'.

Each key is unique in a given language. The same key refers to different
translations of the same message in different languages.

The key name should be in English; it shall not be translated.
It should contain only ASCII characters in the range a-z, A-Z, 0-9;
the character '.' may be used to group keys in categories and subcategories.

The name of the key, as well as the name of categories and subcategories,
may be made of several words; the first word should start with a lower-case
letter, and following words should be capitalized, following the camel-case
convention.

A value is a text string made of [Unicode][] characters. A value may be
a fixed message, or a message template with named parameters to replace
with dynamic values to produce a message.

Parameters are delineated from surrounding text by a start and end separator.
The start and end separator may be identical or different, and may span
one or more characters. The name of the parameter is found directly between
the start and end separators, without additional whitespace. The name of
a parameter follows the same conventions as the name of a key.

TODO: add examples with different separators:
- same start and end: #
- different start and end, single character { }
- different start and end, two characters {{ }}

Names of parameters are not unique: the same name may appear in different
message templates, and even multiple times within a single message template.

Parameter values are provided by the application; each parameter value is
a string, or a null value to represent a missing value: no value is provided
for this parameter.

When a string value has been provided, the parameter, from the start separator
to the end separator, is replaced with the value, which may be empty.

If no value has been provided for a parameter but a key with the same name
as the parameter is found, the value of the key is computed and used as
replacement for the parameter.

When computing the value of a key for inclusion in another message template,
the same parameter values provided by the application are used in both message
templates.

When no value has been provided and no key of the same name is found,
not only the parameter, but the whole message template is replaced
with an empty string.

These voidable blocks provide a mechanism for advanced use cases where
the localized message takes different forms depending on the number and
gender of a parameter value.

TODO: Add examples of advanced use cases, with separate voidable blocks:
- plural
- agreement with genre

In order to preserve the parameter unreplaced in the computed template
message, the application may return a default value instead of null.
This default value is the original text of the parameter placeholder,
from start delimiter to end delimiter, provided to the application by the API.
When the default value is returned, the parameter is left unchanged, and the
processing of the template value goes on as if no parameter had been found
in current position, skipping just the first character of the start delimiter.

Thanks to the above behavior, no escaping is usually needed when a start
delimiter happens to be included in the regular text of a localized message.

TODO: add example of parameters not found,
      with end of unreplaced parameters correctly used as start of next one

APPLICATION PROGRAMMING INTERFACE FOR INTERNATIONALIZATION (I18N API)
---------------------------------------------------------------------

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

    Function getValue(String name, String defaultValue) -> String

TODO: add example 3, the value of the parameter is a template itself

The name parameter identifies the parameter to replace.

TODO: show successive calls in an example with recursion

REFERENCES
----------

* [RFC-5646 - Tags for Identifying Languages][language tag]
[language tag]: https://tools.ietf.org/html/rfc5646

* [The Unicode Consortium][Unicode]
[Unicode]: http://unicode.org/

