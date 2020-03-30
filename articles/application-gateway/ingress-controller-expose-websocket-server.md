---
title: Esporre un server WebSocket al gateway applicazione
description: In questo articolo vengono fornite informazioni su come esporre un server WebSocket al gateway applicazione con il controller in ingresso per i cluster AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297833"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Esporre un server WebSocket al gateway applicazione

Come descritto nella documentazione del gateway applicazione v2, [fornisce supporto nativo per i protocolli WebSocket e HTTP/2.](features.md#websocket-and-http2-traffic) Si prega di notare, che sia per il gateway applicazione e Kubernetes Ingress - non vi è alcuna impostazione configurabile dall'utente per abilitare o disabilitare selettivamente il supporto WebSocket.

La distribuzione di Kubernetes YAML riportata di seguito mostra la configurazione minima utilizzata per distribuire un server WebSocket, che equivale alla distribuzione di un server Web normale:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Dato che tutti i prerequisiti sono soddisfatti e si dispone di un gateway applicazione controllato da un Kubernetes Ingress in AKS, la distribuzione precedente `ws.contoso.com` comporterebbe un server WebSockets esposto sulla porta 80 dell'indirizzo IP pubblico del gateway applicazione e il dominio.

Il seguente comando cURL testerebbe la distribuzione del server WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sonde di integrità WebSocket

Se la distribuzione non definisce in modo esplicito i probe di integrità, il gateway applicazione tenterebbe un'http GET sull'endpoint server WebSocket.
A seconda dell'implementazione del server ( qui è uno`Sec-Websocket-Version` che[amiamo](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) intestazioni specifiche WebSocket possono essere necessarie (ad esempio).
Poiché il gateway applicazione non aggiunge intestazioni WebSocket, la risposta del probe `400 Bad Request`di integrità del gateway applicazione dal server WebSocket sarà molto probabilmente .
Di conseguenza il gateway applicazione contrassegnerà i pod come `502 Bad Gateway` non integri, il che alla fine si tradurrà in un per i consumer del server WebSocket.
Per evitare questo problema, potrebbe essere necessario aggiungere al`/health` server un gestore HTTP GET per un controllo di integrità (ad esempio, che restituisce `200 OK`).
