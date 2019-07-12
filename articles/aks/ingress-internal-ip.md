---
title: Creare un controller di ingresso per una rete interna del servizio Azure Kubernetes
description: Informazioni su come installare e configurare un controller di ingresso NGINX per una rete interna e privata in un cluster del servizio Azure Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 935b96bd553c9ae73b55086483baa0ea7c4aeaa4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615477"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso per una rete interna virtuale del servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingress NGINX][nginx-ingress] in un cluster Azure Kubernetes Service (AKS). Il controller di ingresso è configurato su una rete virtuale interna e privata e su un indirizzo IP. Non è consentito alcun accesso esterno. Due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterno][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione di HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che utilizza i propri certificati TLS][aks-ingress-own-tls]
- Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo usa Helm per installare il controller di ingresso NGINX, il certificato cert-manager e un'app Web di esempio. È necessario disporre di Helm inizializzato nel cluster servizio Azure Kubernetes e usare un account del servizio per Tiller. Per altre informazioni su come configurare e usare Helm, vedere [installare le applicazioni con Helm in Azure Kubernetes Service (AKS)][use-helm].

Questo articolo richiede anche di eseguire il comando di Azure versione 2.0.64 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per impostazione predefinita, il controller di ingresso NGINX viene creato con un'assegnazione di indirizzo IP pubblico e dinamico. Un requisito di configurazione comune consiste nell'usare una rete privata interna e un indirizzo IP. Questo approccio consente di limitare l'accesso ai servizi ai soli utenti interni, escludendo qualsiasi accesso esterno.

Creare un file denominato *interne ingress.yaml* usando il file manifesto di esempio seguente. Questo esempio assegna *10.240.0.42* alla risorsa *loadBalancerIP*. Fornire il proprio indirizzo IP interno per l'uso con il controller di ingresso. Assicurarsi che questo indirizzo IP non sia già in uso all'interno della rete virtuale.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

A questo punto distribuire il grafico *ingress nginx* con Helm. Per usare il file manifesto creato nel passaggio precedente, aggiungere il parametro `-f internal-ingress.yaml`. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE) non devono eseguire il controller di ingresso. Un selettore di nodo è specificato utilizzando il `--set nodeSelector` parametro per indicare l'utilità di pianificazione di Kubernetes per eseguire il controller di ingress NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse di ingresso denominato *ingress-basic*. Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze. Se il cluster AKS non RBAC abilitato, aggiungere `--set rbac.create=false` ai comandi di Helm.

> [!TIP]
> Se si vuole abilitare [mantenimento dell'IP di origine client][client-source-ip] per le richieste per i contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` per controllare tutti gli aspetti di comando di installazione. Origine del client IP viene archiviato nell'intestazione della richiesta sotto *X-Forwarded-For*. Quando si usa un controller di ingresso con conservazione dell'IP di origine client abilitata, pass-through SSL non funzionerà.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato l'indirizzo IP interno, come illustrato nell'output dell'esempio seguente:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Non sono state create regole di ingresso, pertanto si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX se si passa all'indirizzo IP interno. Le regole di ingresso sono configurate nei passaggi seguenti.

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Per visualizzare il controller di ingresso in azione, è possibile eseguire due applicazioni demo nel cluster servizio Azure Kubernetes. In questo esempio Helm viene usato per distribuire due istanze di una semplice applicazione "Hello world".

Prima di installare i grafici Helm di esempio, aggiungere il repository degli esempi di Azure all'ambiente Helm come indicato di seguito:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Creare la prima applicazione demo da un grafico Helm con il comando seguente:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installare ora una seconda istanza dell'applicazione demo. Specificare un nuovo titolo per la seconda istanza in modo che le due applicazioni siano visivamente distinte. È anche possibile specificare un nome di servizio univoco:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono in esecuzione nel cluster Kubernetes. Per instradare il traffico a ogni applicazione, creare una risorsa ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso l'indirizzo `http://10.240.0.42/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `http://10.240.0.42/hello-world-two` viene instradato al servizio `ingress-demo`.

Creare un file denominato `hello-world-ingress.yaml` e copiarlo nell'esempio YAML seguente.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Creare la risorsa di ingresso con il comando `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testare il controller di ingresso

Per testare le route per il controller di ingresso, passare alle due applicazioni con client Web. Se necessario, è possibile testare rapidamente questa funzionalità solo interna da un pod nel cluster servizio Azure Kubernetes. Creare un pod test e collegarvi una sessione terminal:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Installare `curl` nel pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Accedere a questo punto all'indirizzo del controller di ingresso Kubernetes usando `curl`, ad esempio *http://10.240.0.42* . Fornire l'indirizzo IP interno specificato al momento della distribuzione del controller di ingresso nel primo passaggio di questo articolo.

```console
curl -L http://10.240.0.42
```

Non è stato fornito nessun percorso aggiuntivo con l'indirizzo, pertanto, il controller di ingresso per impostazione predefinita viene instradato verso la route */* . Viene restituita la prima applicazione demo, come illustrato nell'output di esempio condensato seguente:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

A questo punto aggiungere il percorso */hello-world-two* all'indirizzo, ad esempio *http://10.240.0.42/hello-world-two* . Viene restituita la seconda applicazione demo con titolo personalizzato, come illustrato nell'output di esempio condensato seguente:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Questo articolo ha usato Helm per installare i componenti di ingresso e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire le risorse, è possibile eliminare lo spazio dei nomi di campione intera o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di campione e tutte le risorse

Per eliminare lo spazio dei nomi di esempio completo, usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi. Vengono eliminate tutte le risorse nello spazio dei nomi.

```console
kubectl delete namespace ingress-basic
```

Rimuovere quindi il repository Helm per l'app hello world di servizio contenitore di AZURE:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminare le risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Elenco di Helm rilascia con il `helm list` comando. Cercare i grafici denominati *nginx-ingress* e *aks-helloworld*, come illustrato nell'output di esempio seguente:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Eliminare le versioni con il comando `helm delete`. L'esempio seguente elimina la distribuzione di ingresso NGINX e le due app servizio Azure Kubernetes hello world di esempio.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Rimuovere quindi il repository Helm per le app servizio Azure Kubernetes hello world:

```console
helm repo remove azure-samples
```

Rimuovere la route in ingresso che ha indirizzato il traffico verso le app di esempio:

```console
kubectl delete -f hello-world-ingress.yaml
```

Infine, è possibile eliminare l'oggetto stesso spazio dei nomi. Usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad servizio Azure Kubernetes. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterno][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione di HTTP][aks-http-app-routing]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico statico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers