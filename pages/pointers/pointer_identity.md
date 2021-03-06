---
title: Pointer Identity
keywords: engage about
# tags: [pointers,for_providers,for_consumers]
sidebar: overview_sidebar
permalink: pointer_identity.html
summary: NRL Pointer Identity
---

## Pointer Identity

Once persisted within the NRL there are up to two ways to refer to a specific Pointer instance. Both are identifiers that are stored on the Pointer itself, but the master identifier is optional and is specified by the Provider.

- Logical identifier – This identifier is assigned by the NRL service when it persists a Pointer. It uniquely identifies that Pointer within the NRL service. The NRL service instance is the namespace for a given Pointer’s Logical identifier. 

- Master identifier – This also uniquely identifies the Pointer within the boundary of the NRL service. However, unlike the logical identifier, the master identifier is optional and is under the control of the Provider. For more details, see the [Master Identifier](#master-identifier) and [Uniqueness](#uniqueness) sections below.

### Logical Identifier

The logical identifier (ID) is generated by the NRL during the creation of a new Pointer. It is unique across all Pointers on the NRL service that created the Pointer. If the Pointer were ever to be migrated to a different NRL service instance then it is possible that its ID might need to change to avoid clashes with Pointers on the target NRL service.

The format of the ID is under the control of the NRL service. Clients should treat the id as an opaque identifier. In other words, the client should not make assumptions about the structure of the ID.

### Master Identifier

The master identifier is an optional identifier on the Pointer. It is under the control of the Provider. Guidance on the use of a unique master identifier value:

- Once a pointer with a master identifier value has been created, that same master identifier value must not be re-used with another pointer.
- 'Superseding' requires a new master identifier value. If a pointer is superseded, the new Pointer that replaces it must have a new, unique master identifier value.
- Once a pointer with a master identifier is deleted, the master identifier value used in that pointer cannot be used again on the NRL.

### Uniqueness

It is important that the master identifier be unique. The scope of that uniqueness is recommended to extend beyond the NRL service that the Pointer was originally created on. This is to support the notion that master identifier is a business identifier — it is tied to a specific Pointer and should never change regardless of which NRL service the Pointer is on.
In terms of the uniqueness of a given master identifier, a Pointer can be seen be seen as existing in a collection of namespaces:

* Patient — the smallest namespace in NRL. The set of master identifiers associated with a given Patient.
* Provider — this namespace spans Patients but is constrained to those Pointers owned by a given Provider.
* NRL — the largest namespace, which holds both the Patient and Provider namespaces.

Each of the namespaces are discussed in more detail below in particular who is responsible for ensuring that a given Master identifier is unique in that space and the consequences of failing to ensure uniqueness. Overall though, the content can be summarised as one strong recommendation to Providers:

***Providers should use a unique value for the Master identifier.***

### Patient Namespace

The NRL guarantees that no two Pointers for the same Patient (identified by NHS number) will have the same master identifier. If the Provider attempts to create a Pointer using a master identifier that has already been assigned to one of that patient’s other Pointers, the Provider’s request will be blocked.

### Provider Namespace

It is the Provider’s responsibly to ensure that the Master identifiers that it creates are unique across all of its own Pointers. It is perfectly possible for a Provider to create Pointers for different patients with the same master identifier. The NRL service will not prevent this scenario from arising.

### NRL Namespace

It is the Provider’s responsibly to ensure that the master identifiers that it creates are unique across all Pointers in the NRL. It is not unheard of for organisations to merge. In this case the Pointers previously owned by two organisations will come under the ownership of one of those organisations.

To provide the NRL with a degree of flexibility in terms of deployment, a Provider should not assume that there is a single instance of the NRL service. With this constraint in mind, the uniqueness of a Master identifier extends across NRL instances.

In the end, we arrive back at the recommendation made at the beginning of this section:

***Providers should use a unique value for the Master identifier.***

### Unique Value for the Master Identifier

The NRL recommends the use of a unique value when assigning a value for a Master identifier. To avoid the negative scenarios outlined above the value should be unique within the NRL namespace.

The problem of uniquely identifying an entity such as a Pointer is not a new one and there are several standard ways of generating a unique value. Two approaches of note are:

- The Universally Unique Identifier (UUID) – the uniqueness of a UUID is based on the extremely low chance that the same 128-bit random number will be generated twice. There exist a wealth of libraries that will generate a UUID that result in the practical likelihood that a UUID is unique being very high.

- Object Identifier (OID) – An OID is a set of decimal numbers called arcs that are separated by periods. The OID system relies on the concept of authorities who can hand out an OID that is guaranteed to be unique. From there, the owner of that OID can itself act as an authority handing out an arc to another organisation that is unique below the owner’s arc. This process can repeat and repeat creating a chain of arcs that are all unique within the scope of their parent arc.

As long as all organisations execute their responsibility to ensure uniqueness, the OID system provides a stronger guarantee of uniqueness than the UUID. However, it does rely on all members of an OID chain making sure that they do not reuse an value when assigning an arc to a child.
