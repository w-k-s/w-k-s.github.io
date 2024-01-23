---
title: "Understanding WSDLs"
date: 2024-01-23T00:00:00+03:00
categories:
- "Engineering"
- "Integrations"
comments: false
toc: false
meta:
- categories
- date
draft: false
---

I've integrated with a number of systems through SOAP Services. SOAP Services are described using WSDL files (Web Service Description Language). I felt it was worth taking the time to understand the structure of these files. I drew this diagram to summarise the structure:

![Worst API Ever](/Understanding-WSDLs/wsdl-structure.png)

---

- A WSDL is an XML file that defines one or more **Services**. 
- Each service can have one or more **endpoints**, where each endpoint is a remote procedure call.
- A service contains one or more **Port**s, and a port is an implementation of a **Port Type**. That's a little tricky to follow so let's break this down:
	
	- A **Port Type** is like an interface class. It defines operations that a service can perform along with their request and response objects.

	- Implementations of **Port Types** are called **Ports**. SOAP supports multiple transportation protocols including: HTTP, SMTP and FTP. A Port is an implementation of a port type for one of these protocols.

	- A **Binding** specifies the transport protocol that the port implements.

- So to summarise: A **Service** contains **Ports**, each port implement a **Port Type** interface for different transport protocols. The service defines the **binding** for each port, which provides details about which transport protocol this particular port is implementing.

- Finally, there's a **SOAP Action**. The SOAP Action is uniquely identifies an operation in a binding. It is typically formatted as a URL, but doesn't have to be. The header is optional, but can be used to route the request to the correct server.