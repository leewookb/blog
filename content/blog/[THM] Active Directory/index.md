---
title: "[THM] Active Directory"
author: "Wook Lee"
date: "2024-07-16"
tags: ["TryHackMe"]
---

Microsoft's **_Active Directory_** is the backbone of the corporate world. It simplifies the management of devices and users within a corporate environment.

`Windows domain` is a group of users and computers under the administration of a given business. The main idea behind a domain is to centralize the administration of common components of a Windows computer network in a sigle repository called `Active Directory`. The server that runs the Active Directory service is known as a `Domain Controller (DC)`.

The main advantages of having a configured Windows domain are:

- **_Centralized identity management_**: All users across the network can be configured from Active Directory with minimum effort.
- **_Managing security policies_**: You can configure security policies directly from Active Directory and apply them to users and computers across the network as needed.

#### A Real-World Example

In school/university networks, you will often be provided with a username and password that you can use on any of the computers available on campus. Your credentials are valid for all machines because whenever you input them on a machine, it will forward the authentication process back to the Active Directory, where you credentials will be checked. Thanks to Active Directory, your credentials don't need to exist in each machine and are available throughout the network.

#### In a Windows domain, credentials are stored in a centralised repository called..

```
Active Directory
```

<br>

#### The server in charge of running the Active Directory services is called..

```
Domain Controller
```

<br>

The core of any Windows Domain is the **_Active Directory Domain Service (AD DS)_**. This service acts as a catalogue that holds the information of all the "objects" that exist on your network. Amongst the many objects supported by AD, we have users, groups, machines, printers, shares, and many others.

![alt text](image.png#center)

<br>

#### Security Groups vs OUs (Orgianizational Units)

- **_OUs_** are handy for applying policies to users and computers, which include specific configurations that pertain to sets of users depending on their particular role in the enterprise. Remember, a user can only be a member of a single OU at a time, as it wouldn't make sense to try to apply two different policies to a single user.
- **_Security Groups_**, on the other hand, are used to grant permissions over resources. For example, you will use groups if you want to allow some users to access a shared folder or network printer. A user can be a part of many groups, which is needed to grant access to multiple resources.

<br>

#### Which group normally administrates all computers and resources in a domain?

```
Domain Admins
```

![alt text](image-1.png#center)

<br>

#### What would be the name of the machine account associated with a machine named TOM-PC?

```
TOM-PC$
```

<br>

#### Suppose our company creates a new department for Quality Assurance. What type of containers should we use to group all Quality Assurance users so that policies can be applied consistently to them?

```
Organizational Units
```

<br>

#### Deletgation

One of the nice things you can do in AD is to give specific users some control over some OUs. This process is known as **_delegation_** and allows you to grant users specific privileges to perform advanced tasks on OUs without needing a Domain Administrator to step in.

One of the most common use cases for this is granting **_IT Support_** the privileges to reset othe low-privilege users' passwords.

<br>

I delegated our IT support (phillip) the rights to reset other people's passwords. Then I logged in as phillip then changed Sophie's passwords by running the following command:

`Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') - Verbose`

![alt text](image-2.png#center)

<br>

#### What was the flag found on Sophie's desktop?

```
THM{thanks_for_contacting_support}
```

![alt text](image-3.png#center)
![alt text](image-4.png#center)

<br>

#### The process of granting privileges to a user over some OU or other AD Object is called...

```
delegation
```

<br>

#### After organizing the available computers, how many ended up in the Workstations OU?

```
7
```

![alt text](image-5.png#center)

<br>

#### Is it recommendable to create separate OUs for Servers and Workstations? (yay/nay)

```
yay
```

<br>

To deploy different policies for each OU individually Windows use **_Group Policy Objects (GPO)_**. GPOs are simply a collection of settings that can be applied to OUs. GPOs can contain policies aimed at either users or computers, allowing you to set a baseline on specific machines and identities.

<br>

#### What is the name of the network share used to distribute GPOs to domain machines?

```
SYSVOL
```

GPOs are distributed to the network via a network share called `SYSVOL`, which is stored in the DC. All users in a domain should typically have access to this share over the network to sync their GPOs periodically.

<br>

#### Can a GPO be used to apply settings to users and computers? (yay/nay)

```
yay
```

<br>

When using Windows domains, all credentials are stored in the `Domain Controllers`. Whenever a user tries to authenticate to a service using domain credentials, the service will need to ask the Domain Controller to verify if they are correct. Two protocols can be used for network authentication in windows domains:

- **_Kerberos_**: used by any recent version of Windows. This is the default protocol in any recent domain.
- **_NetNTLM_**: Legacy authentication protocol kept for compatibility purposes.

#### Kerberos Authentication

Kerberos authentication is the default authentication protocol for any recent version of Windows. users who log into a service using Kerberos will be assigned tickets. Think of tickets as proof of a previous authentication. users with tickets can present them to a service to demonstrate they have already authenticated into the network before and are therefore enabled to use it.

When `Kerberos` is used for authentication, the following process happens:

1. The user sends their encrypted credentials to the `Key Distribution Center (KDC)` in charge of creating Kerberos tickets on the network. The KDC will create and send back a `Ticket Granting Ticket`, which will allow the user to request additional tickets to access specific services. `TGT` is encrypted using the `krbgt` account's password hash and therefore the user can't access its contents.

![alt text](image-6.png#center)

2. When a user wants to connect to a service on the network, they will use their TGT to ask the KDC for a `Ticket Granting Service (TGS)`.
   TGS are tickets that allow connection only to the specific service they were created for. To request a TGS, the user will send their credentials encrypted using the Session Key, along with the TGT and a `Service Principal Name (SPN)`, which indicates the service and server name we intend to access.

   As a result, the KDC will send us a TGS along with a `Service Session Key`, which we will need to authenticate to the service we want to access. The TGS is encrypted using a key derived from the `Service Owner Hash`.

   ![alt text](image-7.png#center)

3. The TGS can then be sent to the desired service to authenticate and establish a connection. The service will use its configured account's password hash to decrypt the TGS and validate the Service Session Key.
   ![alt text](image-8.png#center)

<br>

#### NetNTLM Authentication

![alt text](image-9.png#center)

<br>

#### Will a current version of Windows use NetNTLM as the preferred authentication protocol by default? (yay/nay)

```
nay
```

<br>

#### When referring to Kerberos, what type of ticket allows us to request further tickets known as TGS?

```
Ticket Granting Ticket
```

<br>

#### When using NetNTLM, is a user's password transmitted over the network at any point? (yay/nay)

```
nay
```

<br>

#### What is a group of Windows domains that share the same namespace called?

```
Tree
```

<br>

#### What should be configured between two domains for a user in Domain A to access a resource in Domain B?

```
A Trust Relationship
```

<br>
