**Gluster** is a scalable distributed storage system, that provides interfaces for file block and object storage. It is free and open source software that can utilize common off-the-shelf hardware, and aggregate disk storage resources from multiple servers into a single global namespace. Gluster can scale capacity, performance, and availability on demand, with no vendor lock-in, across on-premise, public cloud, and hybrid environments. It is used in production at thousands of enterprises spanning media, healthcare, government, education, web 2.0, and financial services.


### Key Data Services
 - High Availability
     - Replication with automatic data healing
         - Erasure coding
         - Thin arbiter
 - Disaster management, data recovery using geo replication
 - On demand scaling and data rebalance
 - Snapshots
 - Thin provisioning
 - Sharding
 - Bitrot detection
 - Cloud backup
 - Client Cache and many more


### Use cases:
Here is a brief glimpse of how Gluster is being used across the globe.

#### NAS solution
Gluster file system is POSIX compatible and can be accessed via native linux mount(FUSE), NFS and SMB protocols. This is one of the predominant use cases of Gluster. For more information please refer to [Gluster Documentation](https://docs.gluster.org/en/v3/Administrator%20Guide/) 

#### Storage for Hyper converged inrastructure
Gluster being a software defined storage with all the data services it offers, is a good fit for hosting virtual machines. Gluster integrates with Ovirt, a free open-source virtualization solution. For more information please refer to [Ovirt Documentation](https://www.ovirt.org/documentation/gluster-hyperconverged/Gluster_Hyperconverged_Guide.html) 

#### Container storage
Gluster a scaleout storage with a wide set of data services available is a perfect suit for container storage. Gluster was one of the first storage to have been integrated with Kubernetes and is widely used in production setups. As Gluster is a lightweight software storage solution, it can seamlessly work on public cloud, hybrid cloud, and private cloud. Please refer to [Gluster-Kubernetes](https://github.com/gluster/gluster-kubernetes) for more information. The next version of Gluster-Kubernetes is a project called [GCS(Gluster Container Storage)](https://github.com/gluster/gcs/), the project is under alpha state.


#### Cloud storage
hybrid cloud, on premise and AWS and other cloyd vendors.

Container storage on cloud in AWS Vsphere etc.

#### Video streaming


#### Storage for Surveillance System(CCTV store)
Gluster support for Erasure coding, its on demand scalability, and being able to access seamlessly from Windows, Mac, NFS, makes Gluster the right chioce for 

#### High Availbility through replication
Gluster is also popularly used as a plain replication solution. Gluster offers synchronous replication in the IO path, with automatic healing capability across replicas. This makes Gluster suitable for use cases that just need plain replication 

#### Data recovery through Geo replication
Though Gluster is traditionally used as a scaleout storage solution for NAS container storage etc., it is not uncommon to use Gluster for just the Geo replication data service it offers. Geo replication  can be used to:
- replicate data across data centers in different geo locations
- replicate data across cloud providers

For more information on setting up geo-replication please refer to [4]

#### Web hosting


#### Backup store
