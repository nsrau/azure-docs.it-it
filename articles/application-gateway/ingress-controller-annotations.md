---
title: Annotazioni del controller di ingresso del gateway applicazioneApplication Gateway Ingress Controller annotations
description: In questo articolo viene fornita la documentazione sulle annotazioni specifiche per il controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335815"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Annotazioni per il controller di ingresso del gateway applicazioneAnnotations for Application Gateway Ingress Controller 

## <a name="introductions"></a>Introduzioni

La risorsa Kubernetes Ingress può essere annotata con coppie chiave/valore arbitrarie. AGIC si basa sulle annotazioni per programmare le funzionalità del gateway applicazione, che non sono configurabili tramite Ingress YAML. Le annotazioni in ingresso vengono applicate a tutte le impostazioni HTTP, ai pool back-end e ai listener derivati da una risorsa in ingresso.

## <a name="list-of-supported-annotations"></a>Elenco delle annotazioni supportate

Affinché una risorsa Ingress venga osservata da AGIC, `kubernetes.io/ingress.class: azure/application-gateway`deve essere **annotata** con . Solo allora AGIC funzionerà con la risorsa Ingress in questione.

| Chiave di annotazione | Tipo di valore | Default Value | Valori consentiti
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(secondi) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(secondi) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefisso percorso back-end

Questa annotazione consente di riscrivere il percorso del back-end specificato in una risorsa in ingresso con il prefisso specificato in questa annotazione. Ciò consente agli utenti di esporre i servizi i cui endpoint sono diversi dai nomi di endpoint usati per esporre un servizio in una risorsa in ingresso.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
Nell'esempio precedente è stata definita una `go-server-ingress-bkprefix` risorsa `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` in ingresso denominata con un'annotazione . L'annotazione indica al gateway applicazione di creare un'impostazione `/hello` `/test/`HTTP, che avrà un override del prefisso di percorso per il percorso di .

> [!NOTE] 
> Nell'esempio precedente è stata definita una sola regola. Tuttavia, le annotazioni sono applicabili all'intera risorsa in ingresso, pertanto se un utente ha definito più regole, il prefisso del percorso back-end verrà impostato per ognuno dei percorsi specificati. Pertanto, se un utente desidera regole diverse con prefissi di percorso diversi (anche per lo stesso servizio) è necessario definire risorse in ingresso diverse.

## <a name="tls-redirect"></a>Reindirizzamento TLS

Il gateway applicazione [può essere configurato](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) per reindirizzare automaticamente gli URL HTTP alle relative controparti HTTPS. Quando questa annotazione è presente e TLS è configurato correttamente, il controller di ingresso Kubernetes creerà una regola di routing con una configurazione di [reindirizzamento](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) e applicherà le modifiche al gateway applicazione. Il reindirizzamento creato `301 Moved Permanently`sarà HTTP .

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Drenaggio della connessione

`connection-draining`: questa annotazione consente agli utenti di specificare se abilitare lo svuotamento della connessione.
`connection-draining-timeout`: questa annotazione consente agli utenti di specificare un timeout dopo il quale il gateway applicazione terminerà le richieste all'endpoint back-end di svuotamento.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Affinità basata su cookie

Questa annotazione consente di specificare se abilitare l'affinità basata su cookie.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Timeout richiesta

Questa annotazione consente di specificare il timeout della richiesta in secondi dopo i quali il gateway applicazione non riuscirà la richiesta se la risposta non viene ricevuta.

### <a name="usage"></a>Uso

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Usa IP privato

Questa annotazione consente di specificare se esporre questo endpoint nell'IP privato del gateway applicazione.

> [!NOTE]
> * Il gateway applicazione non supporta più indirizzi IP sulla stessa porta (ad esempio: 80/443). Ingress with `appgw.ingress.kubernetes.io/use-private-ip: "false"` annotation `appgw.ingress.kubernetes.io/use-private-ip: "true"` e `HTTP` un altro with causerà AGIC fallire nell'aggiornamento del gateway applicazione.
> * Per il gateway applicazione che non dispone di un `appgw.ingress.kubernetes.io/use-private-ip: "true"` indirizzo IP privato, le ingrechezioni con verranno ignorate. Ciò si rifletterà nei registri del controller e negli `NoPrivateIP` eventi in ingresso per gli ingressioni con avviso.


### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Esempio
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protocollo back-end

Questa annotazione consente di specificare il protocollo che il gateway applicazione deve utilizzare durante la comunicazione con i pod. Protocolli supportati: `http`,`https`

> [!NOTE]
> * Mentre i certificati autofirmati sono supportati nel gateway `https` applicazione, attualmente AGIC supporta solo quando Pods utilizza il certificato firmato da una CA nota.
> * Assicurarsi di non utilizzare la porta 80 con HTTPS e la porta 443 con HTTP sui pod.

### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Esempio
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```