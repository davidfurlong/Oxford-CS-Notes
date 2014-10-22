# Computer Security

## Introduction

- Security: freedom from risk or danger
- Computer Security: risks we prevent and detect - restricted to malicious behaviour (from the *enemy*)
- Authorization: determines the rights of *actors* to *access of resources* 
- Confidentiality: information is not disclosed to *unauthorized* entities
- Integrity: unauthorized alteration of information can be detected
- Authentication: means of verification of identity
	- of entity to a resource (logging on)
	- of message originator (email sent from X)
	- channel (messages intended, integrity preserved, message indeed sent by the originator)
- Non-repudiation: actor cannot deny having taken an action (digital signature: sender cannot deny having sent a message)
- Availability: information and resources are available to authorized parties

## Access Control
_the authorization aspect of security_ 

- Subject: properly-authenticated user, _S_
- Object: resource of the system, _O_
- Modes or Permissions: ways the resources can be accessed, _P_

Set of all authorized accesses:
_A_ ⊆ _S_ x _O_ x _P_

### Modelling access control

- **access control matrix** : M<sub>s,o</sub> = { p | (s,o,p) ∈ *A* }
- **access control lists** : A<sub>o</sub> = { (s,p) | (s,o,p) ∈ *A* }
- Capability: row of the access control matrix (usually implemented by tokens stored by the subjects)


### Mandatory access control
- discretionary: Each object is assigned a subject called _owner_. Owner may change the permissions for that object.
- mandatory access control: A security policty admin sets all permissions - these system permissions enforced even when users behave poorly.

## Security Models

### Bella-LaPadula Model
_captures confidentiality only_

#### Spec

- Permissions: {READ, WRITE, APPEND, EXECUTE}
- Security levels _L_: Set defined over ≤
- Max security level (clearance) for each subject: _f_<sub>s</sub>: _S_ -> _L_
- Current security level for each subject: _f_<sub>c</sub>: _S_ -> _L_ 
- Classification of each object: _f_<sub>o</sub> : _O_ -> _L_
- Set of currently granted permissions {(s,o,p),...}
- Set of allowable permissions _A_ = {(s,o,p),...}


#### System

- Evolves through a sequence of states
- States are **secure** if they satisfy certain policy conditions
- System is secure if every implemented **action** preserves these conditions

#### Security conditions
- ds-property: whenever access (s,o,p) has been granted, (s,o,p) ∈ _A_
- ss-property (*no read-up*): whenever access {(s,o,READ),(s,o,WRITE)} has been granted, _f_<sub>O</sub>(o) ≤ _f_<sub>S</sub>(s)
- \*-property (*no write-down*): whenever access {(s,o,WRITE),(s,o,APPEND)} has been granted, _f_<sub>C</sub>(o) ≤ _f_<sub>O</sub>(o) and _f_<sub>O</sub>(o') ≤ _f_<sub>O</sub>(o) for all o' where access {(s,o,READ),(s,o,WRITE)} has been granted.

## Access Control in Practice

- Authorization is checked by a *reference monitor* - Modern CPUs have a simple multilevel (Applications, Services, OS, Kernel, Hardware) security reference monitor. Each thread of execution and page of memory marked with a level.
- Two levels usually: 
	- user mode (ring 3, 'low clearance')
	- kernel mode (system mode, ring 0, 'high clearance')
- User mode threads may not issue certain instructions (write into kernel mode areas, access hardware, control system interrupts) and must call kernel mode procedures to access these.

## Superusers
_need a method for overriding and changing the permissions_
The superuser is still a user and their processes will run in user mode despite having universal access.

## Attacking Access Control
_the enemy wants to gain permission to a resource not granted_

- Fool the reference monitor (exploit bugs in reference monitor)
- Fool the CPU into executing kernel mode code to disable the reference monitor (exploit bugs in OS kernel)
	- privilege escalation: Buffer overrun to write attacker-supplied data over kernel mode instructions, the reference monitor thinks we are now root
	
## Unix file permissions
_access control stored per object_

- Each user has a unique UID
- Each user belongs to one or more **groups**
- Each group has a unique GID
- Each object is owned by a UID and a GID
- Each object has nine permissions flags: read, write, execute for owner, group owner, other. Ex. rwxr-x----

A process runs with the UID and GID of the user who executes it. When it requests access:
- if its UID matches the file, the owner permissions are used else
- if its GID matches the file the group permissions are used else
- the other permissions are used.

root has universal access and can change owners and groups, etc.

Unix has many quirks, with different versions having different implementations:
- owner/group can conflict
- same permissions used for directories