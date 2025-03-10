opengist-port
=============

FreeBSD [port][4] script for `opengist`.

You can find `opengist` [here][1]

> Adds LDAP support via https://github.com/thomiceli/opengist/pull/385

> NOTE: This is not commited into mainline ports tree.

Installation
------------

Copy `www/opengist` folder to `/usr/port` directory.

NOTE: If your port directory is different from above, copy to the respective
place.

Usage
-----

Once you have copied the folder, install it as you would do for any port.

`$ cd /usr/port/www/opengist`<br>
`$ make install clean`

For a list of dependencies for the build check [here][2]

Credits
-------

* `opengist` is developed and maintained by [Thomas Miceli][3]

License
-------

BSD 2-clause. See LICENSE.

[1]: https://github.com/thomiceli/opengist
[2]: https://opengist.io/docs/installation.html
[3]: https://github.com/thomiceli
[4]: http://freshports.org/www/opengist

