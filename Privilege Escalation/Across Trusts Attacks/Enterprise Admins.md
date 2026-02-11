
The `sIDHistory` attribute is the primary mechanism used to make these cross-domain attacks powerful.

- **Purpose**: It is intended for users moving between domains; their old Security Identifier (SID) is stored in `sIDHistory` so they don't lose access to old resources.
- **Abuse**: Attackers can add the SID of the **Enterprise Admins** group (from the parent domain) into the `sIDHistory` of a ticket they forge in the child domain.

- **Methods**: This can be achieved by compromising the 
1. `krbtgt` hash of the child domain or 
2. by using trust tickets as described in the Trust Key Abuse flow.


# Diagram Overview :

```
[ CHILD DOMAIN ]                  [ PARENT DOMAIN ]
  (Compromised)                     (Target)
       |                               |
  [ Child DC ] <--- Trust Key ---> [ Parent DC ]
       |            (Stolen)           |
       |                               |
  [ Attacker ] --(Forged TGT w/ SID)-->|
               (Enterprise Admin SID)  |--[ ST issued ]--> [ Success ]
```


