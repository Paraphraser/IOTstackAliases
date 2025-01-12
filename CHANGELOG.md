# IOTstackAliases Change Summary

* 2025-01-12

	- Now re-checks whether *running* containers have existing aliases and adds those dynamically. This means you can add a new container and you will get a `container_SHELL` alias automatically the next time you login.
	- There is no longer any need to logout and login to make the aliases become available. Any time the script senses a need to create an alias, it is both added to the cache (which saves time on future logins) and applied dynamically to the current session.
	- Does not prune obsolete aliases. The only way to get rid of obsolete aliases is to either edit the cache file by hand, or erase it then logout and login so one alias is created for each running container.

* 2024-07-08

	- Further deprecate `TERMINATE` (already aliased to `down`) by removing from inline documentation in favour of `DOWN`.
	- Updated main README to reflect this.

* 2023-07-25

	- Changes behaviour of `TERMINATE` alias depending on the version of `docker-compose` that is installed. If v2.19.0 or later, `TERMINATE` utilises `docker-compose down` which cleans up dangling resources such as internal networks that are no longer in use.
