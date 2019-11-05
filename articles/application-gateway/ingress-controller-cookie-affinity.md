---
title: Abilitare l'affinità basata su cookie con il gateway applicazione
description: Questo articolo fornisce informazioni su come abilitare l'affinità basata su cookie con un gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513575"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Abilitare l'affinità basata su cookie con un gateway applicazione
Come descritto nella [documentazione del gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), il gateway applicazione supporta l'affinità basata su cookie, il che significa che può indirizzare il traffico successivo da una sessione utente allo stesso server per l'elaborazione.

## <a name="example"></a>Esempio
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
