---
title: Esporre un servizio AKS su HTTP o HTTPS tramite il gateway applicazioneExpose an AKS service over HTTP or HTTPS using Application Gateway
description: In questo articolo vengono fornite informazioni su come esporre un servizio AKS su HTTP o HTTPS tramite il gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795578"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Esporre un servizio AKS su HTTP o HTTPS tramite il gateway applicazioneExpose an AKS service over HTTP or HTTPS using Application Gateway 

Queste esercitazioni illustrano l'utilizzo delle risorse in uscita di [Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) per esporre un servizio Kubernetes di esempio tramite il [gateway applicazione di Azure](https://azure.microsoft.com/services/application-gateway/) tramite HTTP o HTTPS.

## <a name="prerequisites"></a>Prerequisiti

- Grafico `ingress-azure` del timone installato.
  - [**Distribuzione di Greenfield:**](ingress-controller-install-new.md)se si parte da zero, fare riferimento a queste istruzioni di installazione, che illustrano i passaggi per distribuire un cluster AKS con il gateway applicazione e installare il controller in ingresso del gateway applicazione nel cluster AKS.
  - [**Distribuzione Di Brownfield:**](ingress-controller-install-existing.md)se si dispone di un cluster AKS e di un gateway applicazione AKS esistenti, fare riferimento a queste istruzioni per installare il controller in ingresso del gateway applicazione nel cluster AKS.
- Se si desidera utilizzare HTTPS in questa applicazione, è necessario un certificato x509 e la relativa chiave privata.

## <a name="deploy-guestbook-application"></a>Distribuire `guestbook` l'applicazioneDeploy application

L'applicazione guestbook è un'applicazione Kubernetes canonica che compone un front-end Web UI, un back-end e un database Redis. Per impostazione predefinita, `guestbook` espone l'applicazione tramite un servizio con nome `frontend` sulla porta `80`. Senza una risorsa in ingresso Kubernetes, il servizio non è accessibile dall'esterno del cluster AKS. Utilizzeremo l'applicazione e configureremo le risorse in ingresso per accedere all'applicazione tramite HTTP e HTTPS.

Seguire le istruzioni riportate di seguito per distribuire l'applicazione guestbook.

1. Scarica `guestbook-all-in-one.yaml` da [qui](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Eseguire `guestbook-all-in-one.yaml` la distribuzione nel cluster AKS eseguendo

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

A questo `guestbook` punto, l'applicazione è stata distribuita.

## <a name="expose-services-over-http"></a>Esporre i servizi su HTTP

Per esporre l'applicazione guestbook, useremo la seguente risorsa in ingresso:

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

Questo ingresso esporrà `frontend` `guestbook-all-in-one` il servizio della distribuzione come back-end predefinito del gateway applicazione.

Salvare la risorsa in `ing-guestbook.yaml`ingresso precedente come .

1. Eseguire `ing-guestbook.yaml` la distribuzione eseguendo:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Controllare il log del controller in ingresso per lo stato della distribuzione.

Ora `guestbook` l'applicazione dovrebbe essere disponibile. È possibile verificarlo visitando l'indirizzo pubblico del gateway applicazione.

## <a name="expose-services-over-https"></a>Esporre i servizi su HTTPS

### <a name="without-specified-hostname"></a>Senza nome host specificato

Senza specificare il nome host, il servizio guestbook sarà disponibile in tutti i nomi host che puntano al gateway applicazione.

1. Prima di distribuire in ingresso, è necessario creare un segreto kubernetes per ospitare il certificato e la chiave privata. È possibile creare un segreto kubernetes eseguendo

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definire l'ingresso seguente. Nell'ingresso specificare il nome del `secretName` segreto nella sezione.

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
    > Sostituire `<guestbook-secret-name>` nella risorsa in ingresso sopra con il nome del segreto. Archiviare la risorsa in ingresso `ing-guestbook-tls.yaml`precedente in un nome file .

1. Distribuire ing-guestbook-tls.yaml eseguendo

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Controllare il log del controller in ingresso per lo stato della distribuzione.

Ora `guestbook` l'applicazione sarà disponibile sia su HTTP che HTTPS.

### <a name="with-specified-hostname"></a>Con il nome host specificato

È inoltre possibile specificare il nome host in ingresso per le configurazioni e i servizi TLS multiplex.
Specificando il nome host, il servizio guestbook sarà disponibile solo nell'host specificato.

1. Definire l'ingresso seguente.
    Nell'ingresso, specificare il nome del `secretName` segreto nella sezione `hosts` e sostituire il nome host nella sezione di conseguenza.

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

1. Distribuisci `ing-guestbook-tls-sni.yaml` eseguendo

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Controllare il log del controller in ingresso per lo stato della distribuzione.

Ora `guestbook` l'applicazione sarà disponibile sia su HTTP che`<guestbook.contoso.com>` HTTPS solo sull'host specificato (in questo esempio).

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

L'ingresso seguente consentirà di aggiungere ulteriori percorsi in questo ingresso e reindirizzare tali percorsi ad altri servizi:

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
