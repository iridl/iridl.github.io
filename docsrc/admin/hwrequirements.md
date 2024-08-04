(hwrequirements)=
# Server Requirements
The Data Library can be run on a single server/workstation, providing it has enough CPU power, memory, disk space, and internet bandwidth. Each
installation is different, so determining your requirements depends on the performance you expect to achieve, how many people will be using it at
the same time and how much data you intend to store on the server.  The following are general recommendations. If you have questions about the
server you intend to use, please contact IRIDL Help <help@iri.columbia.edu> to review your server configuration. You should have room for at least
4 disks in your server.  While using SSD disks for your operating system is a good idea, it's not entirely necessary.  But if you can get a server that
has the capacity for 2 operating system disks (SSD or NVME) and 2 Data disks, this would be the ideal server.  An external USB drive for backups is also
recommended.  Ideally the server should have at least 4 internal drive bays.  You will be installing the CentOS7 operating system, so don't pay for an operating
system if you can avoid it.

Typical costs for servers with the appropriate specifications range from \$3,000 to \$5,000 in the United States.  Plus an additional cost for the UPS.

## CPU
For a minimal server, the basic requirements include a high performance CPU such as Intel Xeon or AMD Epyc, with a minimum of 8 cores.
For a more powerful server, we'd recommend a dual core server.

## RAM
The ability of the Data Library to process large datasets depends on the amount of memory installed on the system.  While a Data Library may work with
as little as 32GB of RAM, we recommend a minimum of 64GB. If you have 2 processors, you should double that to 128GB.  The more memory you can install,
the better the performance you will have.  For most cases, 128GB will be more than enough.

## Disk Space
We recommend using 2 disks for the operating system, configured as mirror devices.  For best results, use fast disks such as SSDs or NVME for
the operating system.  These can be as small as 128GB. If you want to speed up DL results, increase this to 256GB and place the squid cache on this device.

You will need space for your Datasets.  We recommend getting multiple disks, so you can mirror the drive and an external disk to back up the data regularly.
Mirroring is not considered a backup, it is simply a convenience for higher availability.  A separate backup of your data is very important. The amount of disk
space you may need is unique to your installation.  We suggest getting disks that are about twice the size you think you need.  You will use 2 disks to mirror
the actual data and one disk, preferably an external USB disk, to back up your data.

## Networking
You should use the highest network speed you have access to.  If you have a 10Gb network, you should try to place your server on this network, with a 10Gb
network card on your server.  But this is entirely optional.  Whatever network you have access to will work.

## Graphics
The Data Library will not take advantage of any GPU card you have installed.  A simple graphics card is sufficient.

## UPS
An uninterruptible power supply that can power your server for whatever downtime you're likely to run into.  Typically a 1250VA UPS is sufficient, but it
will depend on your server.

### Basic Requirements
* 1 Intel Xeon processor, 4-8 cores
* 32 to 64GB RAM
* 128 to 256GB HD for Operating System, preferably SSD or NVME
  * Optionally, two disks mirrored
* (2) internal Enterprise class 4TB hard drives for data, mirrored
  * disks large enough to hold all the data you require plus room for expansion
* (1) external HD for backups
  * the same size as the internal hard drives.

### High-End Requirements
* 2x Intel Xeon Scalable, 8 core or higher
* 64 - 128GB RAM
* 128	to 256GB HD for	Operating System, preferably SSD or NVME
  * Optionally, two disks mirrored
* (2) internal Enterprise class 4TB hard drives for data, mirrored
  * disks large enough to hold all the data you require plus room for expansion
* (1) external HD for backups
  * the same size as the internal hard drives.

