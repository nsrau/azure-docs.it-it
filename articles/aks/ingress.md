---
title: Configurare l'ingresso con il cluster di Azure Kubernetes Service (AKS)
description: Installare e configurare un controller di ingresso NGINX in un cluster di Azure Kubernetes Service (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fcf0b6f3b7f6d75006d8c10aab041c25fc0d8c39
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751286"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Ingresso HTTPS in Azure Kubernetes Service (AKS)

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso, è possibile servirsi di un singolo indirizzo esterno per instradare il traffico a più servizi in un cluster Kubernetes.

Questo documento descrive in dettaglio una distribuzione di esempio del [controller di ingresso NGINX][nginx-ingress] in un cluster di Azure Kubernetes Service (AKS). Inoltre, viene usato il progetto [cert-manager][cert-manager] per generare e configurare automaticamente certificati [Let's Encrypt][lets-encrypt]. Infine, nel cluster AKS vengono eseguite varie applicazioni, ognuna delle quali è accessibile tramite un singolo indirizzo.

## <a name="install-an-ingress-controller"></a>Installare un controller di ingresso

Usare Helm per installare il controller di ingresso NGINX. Per informazioni dettagliate sulla distribuzione, vedere la [documentazione][nginx-ingress] relativa al controller di ingresso NGINX.

In questo esempio si installa il controller nello spazio dei nomi `kube-system`; è possibile scegliere un altro spazio dei nomi. Se il cluster AKS non dispone dell'abilitazione RBAC, aggiungere `--set rbac.create=false` al comando. Per altre informazioni, vedere il [grafico nginx-ingress][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Durante l'installazione viene creato un indirizzo IP pubblico di Azure per il controller di ingresso. Per ottenere l'indirizzo IP pubblico, usare il comando kubectl get service. L'assegnazione dell'indirizzo IP al servizio potrebbe richiedere del tempo.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Perché non sono state create regole di ingresso, se si passa all'indirizzo IP pubblico si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX.

![Back-end di NGINX predefinito](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Configurare il nome DNS

Poiché vengono usati certificati HTTPS, è necessario configurare un nome FQDN per l'indirizzo IP dei controller di ingresso. Per questo esempio viene creato un nome di dominio completo di Azure mediante l'interfaccia della riga di comando di Azure. Aggiornare lo script con l'indirizzo IP del controller di ingresso e il nome che si vuole usare nel nome di dominio completo.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Il controller di ingresso dovrebbe ora essere accessibile tramite il nome di dominio completo.

## <a name="install-cert-manager"></a>Installare cert-manager

Il controller di ingresso NGINX supporta la terminazione TLS. Anche se esistono vari modi per recuperare e configurare i certificati per HTTPS, questo documento illustra come usare [cert-manager][cert-manager], che offre funzionalità di generazione e gestione automatica di certificati [Let's Encrypt][lets-encrypt].

Per installare il controller cert-manager, usare il comando di installazione Helm seguente.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Se il cluster non dispone dell'abilitazione RBAC, usare questo comando.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Per altre informazioni sulla configurazione di cert-manager, vedere [progetto cert-manager][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Creare un'autorità di certificazione (CA) cluster

Per poter emettere i certificati, cert-manager richiede un [autorità di certificazione][cert-manager-issuer] o una risorsa [ClusterIssuer][cert-manager-cluster-issuer]. Le risorse hanno funzionalità identiche tuttavia `Issuer` funziona in uno spazio dei nomi singolo mentre `ClusterIssuer` funziona in tutti gli spazi dei nomi. Per altre informazioni, vedere la documentazione relativa all'[autorità di certificazione cert-manager][cert-manager-issuer].

Creare un'autorità di certificazione cluster usando il manifesto seguente. Aggiornare l'indirizzo di posta elettronica con un indirizzo valido della propria organizzazione.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Creare un oggetto certificato

Successivamente, è necessario creare una risorsa certificato. La risorsa certificato definisce il certificato X.509 desiderato. Per altre informazioni, vedere [certificati cert-manager][cert-manager-certificates].

Creare la risorsa certificato con il manifesto seguente.

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
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Eseguire l'applicazione

A questo punto sono stati configurati un controller di ingresso e una soluzione di gestione dei certificati. Ora è possibile eseguire alcune applicazioni nel cluster del servizio contenitore di Azure.

Per questo esempio viene usato Helm per eseguire più istanze di una semplice applicazione semplice Hello World.

Prima di eseguire l'applicazione, aggiungere il repository Helm di esempi di Azure nel sistema di sviluppo.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Eseguire il grafico Hello World del servizio contenitore di Azure con il comando seguente:

```bash
helm install azure-samples/aks-helloworld
```

Installare una seconda istanza dell'applicazione Hello World.

Specificare un nuovo titolo per la seconda istanza, in modo che le due applicazioni siano visivamente distinte. È anche necessario specificare un nome di servizio univoco. Queste configurazioni sono visibili nel comando seguente.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Creare la route di ingresso

Ora entrambe le applicazioni sono in esecuzione nel cluster Kubernetes, tuttavia sono state configurate con un servizio di tipo `ClusterIP`. Di conseguenza, le applicazioni non sono accessibili da Internet. Per renderle disponibili, creare una risorsa di ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Creare un file denominato `hello-world-ingress.yaml` e copiarvi il codice YAML seguente.

Si noti che il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/` viene instradato al servizio denominato`aks-helloworld`. Il traffico verso l'indirizzo `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` viene instradato al servizio `ingress-demo`.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

Creare la risorsa di ingresso con il comando `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Testare la configurazione di ingresso

Passare al nome di dominio completo del controller di ingresso Kubernetes. Dovrebbe essere visualizzata l'applicazione Hello World.

![Esempio di applicazione 1](media/ingress/app-one.png)

Ora passare al nome di dominio completo del controller di ingresso con il percorso `/hello-world-two`. Dovrebbe essere visualizzata l'applicazione Hello World con il titolo personalizzato.

![Esempio di applicazione 2](media/ingress/app-two.png)

Si noti inoltre che la connessione è crittografata e che viene usato un certificato emesso da Let's Encrypt.

![Certificato Let's Encrypt](media/ingress/certificate.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul software illustrato in questo documento.

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
