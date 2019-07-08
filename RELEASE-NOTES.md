Inscope Metrics Parent POM
==========================

2.1.0 - TBD
------------------------
* Use `maven.gpg.plugin.version` property which locks to version `1.6`
* Set `trimStackTrace` property to `false` on Failsafe plugin
* Set `quiet` property to `true` on Javadoc plugin
* Activate `display-plugin-updates` goal on Versions plugin only locally (e.g. outside of Travis; where it continues to fail)

2.0.5 - June 30, 2019
------------------------
* Initial release to `io.inscopemetrics.build`.
