# [Dropbear](https://matt.ucc.asn.au/dropbear/dropbear.html), a smallish SSH server and client.

[![Version](https://img.shields.io/badge/dynamic/json?color=blue&label=Version&query=%24%5B%27name%27%5D&url=https%3A%2F%2Fapi.github.com%2Frepos%2Fmkj%2Fdropbear%2Freleases%2Flatest)](https://github.com/mkj/dropbear/releases/latest)

[INSTALL](INSTALL) has compilation instructions.

[MULTI](MULTI) has instructions on making a multi-purpose binary (i.e., a single binary
which performs multiple tasks to save disk space)

[SMALL](SMALL) has some tips on creating small binaries.


## Generic Notes

### Server public key auth:

You can use `~/.ssh/authorized_keys` in the same way as with OpenSSH, just put
the key entries in that file. They should be of the form:

```bash
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAIEAwVa6M6cGVmUcLl2cFzkxEoJd06Ub4bVDsYrWvXhvUV+ZAM9uGuewZBDoAqNKJxoIn0Hyd0Nk/yU99UVv6NWV/5YSHtnf35LKds56j7cuzoQpFIdjNwdxAN0PCET/MG8qyskG/2IE2DPNIaJ3Wy+Ws4IZEgdJgPlTYUBWWtCWOGc= someone@hostname
```

You must make sure that `~/.ssh` and the key file are only writable by the
user. Beware of editors that split the key into multiple lines.

Dropbear supports some options for `authorized_keys` entries; see the manpage.

### Client public key auth:

Dropbear can do public key auth as a client, but you will have to convert
OpenSSH style keys to Dropbear format, or use `dropbearkey` to create them.

#### Generating Private Keys

If you have an OpenSSH-style private key `~/.ssh/id_rsa`, you need to do:

```bash
dropbearconvert openssh dropbear ~/.ssh/id_rsa  ~/.ssh/id_rsa.db
dbclient -i ~/.ssh/id_rsa.db <hostname>
```

Dropbear does not support encrypted hostkeys though it can connect to ssh-agent.

If you want to get the public-key portion of a Dropbear private key, look at
dropbearkey's `-y` option.

#### Generating Server Keys

To run the server, you need to generate server keys, this is one-off:

```bash
./dropbearkey -t rsa -f dropbear_rsa_host_key
./dropbearkey -t dss -f dropbear_dss_host_key
./dropbearkey -t ecdsa -f dropbear_ecdsa_host_key
./dropbearkey -t ed25519 -f dropbear_ed25519_host_key
```

or alternatively convert OpenSSH keys to Dropbear:

```bash
./dropbearconvert openssh dropbear /etc/ssh/ssh_host_dsa_key dropbear_dss_host_key
```

You can also get Dropbear to create keys when the first connection is made, 
which is preferable to generating keys when the system boots. Make sure 
`/etc/dropbear/` exists and then pass `-R` to the dropbear server.

## Miscellaneous

- If the server is run as non-root, you most likely won't be able to allocate a
`pty`, and you cannot login as any user other than that running the daemon
(obviously). Shadow passwords will also be unusable as non-root.
- The Dropbear distribution includes a standalone version of OpenSSH's `scp`
program. You can compile it with `make scp`; you may want to change the path
of the ssh binary, specified by `_PATH_SSH_PROGRAM` in `options.h`. By default,
the progress meter isn't compiled to save space. You can enable it by 
adding `SCPPROGRESS=1` to the make commandline.

## Contact

Please contact me if you have any questions/bugs found/features/ideas/comments etc :)
There is also a mailing list http://lists.ucc.gu.uwa.edu.au/mailman/listinfo/dropbear

Matt Johnston
matt@ucc.asn.au
