---
title: Creare un controller di ingresso di base del servizio Azure Kubernetes
description: Informazioni su come installare e configurare un controller di ingresso di base NGINX in un cluster del servizio Azure Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7b1fb26adc49067c35745011414ada7b33d7e55e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913585"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso del servizio Azure Kubernetes

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso, è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il controller di [ingresso nginx][nginx-ingress] in un cluster di Azure Kubernetes Service (AKS). Due applicazioni vengono eseguite nel cluster servizio Azure Kubernetes, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Abilita il componente aggiuntivo routing applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usi una rete interna, privata e un indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- Creare un controller di ingresso che usa la crittografia di crittografia per generare automaticamente i certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo usa Helm per installare il controller di ingresso NGINX, il certificato cert-manager e un'app Web di esempio. È necessario disporre di Helm inizializzato nel cluster servizio Azure Kubernetes e usare un account del servizio per Tiller. Per altre informazioni sulla configurazione e sull'uso di Helm, vedere [installare applicazioni con Helm in Azure Kubernetes Service (AKS)][use-helm].

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.64 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per creare il controller di ingresso, usare `Helm` per installare *Ingresso nginx*. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster servizio Azure Kubernetes siano presenti più nodi.

Il controller di ingresso deve anche essere pianificato in un nodo Linux. I nodi di Windows Server (attualmente in anteprima in AKS) non devono eseguire il controller di ingresso. Un selettore di nodo viene specificato con il parametro `--set nodeSelector` per indicare all'utilità di pianificazione Kubernetes di eseguire il controller di ingresso NGINX in un nodo basato su Linux.

> [!TIP]
> L'esempio seguente crea uno spazio dei nomi Kubernetes per le risorse in ingresso denominate *ingress-Basic*. Specificare uno spazio dei nomi per il proprio ambiente in base alle esigenze. Se il cluster AKS non è abilitato per il controllo degli accessi in base al ruolo, aggiungere `--set rbac.create=false` ai comandi Helm.

> [!TIP]
> Se si vuole abilitare la [conservazione degli indirizzi IP delle origini client][client-source-ip] per le richieste ai contenitori nel cluster, aggiungere `--set controller.service.externalTrafficPolicy=Local` al comando Helm install. L'IP di origine del client viene archiviato nell'intestazione della richiesta sotto *X-inoltred-for*. Quando si usa un controller di ingresso con la conservazione IP dell'origine client abilitata, il pass-through SSL non funzionerà.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato un indirizzo IP dinamico, come illustrato nell'output dell'esempio seguente:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Non sono state create regole di ingresso, pertanto che si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX se si passa all'indirizzo IP interno. Le regole di ingresso sono configurate nei passaggi seguenti.

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

Nell'esempio seguente il traffico verso l'indirizzo `http://40.117.74.8/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `http://40.117.74.8/hello-world-two` viene instradato al servizio `ingress-demo`.

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

Per testare le route per il controller di ingresso, passare alle due applicazioni. Aprire un Web browser per l'indirizzo IP del controller di ingresso NGINX, ad esempio *http://40.117.74.8* . La prima applicazione demo viene visualizzata nel Web browser, come illustrato nell'esempio seguente:

![Prima app in esecuzione dietro al controller di ingresso](media/ingress-basic/app-one.png)

A questo punto aggiungere il percorso */hello-world-two* all'indirizzo IP, ad esempio *http://40.117.74.8/hello-world-two* . Viene visualizzata la seconda applicazione demo con il titolo personalizzato:

![Seconda app in esecuzione dietro al controller di ingresso](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Questo articolo ha usato Helm per installare i componenti di ingresso e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, è possibile eliminare l'intero spazio dei nomi di esempio o le singole risorse.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminare lo spazio dei nomi di esempio e tutte le risorse

Per eliminare l'intero spazio dei nomi di esempio, usare il comando `kubectl delete` e specificare il nome dello spazio dei nomi. Vengono eliminate tutte le risorse nello spazio dei nomi.

```console
kubectl delete namespace ingress-basic
```

Rimuovere quindi il repository Helm per l'app AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Elimina risorse singolarmente

In alternativa, un approccio più granulare consiste nell'eliminare le singole risorse create. Elencare le versioni Helm con il comando `helm list`. Cercare i grafici denominati *nginx-ingress* e *aks-helloworld*, come illustrato nell'output di esempio seguente:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

Eliminare le versioni con il comando `helm delete`. L'esempio seguente elimina la distribuzione di ingresso NGINX e le due app servizio Azure Kubernetes hello world di esempio.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
```

Rimuovere quindi il repository Helm per le app servizio Azure Kubernetes hello world:

```console
helm repo remove azure-samples
```

Rimuovere la route in ingresso che ha indirizzato il traffico verso le app di esempio:

```console
kubectl delete -f hello-world-ingress.yaml
```

Infine, è possibile eliminare lo spazio dei nomi stesso. Usare il comando `kubectl delete` e specificare il nome dello spazio dei nomi:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad servizio Azure Kubernetes. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [INTERFACCIA della riga di comando][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]

È anche possibile:

- [Abilita il componente aggiuntivo routing applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usi una rete interna, privata e un indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso che usa i propri certificati TLS][aks-ingress-own-tls]
- Creare un controller di ingresso che usa la crittografia di crittografia per generare automaticamente i certificati TLS [con un indirizzo IP pubblico dinamico][aks-ingress-tls] o [con un indirizzo IP pubblico statico][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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
