---
title: Abilitare l'affinità basata su cookie con il gateway applicazioneEnable cookie based affinity with Application Gateway
description: In questo articolo vengono fornite informazioni su come abilitare l'affinità basata su cookie con un gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795986"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Abilitare l'affinità basata su cookie con un gateway applicazioneEnable Cookie based affinity with an Application Gateway
Come descritto nella [documentazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)del gateway applicazione di Azure , il gateway applicazione supporta l'affinità basata su cookie, il che significa che può indirizzare il traffico successivo da una sessione utente allo stesso server per l'elaborazione.

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
