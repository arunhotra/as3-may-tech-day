# F5 AS3 Tech Day Lab

## Lab Environment

UDF Blueprint: AS3 PS Techday Lab Jan 2019

### Networks

| VLAN       | Subnet       |
| ---------- | ------------ |
| Management | 10.1.1.0/24  |
| Subnet 10  | 10.1.10.0/24 |

### Component Info & Credentials

| Host              | Management IP | Subnet 10 IP    | Username | Password |
| ----------------- | ------------- | --------------- | -------- | -------- |
| BIG-IP 1          | 10.1.1.4      | 10.1.10.11      | admin    | admin    |
| BIG-IQ CM         | 10.1.1.9      | 10.1.10.16      | admin    | admin    |
| Linux WebServer   | 10.1.1.6      | 10.1.10.100-105 | ubuntu   | key-only |
| Windows Jump Host | 10.1.1.7      | 10.1.10.200     | User     | user     |

## AS3 Reference Links

- [AS3 Docs](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/)
- [AS3 Composing a Declaration](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/userguide/composing-a-declaration.html)
- [AS3 Example Declarations](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/declarations/)
- [AS3 Schema Reference](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/refguide/schema-reference.html)
- [AS3 GitHub Page](https://github.com/F5Networks/f5-appsvcs-extension)
- [Hive Landing page for AS3] - ( https://hive.f5.com/docs/DOC-50751 )
- [Declarative - Hitesh's red talk] - ( https://redtalks.live/2016/11/10/redtalks-08-hitesh-on-imperative-vs-declarative-and-sandwiches/ )
- [Declarative - benefits] - ( https://f5.com/about-us/blog/articles/why-is-a-declarative-model-important-for-netops-automation-30211 )
- [GIT and GITHUB - core concepts] - (https://www.youtube.com/watch?v=uR6G2v_WsRA)
- [GIT and GITHUB - branching and merging] - (https://www.youtube.com/watch?v=FyAAIHHClqI&t=209s)
- [GIT and GITHUB - Remotes] - (https://www.youtube.com/watch?v=Gg4bLk8cGNo&t=71s)

## Lab Guide

### Start UDF Lab Environment

Once the environment has been started, confirm you are able to login to windows and big-iq and big-ip instances with credentials above.

---

#### Clone this git repo

From the windows host:

1. Open cmd or bash git console, which ever your prefer.
2. Clone repo with the following command: 'git clone https://github.com/arunhotra/as3-may-tech-day.git'
3. This will clone this repository into the current working directory, which is likely: `C:\User\Administrator`.
4. navigate a file explorer to the newly created folder: `C:\Users\Administrator\as3-may-tech-day`

---

#### Setup Postman Environment [Postman will be enabled with the correct settings. Follow these steps in case they are not]

1. Open up Postman.
2. Ensure SSL Validation is disabled: File Menu => Settings. Then turn off "SSL certificate verification"
3. Click Import button in top left, to import the postman collection.
4. Drag and drop the `postman_collection.json` file into the import window. (lab files folder on desktop)
5. Click Import button in top left, to import the postman environment.
6. Drag and drop the `AS3-demo.postman_environment.json` file into the import window.

Next, set the environment and start exploring the collection:

1. In the dropdown at the top right, where it says "No Environment" select: "AS3Demo"
2. On the left hand side, where you see History & Collections, select Collections, and expand out "Postman Collections AS3".

Follow lab guide

---
