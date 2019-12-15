# The Sections of a Cadmium Extension (CE) document
## Introduction
The introduction to a CE document should contain description of the extension and example of problems which this extension can solve.

## Message type identifiers
In this section, specify the identifiers of the new types of protocol messages (which are introduced by the extension)

## Glossary
If your CE document uses terms that may not be familiar to the reader, please define them in this section.

## Use Cases
It is recommended that document authors structure their proposals according to the use cases that the proposal will address. We have found that use cases force authors to focus on functionality rather than "protocol for the sake of protocol". It is also helpful to sort use cases by actor. Include one subsection for each use case.

When writing use cases and the associated protocols, make sure to:

* Clearly define the success scenarios, alternate flows, and possible errors.
* Describe the expected behavior of Cadmium clients, servers, and components when using this protocol.
* Include lots of protocol examples.

*Example 1. An Example from Shakespeare*
```json
{
    "id": "abcd",
    "type": "profile:login",
    "to": "cadmium.org",
    "payload": {
        "username": "juliet",
        "password": "romeo1"
    }
}
```
## Error Codes
If your proposal defines a number of error and status codes, it is a good idea to include a table of all the codes defined in your document.

## Business Rules
You may want to include a section describing various business rules (essentially, a variety of MUSTs, SHOULDs, and MAYs regarding application behavior). This is not required but can be helpful to implementers.

## Implementation Notes
You may want to include a section devoted to implementation notes. Again, this is not required but can be helpful to implementers.

## Internationalization Considerations
If there are any internationalization or localization issues related to your proposal, define them in this optional section.

## Security Considerations
Your proposal MUST include a section entitled "Security Considerations". Even if there are no security features or concerns related to your proposal, you MUST note that fact. For helpful guidelines, refer to RFC 3552.

## JSON Schema
An JSON Schema is required in order for protocols to be approved by the Cadmium Developers. The Cadmium Developers team can assist you in defining an JSON Schema for the protocol you are proposing. Also you can define your schema as TypeScript interfaces, this is also allowed.

## Acknowledgements (optional)
Most CE documents end with a section thanking non-authors who have made significant contributions or who have provided feedback regarding the specification.

# Cadmium Extension Styleguide
CE document are written in English. It is not expected that you will be a fine prose writer, but try to write in a clear, easily-understood fashion.

## Code Examples
To show the hierarchy of JSON objects, indent two spaces for every level.

If an element possesses a large number of attributes, include a line break before each attribute and indent them so that they are vertically aligned for readability.

If the JSON data of an element is long, include line breaks and indent by two spaces.

*Example*:
```json
{
    "id": "abcd",
    "type": "profile:register",
    "to": "cadmium.org",
    "payload": {
        "username": "juliet",
        "thirdPIDs": [
            {"type":"email", "value":"juliet@capulett.com"},
            {"type":"msisdn", "value":"+1234567890"},
        ],
        "password": "romeo1"
    }
}
```
Some examples include strings that are the output of a hashing algorithm such as SHA-1 or SHA-256. An easy way to generate these is to use the OpenSSL "dgst" command to generate the hash. For example, the following command will generate the SHA-1 hash `a6cf4baabcefb63189a1a1c56158aa431990bba9`:
```
echo -n '@juliet@396277b7dcd0f1173f2007baa604de7593529cc3fbf335fb7924851cb25c1fdf' | openssl dgst -hex -sha1
```
    
Some examples include strings that are encoded using Base64. An easy way to generate these is to use the OpenSSL "enc" command to generate the base64-encoded equivalent. For example, the following command will generate the base64-encoded string `QGp1bGlldEAzOTYyNzdiN2RjZDBmMTE3M2YyMDA3YmFhNjA0ZGU3NTkzNTI5Y2MzZmJmMzM1ZmI3OTI0ODUxY2IyNWMxZmRm`:
```
echo -n '@juliet@396277b7dcd0f1173f2007baa604de7593529cc3fbf335fb7924851cb25c1fdf' | openssl enc -nopad -base64
``` 
## Conformance Terms
Conformance terms (e.g,, "MUST" and "SHOULD") are specified in RFC 2119. Use them. When such terms are not in ALL CAPS, the special conformance sense does not apply (although it is preferable to use terms such as 'might' instead of 'may' and 'ought' instead of 'should').