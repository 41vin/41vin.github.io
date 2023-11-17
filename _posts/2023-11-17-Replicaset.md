---
category: K8s
tags: [CKA]
usemath: [latex, ascii] 
---

# 레플리카셋

## Replicaset과 Replication Controller 비교

Replicaset은 Replication Controller와 똑같이 동작하지만 좀 더 풍부한 표현식을 사용하는 pod selector를 가지고 있다. Replicaset의 Selector는 특정 label이 없는 pod나 label의 값과 상관없이 특정 label의 키를 갖는 pod를 매칭시킬 수 있다.

예를 들어, 하나의 replicationcontroller는 label이 env=production인 파드와 label이 env=devel인 파드를 동시에 매칭시킬 수 없다. 그러나 replicaset은 하나의 replicaset으로 두 pod set를 모두 매칭시켜 하나의 그룹으로 취급할 수 있다.