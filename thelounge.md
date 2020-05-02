# TheLounge Installation
Download from <https://github.com/thelounge/thelounge/releases/tag/v4.1.0>
```
apt-get install curl software-properties-common
curl -sL https://deb.nodesource.com/setup_12.x | bash -
apt-get install nodejs
apt install ./thelounge.deb
systemctl start thelounge
systemctl enable thelounge
systemctl status thelounge
```

## Active Directory Configuration
`/etc/thelounge/config.js`

```
ldap: {
        // - `enable`: when set to `false`, LDAP support is disabled and all other
        //   values are ignored.
        enable: true,

        // - `url`: A url of the form `ldaps://<ip>:<port>`.
        //   For plain connections, use the `ldap` scheme.
        url: "ldap://pdc.example.com",

        // - `tlsOptions`: LDAP connection TLS options (only used if scheme is
        //   `ldaps://`). It is an object whose values are Node.js' `tls.connect()`
        //   options. It is set to `{}` by default.
        //   For example, this option can be used in order to force the use of IPv6:
        //   ```js
        //   {
        //     host: 'my::ip::v6',
        //     servername: 'example.com'
        //   }
        //   ```
        tlsOptions: {},

        // - `primaryKey`: LDAP primary key. It is set to `"uid"` by default.
        primaryKey: "sAMAccountName",

        // - `baseDN`: LDAP base DN, alternative to `searchDN`. For example, set it
        //   to `"ou=accounts,dc=example,dc=com"`.
        //   When unset, the LDAP auth logic with use `searchDN` instead to locate users.

        // - `searchDN`: LDAP search DN settings. This defines the procedure by
        //   which The Lounge first looks for the user DN before authenticating them.
        //   It is ignored if `baseDN` is specified. It is an object with the
        //   following keys:
        searchDN: {
                //   - `rootDN`: This bind DN is used to query the server for the DN of
                //     the user. This is supposed to be a system user that has access in
                //     read-only to the DNs of the people that are allowed to log in.
                //     It is set to `"cn=thelounge,ou=system-users,dc=example,dc=com"` by
                //     default.
                rootDN: "cn=viewer,ou=Server-Users,dc=example,dc=com",

                //   - `rootPassword`: Password of The Lounge LDAP system user.
                rootPassword: "password",

                //   - `ldapFilter`: it is set to `"(objectClass=person)(memberOf=ou=accounts,dc=example,dc=com)"`
                //     by default.
                filter: "(objectCategory=person)(memberOf:1.2.840.113556.1.4.1941:=CN=TheLounge Group,OU=Groups,dc=example,dc=com)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))",

                //   - `base`: LDAP search base (search only within this node). It is set
                //     to `"dc=example,dc=com"` by default.
                base: "dc=example,dc=com",

                //   - `scope`: LDAP search scope. It is set to `"sub"` by default.
                scope: "sub",
        },
},
```