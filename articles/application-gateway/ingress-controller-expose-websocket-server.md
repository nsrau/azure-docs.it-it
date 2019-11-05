---
title: Esporre un server WebSocket al gateway applicazione
description: Questo articolo fornisce informazioni su come esporre un server WebSocket al gateway applicazione con il controller di ingresso per i cluster AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 08a12a2de0c4912913058291206772bbcb2c4a44
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513432"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Esporre un server WebSocket al gateway applicazione

Come descritto nella documentazione del gateway applicazione V2, [fornisce il supporto nativo per i protocolli WebSocket e http/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic). Si noti che per il gateway applicazione e il traffico in ingresso Kubernetes non è disponibile alcuna impostazione configurabile dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket.

La distribuzione di Kubernetes YAML seguente mostra la configurazione minima usata per distribuire un server WebSocket, che equivale alla distribuzione di un server Web normale:
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

Dato che tutti i prerequisiti sono soddisfatti e si dispone di un gateway applicazione controllato da un ingresso Kubernetes nel servizio contenitore di applicazioni, la distribuzione precedente comporterebbe un server WebSocket esposto sulla porta 80 dell'IP pubblico del gateway applicazione e il `ws.contoso.com` dominio.

Il comando cURL seguente testa la distribuzione del server WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Probe di integrità WebSocket

Se la distribuzione non definisce in modo esplicito i probe di integrità, il gateway applicazione tenterà un'operazione HTTP GET sull'endpoint del server WebSocket.
A seconda dell'implementazione del server (in questo caso,[è opportuno che](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)le intestazioni specifiche di WebSocket siano obbligatorie (`Sec-Websocket-Version` ad esempio).
Poiché il gateway applicazione non aggiunge intestazioni WebSocket, la risposta del probe di integrità del gateway applicazione dal server WebSocket sarà probabilmente `400 Bad Request`.
Di conseguenza, il gateway applicazione contrassegna i pod come non integri, il che comporterà infine un `502 Bad Gateway` per gli utenti del server WebSocket.
Per evitare questo problema, potrebbe essere necessario aggiungere un gestore HTTP GET per un controllo di integrità al server (`/health` ad esempio, che restituisce `200 OK`).
