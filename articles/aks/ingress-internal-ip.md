---
title: Controller di ingresso nella rete interna
titleSuffix: Azure Kubernetes Service
description: Informazioni su come installare e configurare un controller di ingresso NGINX per una rete interna e privata in un cluster del servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8ef83c25f4af85fcf8dbb1ee78bd3f797e5a3581
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131140"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso per una rete interna virtuale del servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Azure Kubernetes. Il controller di ingresso è configurato su una rete virtuale interna e privata e su un indirizzo IP. Non è consentito alcun accesso esterno. Due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo usa [Helm 3][helm] per installare il controller di ingresso nginx. Assicurarsi di usare la versione più recente di Helm e di avere accesso al repository Helm in *ingresso-nginx* . Per altre informazioni sulla configurazione e l'uso di Helm, vedere [Installare le applicazioni con Helm nel servizio Azure Kubernetes][use-helm].

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.64 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per impostazione predefinita, il controller di ingresso NGINX viene creato con un'assegnazione di indirizzo IP pubblico e dinamico. Un requisito di configurazione comune consiste nell'usare una rete privata interna e un indirizzo IP. Questo approccio consente di limitare l'accesso ai servizi ai soli utenti interni, escludendo qualsiasi accesso esterno.

Creare un file denominato *interne ingress.yaml* usando il file manifesto di esempio seguente. Questo esempio assegna *10.240.0.42* alla risorsa *loadBalancerIP* . Fornire il proprio indirizzo IP interno per l'uso con il controller di ingresso. Assicurarsi che questo indirizzo IP non sia già in uso all'interno della rete virtuale. Inoltre, se si usa una rete virtuale e una subnet esistenti, è necessario configurare il cluster AKS con le autorizzazioni corrette per gestire la rete virtuale e la subnet. Per altre informazioni, vedere [usare la rete kubenet con gli intervalli di indirizzi IP in Azure Kubernetes Service (AKS)][aks-configure-kubenet-networking] o [configurare la rete di Azure CNI in Azure KUBERNETES Service (AKS)][aks-configure-advanced-networking] .

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

A questo punto distribuire il grafico *ingress nginx* con Helm. Per usare il file manifesto creato nel passaggio precedente, aggiungere il parametro `-f internal-ingress.yaml`. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate *ingress-Basic* . Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze. Se il cluster AKS non è abilitato per il controllo degli accessi in base al ruolo, aggiungere `--set rbac.create=false` ai comandi Helm.

> [!TIP]
> Per abilitare la [conservazione dell'indirizzo IP di origine client][client-source-ip] per le richieste ai contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` al comando Helm install. L'IP di origine del client viene archiviato nell'intestazione della richiesta sotto *X-inoltred-for* . Quando si usa un controller di ingresso con la conservazione IP dell'origine client abilitata, il pass-through TLS non funzionerà.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando viene creato il servizio di bilanciamento del carico Kubernetes per il controller di ingresso NGINX, viene assegnato l'indirizzo IP interno. Per ottenere l'indirizzo IP pubblico, usare il comando `kubectl get service`.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Sono necessari alcuni minuti per l'assegnazione dell'indirizzo IP al servizio, come illustrato nell'output di esempio seguente:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Non sono state create regole di ingresso, pertanto si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX se si passa all'indirizzo IP interno. Le regole di ingresso sono configurate nei passaggi seguenti.

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Per visualizzare il controller di ingresso in azione, eseguire due applicazioni demo nel cluster AKS. In questo esempio si usa `kubectl apply` per distribuire due istanze di una semplice applicazione *Hello World* .

Creare un file *AKS-HelloWorld. YAML* e copiarlo nel seguente esempio YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Creare un file in *ingresso-demo. YAML* e copiarlo nell'esempio YAML seguente:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Eseguire le due applicazioni demo usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono in esecuzione nel cluster Kubernetes. Per instradare il traffico a ogni applicazione, creare una risorsa ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso l'indirizzo `http://10.240.0.42/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `http://10.240.0.42/hello-world-two` viene instradato al servizio `ingress-demo`.

Creare un file denominato `hello-world-ingress.yaml` e copiarlo nell'esempio YAML seguente.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Creare la risorsa di ingresso con il comando `kubectl apply -f hello-world-ingress.yaml`.

```console
kubectl apply -f hello-world-ingress.yaml
```

L'output di esempio seguente mostra che la risorsa di ingresso è stata creata.

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

Ora accedere all'indirizzo del controller di ingresso Kubernetes usando `curl` , ad esempio *http://10.240.0.42* . Fornire l'indirizzo IP interno specificato al momento della distribuzione del controller di ingresso nel primo passaggio di questo articolo.

```console
curl -L http://10.240.0.42
```

Non è stato fornito alcun percorso aggiuntivo con l'indirizzo, quindi il controller di ingresso per impostazione predefinita è la */* Route. Viene restituita la prima applicazione demo, come illustrato nell'output di esempio condensato seguente:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Aggiungere ora il percorso */Hello-World-Two* all'indirizzo, ad esempio *http://10.240.0.42/hello-world-two* . Viene restituita la seconda applicazione demo con titolo personalizzato, come illustrato nell'output di esempio condensato seguente:

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

Questo articolo ha usato Helm per installare i componenti in ingresso. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, è possibile eliminare l'intero spazio dei nomi di esempio o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di esempio e tutte le risorse

Per eliminare l'intero spazio dei nomi di esempio, usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi. Vengono eliminate tutte le risorse nello spazio dei nomi.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Elimina risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Elencare le versioni Helm con il `helm list` comando. 

```console
helm list --namespace ingress-basic
```

Cercare i grafici denominati *nginx-ingress* e *aks-helloworld* , come illustrato nell'output di esempio seguente:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Disinstallare le versioni con il `helm uninstall` comando.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Nell'esempio seguente viene disinstallata la distribuzione di ingresso NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Rimuovere quindi le due applicazioni di esempio:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Rimuovere la route in ingresso che ha indirizzato il traffico verso le app di esempio:

```console
kubectl delete -f hello-world-ingress.yaml
```

Infine, è possibile eliminare lo spazio dei nomi stesso. Usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad servizio Azure Kubernetes. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Interfaccia della riga di comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]

È anche possibile:

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico statico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
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
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md