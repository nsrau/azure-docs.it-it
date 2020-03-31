---
title: Usare i certificati di LetsEncrypt.org con il gateway applicazioneUse a certificates with Application Gateway
description: In questo articolo vengono fornite informazioni su come ottenere un certificato da LetsEncrypt.org e usarlo nel gateway applicazione per i cluster AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957975"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Usare i certificati con LetsEncrypt.org nel gateway applicazione per i cluster AKSUse certificates with LetsEncrypt.org on Application Gateway for AKS clusters

In questa sezione viene configurato l'AKS per sfruttare [LetsEncrypt.org](https://letsencrypt.org/) e ottenere automaticamente un certificato TLS/SSL per il dominio. Il certificato verrà installato nel gateway applicazione, che eseguirà la terminazione SSL/TLS per il cluster AKS. La configurazione descritta di seguito utilizza il componente aggiuntivo Kubernetes del [cert-manager,](https://github.com/jetstack/cert-manager) che automatizza la creazione e la gestione dei certificati.

Seguire i passaggi seguenti per installare [cert-manager](https://docs.cert-manager.io) nel cluster AKS esistente.

1. Grafico Del timhelm

    Eseguire lo script seguente `cert-manager` per installare il grafico helm. In tal modo, si verificheranno i seguenti eventi:

    - creare un `cert-manager` nuovo spazio dei nomi in AKS
    - creare i seguenti CRD: Certificato, Sfida, ClusterIssuer, Autorità emittente, Ordine
    - installare il grafico cert-manager (da [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Risorsa ClusterIssuer

    Creare `ClusterIssuer` una risorsa. È richiesto `cert-manager` da per `Lets Encrypt` rappresentare l'autorità di certificazione in cui verranno ottenuti i certificati firmati.

    Utilizzando la risorsa non `ClusterIssuer` spazi dei nomi, cert-manager emetterà certificati che possono essere utilizzati da più spazi dei nomi. `Let’s Encrypt`utilizza il protocollo ACME per verificare che si controlla un determinato nome di dominio ed emettere un certificato. Ulteriori dettagli sulla `ClusterIssuer` configurazione delle proprietà [sono disponibili qui](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`indicherà `cert-manager` di emettere certificati utilizzando l'ambiente `Lets Encrypt` di gestione temporanea utilizzato per il test (il certificato radice non presente negli archivi di attendibilità del browser/client).

    Il tipo di verifica predefinito in `http01`YAML riportato di seguito è . Altre sfide sono documentate su [letsencrypt.org - Tipi di sfida](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Aggiornamento `<YOUR.EMAIL@ADDRESS>` nel YAML qui sotto

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Distribuire l'app

    Creare una risorsa Ingress `guestbook` per esporre l'applicazione utilizzando il gateway applicazione con il certificato Lets Encrypt.

    Verificare che il gateway applicazione disponga di una configurazione IP `azure.com` front-end `Azure DNS Zone` pubblica con un nome DNS (utilizzando il dominio predefinito o effettuando il provisioning di un servizio e assegnare il proprio dominio personalizzato).
    Si noti l'annotazione `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, che indica a cert-manager di elaborare la risorsa Ingress con tag.

    > [!IMPORTANT] 
    > Aggiornamento `<PLACEHOLDERS.COM>` nel File YAML riportato di seguito con il proprio dominio (o quello del gateway applicazione, ad esempio 'kh-aks-ingress.westeurope.cloudapp.azure.com')

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Dopo alcuni secondi, è `guestbook` possibile accedere al servizio tramite l'URL HTTPS del gateway applicazione utilizzando il certificato di **gestione temporanea** `Lets Encrypt` emesso automaticamente.
    Il tuo browser potrebbe avvisarti di un'autorità certi non valida. Il certificato di `CN=Fake LE Intermediate X1`gestione temporanea viene emesso da . Questa è un'indicazione che il sistema ha funzionato come previsto e si è pronti per il certificato di produzione.

4. Certificato di produzione

    Una volta configurato correttamente il certificato di gestione temporanea, è possibile passare a un server ACME di produzione:
    1. Sostituire l'annotazione di gestione temporanea nella risorsa Ingress con:Replace the staging annotation on your Ingress resource with:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Eliminare la `ClusterIssuer` gestione temporanea esistente creata nel passaggio precedente e crearne una nuova sostituendo il server ACME da ClusterIssuer YAML precedente con`https://acme-v02.api.letsencrypt.org/directory`

5. Scadenza e rinnovo del certificato

    Prima `Lets Encrypt` della scadenza del `cert-manager` certificato, il certificato verrà aggiornato automaticamente nell'archivio segreto Kubernetes. A questo punto, Application Gateway Ingress Controller applicherà il segreto aggiornato a cui si fa riferimento nelle risorse in ingresso che utilizza per configurare il gateway applicazione.
