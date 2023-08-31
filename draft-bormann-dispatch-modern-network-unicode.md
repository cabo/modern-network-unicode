---
v: 3

coding: utf-8

title: Modern Network Unicode
abbrev: Modern Network Unicode
docname: draft-bormann-dispatch-modern-network-unicode-latest
# date: 2023-08-31
category: std
stream: IETF

ipr: trust200902
area: Applications
workgroup: DISPATCH Working Group
keyword: Internet-Draft

venue:
  group: ART area
  mail: art@ietf.org
  github: cabo/modern-network-unicode

author:
  -
    ins: C. Bormann
    name: Carsten Bormann
    org: Universität Bremen TZI
    street: Postfach 330440
    city: Bremen
    code: D-28359
    country: Germany
    phone: +49-421-218-63921
    email: cabo@tzi.org

normative:
  STD80:
    =: RFC0020
    -: ascii
  BCP18:
    =: RFC2277
    -: policy
  STD63:
    =: RFC3629
    -: utf8
  STD68:
    =: RFC5234
    -: abnf
informative:
  RFC8265: userprep
  RFC2223: old-rfc
  RFC7464: json-seq
  RFC5137: escaping
  RFC0764: older-telnet
  RFC0854: telnet
  RFC5198: unvt
  UNICODE:
    target: https://www.unicode.org/versions/Unicode15.0.0/UnicodeStandard-15.0.pdf
    title: >
      The Unicode® Standard:
      Version 15.0 — Core Specification
    author:
    - org: The Unicode Consortium
    date: 2022-09
    ann: >
      For convenience, this bibliography entry points to what was the
      most recent version of Unicode at the time of writing.
      It is, however, intended to be a generic reference to the most
      recent version of Unicode, which always can be found at
      <http://www.unicode.org/versions/latest/>.
  XML:
    target: http://www.w3.org/TR/2008/REC-xml-20081126/
    title: Extensible Markup Language (XML) 1.0 (Fifth Edition)
    author:
    - name: Tim Bray
    - name: Jean Paoli
    - name: C.M. Sperberg-McQueen
    - name: Eve Maler
    - name: François Yergeau
    date: '2008-11-26'

entity:
        SELF: "[RFC-XXXX]"

--- abstract

BCP18 (RFC 2277) has been the basis for the handling of
character-shaped data in IETF specifications for more than a quarter
of a century now.
It singles out UTF-8 (STD63, RFC 3629) as the "charset" that MUST be
supported, and pulls in the Unicode standard with that.

Based on this, RFC 5198 both defines common conventions for the use of
Unicode in network protocols and caters for the specific requirements
of the legacy protocol Telnet.
In applications that do not need Telnet compatibility, some of the
decisions of RFC 5198 can be cumbersome.

The present specification defines "Modern Network Unicode" (MNU),
which is a form of RFC 5198 Network Unicode that can be used in
specifications that require the exchange of plain text over networks
and where just mandating UTF-8 may not be sufficient, but
there is also no desire to import all of the baggage of RFC 5198.

As characters are used in different environments, MNU is defined in a
one-dimensional (1D) variant that is useful for identifiers and
labels, but does not use a structure of lines.
A 2D variant is defined for text that is a sequence of text lines,
such as plain text documents or markdown format.
Additional variances of these two base formats can be used to tailor
MNU to specific areas of application.

--- note_Status

The present version of this document represents the author's reaction
to initial exposure on the art@ietf.org mailing list.  Some more
editorial cleanup is probably desirable, but could not be achieved in
time for the IETF105 Internet-Draft deadline.

--- middle

# Introduction

(Insert embellished copy of abstract here.)


<!-- And perhaps a few examples of modern/current specs where these
considerations are very relevant would help readers to evaluate if
they should take this stuff into account in their specs may help too
-->

