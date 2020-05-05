Inscope Metrics Parent POM
==========================

2.2.0 - TBD
------------------------
* Updated Findbugs and Checkstyle.
* Latest build-resources.

2.1.0 - July 15, 2019
------------------------
* Use `maven.gpg.plugin.version` property which locks to version `1.6`
* Set `trimStackTrace` property to `false` on Failsafe plugin
* Set `quiet` property to `true` on Javadoc plugin
* Activate `display-plugin-updates` goal on Versions plugin only locally (e.g. outside of Travis; where it continues to fail)
* Update to io.inscopemetrics build-resources release version `2.0.3`

2.0.5 - June 30, 2019
------------------------
* Initial release to `io.inscopemetrics.build:parent-pom`.

Published under Apache Software License 2.0, see LICENSE

&copy; Inscope Metrics, 2019
