# Maintenance

(debugging)=
## Debugging tips

- To see what services are running under docker, use `sudo docker ps`.

- To start and stop services, cd to `/usr/local/datalib` and then use `docker-compose`, *e.g.*

        cd /usr/local/datalib
        sudo docker-compose up -d maproom

- Most services output logs to stdout, which is captured by the docker daemon and routed to journald. You can read the logs by using `journalctl`, *e.g.*

        sudo journalctl CONTAINER_NAME=datalib_maproom_1 --since='1 hour ago'


- squid produces two separate logs: the error log and the access log. The latter contains a line for each request served. The error log is piped to journalctl, while the access log is written to a docker volume. To read the access log, exec into the squid container:

        sudo docker exec -it datalib_squid_1 tail -n 100 /var/log/squid/access.log


- If an embedded image in a maproom is broken/empty, copy the image URL to a new browser window and remove the .gif at the end. Sometimes this will get you an informative error message instead of just an empty response.

- If there’s an error message about a specific file or database table, look into that as described below. If no specific file or table is mentioned, identify the datasets that are used in the query, and read the catalog entries (dlentries) for those datasets to identify the files and/or tables that they use.

- To check on a database table, exec into the postgres container and use psql. If the table doesn’t exist, either run the sql script that creates it, or add it to the sql scripts if it’s missing. If the table exists, check that the readonlyaccess role has select permission for it. (Our install process is supposed to grant that permission, but in rehearsing the installation we have sometimes needed to grant it by hand.)
