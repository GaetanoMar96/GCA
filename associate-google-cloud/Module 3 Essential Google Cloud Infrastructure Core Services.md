## Essential Google Cloud Infrastructure: Core Services

### Identity and Access Management (IAM)
It is a way of identifying who can do what on which resource. The who can be a person, group or application. The what refers to specific privileges or actions and the resource could be any Google Cloud service.

Organization (root node) -> Folders -> Projects -> Resources (leafs)
<div align="center">
  <strong>IAM OBJECTS</strong>
  </br>
<img width="600" src="https://user-images.githubusercontent.com/59575502/194280610-00a5ce05-7672-454b-8877-5d4d1530e0ab.png">

</div>

#### Organization
**Organization roles:** ```organization admin``` (control over all resource), ```project creator``` (controls project creation and who can create it).

When a user with G suite or CIA creates a project an organization account is provisioned.

##### Resource manager roles
- **Organization**
    - Admin (full control over resource), define IAM policies, define hierarchy and delegate roles over the different components such as billing, networking with IAM roles.
    - Viewer (view access to all resource)
- **Folder**
    - Admin (full control over folders)
    - Creator (browse hierarchy and create folders)
    - Viewer (view folders and projects)
    Can create delegation over a department, so resources are limited to a single folder
- **Project**
    - Creator (create or migrate projects)
    - Deleter (delete projects)

### Roles
There are three types of roles in Cloud IAM: 
- ```Basic roles``` (apply across all services in a project) ```owner``` (all administrative)```editor```(deploy, modify code, configure services) ```viewer``` (read-only) ```billing admin``` (manage billing)
- ```Predefined roles``` (apply to a specific resource in a project) ```InstanceAdminRole``` -> ```compute.instance.(get,list,delete,start,stop,setMachineType)``` list of permission bundled together.
- ```Custom roles``` (lets you define which permission to add) Not update automatically when google updates something...
because most of the time users have the least amount of privileges, so to give to specific usercs certain permissions.

Create custom role on GC console -> IAM admin -> create role button 

IAM roles: compute admin (full controlo over compute engine), network admin (permissions on networking resources), storage admin (persmission on storage)

- roles/viewer: Permissions for read-only actions that do not affect state, such as viewing (but not modifying) existing resources or data.
- roles/editor: All viewer permissions, plus permissions for actions that modify state, such as changing existing resources.
- roles/owner: All editor permissions and permissions for the following actions:
  - Manage roles and permissions for a project and all resources within the project.
  - Set up billing for a project.
- roles/browser: Read access to browse the hierarchy for a project, including the folder, organization, and Cloud IAM policy. This role doesn't include permission to view resources in the project.

### Members
which define the who part of who can do what on which resource. There are five different types of members, Google accounts, service accounts (belong to the application instead of the single user), Google Groups, Google Workspace domains, and Cloud Identity domains (linked to organizations). 

IAM allow policies: binds more memebers to speecific roles
IAM deny policies: opposite to allow, deny specific permissions
IAM conditions: give permissions only if some conditions are met

<img width="1200" src="https://user-images.githubusercontent.com/59575502/194287087-c5b0c858-2693-4696-b11a-208e8d84d358.png">

#### Service accounts
A service account is an account that belongs to your application instead of an individual end user. When you run code that is hosted in Google Cloud, you specify an account that that code should run as.
Identified by email address.
3 types: user created service accounts (custom), built in like app engine, GAPI service accounts
Scopes: to decide which kind of privileges an user has accessing a resource 
2 type of keys to access resources: gogle managed or user managed

#### Google groups
A Google Group is a named collection of Google accounts and service accounts. Every group has a unique e-mail address that is associated with that group. Google Groups are a convenient way to apply an access policy to a collection of users.

#### Google Workspace domains
A Workspace domain represents a virtual group of all the Google accounts that have been created in an organization's workspace account. Workspace domains represent your organization's Internet domain name, such as Example.com. And when you add a user to your Workspace domain, a new Google account is created for the user inside of this virtual group, such as username@example.com.

#### Cloud Identity
Cloud Identity lets you manage users and groups using Google Admin console.

### IAM Policy
- A policy contains a ```list of bindings```.
- A binding binds a ```list of members to a role```.
- where the members can be ```user accounts, Google Groups, Google Domains, and service accounts```. 

