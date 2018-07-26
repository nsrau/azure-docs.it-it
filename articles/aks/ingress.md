---
title: Configurare l'ingresso con il cluster di Azure Kubernetes Service (AKS)
description: Informazioni su come installare e configurare un controller di ingresso NGINX che usa Let's Encrypt per la generazione automatica di certificati SSL in un cluster del servizio Kubernetes di Azure (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c65cfec41c2002fd4d4ff27ea74daf0bb4246b5f
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145598"
---
# <a name="deploy-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Distribuire un controller di ingresso HTTPS nel servizio Kubernetes di Azure (AKS)

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso, è possibile servirsi di un singolo indirizzo esterno per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Kubernetes di Azure (AKS). Il progetto [cert-manager][cert-manager] viene usato per generare e configurare automaticamente certificati [Let's Encrypt][lets-encrypt]. Infine, nel cluster AKS vengono eseguite varie applicazioni, ognuna delle quali è accessibile tramite un singolo indirizzo.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo usa Helm per installare il controller di ingresso NGINX, il certificato cert-manager e un'app Web di esempio. È necessario disporre di Helm inizializzato nel cluster AKS e usare un account del servizio per Tiller. Per altre informazioni sulla configurazione e l'uso di Helm, vedere [Installare le applicazioni con Helm nel servizio Kubernetes di Azure (AKS)][use-helm].

Questo articolo richiede anche che sia in esecuzione l'interfaccia della riga di comando di Azure versione 2.0.41 o successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="install-an-ingress-controller"></a>Installare un controller di ingresso

Usare Helm per installare il controller di ingresso NGINX. Per informazioni dettagliate sulla distribuzione, vedere la [documentazione sul controller di ingresso NGINX][nginx-ingress].

L'esempio seguente installa il controller nello spazio dei nomi `kube-system`. È possibile specificare uno spazio dei nomi diverso per il proprio ambiente. Se il cluster AKS non dispone dell'abilitazione RBAC, aggiungere `--set rbac.create=false` al comando.

```console
helm install stable/nginx-ingress --namespace kube-system
```

Durante l'installazione viene creato un indirizzo IP pubblico di Azure per il controller di ingresso. Per ottenere l'indirizzo IP pubblico, usare il comando `kubectl get service`. Per l'assegnazione dell'indirizzo IP al servizio, sono necessari pochi minuti.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Non è ancora stata creata alcuna regola di ingresso. Se si accede all'indirizzo IP pubblico, per impostazione predefinita viene visualizzata la pagina 404 del controller di ingresso NGINX, come illustrato nell'esempio seguente:

![Back-end di NGINX predefinito](media/ingress/default-back-end.png)

## <a name="configure-a-dns-name"></a>Configurare un nome DNS

Per il funzionamento corretto dei certificati HTTPS, è necessario configurare un nome FQDN per l'indirizzo IP del controller di ingresso. Aggiornare lo script seguente con l'indirizzo IP del controller di ingresso e un nome univoco da usare per il nome FQDN:

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Il controller di ingresso è ora accessibile tramite il nome di dominio completo.

## <a name="install-cert-manager"></a>Installare cert-manager

Il controller di ingresso NGINX supporta la terminazione TLS. Il recupero e la configurazione di certificati per HTTPS possono essere eseguiti in modi diversi. Questo articolo descrive l'uso di [cert-manager][cert-manager], che consente di generare automaticamente il certificato [Let's Encrypt][lets-encrypt] e che dispone di funzionalità di gestione.

> [!NOTE]
> Nell'articolo viene usato l'ambiente `staging` per Let's Encrypt. Nelle distribuzioni di produzione usare `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nella definizione delle risorse e durante l'installazione del grafico Helm.

Per installare il controller cert-manager in un cluster abilitato per il controllo degli accessi in base al ruolo, usare il comando `helm install` seguente:

