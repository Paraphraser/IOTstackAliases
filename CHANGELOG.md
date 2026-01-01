# IOTstackAliases Change Summary

* 2026-01-01

	- All previous versions of this repo have used this basic syntax for the relevant aliases:
    
	    ```
	    docker compose -f ~/IOTstack/docker-compose.yml «other arguments here»
	    ```
    
    	This avoids the need to `cd` into the project directory. However, it turns out that this syntax prevents `docker-compose.override.yml` from being applied correctly. The solution is to adjust the basic syntax:
		    
		```
		docker compose --project-directory ~/IOTstack «other arguments here»
		```
    
	- Because it is useful to be able to preview the effect of applying override files, a `CONFIG` alias has been added which invokes:

		```
		docker compose --project-directory ~/IOTstack config {«container»...}
		```

* 2025-09-16

	- remove conditional check for `docker-compose`-related aliases which were not being created if `~/IOTstack/docker-compose.yml` did not exist. Although aliases like `UP` will not actually work if the compose file is missing, the check creates a chicken-and-egg problem on clean installs. A subsequent *restore from backup* or a *menu run* will create the initial compose file but aliases like `UP` will still not work until `dot_iotstack_aliases` is sourced again (eg logout, login). This is suboptimal.

* 2025-09-13

	- fix long-standing logic bug in shell discovery. Was testing for rc=1 on assumption no container would include all four shells.

		> The exception proving the rule was the Home Assistant `addon_a0d7b954_ssh` container.

* 2025-09-09

	- silent exit if prerequisites (docker, jq) not satisfied
	- discover and persist `docker-compose` vs `docker compose`
	- better use of variables to facilitate future maintenance
	- `TERMINATE` removed (deprecated 2 years ago)
	- restructure hints displayed with aliases file is sourced
	- general code restructure

* 2025-01-12

	- Now re-checks whether *running* containers have existing aliases and adds those dynamically. This means you can add a new container and you will get a `container_SHELL` alias automatically the next time you login.
	- There is no longer any need to logout and login to make the aliases become available. Any time the script senses a need to create an alias, it is both added to the cache (which saves time on future logins) and applied dynamically to the current session.
	- Does not prune obsolete aliases. The only way to get rid of obsolete aliases is to either edit the cache file by hand, or erase it then logout and login so one alias is created for each running container.

* 2024-07-08

	- Further deprecate `TERMINATE` (already aliased to `down`) by removing from inline documentation in favour of `DOWN`.
	- Updated main README to reflect this.

* 2023-07-25

	- Changes behaviour of `TERMINATE` alias depending on the version of `docker-compose` that is installed. If v2.19.0 or later, `TERMINATE` utilises `docker-compose down` which cleans up dangling resources such as internal networks that are no longer in use.
