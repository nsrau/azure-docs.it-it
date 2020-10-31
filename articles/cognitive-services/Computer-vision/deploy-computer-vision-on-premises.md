---
title: Usare Visione artificiale contenitore con Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire il contenitore Visione artificiale usando Kubernetes e Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: aahi
ms.openlocfilehash: 1e77b5ea2bbd5bae79295a5680fa6e143efa5e99
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131531"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Usare Visione artificiale contenitore con Kubernetes e Helm

Un'opzione per gestire i contenitori di Visione artificiale in locale consiste nell'usare Kubernetes e Helm. Usando Kubernetes e Helm per definire un'immagine del contenitore Visione artificiale, verrà creato un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale. Si esaminerà infine come testare i servizi distribuiti. Per altre informazioni sull'esecuzione di contenitori Docker senza orchestrazione Kubernetes, vedere [installare ed eseguire visione artificiale contenitori](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti prima di usare Visione artificiale contenitori in locale:

| Necessario | Scopo |
|----------|---------|
| Account Azure | Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free-azure-account] prima di iniziare. |
| INTERFACCIA della riga di comando Kubernetes | L' [interfaccia][kubernetes-cli] della riga di comando di Kubernetes è necessaria per gestire le credenziali condivise dal registro contenitori. Kubernetes è necessario anche prima di Helm, ovvero Kubernetes Package Manager. |
| Interfaccia della riga di comando di Helm | Installare l' [interfaccia][helm-install]della riga di comando di Helm, che viene usata per installare un grafico Helm (definizione del pacchetto del contenitore). |
| Risorsa Visione artificiale |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **visione artificiale** di Azure e la chiave API associata l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica e chiavi per la risorsa e sono necessari per avviare il contenitore.<br><br>**{API_KEY}** : una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}** : endpoint fornito nella pagina **Panoramica**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al cluster Kubernetes

Il computer host dovrebbe avere un cluster Kubernetes disponibile. Per informazioni concettuali su come distribuire un cluster Kubernetes in un computer host, vedere questa esercitazione sulla [distribuzione di un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) . È possibile trovare altre informazioni sulle distribuzioni nella [documentazione di Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="configure-helm-chart-values-for-deployment"></a>Configurare i valori del grafico Helm per la distribuzione

Per iniziare, creare una cartella denominata *Read* . Incollare quindi il contenuto YAML seguente in un nuovo file denominato `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Per configurare i valori predefiniti del grafico Helm, copiare e incollare il codice YAML seguente in un file denominato `values.yaml` . Sostituire i `# {ENDPOINT_URI}` `# {API_KEY}` commenti e con i propri valori. Configurare resultExpirationPeriod, Redis e RabbitMQ, se necessario.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.1-preview
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Se i `billing` `apikey` valori e non vengono specificati, i servizi scadono dopo 15 minuti. Analogamente, la verifica ha esito negativo perché i servizi non sono disponibili.
> 
> - Se si distribuiscono più contenitori di lettura dietro un servizio di bilanciamento del carico, ad esempio in Docker Compose o Kubernetes, è necessario disporre di una cache esterna. Poiché il contenitore di elaborazione e il contenitore di richieste GET potrebbero non essere uguali, una cache esterna archivia i risultati e li condivide tra i contenitori. Per informazioni dettagliate sulle impostazioni della cache, vedere [configurare visione artificiale contenitori Docker](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).
>

Creare una cartella *templates* sotto la directory *Read* . Copiare e incollare il codice YAML seguente in un file denominato `deployment.yaml` . Il `deployment.yaml` file fungerà da modello Helm.

> I modelli generano file manifesto, che sono descrizioni di risorse in formato YAML che Kubernetes può comprendere. [-Guida ai modelli del grafico Helm][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Nella stessa cartella *templates* copiare e incollare le funzioni helper seguenti in `helpers.tpl` . `helpers.tpl` definisce funzioni utili per la generazione del modello Helm.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
Il modello specifica un servizio di bilanciamento del carico e la distribuzione del contenitore/immagine per la lettura.

### <a name="the-kubernetes-package-helm-chart"></a>Pacchetto di Kubernetes (grafico Helm)

Il *grafico Helm* contiene la configurazione dell'immagine o delle immagini Docker da estrarre dal `mcr.microsoft.com` Registro contenitori.

> Un [grafico Helm][helm-charts] è una raccolta di file che descrivono un set correlato di risorse Kubernetes. Un singolo grafico può essere usato per distribuire elementi semplici, ad esempio un pod Memcache, o un elemento complesso, ad esempio uno stack di app Web completo con server HTTP, database, cache e così via.

I *grafici Helm* forniti tirano le immagini docker del servizio visione artificiale e il servizio corrispondente dal `mcr.microsoft.com` Registro contenitori.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installare il grafico Helm nel cluster Kubernetes

Per installare il *grafico Helm* , è necessario eseguire il [`helm install`][helm-install-cmd] comando. Assicurarsi di eseguire il comando install dalla directory sopra la `read` cartella.

```console
helm install read ./read
```

Di seguito è riportato un esempio di output che può essere visualizzato dopo una corretta esecuzione dell'installazione:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Per il completamento della distribuzione di Kubernetes possono essere necessari diversi minuti. Per verificare che i pod e i servizi siano distribuiti e disponibili correttamente, eseguire il comando seguente:

```console
kubectl get all
```

Dovrebbe essere visualizzato un output simile al seguente:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altri dettagli sull'installazione di applicazioni con Helm in Azure Kubernetes Service (AKS), [visitare qui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contenitori di Servizi cognitivi][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
