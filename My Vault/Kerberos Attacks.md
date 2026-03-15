#activedirectory #activedirectory/protocols #kerberos
## Kerberoasting

When an attack requests a TGS ticket for service accounts and cracks the service account's password. As Kerberos encrypts the TGS ticket with service account's password.

If the encryption is weaker the ticket is easier to crack.

To perform this attack, attacker needs a low-privilege domain account.

## AS-REP roasting

This attack targets the user account that have pre authentication disabled. 

To perform this attack, attacker needs a low-privilege domain account.

## Pass the Ticket

Attacker dump the Kerberos tickets from the LSASS memory and use them to impersonate as the user without needing the password.

To perform this attack, attacker needs access to a domain computer.

## Golden Ticket Attack

When attacker steal krbtgt account's NTLM hash, they can forge tickets to access any resource in the domain, by injecting the forged tickets into current session.

The krbtgt hash can be obtained via DCsync or either compromising DC.

## Silver Ticket Attack

A silver ticket attack is performed by forging a service ticket using the service account's password hash. This attack unlike, golden ticket, only gives access to the specific server.

## DCSync Attack

Its an attack where attacker compromises a user account replication rights, using these replication rights, attacker can send a replication request requesting credentials for accounts such as krbtgt account enabling them to perform golden ticket attack gaining full domain compromise.

## Common Mitigations

