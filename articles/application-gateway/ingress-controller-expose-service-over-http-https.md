---
title: Esporre un servizio AKS tramite HTTP o HTTPS usando il gateway applicazione
description: Questo articolo fornisce informazioni su come esporre un servizio AKS tramite HTTP o HTTPS usando il gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850356"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Esporre un servizio AKS tramite HTTP o HTTPS usando il gateway applicazione 

Queste esercitazioni illustrano l'uso delle [risorse di ingresso Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) per esporre un servizio Kubernetes di esempio tramite il [gateway applicazione Azure](https://azure.microsoft.com/services/application-gateway/) su http o HTTPS.

## <a name="prerequisites"></a>Prerequisiti

- `ingress-azure`Grafico Helm installato.
  - [**Distribuzione di Greenfield**](ingress-controller-install-new.md): se si inizia da zero, fare riferimento a queste istruzioni di installazione, che illustra i passaggi per distribuire un cluster AKS con il gateway applicazione e installare il controller di ingresso del gateway applicazione nel cluster AKS.
  - [**Distribuzione di Brownfield**](ingress-controller-install-existing.md): se si dispone di un cluster AKS e di un gateway applicazione esistenti, fare riferimento a queste istruzioni per installare il controller di ingresso del gateway applicazione nel cluster AKS.
- Se si vuole usare HTTPS in questa applicazione, sarà necessario un certificato X509 e la relativa chiave privata.

## <a name="deploy-guestbook-application"></a>Distribuisci `guestbook` applicazione

L'applicazione guestbook è un'applicazione Kubernetes canonica che compone un front-end dell'interfaccia utente Web, un back-end e un database Redis. Per impostazione predefinita, `guestbook` espone l'applicazione tramite un servizio con nome `frontend` sulla porta `80` . Senza una risorsa di ingresso Kubernetes, il servizio non è accessibile dall'esterno del cluster AKS. Si userà l'applicazione e si installeranno le risorse in ingresso per accedere all'applicazione tramite HTTP e HTTPS.

Per distribuire l'applicazione guestbook, seguire le istruzioni riportate di seguito.

1. Scarica `guestbook-all-in-one.yaml` da [qui](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. `guestbook-all-in-one.yaml`Eseguire la distribuzione nel cluster AKS eseguendo

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

A questo punto, l' `guestbook` applicazione è stata distribuita.

## <a name="expose-services-over-http"></a>Esporre i servizi su HTTP

Per esporre l'applicazione guestbook, si utilizzerà la risorsa di ingresso seguente:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Questo ingresso esporrà il `frontend` servizio della `guestbook-all-in-one` distribuzione come back-end predefinito del gateway applicazione.

Salvare la risorsa in ingresso precedente come `ing-guestbook.yaml` .

1. Eseguire la distribuzione `ing-guestbook.yaml` eseguendo:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Controllare il log del controller di ingresso per lo stato di distribuzione.

A questo punto l' `guestbook` applicazione dovrebbe essere disponibile. Per verificarlo, visitare l'indirizzo pubblico del gateway applicazione.

## <a name="expose-services-over-https"></a>Esporre i servizi tramite HTTPS

### <a name="without-specified-hostname"></a>Senza nome host specificato

Senza specificare il nome host, il servizio Guestbook sarà disponibile in tutti i nomi host che puntano al gateway applicazione.

1. Prima di distribuire il traffico in ingresso, è necessario creare un segreto kubernetes per ospitare il certificato e la chiave privata. È possibile creare un segreto kubernetes eseguendo

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definire il seguente ingresso. In ingresso specificare il nome del segreto nella `secretName` sezione.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Sostituire `<guestbook-secret-name>` nella risorsa in ingresso precedente con il nome del segreto. Archiviare la risorsa in ingresso sopra in un nome file `ing-guestbook-tls.yaml` .

1. Distribuire ing-Guestbook-TLS. YAML eseguendo

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Controllare il log del controller di ingresso per lo stato di distribuzione.

A questo punto l' `guestbook` applicazione sarà disponibile sia su http che su HTTPS.

### <a name="with-specified-hostname"></a>Con il nome host specificato

È anche possibile specificare il nome host per il traffico in ingresso, in modo da eseguire il multiplexing di configurazioni e servizi TLS.
Specificando hostname, il servizio Guestbook sarà disponibile solo nell'host specificato.

1. Definire il seguente ingresso.
    In ingresso specificare il nome del segreto nella `secretName` sezione e sostituire di conseguenza il nome host nella `hosts` sezione.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Eseguire la distribuzione `ing-guestbook-tls-sni.yaml` eseguendo

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Controllare il log del controller di ingresso per lo stato di distribuzione.

`guestbook`A questo punto l'applicazione sarà disponibile sia su http che su HTTPS solo nell'host specificato ( `<guestbook.contoso.com>` in questo esempio).

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

L'ingresso seguente consente di aggiungere percorsi aggiuntivi in ingresso e di reindirizzare tali percorsi ad altri servizi:

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
