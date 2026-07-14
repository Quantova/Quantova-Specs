# Canonical encoding

Status. This document is normative. It sits under the crypto policy. If anything conflicts with that policy, stop and report.

## Purpose

Every value has exactly one valid encoding. Two encoders that follow this specification produce the same bytes for the same value, and a decoder rejects any input that is not the canonical form. This one to one property is what lets the Airlock treat the wire as a grammar, so a classical artifact and any malformed input have no valid form and are unparseable.

## Integers

Integers are unsigned and fixed width. They are encoded little endian in the natural width of the type, meaning one, two, four, eight, or sixteen bytes. There is no variable length integer for a fixed width type, because that would give a value more than one encoding. Signed integers are not used on the wire.

## Byte strings and variable length fields

A variable length field is encoded as its length followed by its bytes. The length is a fixed width unsigned integer whose width is stated by the field. A decoder rejects a length that does not match the bytes that follow, and it rejects any trailing bytes after the last declared field.

## Structures

A structure is the concatenation of its fields in the order the specification lists them. There is no padding and no optional presence flag unless the specification states one. Where a field is optional, a single leading byte states whether it is present, and only the values zero and one are accepted for that byte.

## Enumerations and tags

A tagged value begins with a single tag byte that selects the variant, followed by that variant encoded by this specification. Only the tag values the specification assigns are accepted. An unknown tag is unparseable, which is how a classical scheme identifier at ingress is refused.

## Hashes and identifiers

A hash is a fixed 32 byte value on the wire. When a hash, an address, or a key becomes a value that a person or a developer can see, it is rendered in the Q format described in the accounts specification, never in Ethereum hex. The wire carries raw bytes and the rendered string carries the Q format.