Complex specifications that use Unicode often come with detailed
information on their Unicode usage; this level of detail generally is
necessary to support some legacy applications.  New, simple protocol
specifications generally do not have such a legacy or need such
details, but can instead simply use common practice, informed by
decades of using Unicode.  The present specification attempts to serve
as a convenient reference for such protocol specifications, reducing
their need for discussing Unicode to just pointing to the present
specification and making a few simple choices.

There is no intention that henceforth all new protocols "must" use the
present specification.  It is offered as a standards-track
specification simply so it can be normatively referenced from other
standards-track specifications.


## Conventions

Characters in this specification are named with their Unicode name
notated in the usual form U+NNNN (where NNNN is a
four-to-six-digit upper case hexadecimal number giving the Unicode scalar
value), or with their ASCII names (such as CR, LF, HT, RS, NUL)
{{-ascii}}.

{:aside}
>
See {{-escaping}} for a more detailed discussion of ways to refer in ASCII to
Unicode characters (as well as to code points and code units in various
transformation formats).

The following definitions apply:

Byte:
: 8-bit unit of information interchange, synonym for octet.

Please see {{terminology}} for some more detailed term definitions that
may be helpful to relate this specification with the Unicode Standard;
please do read its first paragraph if the definitions in this section
seem inadequate.

{::boilerplate bcp14-tagged}

# 1D Modern Network Unicode {#mnu1d}

One-dimensional Modern Network Unicode (1D MNU) is the form of Modern
Network Unicode that can be used for one-dimensional text, i.e., text
without a structure of separate text lines.
It requires conformance to {{-utf8}}, as well as to the following
four mandates:

1. Control codes (U+0000 to U+001F and U+007F to U+009F) MUST NOT
  be used.  (Note that this also excludes line endings, so a 1D MNU text
  string cannot extend beyond a single line.  See {{mnu2d}}
  below if line structure is needed.)

2. The characters U+2028 and U+2029 MUST NOT be used.  (In case future
  Unicode versions add to the Unicode character categories Zl or Zp,
  any characters in these categories MUST NOT be used.)

3. Modern Network Unicode strongly RECOMMENDS that, except in unusual
  circumstances (see {{normalization}}), all text is transmitted in
  normalization form NFC.  This mandate should not be
  realized by routinely normalizing all text, but by encouraging text
  sources to use NFC if applicable.

4. As per the Unicode specification, the code points U+FFFE and U+FFFF
  MUST NOT be used.  Also, Byte Order Marks (leading U+FEFF
  characters) MUST NOT be used.

Note that several control codes have been in use historically in ASCII
documents even within a single text line.
For instance BS (U+0008) and CR (U+000D) have been used as a crude way
to combine (by overtyping) characters; the present specification does not
explicitly support this behavior anymore.
HT (U+0009, "TAB") has been used for a form of compressing stretches
of blank space; by keeping its actual definition open it has also been
used to enable users of text to specify variable blank space
("indentation").
The present specification RECOMMENDS not using a variance for 1D MNU
to enable using BS, HT, or CR; legacy usage of HT may be more
appropriate in certain forms of 2D text.

# 2D Modern Network Unicode {#mnu2d}

Two-dimensional Modern Network Unicode (2D MNU) enhances 1D MNU by
structuring the text into lines.
2D MNU does this is by using the control code LF (U+000A) for a line
terminator.
The use of an unterminated last line is permitted, but not encouraged.
(This is not a variance.)

Historically, the Internet NVT {{-telnet}} and thus, much later, Network
Unicode {{RFC5198}} have used the combination CR+LF as a line
terminator, reflecting its heritage based on teletype functionality.
The use of U+000D mostly introduces additional ways to create
interoperability problems.
{{XML}} goes to great lengths to reduce the harm the presence of CR
characters does to interchange of XML documents.
Pages 10 and 11 of {{-older-telnet}} discuss how CR can actually only be
used in the combinations CR+NUL and CR+LF.
2D MNU simply elides the CR.

