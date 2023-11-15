---
category: K8s
tags: [CKA]
usemath: [latex, ascii] 
---

# Annotation

## 파드에 어노테이션 달기

어노테이션은 키-값 쌍으로 레이블과 거의 비슷하지만 식별 정보를 가지지 않는다. 레이블은 오브젝트를 묶는데 사용할 수 있지만, 어노테이션은 그렇게 할 수 없다. 레이블 셀렉터를 통해서 오브젝트를 선택하는 것이 가능하지만 어노테이션 셀렉터와 같은 것은 없다.

반면 어노테이션은 훨씬 더 많은 정보를 보유할 수 있다. 특정 어노테이션은 쿠버네티스에 의해 자동으로 오브젝트에 추가되지만, 나머지 어노테이션은 사용자에 의해 수동으로 추가된다.

어노테이션은 쿠버네티스에 새로운 기능을 추가할 때 흔히 사용된다. 일반적으로 새로운 기능의 알파 혹은 베타 버전은 API 오브젝트에 새로운 필드를 바로 도입하지 않는다. 필드 대신 어노테이션을 사용하고, 필요한  API 변경이 명확해지고 쿠버네티스 개발자가 이에 동의하면 새로운 필드가 도입된다. 그리고 관련된 어노테이션은 사용이 중단된다.

어노테이션이 유용하게 사용되는 경우는 파드나 다른 API 오브젝트에 설명을 추가해 두는 것이다. 이렇게 하면 클러스터를 사용하는 모든 사람들이 개별 오브젝트에 관한 정보를 신속하게 찾아볼 수 있다.



### 어노테이션 추가 및 수정

kubia-manual 파드에 어노테이션을 추가해보자

```bash
$ kubectl annotate pod kubia-manual mycompany.com/someannotation="foo bar"
```

mycompany.com/someannotation 어노테이션을 foo bar라는 값과 함께 추가했다. 키 충돌을 방지하기 위해 어노테이션 키로 이런 형식을 사용하는 것이 좋다. kubectl describe 커맨드를 입력하여 확인할 수 있다.



```bash
$ kubectl describe pod kubia-manual
...
Annotations: mycompany.com/someannotation=foo bar
...
```

