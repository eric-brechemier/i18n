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

    print( getText( "Greeting" ) );

The key is a unique identifier for the message.
Like a variable name, it should be explicit,
but it is not intended for end-users,
only for developers and translators.

The message that you extracted from the code
shall be filed with the key, in a spreadsheet,
a database, or a simple text file:

    // en.txt
    Greeting: "Hi there!"

Translators can then use the messages in the source language
as the basis for translations in a target language.
Each translation can be saved in a separate text file,
named after the [language tag][] of the target language:

    // fr.txt
    Greeting: "Salut toi !"

As seen in the above example, only the message is translated,
the key which identifies the message must be left untouched.

Some difficulty arises when the message is built from the
concatenation of separate parts with variable values:

    print( "Hi " + getFirstName() + "!" );

Since the order of grammatical structures changes depending on the language,
it is not enough in this case to translate the fixed parts of the message,
`"Hi "` and `"!"`; the message must be translated as a whole:

    print( getText( "Greeting", getFirstName ) );

In the message string, a placeholder is included to mark the position
of the variable value. Although numbers can be used in these placeholders,
`{0}`, `{1}`, etc.,  names are more explicit:

    // en.txt
    Greeting: "Hi {firstName}!"

    // fr.txt
    Greeting: "Salut {firstName} !"

Each time a parameter is found in a message string, the function provided
as second parameter of `getText()` is called to retrieve the value, with the
name of the parameter as argument:

    getFirstName( "firstName" );

Unlike in the example above, where the parameter has no use,
the next example shows how A single function can return values
for several parameters to be replaced within the same message:

    print( getText( "Greeting", getUserInfo ) );

    // en.txt
    Greeting: "Hello {firstName} {lastName}!"

    // fr.txt
    Greeting: "Bonjour {firstName} {lastName} !"

The translation of the above message results in two calls to `getUserInfo()`:

    getUserInfo( "firstName" );
    getUserInfo( "lastName" );

CONVENTIONS FOR LOCALIZATION (L10N)
-----------------------------------

Each language is identified by a [language tag][] and is associated with
a set of key/value pairs for the messages translated in this language.
A language tag is made of one or several subtags, separated with '-'.

    'en'
    'en-GB'
    'en-US'

The case of a language tag is not relevant for comparison; language tags
should be put in lower-case before comparison. Also, the '\_' character
which appears sometimes as subtag separator must be replaced with '-'.

    'EN' -> 'en'
    'en-GB' -> 'en-gb'
    'en-US' -> 'en-us'
    'en_US' -> 'en-us'

Each key is unique in a given language. The same key refers to different
translations of the same message in different languages.

    // en.txt
    Greetings: "Hi"

    // fr.txt
    Greetings: "Bonjour"

The key name should be in English; it shall not be translated.
It should contain only ASCII characters in the range a-z, A-Z, 0-9;
the character '.' may be used to group keys in categories and subcategories.

    Name
    User.Name
    Label.User.Name

The name of the key, as well as the name of categories and subcategories,
may be made of several words; the first word should start with an upper-case
letter, and following words should be capitalized, following the camel-case
convention.

    FirstName
    User.FirstName
    Label.User.FirstName
    Placeholder.User.FirstName
    Invalid.User.FirstName

A value is a text string made of [Unicode][] characters. A value may be
a fixed message, or a message template with named parameters to replace
with dynamic values to produce a message.

    "Fixed message in English"
    "Message fixe en français"

    "Template message in English with parameters: {param1} and {param2}"
    "Patron de message en français avec des paramètres : {param1} et {param2}"

Parameters are delineated from surrounding text by a start and end separator.
The start and end separator may be identical or different, and may span
one or more characters. The name of the parameter is found directly between
the start and end separators, without additional whitespace.

    "Parens: (parameter)"
    "Double parens: ((parameter))"
    "Braces: {parameter}"
    "Double braces: {{parameter}}"
    "Dollar and braces: ${parameter}"
    "Number/Sharp sign: #parameter#"

