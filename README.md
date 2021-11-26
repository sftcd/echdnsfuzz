# echdnsfuzz

Collection of zone file stanzas containing dodgy HTTPS RR values, to help with fuzz testing ECH.

Entries should be files suitable for inclusion in a standard zone file. Each such file should be
called ``<something>.bad.stanza``. The owner name for all stanzas should be the string "OWNER"
and the TTL for all stanzas should be either a number of the string "TTL" so that both can be
overridden to match where the values will be published in the DNS. 

Each file should also contain a TXT RR that describes the specific badness to help with 
identifying cases where these DNS RR values produce errors in someone's ECH client code.

Seems useful to also include a comment in each file as to how it was generated.

Here's an example: [publen.bad.stanza](publen.bad.stanza) - that has one 
HTTPS RR, containing an ECHConfigList with 3 entries, the first of which has a
bad length (0x21 instead of 0x20) for the x25519 public key contained therein.
To access these values, once published:

```
# Read the descriptive TXT RR
$ dig +short TXT _13413._https.draft-13.esni.defo.ie
"publen.bad.stanza: 0x21 length for x25519 public instead of 0x20"
# Read the HTTPS RR
$ dig +short -t TYPE65 _13413._https.draft-13.esni.defo.ie
\# 201 000100000500C200C0FE0D003C0200200021AE5F0D36FE5516C60322 C21859CE390FD752F1A13C22E132F10C7FE032D54121000400010001 000D636F7665722E6465666F2E69650000FE0D003C8700200020E4E2 5EFC9C409E41395DE1B61EF30752FF0BEAC26F949471099BE9BAD51E 5C71000400010001000D636F7665722E6465666F2E69650000FE0D00 3CA50020002073E95F87091F2770106036AD6680A955D768193694E3 D648B50A707B8B8CBC0C000400010001000D636F7665722E6465666F 2E69650000

```

In our [defo.ie](https://defo.ie) deployment, each hour, (at :33) a cronjob
randomly selects one of the bad stanza files from this repo to publish with an
owner of ``_13413._https.draft-13.esni.defo.ie`` and, if not overidden, a TTL
of 10 seconds.  If the selected file is somehow malformed, then no bad records
will be published that hour, but hopefully that'll not happen much.
ECH-enabled clients can then attempt connection to draft-13.esni.defo.ie port
13413 (where we do have an ECH enabled instance of lighttpd running).  In
probably most cases the client should exit before making a connection, in other
cases, the client may connect and have it's ECH attempt treated as GREASE.

Happy to take PRs, especially with additional test cases.


