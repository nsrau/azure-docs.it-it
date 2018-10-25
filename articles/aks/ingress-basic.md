---
title: Creare un controller di ingresso di base del servizio Kubernetes di Azure (AKS)
description: Informazioni su come installare e configurare un controller di ingresso di base NGINX in un cluster del servizio Kubernetes di Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 3b6a0bb47e070c094fd955257e6ed041b6634db8
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362978"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Creare un controller di ingresso del servizio Kubernetes di Azure (AKS)

Un controller di ingresso è un componente software che fornisce proxy inverso, routing del traffico configurabile e terminazione TLS per i servizi Kubernetes. Le risorse di ingresso Kubernetes vengono usate per configurare le regole di ingresso e le route per i singoli servizi Kubernetes. Usando un controller di ingresso e regole di ingresso è possibile servirsi di un singolo indirizzo IP per instradare il traffico a più servizi in un cluster Kubernetes.

Questo articolo illustra come distribuire il [controller di ingresso NGINX][nginx-ingress] in un cluster del servizio Kubernetes di Azure (AKS). Due applicazioni vengono eseguite nel cluster AKS, ognuna delle quali è accessibile tramite un singolo indirizzo IP.

È anche possibile:

- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico statico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo usa Helm per installare il controller di ingresso NGINX, il certificato cert-manager e un'app Web di esempio. È necessario disporre di Helm inizializzato nel cluster AKS e usare un account del servizio per Tiller. Per altre informazioni sulla configurazione e l'uso di Helm, vedere [Installare le applicazioni con Helm nel servizio Kubernetes di Azure (AKS)][use-helm].

Questo articolo richiede anche che sia in esecuzione l'interfaccia della riga di comando di Azure versione 2.0.41 o successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Creare un controller di ingresso

Per creare il controller di ingresso, usare `Helm` per installare *Ingresso nginx*. Per maggiore ridondanza, vengono distribuite due repliche dei controller di ingresso NGINX con il parametro `--set controller.replicaCount`. Per sfruttare appieno le repliche del controller di ingresso in esecuzione, assicurarsi che nel cluster AKS siano presenti più nodi.

> [!TIP]
> L'esempio seguente illustra come installare il controller di ingresso nello spazio dei nomi `kube-system`. Se lo si desidera, è possibile specificare uno spazio dei nomi diverso per il proprio ambiente. Se il cluster AKS non dispone dell'abilitazione RBAC, aggiungere `--set rbac.create=false` ai comandi.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Quando viene creato il servizio di bilanciamento del carico di Kubernetes per il controller di ingresso NGINX, viene assegnato un indirizzo IP dinamico, come illustrato nell'output dell'esempio seguente:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
```

Non sono state create regole di ingresso, pertanto si viene instradati alla pagina 404 predefinita dei controller di ingresso NGINX se si passa all'indirizzo IP interno. Le regole di ingresso sono configurate nei passaggi seguenti.

## <a name="run-demo-applications"></a>Eseguire applicazioni demo

Per visualizzare il controller di ingresso in azione, è possibile eseguire due applicazioni demo nel cluster AKS. In questo esempio Helm viene usato per distribuire due istanze di una semplice applicazione "Hello world".

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

Entrambe le applicazioni sono in esecuzione nel cluster Kubernetes. Per instradare il traffico a ogni applicazione, creare una risorsa ingresso Kubernetes. La risorsa di ingresso configura le regole che instradano il traffico a una delle due applicazioni.

Nell'esempio seguente il traffico verso l'indirizzo `http://40.117.74.8/` viene instradato al servizio denominato `aks-helloworld`. Il traffico verso l'indirizzo `http://40.117.74.8/hello-world-two` viene instradato al servizio `ingress-demo`.

Creare un file denominato `hello-world-ingress.yaml` e copiarlo nell'esempio YAML seguente.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
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

Creare la risorsa di ingresso con il comando `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testare il controller di ingresso

Per testare le route per il controller di ingresso, passare alle due applicazioni. Aprire un Web browser per l'indirizzo IP del controller di ingresso NGINX, ad esempio *http://40.117.74.8*. La prima applicazione demo viene visualizzata nel Web browser, come illustrato nell'esempio seguente:

![Prima app in esecuzione dietro al controller di ingresso](media/ingress-basic/app-one.png)

A questo punto aggiungere il percorso */hello-world-two* all'indirizzo IP, ad esempio *http://40.117.74.8/hello-world-two*. Viene visualizzata la seconda applicazione demo con il titolo personalizzato:

![Seconda app in esecuzione dietro al controller di ingresso](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Questo articolo ha usato Helm per installare i componenti di ingresso e le app di esempio. Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per eseguire la pulizia di queste risorse, elencare prima le versioni di Helm con il comando `helm list`. Cercare i grafici denominati *nginx-ingress* e *aks-helloworld*, come illustrato nell'output di esempio seguente:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
gilded-duck         1           Tue Oct 16 16:52:25 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
righteous-numbat    1           Tue Oct 16 16:53:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
looming-moth        1           Tue Oct 16 16:53:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Eliminare le versioni con il comando `helm delete`. L'esempio seguente elimina la distribuzione di ingresso NGINX e le due app AKS hello world di esempio.

```
$ helm delete gilded-duck righteous-numbat looming-moth

release "gilded-duck" deleted
release "righteous-numbat" deleted
release "looming-moth" deleted
```

Rimuovere quindi il repository Helm per le app AKS hello world:

```console
helm repo remove azure-samples
```

Rimuovere infine la route in ingresso che ha indirizzato il traffico verso le app di esempio:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati inclusi alcuni componenti esterni ad AKS. Per altre informazioni su questi componenti, vedere le pagine di progetto seguenti:

- [Interfaccia della riga di comando di Helm][helm-cli]
- [Controller di ingresso NGINX][nginx-ingress]

È anche possibile:

- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico statico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
