#activedirectory

It is a directory service that stores, lists and organizes information about entities in an organization such as users, resources, groups.

It provides an authentication and authorization system to network resources. It allows a user to login and access specific resources or data in a domain based on the permissions.
## AD Objects

The information stored in Active Directory is known as AD Objects. They are categorized as below:

- Users
- Resources
- Groups

## Physical Components

### Domain Controllers

A server with AD DS server role installed and has been specifically promoted to Domain Controller. 

- It hosts AD DS directory store which holds information about all the objects in the domain
- It provides authentication and authorizations services
- Replicate updates to other domain controllers in the domain
- Allow administrative access to manager user accounts and network resources.

### Active Directory Data Store

It contains the database files and processes that store and manage directory information for users, services and applications.

Consists of the ntds.dit file. It contains all the data present in the AD, including all the password hashes. The file is only accessible through the domain controller processes and protocols.


## Logical Components


### Active Directory Schema

It holds the definition of every objects which can be created in AD. Enforces rules about object creation and configuration.


### Domain

Domains are used to group and manage objects in an organization. They are administrative boundary to applying policies to group of objects. It can also be considered as a authentication and authorization boundary that provides a way of limiting the scope of access to resources.


### Trees

A domain tree is a hierarchy of domains in AD DS. The domains have by default a two-way transitive trust with other domains. All domains in the tree share the same contiguous namespace with the parent domain.

### Forests

A forest is a collection of one or more domain trees.

### Organizational Units

OUs are active directory containers that consists of users, groups and computers and other OUs. These are used to apply policies and manage a collections of objects in a consistent way.

### Trusts

It provides a mechanism for users to gain access to resources in another domain.

#### Types of Trusts

- Directional: The trusts direction flows from trusting domain to the trusted domain
- Transitive: Trust domain is extended beyond two-domain trust to include other trusted domains

### Objects

- Users
- Groups
- Computers
- Printers
- Shared Folders






