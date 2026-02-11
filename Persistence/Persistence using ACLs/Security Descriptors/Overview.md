*  It is possible to modify Security Descriptors (security information like Owner, primary group, DACL and SACL) of multiple remote access methods (securable objects) to allow access to non-admin users.
* Administrative privileges are required for this.
*  It, of course, works as a very useful and impactful backdoor mechanism.
* Security Descriptor Definition Language defines the format which is used to describe a security descriptor. SDDL uses ACE strings for DACL and SACL:
`ace_type;ace_flags;rights;object_guid;inherit_object_guid;account_sid`

* ACE for built-in administrators for WMI namespaces :
`A;CI;CCDCLCSWRPWPRCWD;;;SID`

