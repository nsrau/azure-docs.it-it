---
title: Usare i certificati LetsEncrypt.org con il gateway applicazione
description: Questo articolo fornisce informazioni su come ottenere un certificato da LetsEncrypt.org e usarlo nel gateway applicazione per i cluster AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807023"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Usare i certificati con LetsEncrypt.org nel gateway applicazione per i cluster AKS

Questa sezione Configura la AKS per sfruttare [LetsEncrypt.org](https://letsencrypt.org/) e ottenere automaticamente un certificato TLS/SSL per il dominio. Il certificato verrà installato nel gateway applicazione, che eseguirà la terminazione SSL/TLS per il cluster AKS. La configurazione descritta di seguito usa il componente aggiuntivo [Cert-Manager](https://github.com/jetstack/cert-manager) Kubernetes, che consente di automatizzare la creazione e la gestione dei certificati.

Attenersi alla procedura seguente per installare [Cert-Manager](https://docs.cert-manager.io) nel cluster AKS esistente.

1. Grafico Helm

    Eseguire lo script seguente per installare il `cert-manager` grafico Helm. In tal modo, si verificheranno i seguenti eventi:

    - creare un nuovo `cert-manager` spazio dei nomi nel AKS
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

    Creare una `ClusterIssuer` risorsa. È richiesto da `cert-manager` per rappresentare l' `Lets Encrypt` autorità di certificazione in cui verranno ottenuti i certificati firmati.

    Utilizzando la risorsa senza spazio dei nomi `ClusterIssuer` , Cert-Manager emetterà certificati che possono essere utilizzati da più spazi dei nomi. `Let’s Encrypt` Usa il protocollo ACME per verificare di avere controllato un determinato nome di dominio e di emettere un certificato. Altre informazioni sulla configurazione delle proprietà sono disponibili `ClusterIssuer` [qui](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer` indicherà `cert-manager` a di emettere certificati usando l' `Lets Encrypt` ambiente di gestione temporanea usato per il test (il certificato radice non presente negli archivi di attendibilità del browser/client).

    Il tipo di richiesta di verifica predefinito nel YAML seguente è `http01` . Altre problematiche sono documentate sui [tipi letsencrypt.org-Challenge](https://letsencrypt.org/docs/challenge-types/)

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

3. Distribuire l'app

    Creare una risorsa di ingresso per esporre l' `guestbook` applicazione usando il gateway applicazione con il certificato consente di crittografare.

    Verificare che il gateway applicazione disponga di una configurazione IP front-end pubblica con un nome DNS (usando il dominio predefinito oppure effettuare il `azure.com` provisioning di un `Azure DNS Zone` servizio e assegnare il proprio dominio personalizzato).
    Si noti l'annotazione `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , che indica a Cert-Manager di elaborare la risorsa di ingresso con tag.

    > [!IMPORTANT] 
    > Aggiornare `<PLACEHOLDERS.COM>` in YAML di seguito con il proprio dominio (o il gateway applicazione, ad esempio ' KH-AKS-ingress.westeurope.cloudapp.Azure.com ')

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

    Dopo alcuni secondi, è possibile accedere al `guestbook` servizio tramite l'URL HTTPS del gateway applicazione usando il certificato di **gestione temporanea** emesso automaticamente `Lets Encrypt` .
    Il browser potrebbe avvertire l'utente di un'autorità di certificazione non valida. Il certificato di gestione temporanea viene emesso da `CN=Fake LE Intermediate X1` . Ciò indica che il sistema ha funzionato come previsto e che si è pronti per il certificato di produzione.

4. Certificato di produzione

    Quando il certificato di gestione temporanea è stato configurato correttamente, è possibile passare a un server ACME di produzione:
    1. Sostituire l'annotazione di gestione temporanea nella risorsa in ingresso con: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Eliminare la gestione temporanea esistente `ClusterIssuer` creata nel passaggio precedente e crearne una nuova sostituendo il server Acme dal CLUSTERISSUER YAML precedente con `https://acme-v02.api.letsencrypt.org/directory`

5. Scadenza e rinnovo del certificato

    Prima della `Lets Encrypt` scadenza del certificato, `cert-manager` aggiornerà automaticamente il certificato nell'archivio segreto Kubernetes. A questo punto, il controller di ingresso del gateway applicazione applicherà il segreto aggiornato a cui viene fatto riferimento nelle risorse di ingresso usate per configurare il gateway applicazione.