In other words, 2D MNU adds the use of line endings, represented by a
single LF character (which is then the only control character
allowed).  Variances are available for (1) allowing or even (2)
requiring the use of CR before LF.

# 3D?

Three-dimensional forms of text have been used, e.g., by using U+000C
FF ("form feed") to add a page structure to the RFC format {{-old-rfc}},
or by using U+001E RS ("record separator") to separate several (2D) JSON
texts in a JSON sequence {{-json-seq}}.

As the need for this form of structure is now mostly being addressed by
building data structures around text items, the present specification
does not define a 3D MNU.
If needed, variances such as the use of FF or RS can be described in
the documents specifying the 3D format.

# Variances

In addition to the basic 1D and 2D versions of MNU, this specification
describes a number of variances that can be used in the forms such as
"2D Modern Network Unicode with VVV", or "2D Modern Network Unicode
with VVV, WWW, and ZZZ" for multiple variances used.  Specifications
that cannot directly use the basic MNU forms may be able to use MNU
with one or more of these variances added.

An example for the usage of variances: Up to RFC 8649, RFCs were
formatted in "2D Modern Network Unicode with CR-tolerant lines and FF
characters", or exceptionally {{-userprep}}, "2D Modern Network Unicode with
CR-tolerant lines and FF characters and BOM".

## With CR-tolerant lines

The variance "with CR-tolerant lines" allows the sequence CR LF as
well as a single LF character as a line ending.  This may enable
existing texts to be used as MNU without processing at the sender side
(substituting that by processing at the receiver side).  Note that,
with this variance, a CR character cannot be used anywhere else but
immediately preceding an LF character.

## With CR-LF

The variance "with CR-LF lines" requires the sequence CR LF as a line
ending; a single LF character is not allowed.  This is appropriate for
certain existing environments that have already made that
determination.

## With Line or Paragraph Separators

For 2D applications, and even for 1D applications that need to address
text in 2D forms, it may be useful to enable the use of U+2028 and
U+2029; this should be explicitly called out.

## With HT Characters

In some cases, the use of HT characters ("TABs") cannot be completely excluded.
The variance "with HT characters" allows their use, without attempting
to define their meaning (e.g., equivalence with spaces, column definitions, etc.).

## With CCC Characters

Some applications of MNU may need to add specific control characters,
such as RS {{-json-seq}} or FF characters {{-old-rfc}}.  This variance is spelled
with the ASCII name of the control character for CCC, e.g., "with RS
characters".

## With NFC

This variance turns the encouragement of using NFC normalization form
into a requirement.
Please see {{normalization}} for why this should be an exceptional case.

## With NFKC

Some applications require a stronger form of normalization than NFC.
The variance "with NFKC" swaps out NFC and uses NFKC instead.
This is probably best used in conjunction with "with Unicode version NNN".

## With Unicode Version NNN {#version}

Some applications need to be sure that a certain Unicode version is
used.
The variance "with Unicode version NNN" (where NNN is a Unicode
version number) defines the Unicode version in use as NNN.
Also, it requires that only characters assigned in that Unicode
version are being used.

See {{Section 4 of -unvt}} for more discussion of Unicode versions.

## With BOM

In some environments, UTF-8 files need to be distinguished from files
in other formats, and one convention that has been used in certain
environments was to prepend a "Byte Order Mark" (U+FEFF) as a
distinguishing mark (as UTF-8 is not influenced by different byte
orders, this mark does not serve a purpose in UTF-8).
Sometimes these BOMs are included for interchange to ensure local
copies of the file include the distinguishing mark.
This variance enables this usage.

# Using ABNF with Unicode

Internet STD 68, {{-abnf}}, defines Augmented BNF for Syntax
Specifications: ABNF.  Since the late 1970s, ABNF has often been used
to formally describe the pieces of text that are meant to be used in
an Internet protocol.  ABNF was developed at a time when character
coding grew more and more complicated, and even in its current form, discusses
encoding of characters only briefly ({{Section 2.4 of -abnf}}).
This discussion offers no information about how this should be used today (it
actually still refers to 16-bit Unicode!).

