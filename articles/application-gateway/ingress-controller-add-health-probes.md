---
title: Aggiungere i probe di integrità ai pod AKS
description: Questo articolo fornisce informazioni su come aggiungere i probe di integrità (idoneità e/o Livezza) ad AKS Pod con un gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795586"
---
# <a name="add-health-probes-to-your-service"></a>Aggiungere i probe di integrità al servizio
Per impostazione predefinita, il controller di ingresso effettuerà il provisioning di un probe HTTP GET per i pod esposti.
Le proprietà del probe possono essere personalizzate aggiungendo un [Probe di conformità o di Livenza](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) alla `deployment` / `pod` specifica.

## <a name="with-readinessprobe-or-livenessprobe"></a>Con `readinessProbe` o`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Informazioni di riferimento sull'API Kubernetes:
* [Probe del contenitore](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Azione HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`e `livenessProbe` sono supportati se configurati con `httpGet`.
> * Il sondaggio su una porta diversa da quella esposta nel pod non è al momento supportato.
> * `HttpHeaders`, `InitialDelaySeconds`, `SuccessThreshold` non sono supportate.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Senza `readinessProbe` o`livenessProbe`
Se non vengono forniti i probe precedenti, il controller di ingresso presuppone che il servizio `Path` sia raggiungibile nel parametro specificato per `backend-path-prefix` l'annotazione o la `path` specificata nella `ingress` definizione del servizio.

## <a name="default-values-for-health-probe"></a>Valori predefiniti per il probe di integrità
Per qualsiasi proprietà che non può essere dedotta dal probe di conformità/durata, vengono impostati i valori predefiniti.

| Proprietà probe del gateway applicazione | Default Value |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |