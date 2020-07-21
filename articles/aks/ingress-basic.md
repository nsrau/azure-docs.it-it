---
title: Creare un controller di ingresso
titleSuffix: Azure Kubernetes Service
description: Informazioni su come installare e configurare un controller di ingresso di base NGINX in un cluster del servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: bb7ac1d76e93a95fedc1dfdbfd67d2b057db60e3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499818"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso del servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Azure Kubernetes. Due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo usa [Helm 3][helm] per installare il controller di ingresso nginx.

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.64 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per creare il controller di ingresso, usare Helm per installare *nginx-ingress*. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate *ingress-Basic*. Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze.

> [!TIP]
> Per abilitare la [conservazione dell'indirizzo IP di origine client][client-source-ip] per le richieste ai contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` al comando Helm install. L'IP di origine del client viene archiviato nell'intestazione della richiesta sotto *X-inoltred-for*. Quando si usa un controller di ingresso con la conservazione IP dell'origine client abilitata, il pass-through SSL non funzionerà.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato un indirizzo IP dinamico, come illustrato nell'output dell'esempio seguente:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Non sono state create regole di ingresso, pertanto si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX se si passa all'indirizzo IP interno. Le regole di ingresso sono configurate nei passaggi seguenti.

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Per visualizzare il controller di ingresso in azione, eseguire due applicazioni demo nel cluster AKS. In questo esempio si usa `kubectl apply` per distribuire due istanze di una semplice applicazione *Hello World* .

Creare un file *AKS-HelloWorld-One. YAML* e copiarlo nel seguente esempio YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Creare un file *AKS-HelloWorld-Two. YAML* e copiarlo nel seguente esempio YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Eseguire le due applicazioni demo usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Creare una route in ingresso

Entrambe le applicazioni sono in esecuzione nel cluster Kubernetes. Per instradare il traffico a ogni applicazione, creare una risorsa ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso *EXTERNAL_IP* viene indirizzato al servizio denominato `aks-helloworld-one` . Il traffico verso *EXTERNAL_IP/Hello-World-Two* viene indirizzato al `aks-helloworld-two` servizio. Il traffico verso *EXTERNAL_IP/routing statico* viene indirizzato al servizio denominato `aks-helloworld-one` per gli asset statici.

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
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Creare la risorsa di ingresso con il comando `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Testare il controller di ingresso

Per testare le route per il controller di ingresso, passare alle due applicazioni. Aprire un Web browser all'indirizzo IP del controller di ingresso NGINX, ad esempio *EXTERNAL_IP*. La prima applicazione demo viene visualizzata nel Web browser, come illustrato nell'esempio seguente:

![Prima app in esecuzione dietro al controller di ingresso](media/ingress-basic/app-one.png)

Aggiungere ora il percorso */Hello-World-Two* all'indirizzo IP, ad esempio *EXTERNAL_IP/Hello-World-Two*. Viene visualizzata la seconda applicazione demo con il titolo personalizzato:

![Seconda app in esecuzione dietro al controller di ingresso](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Questo articolo ha usato Helm per installare i componenti di ingresso e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, è possibile eliminare l'intero spazio dei nomi di esempio o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di esempio e tutte le risorse

Per eliminare l'intero spazio dei nomi di esempio, usare il `kubectl delete` comando e specificare il nome dello spazio dei nomi. Vengono eliminate tutte le risorse nello spazio dei nomi.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Elimina risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Elencare le versioni Helm con il `helm list` comando. Cercare i grafici denominati *nginx-ingress* e *aks-helloworld*, come illustrato nell'output di esempio seguente:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Disinstallare le versioni con il `helm uninstall` comando. Nell'esempio seguente viene disinstallata la distribuzione di ingresso NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Rimuovere quindi le due applicazioni di esempio:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
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

- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- Creare un controller di ingresso che usa Let's Encrypt per generare automaticamente certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