The best current practice of using ABNF for Unicode-based protocols is
as follows: ABNF is used as a grammar for describing sequences of
Unicode code points, valued from 0x0 to 0x10FFFF.  The actual encoding
(as UTF-8) is never seen on the ABNF level; see Section 9.4 of
{{?RFC6020}} for a recent example of this.
Approaches such as representing the rules of UTF-8 encoding in ABNF
(see {{Section 3.5 of ?RFC5255}} for an example) add complexity without
benefit and are NOT RECOMMENDED.

ABNF features such as case-insensitivity in literal text strings
essentially do not work for general Unicode; text string literals
therefore (and by the definition in Section 2.3 of {{-abnf}}) are
limited to ASCII characters.  That is often not actually a problem in
text-based protocol definitions.  Still, characters beyond ASCII need
to be allowed in many productions.  ABNF does not have access to
Unicode character categories and thus will be limited in its
expressiveness here.  The core rules defines in Appendix B of
{{-abnf}} are limited to ASCII as well; new rules will therefore need
to be defined in any protocol employing modern Unicode.

The present specification recommends defining ABNF rules as in these
examples:

~~~~ ABNF
; 1D modern unicode character:
uchar = %x20-7E ; exclude C0
      / %xA0-2027 ; exclude DEL, C1
      / %x202A-D7FF ; exclude U+2028/2029
      / %xE000-FFFD ; exclude U+FFFE/FFFF
      / %x10000-10FFFD ; could exclude more non-characters

; 2D modern unicode with lines -- newline:
unl = %x0A
u2dchar = unl / uchar
; alternatively, CR-tolerant newline:
ucrnl = [%x0D] %x0A
u2dcrchar = ucrnl / uchar

; if really needed, HT-tolerant 1D unicode character:
utchar = %x09 / uchar
~~~~

# IANA considerations

This specification places no requirements on IANA.

# Security considerations

The security considerations of {{-unvt}} apply.

A variance "with NUL characters" would create specific security
considerations as discussed in the security considerations of
{{-unvt}} and should therefore only be used in circumstances that
absolutely do require it.

--- back

# Terminology

In order for the main body of this specification to stay readable for
its target audience, we banish what could be considered excessive
detail into appendices.
Expert readers who know a lot more about Coded Character Sets and
Unicode than the target audience is likely interested in are requested
to hold back the urge to request re-introduction of excessive detail
into the main body.
For instance, this specification has a local definition of "Unicode
character" that is useful for protocol designers, but is not actually
defined by the Unicode consortium; the details are here.

Some definitions below reference definitions given in the Unicode
standard and are simplified from those, which see if the full detail
is needed.

Grapheme:
: The smallest functional unit of a writing system.  For computer
  representation, graphemes are represented by _characters_, sometimes
  a single character, sometimes decomposed into multiple characters.
  In visible presentation, graphemes are made visible as _glyphs_;
  for computer use, glyphs are collected into _typefaces_ (fonts).
  The term grapheme is used to abstract from the specific glyph used —
  a specific glyph from a typeface may be considered one of several
  _allographs_ for a grapheme.

Character:
: Only defined as "abstract character" in {{UNICODE}}:
  A unit of information used for the organization, control, or
  representation of textual data (definition D7).
  Most characters are used to stand for graphemes or to build
  graphemes out of several characters.
  Characters are usually used in ordered sequences, which are referred
  to as "character strings".

Character set, Coded Character Set:
: A mapping from code points to characters.
  Note that the shorter form term "character set" is easily
  misunderstood for a set of characters, which are called "character repertoire"

Character repertoire:
: A set of characters, in the sense of what is available as characters
  to choose from.