```console
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-staging --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Se il cluster non è abilitato per il controllo degli accessi in base al ruolo, usare il comando seguente:

```console
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Per altre informazioni sulla configurazione di cert-manager, vedere [progetto cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Creare un'autorità di certificazione (CA) cluster

Per poter emettere i certificati, cert-manager richiede un [autorità di certificazione][cert-manager-issuer] o una risorsa [ClusterIssuer][cert-manager-cluster-issuer]. Queste risorse Kubernetes hanno funzionalità identiche, ma `Issuer` funziona in uno spazio dei nomi singolo, mentre `ClusterIssuer` funziona in tutti gli spazi dei nomi. Per altre informazioni, vedere la documentazione relativa all'[autorità di certificazione cert-manager][cert-manager-issuer].

Creare un'autorità di certificazione del cluster, ad esempio `cluster-issuer.yaml`, tramite il manifesto di esempio seguente. Aggiornare l'indirizzo di posta elettronica con un indirizzo valido della propria organizzazione:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Per creare l'autorità di certificazione, usare il comando `kubectl create -f cluster-issuer.yaml`.

```
$ kubectl create -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Creare un oggetto certificato

Successivamente, è necessario creare una risorsa certificato. La risorsa certificato definisce il certificato X.509 desiderato. Per altre informazioni, vedere [cert-manager certificates][cert-manager-certificates] (Certificati cert-manager).

Creare la risorsa certificato, ad esempio `certificates.yaml`, con il manifesto di esempio seguente. Aggiornare gli elementi *dnsNames* e *domains* in base al nome DNS creato in un passaggio precedente.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Per creare la risorsa certificato, usare il comando `kubectl create -f certificates.yaml`.

```
$ kubectl create -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Sono stati configurati un controller di ingresso e una soluzione di gestione dei certificati. A questo punto è possibile eseguire due applicazioni demo nel cluster AKS. In questo esempio Helm viene usato per distribuire due istanze di una semplice applicazione "Hello world".

Prima di installare i grafici Helm di esempio, aggiungere il repository degli esempi di Azure all'ambiente Helm come indicato di seguito:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Creare la prima applicazione demo da un grafico Helm con il comando seguente:

```console
helm install azure-samples/aks-helloworld
```

Installare ora una seconda istanza dell'applicazione demo. Specificare un nuovo titolo per la seconda istanza in modo che le due applicazioni siano visivamente distinte. È anche possibile specificare un nome di servizio univoco:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono ora in esecuzione nel cluster Kubernetes, anche se sono configurate con un servizio di tipo `ClusterIP`. Di conseguenza, le applicazioni non sono accessibili da Internet. Per renderle disponibili pubblicamente, creare una risorsa di ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` viene instradato al servizio `ingress-demo`. Aggiornare gli elementi *hosts* e *host* in base al nome DNS creato in un passaggio precedente.

Creare un file denominato `hello-world-ingress.yaml` e copiarlo nell'esempio YAML seguente:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Creare la risorsa di ingresso con il comando `kubectl create -f hello-world-ingress.yaml`.

```
$ kubectl create -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testare la configurazione di ingresso

Aprire un Web browser per il nome FQDN del controller di ingresso Kubernetes, ad esempio *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Questi esempi usano `letsencrypt-staging` e di conseguenza il certificato SSL emesso non è considerato attendibile dal browser. Accettare l'avviso per continuare l'applicazione. Le informazioni sul certificato indicano che il certificato *Fake LE Intermediate X1* è emesso da Let's Encrypt. Questo certificato fittizio indica che `cert-manager` ha elaborato la richiesta in modo corretto e ha ricevuto un certificato dal provider:

![Certificato temporaneo Let's Encrypt](media/ingress/staging-certificate.png)

Quando si modifica Let's Encrypt per usare `prod` anziché `staging`, viene usato un certificato attendibile emesso da Let's Encrypt, come illustrato nell'esempio seguente:

![Certificato Let's Encrypt](media/ingress/certificate.png)

L'applicazione demo viene visualizzata nel Web browser:

![Esempio di applicazione 1](media/ingress/app-one.png)

A questo punto aggiungere il percorso */hello-world-two* al nome FQDN, ad esempio *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Viene visualizzata la seconda applicazione demo con il titolo personalizzato:

![Esempio di applicazione 2](media/ingress/app-two.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad AKS. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Interfaccia della riga di comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]
- [cert-manager][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