#### Cloud Directory Sync
What if you already have a different corporate directory? How can you get your users and groups into Google Cloud? Using Google Cloud Directory Sync, your administrators can log in and manage Google Cloud resources using the same username and passwords that they already use. This tool synchronizes users and groups from your existing active directory or LDAP system with users and groups in your Cloud Identity domain. The synchronization is one-way only, which means that no information in your active directory or LDAP map is modified.

### Service Accounts
- A service account is an account that belongs to your ```application``` instead of to an individual end user.
- Provides an identity for carrying out ```server-to-server``` interactions in a project **without** supplying user credentials.

**Example:** ```service-account-name@project-id.iam.gserviceaccount.com``` </br>
By default, all projects come with a built-in Compute Engine default service account. Apart from the default service account, all projects come with a Google Cloud APIs service account, identifiable by the email project-number@cloudservices.gserviceaccount.com. This is an account designed specifically to run internal Google processes on your behalf and is automatically granted the ```Editor``` role on the project. 

### IAM best practice + LAB

</br>

**There are three types of service accounts:**
- user-created or custom
- built-in (Compute Engine and App Engine service account)
- Google APIs service accounts (Runs internal google processes on your behalf)


![image](https://user-images.githubusercontent.com/59575502/194293393-42af1e9a-0fe6-4ce5-87ee-0f0ff2bc3f4c.png)

![image (1)](https://user-images.githubusercontent.com/59575502/194294029-30a67e67-a5be-48bf-8f37-26cdb969e576.png)

## Storage

<img width="1200" src="https://user-images.githubusercontent.com/59575502/194769910-9a01eaaa-fbb8-435d-949d-48bf632fa4b7.png">
<img width="1200" src="https://user-images.githubusercontent.com/59575502/194298767-a4d67973-0b70-4dbf-8694-6047796fd27c.png">

### Cloud Storage
- Cloud storage is Google Cloud's object storage service. Collection of buckets 
- Object storage for companies of all sizes. Store any amount of data. Retrieve it as often as you’d like.
- Immutable and ```object versioning``` ( a new version is made with every change ) 
- **Access Control Lists** (ACLs) = scope + permissions
- **Lifecycle policies** (delete when you want)
- **Advantages:**
  - Unlimited storage
  - Worldwide accessibility (geo-redundancy)
  - Low latency and high durability.
  - **HTTPS/TLS** security on server-side

#### Types of storage classes: 
- [Standard Storage](https://cloud.google.com/storage/docs/storage-classes#standard): Good for “hot” data that’s accessed frequently, including websites, streaming videos, and mobile apps. No cost for retrieval, highest cost
- [Nearline Storage](https://cloud.google.com/storage/docs/storage-classes#nearline): Low cost. Good for data that can be stored for at least 30 days, including data backup and long-tail multimedia content. Infrequently accessed data
- [Coldline Storage](https://cloud.google.com/storage/docs/storage-classes#coldline): Very low cost. Good for data that can be stored for at least 90 days, including disaster recovery. Infrequently accessed data
- [Archive Storage](https://cloud.google.com/storage/docs/storage-classes#archive): Lowest cost. Good for data that can be stored for at least 365 days, including regulatory archives. Accessed once a year

You can chenge the location moving the url of the object to one bucket to another. Also can change the type of storrage class to decrease costs. Autoclass is a feature wichc automatically migrate objects from one cloud storage to another based on the amounts of access to that object.

ACL to define who can do what, it is an access control list with scopes (the user) and the permissions (what the user can do).
Signed URLs: url to grand read access to a specific resource, the url is signed with a private key

Objects are immutable, so the objects are versioned (multiple versions of the same object)
Lifecycle management of an object based on specific rules

![image (1)](https://user-images.githubusercontent.com/59575502/194335332-a71670ee-f982-49ee-b693-c2887526a811.png)
![image](https://user-images.githubusercontent.com/59575502/194335377-edf013fa-e948-42ae-b62c-7556e5a2d817.png)

<img width="1200" src="https://user-images.githubusercontent.com/59575502/194336034-3039bc1d-d6aa-4de8-9b9d-4af5c80b9440.png">

### Filestore
Filestore is a managed file storage service for applications that require a file system interface and a shared file system for data.
- Fully managed network attatched storage (NAS) for compute engine and GKE instances.
- Predictable performance,
- Full NFSV3 support.
- Scales to 100s of TBs for high performance workloads.

### Cloud SQL

![image (2)](https://user-images.githubusercontent.com/59575502/194347160-e998af3b-5000-4f92-97aa-1ab4f35502fc.png)
![image (3)](https://user-images.githubusercontent.com/59575502/194347174-b9325b47-d241-4b22-8d2b-319b9f3754c3.png)

Services offered: HA configuartion, backup service, import/export, scaling.
I recommend using the Cloud SQL Auth Proxy, which handles authentication, encryption, and key rotation for you.
If you need manual control over the SSL connection, you can generate and periodically rotate the certificates yourself.
Otherwise, you can use an unencrypted connection by authorizing a specific IP address to connect to your SQL server.

### Cloud Spanner
If you need horizontal scalability, consider using cloud spanner.

![image](https://user-images.githubusercontent.com/59575502/194372846-293e5457-16ad-4eca-afad-f4db42ed2fb5.png)
![image (1)](https://user-images.githubusercontent.com/59575502/194373061-d8921853-8afc-4e57-96ac-935168b4b5e7.png)

Cloud Spanner provides ACID (Atomicity, Consistency, Isolation, Durability) properties that enable transactional reads and writes on the database. It can also scale globally.

If you need scalability, sql, schemas, relational database cloud spanner is the best choice. Different replicas in different zones or regions, an update on a replica affect all of them for data consistency.

### Firestore
If you are looking for a highly scalable NoSQL database for your applications, consider using Cloud Firestore.

![image (2)](https://user-images.githubusercontent.com/59575502/194373775-283dca13-2f93-438e-8ee3-079bcb3651b3.png)
<div align="center">
<img width="600" src="https://user-images.githubusercontent.com/59575502/194373808-33238707-53ef-4a78-b418-0a383831a91f.png">
</div>

### Cloud Bigtable
If you don't require transactional consistency, you might want to consider Cloud Bigtable.

Stores data in a table, a big key value map.
The table is made of rows.
Each column is indentified by the combination of column family and column qualifier.
if you need to store more than 1 TB of structured data, have very high volume of writes, need read/write latency of
less than 10 milliseconds along with strong consistency, or need a storage service that is compatible with the HBase API, consider using Cloud Bigtable

![image (3)](https://user-images.githubusercontent.com/59575502/194374567-2a045581-afdc-44ba-b65e-7988f382c6cc.png)

### Memorystore

<div align="center">
<img width="600" src="https://user-images.githubusercontent.com/59575502/194375146-a53adc5c-2acd-46ed-a2dc-ab0098e904f4.png">
</div>

---

### Resource Manager, Quotas, Labels and Billing

<div align="center">
  
| **Labels**                                     | **Tags**                 |
|:----------------------------------------------:|:------------------------:|
| user-defined strings in ```key-value``` format | user-defined strings     |
| propagated through billing                     | used for networking      |
| way to organize resource                       | applied to instance only |

</div>

![image](https://user-images.githubusercontent.com/59575502/194378114-29e0ee2a-4e6f-440e-bfa0-5291b316567a.png)
![image (1)](https://user-images.githubusercontent.com/59575502/194378127-b3cc3a39-5335-4f4c-8075-8e024942d33f.png)
![image (2)](https://user-images.githubusercontent.com/59575502/194378143-6545a954-ff21-412f-aa31-6da9a47b6e47.png)

### Google Cloud's Operations Suite (previously Stackdriver)
- Integarated ```monitoring, logging, diagnostics```.
- Manages across platforms
  - Google cloud and AWS
  - open source agents and integarations ```3rd party```
- Access to powerful data and analytics tools

#### Monitoring
- Dynamic config and intelligent defaults
- Platform, system, and application metrics
    - Ingests data: Metrics, events, metadata
    - Generates insights through dashboards, charts, alerts
- Uptime/health checks
- Dashboards
- Alerts

![image](https://user-images.githubusercontent.com/59575502/194382854-5f864c99-1b11-48ff-9e21-916c7af043a4.png)
![image (1)](https://user-images.githubusercontent.com/59575502/194383148-5be9f773-aaf3-43ab-9082-8be81b66d191.png)

#### Logging, Error Reporting, Tracing and Debugging

![image](https://user-images.githubusercontent.com/59575502/194387366-ae1512d8-a031-4c4b-9cd2-5c17a08bb04d.png)
![image (1)](https://user-images.githubusercontent.com/59575502/194387376-0b99ce74-c2cf-4e40-a6c3-9a068e6bab9b.png)

#### Debugging
- Inspect an application without stopping it or slowing it down significantly.
- Debug snapshots: 
    - Capture call stack and local variables of a running application.
Debug logpoints:
    - Inject logging into a service without stopping it.
- Java, Python, Go, Node.js, Ruby, PHP, and .NET Core






