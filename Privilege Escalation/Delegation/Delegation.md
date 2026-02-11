
*  Kerberos Delegation allows to "reuse the end-user credentials to access resources hosted on a different server".
*  This is typically useful in multi-tier service or applications where Kerberos Double Hop is required. For example, users authenticates to a web server (first hop) and web server makes requests to a database server (second hop).
*  User impersonation is the goal of delegation.

```
            First Hop        Second Hop
               |                  |
            
User   -->  Web Server   -->  Database Server

```

* There are two types of Kerberos Delegation:

1. General/Basic or Unconstrained Delegation - Allows the first hop (web server in our example) to request access to any service on any computer in the domain.
2.  Constrained Delegation - Allows the first hop to request access only to specified services on specified computers. If Kerberos authentication is not used to authenticate to the first hop, Protocol Transition is used to transition the request to Kerberos.



