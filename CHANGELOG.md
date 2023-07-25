# IOTstackAliases Change Summary

* 2023-07-25

	- Changes behaviour of `TERMINATE` alias depending on the version of `docker-compose` that is installed. If v2.19.0 or later, `TERMINATE` utilises `docker-compose down` which cleans up dangling resources such as internal networks that are no longer in use.