The name of a parameter follows the same conventions as the name of a key,
except for the first letter of each word, which may be either in upper case
or in lower case: names starting with upper-case letters refer to keys which
identify separate messages or message templates, while names starting with
lower-case letters refer to dynamic values provided by the application.

    "{Greetings}"
    "{Label.User.FirstName}{user.firstName}"

Names of parameters are not unique: the same name may appear in different
message templates, and even multiple times within a single message template.

    "Hi {user.firstName}!"
    "Sign out if you are not {user.firstName}."

Each parameter value is either a string, which may be an empty string, or a
null value which means that no dynamic value is available for this parameter.

    getValue( "user.firstName" ) -> "John"
    getValue( "user.middleName" ) -> NULL
    getValue( "user.lastName" ) -> "Doe"

When a string value has been provided, the parameter, from the start separator
to the end separator, is replaced with the value:

      "Name: {firstName} {lastName}"
    + getValue( "firstName" ) -> "John"
    + getValue( "lastName" ) -> "Doe"
    -----------------------------------
    = "Name: John Doe"

If no value has been provided for a parameter but a key with the same name
as the parameter is found, the value of the key is computed and used as
replacement for the parameter.

When computing the value of a key for inclusion in another message template,
the same parameter values provided by the application are used in both message
templates.

      "Name: {FullName}"
    + getValue( "FullName" ) -> NULL
    +   FullName: "{firstName} {lastName}"
      + getValue( "firstName" ) -> "John"
      + getValue( "lastName" ) -> "Doe"
      ------------------------------------
      = "John Doe"
    --------------------------------------
    = "Name: John Doe"

When no value has been provided and no key of the same name is found,
the parameter is left unreplaced, including start and end delimiter.

      "See {BIB-2011} for reference."
    + getValue( "BIB-2011" ) -> NULL
    ------------------------------------------------------
    = "See {BIB-2011} for reference."

Thanks to the above behavior, no escaping is usually needed to avoid false
positives in the detection of parameters in regular text.

In advanced use cases, the localized message takes different forms depending
on the number and gender of a parameter value. By responding with an empty
string when requested for the value associated with a key, the application
can silence templates or parts of templates which are not relevant in the
current situation. This mechanism allows to define conditional blocks which
are only included in the final message when the application responds with
a NULL value, and are omitted when it responds with an empty string.

      "{French.Singular}{French.Plural}"
    + getValue( "French.Singular" ) -> ""
    + getValue( "French.Plural" ) -> NULL
    +   French.Plural: "{French.Female.Plural}{French.Other.Plural}"
      + getValue( "French.Female.Plural" ) -> ""
      + getValue( "French.Other.Plural" ) -> NULL
      +   French.Other.Plural: "{FirstName.List} sont français"
        + getValue( "FirstName.List" ) -> NULL
        +   FirstName.List: "{firstName}{FirstName.NotLast}{FirstName.Last}"
          + getValue( "firstName" ) -> "Jeanne"
          + getValue( "FirstName.NotLast" ) -> ""
          + getValue( "FirstName.Last" ) -> NULL
          +   FirstName.Last: " et {firstName}"
            + getValue( "firstName" ) -> "Pierre"
            ------------------------------------
            = " et Pierre"
          ---------------------------------------------------------------
          = "Jeanne et Pierre"
        -------------------------------------------------------------------
        = "Jeanne et Pierre sont français"
      ---------------------------------------------------------------------
      = "Jeanne et Pierre sont français"
    -----------------------------------------------------------------------
    = "Jeanne et Pierre sont français"

As seen in the above example, the same mechanism can be used to format
lists of values: `getValue( "firstName" )` returns a different value
in the list `[ "Jeanne", "Pierre" ]` in each call, and returns either
a null value or an empty string to include or omit templates for the
parts of the list: `getValue( "FirstName.NotLast" )` returns the empty
string to skip the middle part of the template because `"Pierre"` is the
last item in the list, while `getValue( "FirstName.Last" )` returns a null
value to let the template `"FirstName.Last"` be rendered on the last item.

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