Code point:
: The (usually numeric) value used in a coded character set to refer
  to a character.
  Note that code points may undergo some encoding before interchange,
  see Unicode transformation format below.
  Code points can also be allocated to refer to internal constructs of
  a Unicode transformation format instead of referring to characters.

  Unicode uses unsigned numbers between 0 and 1114111 (0x10FFFF) as
  code points.

Unicode transformation format (UTF):
: Character (and character strings) usually are interchanged as a
  sequence of bytes.
  A Unicode Transformation Format or Unicode Encoding Scheme specifies
  a byte serialization for a Unicode encoding form.
  (See definition D94 in Section 3.10, Unicode Encoding Schemes.)

Code unit:
: A bit combination used for encoding text for processing or interchange.
  The Unicode Standard uses 8-bit code units in the UTF-8 encoding
  form, 16-bit code units in the UTF-16 encoding form, and 32-bit code
  units in the UTF-32 encoding form.
  (See Definition D77 in Section 3.9, Unicode Encoding Forms.)

Unicode scalar value:
: Unicode code points except high-surrogate and low-surrogate code
  points (see {{Legacy}}).
  In other words, the ranges of integers 0 to 0xD7FF and 0xE000 to
  0x10FFFF16 inclusive.
  (See Definition D76 in Section 3.9, Unicode
  Encoding Forms.)

Unicode Character:
: For the purposes of the present specification, we use "Unicode
  Character" as a synonym for "Unicode scalar value".
  Note that this term includes Unicode scalar values that are not
  Assigned Characters; for the purposes of a specification using the
  present document, it is rarely useful to make this distinction, as
  Unicode evolves and could assign characters not assigned.


# History, Legacy {#Legacy}

Some of the complexity of using Unicode is, unsurprisingly, rooted in
its long history of development.

Unicode originally was introduced around 1988 as a 16-bit character
standard.
By the early 1990s, a specification had been published, and a number
of environments eagerly picked up Unicode.
Unicode characters were represented as 16-bit values (UCS-2), enabling
a simple character model using these uniformly 16-bit values as the
characters.
Programming language environments such as those of Java, JavaScript
and C# are now intimately entangled with this character model.

In the mid-1990s, it became clear that 16 bits would not suffice.
Unicode underwent an extension to 31 bits, and then back to ~ 21 bits
(0 to 0x10FFFF).
For the 16-bit environments, this was realized by switching to UTF-16,
a "Unicode transformation format" (UTF-16).
This reassigned some of the 16-bit code points not for characters, but
for 2 sets of 1024 "surrogates" that would be used in pairs to
represent characters that do not fit into 16 bits, each supplying 10
bits to together add 2<sup>20</sup> code points to the 2<sup>16</sup>
already available (this leads to the surprising upper limit of
0x10FFFF).

In parallel, UTF-8 was created as an ASCII-compatible form of Unicode
representation that would become the dominant form of text in the Web
and other interchange environments.

The UCS-2 based character models of the legacy 16-bit platforms in
many cases couldn't be updated for fully embracing UTF-16 right away.
For instance, only much later did ECMAScript introduce the "u"
(Unicode) flag for regular expressions to have them actually match
"Unicode" characters.
So, on these platforms, UTF-16 is handled in a UCS-2 character
model, and sometimes orphaned surrogates leak out instead of Unicode
characters as "code points" in interfaces that are not meant to impose
these implementation limitations on the outside world.

UTF-8 is unaffected by this UTF-16 quirk and of course doesn't support
encoding surrogates.
(UTF-8 is careful to allow a single representation only for each
Unicode character, and enabling the alternative use of surrogate pairs
would violate that invariant, while isolated surrogates don't mean
anything in Unicode.)

Newly designed IETF protocols typically do not have to consider these
problems, but occasionally there are attempts to include isolated
surrogate code points into what we call Unicode characters here.

# Normalization

Please see {{Section 3 of -unvt}} for a brief introduction to Unicode
normalization and Normalization Form C (NFC).
However, since that section was written, additional experience with
normalization has led to the realization that the Unicode
normalization rules do not always preserve certain details in certain
writing systems.

