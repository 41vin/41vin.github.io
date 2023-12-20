---

---



# Architecture

The architecture of an xApp consists of the code implementing the xApp's logic and the RIC libraries that allow the xApp to

1. Send and receive messages
2. Read from, write to, and get notifications from the SDL layer.
3. Write log messages.

 xApps can use access libraries to access specific name-spaces in the SDL layer.



## O-RAN Standard Interfaces for xApps

### K8s ConfigMap for Configuration:

- xApps receive their configuration via Kubernetes ConfigMaps.
- Configurations can be updated while the xApp is running.
- xApps can be notified of configuration changes using inotify().



### Sending Statistics (PM):

- Option A: Directly send statistics to the VES collector in VES format.
- Option B: Expose statistics via a REST interface for Prometheus to collect.



### Receiving A1 Policy Guidance:

- xApps receive A1 policy guidance through a specific type of RMR (Reliable Messaaging Router) message;
- These messages pertain to policy instance creation and deletion operations.



### Subscribing and Receiving E2 Events:

- xApps can subscribe to E2 events by constructing an E2 subscription payload in ASN.1 format and sending it as an RMR message;
- E2 messages, such as  E2 INDICATION, are received as RMR messages with the ASN.1 payload.
- xApps can also issue E2 control messages.



### Inter-xApp Communication:

- xApps can send and receive messages processed by other xApps.
- Communication within the RIC is policy-driven.
- An xApp can't specify the direct target of a message; it sends a message of a specific type, and the RIC instance's routing policies determine the message's destination (logical publish/subscribe model).



![image-20231204134401365](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/RICAPP_Architecture/image-20231204134401365.png)