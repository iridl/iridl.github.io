(hwrequirements)=
# Server Requirements
The Data Library can be run on a single server/workstation, providing it has enough CPU power, memory, disk space, and internet bandwidth. Each
installation is different, so determining your requirements depends on the performance you expect to achieve, how many people will be using it at
the same time and how much data you intend to store on the server.  The following are general recommendations. If you have questions about the
server you intend to use, please contact IRIDL Help <help@iri.columbia.edu> to review your server configuration.  While using SSD disks for your operating 
system is a good idea, it's not necessary. You will be installing the CentOS7 operating system, so don't pay for an operating system if you can avoid it.

Typical costs for servers with the appropriate specifications range from \$3,000 to \$5,000 in the United States.  Plus an additional cost for the UPS.

## CPU
For a minimal server, the basic requirements include a high performance CPU such as Intel Xeon or AMD Epyc, with a minimum of 8 cores.
For a more powerful server, we'd recommend a dual core server.

## RAM
The ability of the Data Library to process large datasets depends on the amount of memory installed on the system.  While a Data Library may work with
as little as 32GB of RAM, we recommend 64GB. If you have 2 processors, you should double that to 128GB.  The more memory you can install,
the better the performance you will have.  For most cases, 128GB will be more than enough.

## Storage
You _can_ put everything on one disk if necessary, but we recommend mirroring across 2 disks in case of a single disk failure.  For best results, 
use fast disks such as SSDs or NVME for the operating system.  These can be as small as 128GB for the OS. If you want to speed up DL results, increase this to 256GB 
and place the squid cache on this device.  You should try to get Enterprise hard disk drives.  They are more generally reliable and last significantly longer.

You will also need space for your data.  If you're not installing on the OS disk, we recommend getting multiple disks so you can mirror the drive in case of a single disk failure.
Mirroring is not considered a backup, so you should add an external disk as a regular backup.  The amount of disk
space you may need is unique to your installation.  We suggest getting disks that are about twice the size you think you need.

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
* Storage
  * See notes above on storage

### High-End Requirements
* 2x Intel Xeon Scalable, 8 core or higher
* 64 - 128GB RAM
* Storage
  * See notes above on storage