Therefore, the implementation approach of routinely normalizing all
text before interchange has fallen out of favor.
Instead, implementations are encouraged to use text sources that
already generally use NFC except where normalization would have been harmful.
Where two text strings needs to be compared, it may be appropriate to employ
normalization to both text strings for the purposes of the comparison only.

Additional complications can come from the fact that some
implementations of applications may rely on operating system libraries
over which they have little control.
The need to maintain interoperability in such environments suggests
that receivers of MNU should be prepared to receive unnormalized text
and should not react to that in excessive ways; however, there also is
no expectation for receivers to go out of their way doing so.

# Relationship to RFC 5198

Of the mandates listed in {{mnu1d}},
the third and fourth requirement are also posed by
{{-unvt}}, while the first two remove further legacy compatibility
considerations.

{{-unvt}} contains some discussion and background material that the
present document does not attempt to repeat; the interested reader may
therefore want to consult it as an informative reference.

Mandates of {{-unvt}} that are specific to a version of Unicode are
not picked up in this specification, e.g., there is no check for
unassigned code points.
Some implementations may want to add such a check; however, in
general, this can hinder further evolution as it may become hard to
use new characters as long as not every component on the way has been
upgraded.
(see also {{version}}.)


## Going beyond RFC 5198

The handling of line endings (with 2D MNU prescribing LF as the line
terminator, and adding or specifying CRLF line endings as variances)
may be controversial.
In particular, calling out CR-tolerance as an extra (and often
undesirable) feature may seem novel to some readers.
The handling as specified here is much closer to the way line endings
are handled on the software side than the cumbersome rules of {{-unvt}}.
More generally speaking, one could say that the present specification
is intended to be used by state of the art protocols going forward,
maybe less so by existing protocols.

Even in the "with CR-tolerant lines" variance, the CR character is
only allowed as an embellishment of an immediately following LF
character.  This reflects the fact that overprinting has only seen
niche usage for quite a number of decades now.

Unicode Line and Paragraph separators probably seemed like a good idea
at the time, but have not taken hold.
Today, their occurrence is more likely to trigger a bug or even serve
as an attack.

HT characters ("TABs") were needed on ASR33 terminals to speed up
processing of blank spaces at 110 bit/s line speed.
Unless some legacy applications require compatibility with this
ancient and frequently varied convention, HT characters are no longer
appropriate in Modern Network Unicode.
In support of legacy compatibility cases that do require tolerating
their use, the "with HT characters" variance is defined.

The version-nonspecific nature of MNU creates some fuzziness that may
be undesirable but is more realistic in environments where
applications choose the Unicode version with the Unicode library that
happens to be available to them.

## Byte order marks

For UTF-8, there is no encoding ambiguity and thus no need for a byte
order mark.
However, some systems have made regular of a leading U+FEFF character
in UTF-8 files, anyway, often in order to mark the file as UTF-8 in
case other character codings are also in use and metadata is not
available.
This destroys the ASCII compatibility of UTF-8; it also creates
problems when systems then start to expect a BOM in UTF-8 input and
none is provided.
Section 6 of {{-utf8}} also RECOMMENDS not using Byte Order Marks with
UTF-8 when it is clear that this charset is being used, but does not
phrase this as an unambiguous mandate, so we add that here (as did
{{-unvt}}), unless permitted by a variance.

Some background on the prohibition of byte order marks: The 16-bit and
32-bit encodings for Unicode are available in multiple byte orders.
The byte order in use in a specific piece of text can be provided by
metadata (such as a media type) or by prefixing the text with a "Byte
Order Mark", U+FEFF.  Since code point U+FFFE is never used in
Unicode, this unambiguously identifies the byte order.



# Acknowledgements
{: numbered='no'}

Klaus Hartke and Henk Birkholz drove the author out of his mind enough
to make him finally write this up.  James Manger, Tim Bray and Martin
Thomson provided comments on an early version of this draft.
