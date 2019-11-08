---
title: Abilitare l'affinità basata su cookie con il gateway applicazione
description: Questo articolo fornisce informazioni su come abilitare l'affinità basata su cookie con un gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795986"
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
