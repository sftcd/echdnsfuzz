# echdnsfuzz

Collection of zone file stanzas containing dodgy HTTPS RR values, to help with fuzz testing ECH.

Entries should be files suitable for inclusion in a standard zone file. Each file should be
called <something>.bad.stanzas. The owner name for all stanzas should be the string "OWNER"
and the TTL for all stanzas should be either a number of the string "TTL". 

Each file should also contain a TXT RR that describes the specific badness.

Here's an example: [publen.bad.stanza](badpublen.bad.stanza) - that has one 
HTTPS RR, containing an ECHConfigList with 3 entries, the first of which has a
bad length (0x21 instead of 0x20) for the x25519 public key contained therein.

Those rules are for my script that includes such stanzas in my zone file. As of now, each
hour, (at :33) that script randomly selects one such file to publish with an owner of 
``_13413._https.draft-13.esni.defo.ie`` and, if not overidden, a TTL of 10 seconds.
If a selected file is malformed, then no bad records will be published that hour.

Happy to take PRs.
