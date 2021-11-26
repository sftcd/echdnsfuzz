# echdnsfuzz

Collection of zone file stanzas containing dodgy HTTPS RR values, to help with fuzz testing ECH.

Entries should be files suitable for inclusion in a standard zone file. Each such file should be
called ``<something>.bad.stanzas``. The owner name for all stanzas should be the string "OWNER"
and the TTL for all stanzas should be either a number of the string "TTL" so that both can be
overridden to match where the values will be published in the DNS. 

Each file should also contain a TXT RR that describes the specific badness to help with 
identifying cases where these DNS RR values produce errors in someone's ECH client code.

Seems useful to also include a comment in each file as to how it was generated.

Here's an example: [publen.bad.stanza](publen.bad.stanza) - that has one 
HTTPS RR, containing an ECHConfigList with 3 entries, the first of which has a
bad length (0x21 instead of 0x20) for the x25519 public key contained therein.

The above rules are for my script that includes such stanzas in my zone file.
In the next day or so, once I've modified my cronjobs, then each hour, (at :33)
that script wll randomly select one such file to publish with an owner of
``_13413._https.draft-13.esni.defo.ie`` and, if not overidden, a TTL of 10
seconds.  If the selected file is somehow malformed, then no bad records will be
published that hour, but hopefully that'll not happen much.

Happy to take PRs.
