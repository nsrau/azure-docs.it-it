---
title: Aggiungere probe di integrità ai pod AKS
description: In questo articolo vengono fornite informazioni su come aggiungere probe di integrità (conformità e/o liveness) ai pod AKS con un gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795586"
---
# <a name="add-health-probes-to-your-service"></a>Aggiungere probe di integrità al servizioAdd Health Probes to your service
Per impostazione predefinita, il controller Ingress eseguirà il provisioning di un probe HTTP GET per i pod esposti.
Le proprietà della sonda possono essere personalizzate aggiungendo una [sonda](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di conformità o di liveness alla `deployment` / `pod` specifica.

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

Riferimento API Kubernetes:
* [Sonde per container](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Azione HttpGetHttpGet Action](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`e `livenessProbe` sono supportati `httpGet`quando configurati con .
> * La sonda su una porta diversa da quella esposta nel pod non è attualmente supportata.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` , non sono supportati.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Senza `readinessProbe` o`livenessProbe`
Se i probe precedenti non vengono forniti, il controller in ingresso `Path` presuppone `backend-path-prefix` che il `path` servizio `ingress` sia raggiungibile in base a specificato per l'annotazione o a specificata nella definizione del servizio.

## <a name="default-values-for-health-probe"></a>Valori predefiniti per il probe di integritàDefault Values for Health Probe
Per qualsiasi proprietà che non può essere dedotta dal probe di conformità/liveness, vengono impostati i valori predefiniti.

| Proprietà Probe del gateway applicazione | Default Value |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |