---
title: "Template Function List"
description: "A list of template functions available in Helm"
weight: 6
---

Helm includes many template functions you can take advantage of in templates.
They are listed here and broken down by the following categories:

* [Cryptographic and Security](#cryptographic-and-security-functions)
* [Date](#date-functions)
* [Dictionaries](#dictionaries-and-dict-functions)
* [Encoding](#encoding-functions)
* [File Path](#file-path-functions)
* [Kubernetes and Chart](#kubernetes-and-chart-functions)
* [Logic and Flow Control](#logic-and-flow-control-functions)
* [Lists](#lists-and-list-functions)
* [Math](#math-functions)
* [Float Math](#float-math-functions)
* [Network](#network-functions)
* [Reflection](#reflection-functions)
* [Regular Expressions](#regular-expressions)
* [Semantic Versions](#semantic-version-functions)
* [String](#string-functions)
* [Type Conversion](#type-conversion-functions)
* [URL](#url-functions)
* [UUID](#uuid-functions)

## Logic and Flow Control Functions

Helm includes numerous logic and control flow functions including [and](#and),
[coalesce](#coalesce), [default](#default), [empty](#empty), [eq](#eq),
[fail](#fail), [ge](#ge), [gt](#gt), [le](#le), [lt](#lt), [ne](#ne),
[not](#not), [or](#or), and [required](#required).

### and

Returns the boolean AND of two or more arguments
(the first empty argument, or the last argument).

```
and .Arg1 .Arg2
```

### or

Returns the boolean OR of two or more arguments
(the first non-empty argument, or the last argument).

```
or .Arg1 .Arg2
```

### not

Returns the boolean negation of its argument.

```
not .Arg
```

### eq

Returns the boolean equality of the arguments (e.g., Arg1 == Arg2).

```
eq .Arg1 .Arg2
```

### ne

Returns the boolean inequality of the arguments (e.g., Arg1 != Arg2)

```
ne .Arg1 .Arg2
```

### lt

Returns a boolean true if the first argument is less than the second. False is
returned otherwise (e.g., Arg1 < Arg2).

```
lt .Arg1 .Arg2
```

### le

Returns a boolean true if the first argument is less than or equal to the
second. False is returned otherwise (e.g., Arg1 <= Arg2).

```
le .Arg1 .Arg2
```

### gt

Returns a boolean true if the first argument is greater than the second. False
is returned otherwise (e.g., Arg1 > Arg2).

```
gt .Arg1 .Arg2
```

### ge

Returns a boolean true if the first argument is greater than or equal to the
second. False is returned otherwise (e.g., Arg1 >= Arg2).

```
ge .Arg1 .Arg2
```

### default

To set a simple default value, use `default`:

```
default "foo" .Bar
```

In the above, if `.Bar` evaluates to a non-empty value, it will be used. But if
it is empty, `foo` will be returned instead.

The definition of "empty" depends on type:

- Numeric: 0
- String: ""
- Lists: `[]`
- Dicts: `{}`
- Boolean: `false`
- And always `nil` (aka null)

For structs, there is no definition of empty, so a struct will never return the
default.

### required

Specify values that must be set with `required`:

```
required "A valid foo is required!" .Bar
```

If `.Bar` is empty or not defined (see [default](#default) on how this is 
evaluated), the template will not render and will return the error message 
supplied instead.

### empty

The `empty` function returns `true` if the given value is considered empty, and
`false` otherwise. The empty values are listed in the `default` section.

```
empty .Foo
```

Note that in Go template conditionals, emptiness is calculated for you. Thus,
you rarely need `if not empty .Foo`. Instead, just use `if .Foo`.

### fail

Unconditionally returns an empty `string` and an `error` with the specified
text. This is useful in scenarios where other conditionals have determined that
template rendering should fail.

```
fail "Please accept the end user license agreement"
```

### coalesce

The `coalesce` function takes a list of values and returns the first non-empty
one.

```
coalesce 0 1 2
```

The above returns `1`.

This function is useful for scanning through multiple variables or values:

```
coalesce .name .parent.name "Matt"
```

The above will first check to see if `.name` is empty. If it is not, it will
return that value. If it _is_ empty, `coalesce` will evaluate `.parent.name` for
emptiness. Finally, if both `.name` and `.parent.name` are empty, it will return
`Matt`.

### ternary

The `ternary` function takes two values, and a test value. If the test value is
true, the first value will be returned. If the test value is empty, the second
value will be returned. This is similar to the ternary operator in C and other programming languages.

#### true test value

```
ternary "foo" "bar" true
```

or

```
true | ternary "foo" "bar"
```

The above returns `"foo"`.

#### false test value

```
ternary "foo" "bar" false
```

or

```
false | ternary "foo" "bar"
```

The above returns `"bar"`.

## String Functions

Helm includes the following string functions: [abbrev](#abbrev),
[abbrevboth](#abbrevboth), [camelcase](#camelcase), [cat](#cat),
[contains](#contains), [hasPrefix](#hasprefix-and-hassuffix),
[hasSuffix](#hasprefix-and-hassuffix), [indent](#indent), [initials](#initials),
[kebabcase](#kebabcase), [lower](#lower), [nindent](#nindent),
[nospace](#nospace), [plural](#plural), [print](#print), [printf](#printf),
[println](#println), [quote](#quote-and-squote),
[randAlpha](#randalphanum-randalpha-randnumeric-and-randascii),
[randAlphaNum](#randalphanum-randalpha-randnumeric-and-randascii),
[randAscii](#randalphanum-randalpha-randnumeric-and-randascii),
[randNumeric](#randalphanum-randalpha-randnumeric-and-randascii),
[repeat](#repeat), [replace](#replace), [shuffle](#shuffle),
[snakecase](#snakecase), [squote](#quote-and-squote), [substr](#substr),
[swapcase](#swapcase), [title](#title), [trim](#trim), [trimAll](#trimall),
[trimPrefix](#trimprefix), [trimSuffix](#trimsuffix), [trunc](#trunc),
[untitle](#untitle), [upper](#upper), [wrap](#wrap), and [wrapWith](#wrapwith).

### print

Returns a string from the combination of its parts.

```
print "Matt has " .Dogs " dogs"
```

Types that are not strings are converted to strings where possible.

Note, when two arguments next to each other are not strings a space is added
between them.

### println

Works the same way as [print](#print) but adds a new line at the end.

### printf

Returns a string based on a formatting string and the arguments to pass to it in
order.

```
printf "%s has %d dogs." .Name .NumberDogs
```

The placeholder to use depends on the type for the argument being passed in.
This includes:

General purpose:

* `%v` the value in a default format
  * when printing dicts, the plus flag (%+v) adds field names
* `%%` a literal percent sign; consumes no value

Boolean:

* `%t` the word true or false

Integer:

* `%b` base 2
* `%c` the character represented by the corresponding Unicode code point
* `%d` base 10
* `%o` base 8
* `%O` base 8 with 0o prefix
* `%q` a single-quoted character literal safely escaped
* `%x` base 16, with lower-case letters for a-f
* `%X` base 16, with upper-case letters for A-F
* `%U` Unicode format: U+1234; same as "U+%04X"

 Floating-point and complex constituents:

* `%b` decimal less scientific notation with exponent a power of two, e.g.
  -123456p-78
* `%e` scientific notation, e.g. -1.234456e+78
* `%E` scientific notation, e.g. -1.234456E+78
* `%f` decimal point but no exponent, e.g. 123.456
* `%F` synonym for %f
* `%g` %e for large exponents, %f otherwise.
* `%G` %E for large exponents, %F otherwise
* `%x` hexadecimal notation (with decimal power of two exponent), e.g.
  -0x1.23abcp+20
* `%X` upper-case hexadecimal notation, e.g. -0X1.23ABCP+20

String and slice of bytes (treated equivalently with these verbs):

* `%s` the uninterpreted bytes of the string or slice
* `%q` a double-quoted string safely escaped
* `%x` base 16, lower-case, two characters per byte
* `%X` base 16, upper-case, two characters per byte

Slice:

* `%p` address of 0th element in base 16 notation, with leading 0x

### trim

The `trim` function removes white space from both sides of a string:

```
trim "   hello    "
```

The above produces `hello`

### trimAll

Removes the given characters from the front and back of a string:

```
trimAll "$" "$5.00"
```

The above returns `5.00` (as a string).

### trimPrefix

Trim just the prefix from a string:

```
trimPrefix "-" "-hello"
```

The above returns `hello`

### trimSuffix

Trim just the suffix from a string:

```
trimSuffix "-" "hello-"
```

The above returns `hello`

### lower

Convert the entire string to lowercase:

```
lower "HELLO"
```

The above returns `hello`

### upper

Convert the entire string to uppercase:

```
upper "hello"
```

The above returns `HELLO`

### title

Convert to title case:

```
title "hello world"
```

The above returns `Hello World`

### untitle

Remove title casing. `untitle "Hello World"` produces `hello world`.

### repeat

Repeat a string multiple times:

```
repeat 3 "hello"
```

The above returns `hellohellohello`

### substr

Get a substring from a string. It takes three parameters:

- start (int)
- end (int)
- string (string)

```
substr 0 5 "hello world"
```

The above returns `hello`

### nospace

Remove all whitespace from a string.

```
nospace "hello w o r l d"
```

The above returns `helloworld`

### trunc

Truncate a string

```
trunc 5 "hello world"
```

The above produces `hello`.

```
trunc -5 "hello world"
```

The above produces `world`.

### abbrev

Truncate a string with ellipses (`...`)

Parameters:

- max length
- the string

```
abbrev 5 "hello world"
```

The above returns `he...`, since it counts the width of the ellipses against the
maximum length.

### abbrevboth

Abbreviate both sides:

```
abbrevboth 5 10 "1234 5678 9123"
```

the above produces `...5678...`

It takes:

- left offset
- max length
- the string

### initials

Given multiple words, take the first letter of each word and combine.

```
initials "First Try"
```

The above returns `FT`

### randAlphaNum, randAlpha, randNumeric, and randAscii

These four functions generate cryptographically secure (uses ```crypto/rand```)
random strings, but with different base character sets:

- `randAlphaNum` uses `0-9a-zA-Z`
- `randAlpha` uses `a-zA-Z`
- `randNumeric` uses `0-9`
- `randAscii` uses all printable ASCII characters

Each of them takes one parameter: the integer length of the string.

```
randNumeric 3
```

The above will produce a random string with three digits.

### wrap

Wrap text at a given column count:

```
wrap 80 $someText
```

The above will wrap the string in `$someText` at 80 columns.

### wrapWith

`wrapWith` works as `wrap`, but lets you specify the string to wrap with.
(`wrap` uses `\n`)

```
wrapWith 5 "\t" "Hello World"
```

The above produces `Hello World` (where the whitespace is an ASCII tab
character)

### contains

Test to see if one string is contained inside of another:

```
contains "cat" "catch"
```

The above returns `true` because `catch` contains `cat`.

### hasPrefix and hasSuffix

The `hasPrefix` and `hasSuffix` functions test whether a string has a given
prefix or suffix:

```
hasPrefix "cat" "catch"
```

The above returns `true` because `catch` has the prefix `cat`.

### quote and squote

These functions wrap a string in double quotes (`quote`) or single quotes
(`squote`).

### cat

The `cat` function concatenates multiple strings together into one, separating
them with spaces:

```
cat "hello" "beautiful" "world"
```

The above produces `hello beautiful world`

### indent

The `indent` function indents every line in a given string to the specified
indent width. This is useful when aligning multi-line strings:

```
indent 4 $lots_of_text
```

The above will indent every line of text by 4 space characters.

### nindent

The `nindent` function is the same as the indent function, but prepends a new
line to the beginning of the string.

```
nindent 4 $lots_of_text
```

The above will indent every line of text by 4 space characters and add a new
line to the beginning.

### replace

Perform simple string replacement.

It takes three arguments:

- string to replace
- string to replace with
- source string

```
"I Am Henry VIII" | replace " " "-"
```

The above will produce `I-Am-Henry-VIII`

### plural

Pluralize a string.

```
len $fish | plural "one anchovy" "many anchovies"
```

In the above, if the length of the string is 1, the first argument will be
printed (`one anchovy`). Otherwise, the second argument will be printed (`many
anchovies`).

The arguments are:

- singular string
- plural string
- length integer

NOTE: Helm does not currently support languages with more complex pluralization
rules. And `0` is considered a plural because the English language treats it as
such (`zero anchovies`).

### snakecase

Convert string from camelCase to snake_case.

```
snakecase "FirstName"
```

This above will produce `first_name`.

### camelcase

Convert string from snake_case to CamelCase

```
camelcase "http_server"
```

This above will produce `HttpServer`.

### kebabcase

Convert string from camelCase to kebab-case.

```
kebabcase "FirstName"
```

This above will produce `first-name`.

### swapcase

Swap the case of a string using a word based algorithm.

Conversion algorithm:

- Upper case character converts to Lower case
- Title case character converts to Lower case
- Lower case character after Whitespace or at start converts to Title case
- Other Lower case character converts to Upper case
- Whitespace is defined by unicode.IsSpace(char)

```
swapcase "This Is A.Test"
```

This above will produce `tHIS iS a.tEST`.

### shuffle

Shuffle a string.

```
shuffle "hello"
```

The above will randomize the letters in `hello`, perhaps producing `oelhl`.

## Type Conversion Functions

The following type conversion functions are provided by Helm:

- `atoi`: Convert a string to an integer.
- `float64`: Convert to a `float64`.
- `int`: Convert to an `int` at the system's width.
- `int64`: Convert to an `int64`.
- `toDecimal`: Convert a unix octal to a `int64`.
- `toString`: Convert to a string.
- `toStrings`: Convert a list, slice, or array to a list of strings.
- `toJson` (`mustToJson`): Convert list, slice, array, dict, or object to JSON.
- `toPrettyJson` (`mustToPrettyJson`): Convert list, slice, array, dict, or
  object to indented JSON.
- `toRawJson` (`mustToRawJson`): Convert list, slice, array, dict, or object to
  JSON with HTML characters unescaped.
- `fromYaml`: Convert a YAML string to an object.
- `fromJson`: Convert a JSON string to an object.
- `fromJsonArray`: Convert a JSON array to a list.
- `toYaml`: Convert list, slice, array, dict, or object to indented yaml, can be used to copy chunks of yaml from any source. This function is equivalent to GoLang yaml.Marshal function, see docs here: https://pkg.go.dev/gopkg.in/yaml.v2#Marshal
- `toToml`: Convert list, slice, array, dict, or object to toml, can be used to copy chunks of toml from any source.
- `fromYamlArray`: Convert a YAML array to a list.

Only `atoi` requires that the input be a specific type. The others will attempt
to convert from any type to the destination type. For example, `int64` can
convert floats to ints, and it can also convert strings to ints.

### toStrings

Given a list-like collection, produce a slice of strings.

```
list 1 2 3 | toStrings
```

The above converts `1` to `"1"`, `2` to `"2"`, and so on, and then returns them
as a list.

### toDecimal

Given a unix octal permission, produce a decimal.

```
"0777" | toDecimal
```

The above converts `0777` to `511` and returns the value as an int64.

### toJson, mustToJson

The `toJson` function encodes an item into a JSON string. If the item cannot be
converted to JSON the function will return an empty string. `mustToJson` will
return an error in case the item cannot be encoded in JSON.

```
toJson .Item
```

The above returns JSON string representation of `.Item`.

### toPrettyJson, mustToPrettyJson

The `toPrettyJson` function encodes an item into a pretty (indented) JSON
string.

```
toPrettyJson .Item
```

The above returns indented JSON string representation of `.Item`.

### toRawJson, mustToRawJson

The `toRawJson` function encodes an item into JSON string with HTML characters
unescaped.

```
toRawJson .Item
```

The above returns unescaped JSON string representation of `.Item`.

### fromYaml

The `fromYaml` function takes a YAML string and returns an object that can be used in templates.

`File at: yamls/person.yaml`
```yaml
name: Bob
age: 25
hobbies:
  - hiking
  - fishing
  - cooking
```
```yaml
{{- $person := .Files.Get "yamls/person.yaml" | fromYaml }}
greeting: |
  Hi, my name is {{ $person.name }} and I am {{ $person.age }} years old.
  My hobbies are {{ range $person.hobbies }}{{ . }} {{ end }}.
```

### fromJson

The `fromJson` function takes a JSON string and returns an object that can be used in templates.

`File at: jsons/person.json`
```json
{
  "name": "Bob",
  "age": 25,
  "hobbies": [
    "hiking",
    "fishing",
    "cooking"
  ]
}
```
```yaml
{{- $person := .Files.Get "jsons/person.json" | fromJson }}
greeting: |
  Hi, my name is {{ $person.name }} and I am {{ $person.age }} years old.
  My hobbies are {{ range $person.hobbies }}{{ . }} {{ end }}.
```


### fromJsonArray

The `fromJsonArray` function takes a JSON Array and returns a list that can be used in templates.

`File at: jsons/people.json`
```json
[
 { "name": "Bob","age": 25 },
 { "name": "Ram","age": 16 }
]
```
```yaml
{{- $people := .Files.Get "jsons/people.json" | fromJsonArray }}
{{- range $person := $people }}
greeting: |
  Hi, my name is {{ $person.name }} and I am {{ $person.age }} years old.
{{ end }}
```

### fromYamlArray

The `fromYamlArray` function takes a YAML Array and returns a list that can be used in templates.

`File at: yamls/people.yml`
```yaml
- name: Bob
  age: 25
- name: Ram
  age: 16
```
```yaml
{{- $people := .Files.Get "yamls/people.yml" | fromYamlArray }}
{{- range $person := $people }}
greeting: |
  Hi, my name is {{ $person.name }} and I am {{ $person.age }} years old.
{{ end }}
```


## Regular Expressions

Helm includes the following regular expression functions: [regexFind
(mustRegexFind)](#regexfindall-mustregexfindall), [regexFindAll
(mustRegexFindAll)](#regexfind-mustregexfind), [regexMatch
(mustRegexMatch)](#regexmatch-mustregexmatch), [regexReplaceAll
(mustRegexReplaceAll)](#regexreplaceall-mustregexreplaceall),
[regexReplaceAllLiteral
(mustRegexReplaceAllLiteral)](#regexreplaceallliteral-mustregexreplaceallliteral),
[regexSplit (mustRegexSplit)](#regexsplit-mustregexsplit).

### regexMatch, mustRegexMatch

Returns true if the input string contains any match of the regular expression.

```
regexMatch "^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$" "test@acme.com"
```

The above produces `true`

`regexMatch` panics if there is a problem and `mustRegexMatch` returns an error
to the template engine if there is a problem.

### regexFindAll, mustRegexFindAll

Returns a slice of all matches of the regular expression in the input string.
The last parameter n determines the number of substrings to return, where -1
means return all matches

```
regexFindAll "[2,4,6,8]" "123456789" -1
```

The above produces `[2 4 6 8]`

`regexFindAll` panics if there is a problem and `mustRegexFindAll` returns an
error to the template engine if there is a problem.

### regexFind, mustRegexFind

Return the first (left most) match of the regular expression in the input string

```
regexFind "[a-zA-Z][1-9]" "abcd1234"
```

The above produces `d1`

`regexFind` panics if there is a problem and `mustRegexFind` returns an error to
the template engine if there is a problem.

### regexReplaceAll, mustRegexReplaceAll

Returns a copy of the input string, replacing matches of the Regexp with the
replacement string replacement. Inside string replacement, $ signs are
interpreted as in Expand, so for instance $1 represents the text of the first
submatch

```
regexReplaceAll "a(x*)b" "-ab-axxb-" "${1}W"
```

The above produces `-W-xxW-`

`regexReplaceAll` panics if there is a problem and `mustRegexReplaceAll` returns
an error to the template engine if there is a problem.

### regexReplaceAllLiteral, mustRegexReplaceAllLiteral

Returns a copy of the input string, replacing matches of the Regexp with the
replacement string replacement. The replacement string is substituted directly,
without using Expand

```
regexReplaceAllLiteral "a(x*)b" "-ab-axxb-" "${1}"
```

The above produces `-${1}-${1}-`

`regexReplaceAllLiteral` panics if there is a problem and
`mustRegexReplaceAllLiteral` returns an error to the template engine if there is
a problem.

### regexSplit, mustRegexSplit

Slices the input string into substrings separated by the expression and returns
a slice of the substrings between those expression matches. The last parameter
`n` determines the number of substrings to return, where `-1` means return all
matches

```
regexSplit "z+" "pizza" -1
```

The above produces `[pi a]`

`regexSplit` panics if there is a problem and `mustRegexSplit` returns an error
to the template engine if there is a problem.

## Cryptographic and Security Functions

Helm provides some advanced cryptographic functions. They include
[adler32sum](#adler32sum), [buildCustomCert](#buildcustomcert),
[decryptAES](#decryptaes), [derivePassword](#derivepassword),
[encryptAES](#encryptaes), [genCA](#genca), [genPrivateKey](#genprivatekey),
[genSelfSignedCert](#genselfsignedcert), [genSignedCert](#gensignedcert),
[htpasswd](#htpasswd), [randBytes](#randbytes), [sha1sum](#sha1sum), and
[sha256sum](#sha256sum).

### sha1sum

The `sha1sum` function receives a string, and computes it's SHA1 digest.

```
sha1sum "Hello world!"
```

### sha256sum

The `sha256sum` function receives a string, and computes it's SHA256 digest.

```
sha256sum "Hello world!"
```

The above will compute the SHA 256 sum in an "ASCII armored" format that is safe
to print.

### adler32sum

The `adler32sum` function receives a string, and computes its Adler-32 checksum.

```
adler32sum "Hello world!"
```

### htpasswd

The `htpasswd` function takes a `username` and `password` and generates a
`bcrypt` hash of the password. The result can be used for basic authentication
on an [Apache HTTP
Server](https://httpd.apache.org/docs/2.4/misc/password_encryptions.html#basic).

```
htpasswd "myUser" "myPassword"
```

Note that it is insecure to store the password directly in the template.

### randBytes

The randBytes function accepts a count N and generates a cryptographically
secure (uses crypto/rand) random sequence of N bytes. The sequence is returned
as a base64 encoded string.

```
randBytes 24
```

### derivePassword

The `derivePassword` function can be used to derive a specific password based on
some shared "master password" constraints. The algorithm for this is [well
specified](https://web.archive.org/web/20211019121301/https://masterpassword.app/masterpassword-algorithm.pdf).

```
derivePassword 1 "long" "password" "user" "example.com"
```

Note that it is considered insecure to store the parts directly in the template.

### genPrivateKey

The `genPrivateKey` function generates a new private key encoded into a PEM
block.

It takes one of the values for its first param:

- `ecdsa`: Generate an elliptic curve DSA key (P256)
- `dsa`: Generate a DSA key (L2048N256)
- `rsa`: Generate an RSA 4096 key

### buildCustomCert

The `buildCustomCert` function allows customizing the certificate.

It takes the following string parameters:

- A base64 encoded PEM format certificate
- A base64 encoded PEM format private key

It returns a certificate object with the following attributes:

- `Cert`: A PEM-encoded certificate
- `Key`: A PEM-encoded private key

Example:

```
$ca := buildCustomCert "base64-encoded-ca-crt" "base64-encoded-ca-key"
```

Note that the returned object can be passed to the `genSignedCert` function to
sign a certificate using this CA.

### genCA

The `genCA` function generates a new, self-signed x509 certificate authority.

It takes the following parameters:

- Subject's common name (cn)
- Cert validity duration in days

It returns an object with the following attributes:

- `Cert`: A PEM-encoded certificate
- `Key`: A PEM-encoded private key

Example:

```
$ca := genCA "foo-ca" 365
```

Note that the returned object can be passed to the `genSignedCert` function to
sign a certificate using this CA.

### genSelfSignedCert

The `genSelfSignedCert` function generates a new, self-signed x509 certificate.

It takes the following parameters:

- Subject's common name (cn)
- Optional list of IPs; may be nil
- Optional list of alternate DNS names; may be nil
- Cert validity duration in days

It returns an object with the following attributes:

- `Cert`: A PEM-encoded certificate
- `Key`: A PEM-encoded private key

Example:

```
$cert := genSelfSignedCert "foo.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365
```

### genSignedCert

The `genSignedCert` function generates a new, x509 certificate signed by the
specified CA.

It takes the following parameters:

- Subject's common name (cn)
- Optional list of IPs; may be nil
- Optional list of alternate DNS names; may be nil
- Cert validity duration in days
- CA (see `genCA`)

Example:

```
$ca := genCA "foo-ca" 365
$cert := genSignedCert "foo.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365 $ca
```

### encryptAES

The `encryptAES` function encrypts text with AES-256 CBC and returns a base64
encoded string.

```
encryptAES "secretkey" "plaintext"
```

### decryptAES

The `decryptAES` function receives a base64 string encoded by the AES-256 CBC
algorithm and returns the decoded text.

```
"30tEfhuJSVRhpG97XCuWgz2okj7L8vQ1s6V9zVUPeDQ=" | decryptAES "secretkey"
```

## Date Functions

Helm includes the following date functions you can use in templates:
[ago](#ago), [date](#date), [dateInZone](#dateinzone), [dateModify
(mustDateModify)](#datemodify-mustdatemodify), [duration](#duration),
[durationRound](#durationround), [htmlDate](#htmldate),
[htmlDateInZone](#htmldateinzone), [now](#now), [toDate
(mustToDate)](#todate-musttodate), and [unixEpoch](#unixepoch).

### now

The current date/time. Use this in conjunction with other date functions.

### ago

The `ago` function returns duration from time. Now in seconds resolution.

```
ago .CreatedAt
```

returns in `time.Duration` String() format

```
2h34m7s
```

### date

The `date` function formats a date.

Format the date to YEAR-MONTH-DAY:

```
now | date "2006-01-02"
```

Date formatting in Go is a [little bit
different](https://pauladamsmith.com/blog/2011/05/go_time.html).

In short, take this as the base date:

```
Mon Jan 2 15:04:05 MST 2006
```

Write it in the format you want. Above, `2006-01-02` is the same date, but in
the format we want.

### dateInZone

Same as `date`, but with a timezone.

```
dateInZone "2006-01-02" (now) "UTC"
```

### duration

Formats a given amount of seconds as a `time.Duration`.

This returns 1m35s

```
duration "95"
```

### durationRound

Rounds a given duration to the most significant unit. Strings and
`time.Duration` gets parsed as a duration, while a `time.Time` is calculated as
the duration since.

This return 2h

```
durationRound "2h10m5s"
```

This returns 3mo

```
durationRound "2400h10m5s"
```

### unixEpoch

Returns the seconds since the unix epoch for a `time.Time`.

```
now | unixEpoch
```

### dateModify, mustDateModify

The `dateModify` takes a modification and a date and returns the timestamp.

Subtract an hour and thirty minutes from the current time:

```
now | dateModify "-1.5h"
```

If the modification format is wrong `dateModify` will return the date
unmodified. `mustDateModify` will return an error otherwise.

### htmlDate

The `htmlDate` function formats a date for inserting into an HTML date picker
input field.

```
now | htmlDate
```

### htmlDateInZone

Same as htmlDate, but with a timezone.

```
htmlDateInZone (now) "UTC"
```

### toDate, mustToDate

`toDate` converts a string to a date. The first argument is the date layout and
the second the date string. If the string can't be convert it returns the zero
value. `mustToDate` will return an error in case the string cannot be converted.

This is useful when you want to convert a string date to another format (using
pipe). The example below converts "2017-12-31" to "31/12/2017".

```
toDate "2006-01-02" "2017-12-31" | date "02/01/2006"
```

## Dictionaries and Dict Functions

Helm provides a key/value storage type called a `dict` (short for "dictionary",
as in Python). A `dict` is an _unordered_ type.

The key to a dictionary **must be a string**. However, the value can be any
type, even another `dict` or `list`.

Unlike `list`s, `dict`s are not immutable. The `set` and `unset` functions will
modify the contents of a dictionary.

Helm provides the following functions to support working with dicts: [deepCopy
(mustDeepCopy)](#deepcopy-mustdeepcopy), [dict](#dict), [dig](#dig), [get](#get),
[hasKey](#haskey), [keys](#keys), [merge (mustMerge)](#merge-mustmerge),
[mergeOverwrite (mustMergeOverwrite)](#mergeoverwrite-mustmergeoverwrite),
[omit](#omit), [pick](#pick), [pluck](#pluck), [set](#set), [unset](#unset), and
[values](#values).

### dict

Creating dictionaries is done by calling the `dict` function and passing it a
list of pairs.

The following creates a dictionary with three items:

```
$myDict := dict "name1" "value1" "name2" "value2" "name3" "value 3"
```

### get

Given a map and a key, get the value from the map.

```
get $myDict "name1"
```

The above returns `"value1"`

Note that if the key is not found, this operation will simply return `""`. No
error will be generated.

### set

Use `set` to add a new key/value pair to a dictionary.

```
$_ := set $myDict "name4" "value4"
```

Note that `set` _returns the dictionary_ (a requirement of Go template
functions), so you may need to trap the value as done above with the `$_`
assignment.

### unset

Given a map and a key, delete the key from the map.

```
$_ := unset $myDict "name4"
```

As with `set`, this returns the dictionary.

Note that if the key is not found, this operation will simply return. No error
will be generated.

### hasKey

The `hasKey` function returns `true` if the given dict contains the given key.

```
hasKey $myDict "name1"
```

If the key is not found, this returns `false`.

### pluck

The `pluck` function makes it possible to give one key and multiple maps, and
get a list of all of the matches:

```
pluck "name1" $myDict $myOtherDict
```

The above will return a `list` containing every found value (`[value1
otherValue1]`).

If the given key is _not found_ in a map, that map will not have an item in the
list (and the length of the returned list will be less than the number of dicts
in the call to `pluck`).

If the key is _found_ but the value is an empty value, that value will be
inserted.

A common idiom in Helm templates is to use `pluck... | first` to get the first
matching key out of a collection of dictionaries.

### dig

The `dig` function traverses a nested set of dicts, selecting keys from a list
of values. It returns a default value if any of the keys are not found at the
associated dict.

```
dig "user" "role" "humanName" "guest" $dict
```

Given a dict structured like
```
{
  user: {
    role: {
      humanName: "curator"
    }
  }
}
```

the above would return `"curator"`. If the dict lacked even a `user` field,
the result would be `"guest"`.

Dig can be very useful in cases where you'd like to avoid guard clauses,
especially since Go's template package's `and` doesn't shortcut. For instance
`and a.maybeNil a.maybeNil.iNeedThis` will always evaluate
`a.maybeNil.iNeedThis`, and panic if `a` lacks a `maybeNil` field.)

`dig` accepts its dict argument last in order to support pipelining. For instance:
```
merge a b c | dig "one" "two" "three" "<missing>"
```

### merge, mustMerge

Merge two or more dictionaries into one, giving precedence to the dest
dictionary:

Given:

```
dst:
  default: default
  overwrite: me
  key: true

src:
  overwrite: overwritten
  key: false
```

will result in:

```
newdict:
  default: default
  overwrite: me
  key: true
```
```
$newdict := merge $dest $source1 $source2
```

This is a deep merge operation but not a deep copy operation. Nested objects
that are merged are the same instance on both dicts. If you want a deep copy
along with the merge, then use the `deepCopy` function along with merging. For
example,

```
deepCopy $source | merge $dest
```

`mustMerge` will return an error in case of unsuccessful merge.

### mergeOverwrite, mustMergeOverwrite

Merge two or more dictionaries into one, giving precedence from **right to
left**, effectively overwriting values in the dest dictionary:

Given:

```
dst:
  default: default
  overwrite: me
  key: true

src:
  overwrite: overwritten
  key: false
```

will result in:

```
newdict:
  default: default
  overwrite: overwritten
  key: false
```

```
$newdict := mergeOverwrite $dest $source1 $source2
```

This is a deep merge operation but not a deep copy operation. Nested objects
that are merged are the same instance on both dicts. If you want a deep copy
along with the merge then use the `deepCopy` function along with merging. For
example,

```
deepCopy $source | mergeOverwrite $dest
```

`mustMergeOverwrite` will return an error in case of unsuccessful merge.

### keys

The `keys` function will return a `list` of all of the keys in one or more
`dict` types. Since a dictionary is _unordered_, the keys will not be in a
predictable order. They can be sorted with `sortAlpha`.

```
keys $myDict | sortAlpha
```

When supplying multiple dictionaries, the keys will be concatenated. Use the
`uniq` function along with `sortAlpha` to get a unique, sorted list of keys.

```
keys $myDict $myOtherDict | uniq | sortAlpha
```

### pick

The `pick` function selects just the given keys out of a dictionary, creating a
new `dict`.

```
$new := pick $myDict "name1" "name2"
```

The above returns `{name1: value1, name2: value2}`

### omit

The `omit` function is similar to `pick`, except it returns a new `dict` with
all the keys that _do not_ match the given keys.

```
$new := omit $myDict "name1" "name3"
```

The above returns `{name2: value2}`

### values

The `values` function is similar to `keys`, except it returns a new `list` with
all the values of the source `dict` (only one dictionary is supported).

```
$vals := values $myDict
```

The above returns `list["value1", "value2", "value 3"]`. Note that the `values`
function gives no guarantees about the result ordering; if you care about this,
then use `sortAlpha`.

### deepCopy, mustDeepCopy

The `deepCopy` and `mustDeepCopy` functions take a value and make a deep copy
of the value. This includes dicts and other structures. `deepCopy` panics when
there is a problem, while `mustDeepCopy` returns an error to the template system
when there is an error.

```
dict "a" 1 "b" 2 | deepCopy
```

### A Note on Dict Internals

A `dict` is implemented in Go as a `map[string]interface{}`. Go developers can
pass `map[string]interface{}` values into the context to make them available to
templates as `dict`s.

## Encoding Functions

Helm has the following encoding and decoding functions:

- `b64enc`/`b64dec`: Encode or decode with Base64
- `b32enc`/`b32dec`: Encode or decode with Base32

## Lists and List Functions

Helm provides a simple `list` type that can contain arbitrary sequential lists
of data. This is similar to arrays or slices, but lists are designed to be used
as immutable data types.

Create a list of integers:

```
$myList := list 1 2 3 4 5
```

The above creates a list of `[1 2 3 4 5]`.

Helm provides the following list functions: [append
(mustAppend)](#append-mustappend), [chunk](#chunk), [compact
(mustCompact)](#compact-mustcompact), [concat](#concat), [first
(mustFirst)](#first-mustfirst), [has (mustHas)](#has-musthas), [initial
(mustInitial)](#initial-mustinitial), [last (mustLast)](#last-mustlast),
[prepend (mustPrepend)](#prepend-mustprepend), [rest
(mustRest)](#rest-mustrest), [reverse (mustReverse)](#reverse-mustreverse),
[seq](#seq), [index](#index), [slice (mustSlice)](#slice-mustslice), [uniq
(mustUniq)](#uniq-mustuniq), [until](#until), [untilStep](#untilstep), and
[without (mustWithout)](#without-mustwithout).

### first, mustFirst

To get the head item on a list, use `first`.

`first $myList` returns `1`

`first` panics if there is a problem, while `mustFirst` returns an error to the
template engine if there is a problem.

### rest, mustRest

To get the tail of the list (everything but the first item), use `rest`.

`rest $myList` returns `[2 3 4 5]`

`rest` panics if there is a problem, while `mustRest` returns an error to the
template engine if there is a problem.

### last, mustLast

To get the last item on a list, use `last`:

`last $myList` returns `5`. This is roughly analogous to reversing a list and
then calling `first`.

### initial, mustInitial

This compliments `last` by returning all _but_ the last element. `initial
$myList` returns `[1 2 3 4]`.

`initial` panics if there is a problem, while `mustInitial` returns an error to
the template engine if there is a problem.

### append, mustAppend

Append a new item to an existing list, creating a new list.

```
$new = append $myList 6
```

The above would set `$new` to `[1 2 3 4 5 6]`. `$myList` would remain unaltered.

`append` panics if there is a problem, while `mustAppend` returns an error to the
template engine if there is a problem.

### prepend, mustPrepend

Push an element onto the front of a list, creating a new list.

```
prepend $myList 0
```

The above would produce `[0 1 2 3 4 5]`. `$myList` would remain unaltered.

`prepend` panics if there is a problem, while `mustPrepend` returns an error to
the template engine if there is a problem.

### concat

Concatenate arbitrary number of lists into one.

```
concat $myList ( list 6 7 ) ( list 8 )
```

The above would produce `[1 2 3 4 5 6 7 8]`. `$myList` would remain unaltered.

### reverse, mustReverse

Produce a new list with the reversed elements of the given list.

```
reverse $myList
```

The above would generate the list `[5 4 3 2 1]`.

`reverse` panics if there is a problem, while `mustReverse` returns an error to
the template engine if there is a problem.

### uniq, mustUniq

Generate a list with all of the duplicates removed.

```
list 1 1 1 2 | uniq
```

The above would produce `[1 2]`

`uniq` panics if there is a problem, while `mustUniq` returns an error to the
template engine if there is a problem.

### without, mustWithout

The `without` function filters items out of a list.

```
without $myList 3
```

The above would produce `[1 2 4 5]`

`without` can take more than one filter:

```
without $myList 1 3 5
```

That would produce `[2 4]`

`without` panics if there is a problem, while `mustWithout` returns an error to
the template engine if there is a problem.

### has, mustHas

Test to see if a list has a particular element.

```
has 4 $myList
```

The above would return `true`, while `has "hello" $myList` would return false.

`has` panics if there is a problem, while `mustHas` returns an error to the
template engine if there is a problem.

### compact, mustCompact

Accepts a list and removes entries with empty values.

```
$list := list 1 "a" "foo" ""
$copy := compact $list
```

`compact` will return a new list with the empty (i.e., "") item removed.

`compact` panics if there is a problem and `mustCompact` returns an error to the
template engine if there is a problem.

### index

To get the nth element of a list, use `index list [n]`. To index into 
multi-dimensional lists, use `index list [n] [m] ...`
- `index $myList 0` returns `1`. It is the same as `myList[0]`
- `index $myList 0 1` would be the same as `myList[0][1]`

### slice, mustSlice

To get partial elements of a list, use `slice list [n] [m]`. It is equivalent of
`list[n:m]`.

- `slice $myList` returns `[1 2 3 4 5]`. It is same as `myList[:]`.
- `slice $myList 3` returns `[4 5]`. It is same as `myList[3:]`.
- `slice $myList 1 3` returns `[2 3]`. It is same as `myList[1:3]`.
- `slice $myList 0 3` returns `[1 2 3]`. It is same as `myList[:3]`.

`slice` panics if there is a problem, while `mustSlice` returns an error to the
template engine if there is a problem.

### until

The `until` function builds a range of integers.

```
until 5
```

The above generates the list `[0, 1, 2, 3, 4]`.

This is useful for looping with `range $i, $e := until 5`.

### untilStep

Like `until`, `untilStep` generates a list of counting integers. But it allows
you to define a start, stop, and step:

```
untilStep 3 6 2
```

The above will produce `[3 5]` by starting with 3, and adding 2 until it is
equal or greater than 6. This is similar to Python's `range` function.

### seq

Works like the bash `seq` command.

* 1 parameter  (end) - will generate all counting integers between 1 and `end`
  inclusive.
* 2 parameters (start, end) - will generate all counting integers between
  `start` and `end` inclusive incrementing or decrementing by 1.
* 3 parameters (start, step, end) - will generate all counting integers between
  `start` and `end` inclusive incrementing or decrementing by `step`.

```
seq 5       => 1 2 3 4 5
seq -3      => 1 0 -1 -2 -3
seq 0 2     => 0 1 2
seq 2 -2    => 2 1 0 -1 -2
seq 0 2 10  => 0 2 4 6 8 10
seq 0 -2 -5 => 0 -2 -4
```

### chunk

To split a list into chunks of given size, use `chunk size list`. This is useful for pagination.

```
chunk 3 (list 1 2 3 4 5 6 7 8)
```

This produces list of lists `[ [ 1 2 3 ] [ 4 5 6 ] [ 7 8 ] ]`.

## Math Functions

All math functions operate on `int64` values unless specified otherwise.

The following math functions are available: [add](#add), [add1](#add1),
[ceil](#ceil), [div](#div), [floor](#floor), [len](#len), [max](#max),
[min](#min), [mod](#mod), [mul](#mul), [round](#round), and [sub](#sub).

### add

Sum numbers with `add`. Accepts two or more inputs.

```
add 1 2 3
```

### add1

To increment by 1, use `add1`.

### sub

To subtract, use `sub`.

### div

Perform integer division with `div`.

### mod

Modulo with `mod`.

### mul

Multiply with `mul`. Accepts two or more inputs.

```
mul 1 2 3
```

### max

Return the largest of a series of integers.

This will return `3`:

```
max 1 2 3
```

### min

Return the smallest of a series of integers.

`min 1 2 3` will return `1`.

### len

Returns the length of the argument as an integer.

```
len .Arg
```

## Float Math Functions

All math functions operate on `float64` values.

### addf

Sum numbers with `addf`

This will return `5.5`:

```
addf 1.5 2 2
```

### add1f

To increment by 1, use `add1f`

### subf

To subtract, use `subf`

This is equivalent to `7.5 - 2 - 3` and will return `2.5`:

```
subf 7.5 2 3
```

### divf

Perform integer division with `divf`

This is equivalent to `10 / 2 / 4` and will return `1.25`:

```
divf 10 2 4
```

### mulf

Multiply with `mulf`

This will return `6`:

```
mulf 1.5 2 2
```

### maxf

Return the largest of a series of floats:

This will return `3`:

```
maxf 1 2.5 3
```

### minf

Return the smallest of a series of floats.

This will return `1.5`:

```
minf 1.5 2 3
```

### floor

Returns the greatest float value less than or equal to input value.

`floor 123.9999` will return `123.0`.

### ceil

Returns the greatest float value greater than or equal to input value.

`ceil 123.001` will return `124.0`.

### round

Returns a float value with the remainder rounded to the given number to digits
after the decimal point.

`round 123.555555 3` will return `123.556`.

## Network Functions

Helm has a single network function, `getHostByName`.

The `getHostByName` receives a domain name and returns the ip address.

`getHostByName "www.google.com"` would return the corresponding ip address of `www.google.com`.

## File Path Functions

While Helm template functions do not grant access to the filesystem, they do
provide functions for working with strings that follow file path conventions.
Those include [base](#base), [clean](#clean), [dir](#dir), [ext](#ext), and
[isAbs](#isabs).

### base

Return the last element of a path.

```
base "foo/bar/baz"
```

The above prints "baz".

### dir

Return the directory, stripping the last part of the path. So `dir
"foo/bar/baz"` returns `foo/bar`.

### clean

Clean up a path.

```
clean "foo/bar/../baz"
```

The above resolves the `..` and returns `foo/baz`.

### ext

Return the file extension.

```
ext "foo.bar"
```

The above returns `.bar`.

### isAbs

To check whether a file path is absolute, use `isAbs`.

## Reflection Functions

Helm provides rudimentary reflection tools. These help advanced template
developers understand the underlying Go type information for a particular value.
Helm is written in Go and is strongly typed. The type system applies within
templates.

Go has several primitive _kinds_, like `string`, `slice`, `int64`, and `bool`.

Go has an open _type_ system that allows developers to create their own types.

Helm provides a set of functions for each via [kind functions](#kind-functions)
and [type functions](#type-functions). A [deepEqual](#deepequal) function is
also provided to compare to values.

### Kind Functions

There are two Kind functions: `kindOf` returns the kind of an object.

```
kindOf "hello"
```

The above would return `string`. For simple tests (like in `if` blocks), the
`kindIs` function will let you verify that a value is a particular kind:

```
kindIs "int" 123
```

The above will return `true`.

### Type Functions

Types are slightly harder to work with, so there are three different functions:

- `typeOf` returns the underlying type of a value: `typeOf $foo`
- `typeIs` is like `kindIs`, but for types: `typeIs "*io.Buffer" $myVal`
- `typeIsLike` works as `typeIs`, except that it also dereferences pointers

**Note:** None of these can test whether or not something implements a given
interface, since doing so would require compiling the interface in ahead of
time.

### deepEqual

`deepEqual` returns true if two values are ["deeply
equal"](https://golang.org/pkg/reflect/#DeepEqual)

Works for non-primitive types as well (compared to the built-in `eq`).

```
deepEqual (list 1 2 3) (list 1 2 3)
```

The above will return `true`.

## Semantic Version Functions

Some version schemes are easily parseable and comparable. Helm provides
functions for working with [SemVer 2](http://semver.org) versions. These include
[semver](#semver) and [semverCompare](#semvercompare). Below you will also find
details on using ranges for comparisons.

### semver

The `semver` function parses a string into a Semantic Version:

```
$version := semver "1.2.3-alpha.1+123"
```

_If the parser fails, it will cause template execution to halt with an error._

At this point, `$version` is a pointer to a `Version` object with the following
properties:

- `$version.Major`: The major number (`1` above)
- `$version.Minor`: The minor number (`2` above)
- `$version.Patch`: The patch number (`3` above)
- `$version.Prerelease`: The prerelease (`alpha.1` above)
- `$version.Metadata`: The build metadata (`123` above)
- `$version.Original`: The original version as a string

Additionally, you can compare a `Version` to another `version` using the
`Compare` function:

```
semver "1.4.3" | (semver "1.2.3").Compare
```

The above will return `-1`.

The return values are:

- `-1` if the given semver is greater than the semver whose `Compare` method was
  called
- `1` if the version who's `Compare` function was called is greater.
- `0` if they are the same version

(Note that in SemVer, the `Metadata` field is not compared during version
comparison operations.)

### semverCompare

A more robust comparison function is provided as `semverCompare`. This version
supports version ranges:

- `semverCompare "1.2.3" "1.2.3"` checks for an exact match
- `semverCompare "~1.2.0" "1.2.3"` checks that the major and minor versions
  match, and that the patch number of the second version is _greater than or
  equal to_ the first parameter.

The SemVer functions use the [Masterminds semver
library](https://github.com/Masterminds/semver), from the creators of Sprig.

### Basic Comparisons

There are two elements to the comparisons. First, a comparison string is a list
of space or comma separated AND comparisons. These are then separated by || (OR)
comparisons. For example, `">= 1.2 < 3.0.0 || >= 4.2.3"` is looking for a
comparison that's greater than or equal to 1.2 and less than 3.0.0 or is greater
than or equal to 4.2.3.

The basic comparisons are:

- `=`: equal (aliased to no operator)
- `!=`: not equal
- `>`: greater than
- `<`: less than
- `>=`: greater than or equal to
- `<=`: less than or equal to

### Working With Prerelease Versions

Pre-releases, for those not familiar with them, are used for software releases
prior to stable or generally available releases. Examples of prereleases include
development, alpha, beta, and release candidate releases. A prerelease may be a
version such as `1.2.3-beta.1`, while the stable release would be `1.2.3`. In the
order of precedence, prereleases come before their associated releases. In this
example `1.2.3-beta.1 < 1.2.3`.

According to the Semantic Version specification prereleases may not be API
compliant with their release counterpart. It says,

> A pre-release version indicates that the version is unstable and might not
> satisfy the intended compatibility requirements as denoted by its associated
> normal version.

SemVer comparisons using constraints without a prerelease comparator will skip
prerelease versions. For example, `>=1.2.3` will skip prereleases when looking
at a list of releases, while `>=1.2.3-0` will evaluate and find prereleases.

The reason for the `0` as a pre-release version in the example comparison is
because pre-releases can only contain ASCII alphanumerics and hyphens (along
with `.` separators), per the spec. Sorting happens in ASCII sort order, again
per the spec. The lowest character is a `0` in ASCII sort order (see an [ASCII
Table](http://www.asciitable.com/))

Understanding ASCII sort ordering is important because A-Z comes before a-z.
That means `>=1.2.3-BETA` will return `1.2.3-alpha`. What you might expect from
case sensitivity doesn't apply here. This is due to ASCII sort ordering which is
what the spec specifies.

### Hyphen Range Comparisons

There are multiple methods to handle ranges and the first is hyphens ranges.
These look like:

- `1.2 - 1.4.5` which is equivalent to `>= 1.2 <= 1.4.5`
- `2.3.4 - 4.5` which is equivalent to `>= 2.3.4 <= 4.5`

### Wildcards In Comparisons

The `x`, `X`, and `*` characters can be used as a wildcard character. This works
for all comparison operators. When used on the `=` operator it falls back to the
patch level comparison (see tilde below). For example,

- `1.2.x` is equivalent to `>= 1.2.0, < 1.3.0`
- `>= 1.2.x` is equivalent to `>= 1.2.0`
- `<= 2.x` is equivalent to `< 3`
- `*` is equivalent to `>= 0.0.0`

### Tilde Range Comparisons (Patch)

The tilde (`~`) comparison operator is for patch level ranges when a minor
version is specified and major level changes when the minor number is missing.
For example,

- `~1.2.3` is equivalent to `>= 1.2.3, < 1.3.0`
- `~1` is equivalent to `>= 1, < 2`
- `~2.3` is equivalent to `>= 2.3, < 2.4`
- `~1.2.x` is equivalent to `>= 1.2.0, < 1.3.0`
- `~1.x` is equivalent to `>= 1, < 2`

### Caret Range Comparisons (Major)

The caret (`^`) comparison operator is for major level changes once a stable
(1.0.0) release has occurred. Prior to a 1.0.0 release the minor versions acts
as the API stability level. This is useful when comparisons of API versions as a
major change is API breaking. For example,

- `^1.2.3` is equivalent to `>= 1.2.3, < 2.0.0`
- `^1.2.x` is equivalent to `>= 1.2.0, < 2.0.0`
- `^2.3` is equivalent to `>= 2.3, < 3`
- `^2.x` is equivalent to `>= 2.0.0, < 3`
- `^0.2.3` is equivalent to `>=0.2.3 <0.3.0`
- `^0.2` is equivalent to `>=0.2.0 <0.3.0`
- `^0.0.3` is equivalent to `>=0.0.3 <0.0.4`
- `^0.0` is equivalent to `>=0.0.0 <0.1.0`
- `^0` is equivalent to `>=0.0.0 <1.0.0`

## URL Functions

Helm includes the [urlParse](#urlparse), [urlJoin](#urljoin), and
[urlquery](#urlquery) functions enabling you to work with URL parts.

### urlParse

Parses string for URL and produces dict with URL parts

```
urlParse "http://admin:secret@server.com:8080/api?list=false#anchor"
```

The above returns a dict, containing URL object:

```yaml
scheme:   'http'
host:     'server.com:8080'
path:     '/api'
query:    'list=false'
opaque:   nil
fragment: 'anchor'
userinfo: 'admin:secret'
```

This is implemented used the URL packages from the Go standard library. For more
info, check https://golang.org/pkg/net/url/#URL

### urlJoin

Joins map (produced by `urlParse`) to produce URL string

```
urlJoin (dict "fragment" "fragment" "host" "host:80" "path" "/path" "query" "query" "scheme" "http")
```

The above returns the following string:
```
http://host:80/path?query#fragment
```

### urlquery

Returns the escaped version of the value passed in as an argument so that it is
suitable for embedding in the query portion of a URL.

```
$var := urlquery "string for query"
```

## UUID Functions

Helm can generate UUID v4 universally unique IDs.

```
uuidv4
```

The above returns a new UUID of the v4 (randomly generated) type.

## Kubernetes and Chart Functions

Helm includes functions for working with Kubernetes including
[.Capabilities.APIVersions.Has](#capabilitiesapiversionshas),
[Files](#file-functions), and [lookup](#lookup).

### lookup

`lookup` is used to look up resource in a running cluster. When used with the
`helm template` command it always returns an empty response.

You can find more detail in the [documentation on the lookup
function](functions_and_pipelines.md/#using-the-lookup-function).

### .Capabilities.APIVersions.Has

Returns if an API version or resource is available in a cluster.

```
.Capabilities.APIVersions.Has "apps/v1"
.Capabilities.APIVersions.Has "apps/v1/Deployment"
```

More information is available on the [built-in object
documentation](builtin_objects.md).

### File Functions

There are several functions that enable you to get to non-special files within a
chart. For example, to access application configuration files. These are
documented in [Accessing Files Inside Templates](accessing_files.md).

_Note, the documentation for many of these functions come from
[Sprig](https://github.com/Masterminds/sprig). Sprig is a template function
library available to Go applications._
