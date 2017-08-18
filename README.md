# SWASH (Draft)

A specification for a **S**imple **W**eb **A**ddress **S**c**H**eme.

## Summary

This is a document describing **SWASH**, a specification for a simple address notation for web applications and internet interfaces.

The goals of this proposal is to define a minimalist notation for web application resource address; an alternative to URI. 
The notation maintains general semantics which can then be used to build higher-order semantics. 
swash serves as a foundational grammar of simple address schemes for networking applications alike.

## Conventions

SWASH (all caps) refers to the specification. 
A **swash** (_plural_ swashes) is a encoding scheme defined within the specification.

## Grammar

The notation for a _swash_ is a chain of address elements delimited by the `/` (forward-slash) character:

```
<swash> = ('/' <address-element>)+

<address-element> = <level> | <parameter>

<level> = <value>

<parameter> = <parameter-key> '=' <value>

<parameter-key> = (<valid-char>)*

<value> = (<valid-char>)*

<valid-char> = 'a' ... 'z' | 'A' .. 'Z' | '0' ... '9' | '-' | '.' | '_' | '~' | '?' | '#' | '[' | ']' | '@' | '!' | '$' | '&' | "'" | '(' | ')' | '*' | '+' | ',' | ';' | ':' | '`'
```

_As it stands, `<valid-char>` is open for comments._

## Semantics

A swash is a series of _address-elements_ prefixed with a forward-slash character.
Each address element is assigned a zero-based index called the _element-index_.
An address-element is either a _parameter_ or a _level_.

Every level is assigned a zero-based index called the _level-index_.

Every parameter is assigned a zero-based index called the _parameter-index_.

In addition, a table called the _parameter-table_ maps parameter keys to one or more values. 
Each row contains a unique _parameter-key_ column and a column containing an array called _parameter-values_. 
Each element in parameter-values is a value from a parameter with the same parameter-key of that of the row's parameter-key. 

In conclusion, a swash is a general, unopinionated scheme for encoding web addresses.
This lays the groundwork for further use-case-specific semantics to be built onto the swash specification.

## Swash Semantics to URI (SS2URI)

SS2URI is a rough specification for translation of a swash to a URI.

Semantics for a URI can be specified such that:

- The level's value at index 0 is the URI scheme.
- The level's value at index 1 is the URI authority.
- All values for levels >2 compose the URI path.
- The parameter with `#` as its key composes the URI fragment by joining it's parameter-values.
- All other parameters are formatted into the URI query.

```
/http/domain.tld/path/index.html/x=1/y=2/#=fragment
/http/domain.tld/path/index.html/y=2/x=1/#=fragment
/http/domain.tld/path/index.html/y=2/x=1/#=frag/#=ment
```

The above examples are all equivalent encodings for the following URI:

```
http://domain.tld/path/index.html?x=1&y=2#fragment
```

Note that each of the above swashes are semantically different by the SWASH specification, however
SS2URI has no use for all semantics defined within SWASH.
Some information is lost in translation, therefore this can be referred to as a "**lossy translation**".

Further, certain parts of the swash become case-insensitive, even though a swash is a case-sensitive encoding.

SS2URI implementation may include defaults for certain level values in a swash, for example:

**Without URI scheme, path, query, nor fragment** 
```
//domain.tld
```

**URI Translation** 
```
http://domain.tld/
```