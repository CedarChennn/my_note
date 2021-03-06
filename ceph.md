
# THE CEPH STORAGE CLUSTER
## Ceph Monitor
- Storage cluster clients retrieve a copy of the cluster map from the Ceph Monitor.

## Ceph OSD Daemon
- A Ceph OSD Daemon checks its own state and the state of other OSDs and reports back to monitors.

## Ceph Manager(ceph-mgr)

# CLUSTER MAP
## The Monitor Map
- Contains the cluster fsid, the position, name address and port of each monitor. It also indicates the current epoch, when the map was created, and the last time it changed.
- To view a monitor map, execute```ceph mon dump```
## The OSD Map
- Contains the cluster fsid, when the map was created and last modified, a list of pools, replica sizes, PG numbers, a list of OSDs and their status (e.g., up, in).
- To view an OSD map, execute ```ceph osd dump```.
## The PG Map
- Contains the PG version, its time stamp, the last OSD map epoch, the full ratios, and details on each placement group such as the PG ID, the Up Set, the Acting Set, the state of the PG (e.g., active + clean), and data usage statistics for each pool.
## The CRUSH Map
-  Contains a list of storage devices, the failure domain hierarchy (e.g., device, host, rack, row, room, etc.), and rules for traversing the hierarchy when storing data. 
- To view a CRUSH map, execute ```ceph osd getcrushmap -o {filename}```; 
- then, decompile it by executing ```crushtool -d {comp-crushmap-filename} -o {decomp-crushmap-filename}```. 
- You can view the decompiled map in a text editor or with cat.
## The MDS Map
-  Contains the current MDS map epoch, when the map was created, and the last time it changed. It also contains the pool for storing metadata, a list of metadata servers, and which metadata servers are up and in. 
- To view an MDS map, execute ```ceph fs dump```.


URL: https://localhost.localdomain:8443/
User: admin
Password: 6j0i2d6hq7


7-1 7-2 7-5 7-3   192.168.255.140/24 admin
node1 36 node2 
node3 38   node4 40


# commond
```bash
systemctl restart ceph.target # Restart All Ceph Daemons
```



https://admin:b7fec6be-97e6-4824-b743-859bdd45ff0b@ceph3:8003/server  or https://admin:b7fec6be-97e6-4824-b743-859bdd45ff0b@ceph3:8003/