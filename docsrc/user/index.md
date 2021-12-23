# User Guide

(installing-data)=
## Installing data

A Data Library dataset is made up of a set of files and/or database tables containing the data, and a *catalog entry*, which is a set of instructions for reading the data and transforming it into the Data Library's internal representation. Training materials covering how to create these resources will be provided separately; in the current document, we will assume that the resources have already been created, and explain how to install them on a Data Library server.

### Data files
Data files (*e.g.* NetCDF, GRIB, or GeoTIFF files) should be installed in `/data/datalib/data`. All members of the group `datag` have permission to create and modify files this directory.

Any files and directories that are *created* under `/data/datalib/data` will also be writable by anyone in the `datag` group, but files created elsewhere, *e.g.* in a user's home directory, and then *moved* into `/data/datalib/data` may not be modifiable by other users, which can cause problems if the dataset needs to be updated when the owner is unavailable. The easiest way to avoid this problem is to transfer files directly into `/data/datalib/data` when loading them onto the machine. If you have already created a directory `/data/datalib/data/mydir` with the wrong permissions, you can correct them with the following commands:

    chgrp -R datag /data/datalib/data/mydir
    find /data/datalib/data/mydir -type d -exec chmod g+s {} \;

### Database tables

The following command opens a `psql` session to the PostgreSQL database, allowing you to enter SQL queries interactively:

    sudo /usr/local/bin/sql_interactive

All members of the `datag` group can run this command, even if they don't have permission to use `sudo` more generally. If the SQL statements you want to run are in `~/myfile.sql`, you can use the following command:

    sudo /usr/local/bin/sql_interactive < ~/myfile.sql


## Dataset and maproom development

## Catalog entries

The main data catalog is located in `/usr/local/datalib/dlentries`. This copy of the catalog is read-only for non-admin users. The procedure for creating or modifying catalog entries is to push the changes to your git host and then run the `update_datalib` script as described in [](update-script) below.

Each user also has his/her own personal data catalog at

    /data/datalib/home/<username>/DataCatalog

You can create and edit `index.tex` files in this directory and then test them by visiting

    http://<servername>/home/.<username>/

in a browser, in order to validate your changes before pushing them to your git host. Your personal data catalog can reference files in either `/data/datalib/data` or `/data/datalib/home/<username>`.

## Maprooms

The maproom HTML pages are located in `/usr/local/datalib/maproom`. As for the main data catalog, these files are read-only; all changes must be made via git. Unlike for data catalog development, maproom development typically takes place on a developer's own computer rather than on the server. This process is documented elsewhere.

(update-script)=
## Installing updates

When you have created or modified a maproom or a dataset and you want to install your changes on the server, perform these steps:

- Commit your changes and push them to your git host.

- On the server, run the following command:

        sudo /usr/local/bin/update_datalib

    This command pulls the latest versions of the dlentries and maproom repositories, and builds and installs the maproom HTML files from their templates.
