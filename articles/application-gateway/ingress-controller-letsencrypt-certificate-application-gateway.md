---
title: Usare i certificati LetsEncrypt.org con il gateway applicazione
description: Questo articolo fornisce informazioni su come ottenere un certificato da LetsEncrypt.org e usarlo nel gateway applicazione per i cluster AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: f582a9ae2bfcec90b35e0827d2d75e00bb7cae9c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513471"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Usare i certificati con LetsEncrypt.org nel gateway applicazione per i cluster AKS

Questa sezione Configura la AKS per sfruttare [LetsEncrypt.org](https://letsencrypt.org/) e ottenere automaticamente un certificato TLS/SSL per il dominio. Il certificato verrà installato nel gateway applicazione, che eseguirà la terminazione SSL/TLS per il cluster AKS. La configurazione descritta di seguito usa il componente aggiuntivo [Cert-Manager](https://github.com/jetstack/cert-manager) Kubernetes, che consente di automatizzare la creazione e la gestione dei certificati.

Attenersi alla procedura seguente per installare [Cert-Manager](https://docs.cert-manager.io) nel cluster AKS esistente.

1. Grafico Helm

    Eseguire lo script seguente per installare il grafico Helm `cert-manager`. In tal modo, si verificheranno i seguenti eventi:

    - creare un nuovo spazio dei nomi di `cert-manager` nel servizio contenitore di AKS
    - creare le CRD seguenti: certificate, Challenge, ClusterIssuer, Issuer, Order
    - installare il grafico Cert-Manager (da [docs.Cert-Manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

    Creare una risorsa `ClusterIssuer`. È necessario per `cert-manager` rappresentare l'autorità di certificazione `Lets Encrypt` in cui verranno ottenuti i certificati firmati.

    Utilizzando la risorsa `ClusterIssuer` senza spazio dei nomi, Cert-Manager emetterà certificati che possono essere utilizzati da più spazi dei nomi. `Let’s Encrypt` usa il protocollo ACME per verificare di avere controllato un determinato nome di dominio e di emettere un certificato. Per ulteriori informazioni sulla configurazione di `ClusterIssuer` proprietà, vedere [qui](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` indicherà `cert-manager` emettere certificati utilizzando l'ambiente di gestione temporanea `Lets Encrypt` utilizzato per il testing (il certificato radice non presente negli archivi di attendibilità browser/client).

    Il tipo di richiesta di verifica predefinito nel YAML seguente è `http01`. Altre problematiche sono documentate sui [tipi letsencrypt.org-Challenge](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Aggiornare `<YOUR.EMAIL@ADDRESS>` in YAML di seguito

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

3. Distribuisci app

    Creare una risorsa di ingresso per esporre l'applicazione `guestbook` usando il gateway applicazione con la consente di crittografare il certificato.

    Verificare che il gateway applicazione disponga di una configurazione IP front-end pubblica con un nome DNS (usando il dominio di `azure.com` predefinito oppure effettuare il provisioning di un servizio di `Azure DNS Zone` e assegnare il proprio dominio personalizzato).
    Si noti l'annotazione `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, che indica a Cert-Manager di elaborare la risorsa di ingresso con tag.

    > [!IMPORTANT] 
    > Aggiornare `<PLACEHOLDERS.COM>` nell'YAML seguente con il proprio dominio (o il gateway applicazione, ad esempio ' kh-aks-ingress.westeurope.cloudapp.azure.com ')

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

    Dopo alcuni secondi, è possibile accedere al servizio `guestbook` tramite l'URL HTTPS del gateway applicazione usando il certificato `Lets Encrypt` di **gestione temporanea** emesso automaticamente.
    Il browser potrebbe avvertire l'utente di un'autorità di certificazione non valida. Il certificato di gestione temporanea viene emesso dal `CN=Fake LE Intermediate X1`. Ciò indica che il sistema ha funzionato come previsto e che si è pronti per il certificato di produzione.

4. Certificato di produzione dopo che il certificato di gestione temporanea è stato configurato correttamente, è possibile passare a un server ACME di produzione:
    1. Sostituire l'annotazione di gestione temporanea nella risorsa in ingresso con: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Eliminare il `ClusterIssuer` di staging esistente creato nel passaggio precedente e crearne uno nuovo sostituendo il server ACME dal ClusterIssuer YAML precedente con `https://acme-v02.api.letsencrypt.org/directory`

5. Scadenza e rinnovo del certificato prima della scadenza del certificato `Lets Encrypt`, `cert-manager` aggiornerà automaticamente il certificato nell'archivio segreto Kubernetes. A questo punto, il controller di ingresso del gateway applicazione applicherà il segreto aggiornato a cui viene fatto riferimento nelle risorse di ingresso usate per configurare il gateway applicazione.
