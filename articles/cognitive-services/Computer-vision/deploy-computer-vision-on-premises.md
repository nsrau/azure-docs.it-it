---
title: Usare Visione artificiale contenitore con Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Visione artificiale in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 7560f2395447e81dcd01e1d3e092b39b129b4ce3
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129821"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Usare Visione artificiale contenitore con Kubernetes e Helm

Un'opzione per gestire i contenitori di Visione artificiale in locale consiste nell'usare Kubernetes e Helm. Usando Kubernetes e Helm per definire un'immagine del contenitore Visione artificiale, verrà creato un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale. Si esaminerà infine come testare i servizi distribuiti. Per altre informazioni sull'esecuzione di contenitori Docker senza orchestrazione Kubernetes, vedere [installare ed eseguire visione artificiale contenitori](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti prima di usare Visione artificiale contenitori in locale:

|Obbligatoria|Scopo|
|--|--|
| Account Azure | Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free-azure-account] prima di iniziare. |
| Accesso Container Registry | Per consentire a Kubernetes di eseguire il pull delle immagini Docker nel cluster, sarà necessario accedere al registro contenitori. Per prima cosa è necessario [richiedere l'accesso al registro contenitori][vision-preview-access] . |
| INTERFACCIA della riga di comando Kubernetes | L' [interfaccia][kubernetes-cli] della riga di comando di Kubernetes è necessaria per gestire le credenziali condivise dal registro contenitori. Kubernetes è necessario anche prima di Helm, ovvero Kubernetes Package Manager. |
| INTERFACCIA della riga di comando | Come parte dell'installazione dell'[interfaccia della riga di comando di Helm][helm-install], sarà anche necessario inizializzare Helm, che installerà il [timone][tiller-install]. |
| Risorsa Visione artificiale |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **visione artificiale** di Azure e la chiave API associata l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica e chiavi per la risorsa e sono necessari per avviare il contenitore.<br><br>**{API_KEY}** : Una delle due chiavi di risorsa disponibili nella pagina **chiavi**<br><br>**{ENDPOINT_URI}** : Endpoint fornito nella pagina **Panoramica**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al cluster Kubernetes

Il computer host dovrebbe avere un cluster Kubernetes disponibile. Per informazioni concettuali su come distribuire un cluster Kubernetes in un computer host, vedere questa esercitazione sulla [distribuzione di un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) .

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Condivisione di credenziali Docker con il cluster Kubernetes

Per consentire al cluster Kubernetes di `docker pull` accedere alle immagini configurate `containerpreview.azurecr.io` dal registro contenitori, è necessario trasferire le credenziali Docker nel cluster. Eseguire il [`kubectl create`][kubectl-create] comando seguente per creare un *segreto del registro Docker* in base alle credenziali fornite dal prerequisito di accesso del registro contenitori.

Dall'interfaccia della riga di comando desiderata, eseguire il comando seguente. Assicurarsi di sostituire `<username>`, `<password>`e `<email-address>` con le credenziali del registro contenitori.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se si ha già accesso al `containerpreview.azurecr.io` registro contenitori, è possibile creare un segreto Kubernetes usando invece il flag generico. Si consideri il comando seguente che viene eseguito sul file JSON di configurazione di Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Il seguente output viene stampato nella console quando il segreto è stato creato correttamente.

```console
secret "containerpreview" created
```

Per verificare che la chiave privata sia stata creata, eseguire [`kubectl get`][kubectl-get] con il `secrets` flag.

```console
kuberctl get secrets
```

L'esecuzione di `kubectl get secrets` stampa tutti i segreti configurati.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurare i valori del grafico Helm per la distribuzione

# <a name="readtabread"></a>[Lettura](#tab/read)

Per iniziare, creare una cartella denominata *Read*, quindi incollare il contenuto YAML seguente in un nuovo file denominato *Chart. yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Per configurare i valori predefiniti del grafico Helm, copiare e incollare il codice YAML seguente in un `values.yaml`file denominato. Sostituire i `# {ENDPOINT_URI}` commenti `# {API_KEY}` e con i propri valori.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se i `billing` valori `apikey` e non vengono specificati, i servizi scadranno dopo 15 minuti. Analogamente, la verifica avrà esito negativo perché i servizi non saranno disponibili.

Creare una cartella *templates* sotto la directory *Read* . Copiare e incollare il codice YAML seguente in un file `deployment.yaml`denominato. Il `deployment.yaml` file fungerà da modello Helm.

> I modelli generano file manifesto, che sono descrizioni di risorse in formato YAML che Kubernetes può comprendere. [-Guida ai modelli del grafico Helm][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
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
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Il modello specifica un servizio di bilanciamento del carico e la distribuzione del contenitore/immagine per la lettura.

# <a name="recognize-texttabrecognize-text"></a>[Riconoscimento del testo](#tab/recognize-text)

Per iniziare, creare una cartella denominata *Text-Recognizer*, copiare e incollare il contenuto YAML seguente in un nuovo file denominato `Chart.yml`.

```yaml
apiVersion: v1
name: text-recognizer
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-recognize-text to a Kubernetes cluster
```

Per configurare i valori predefiniti del grafico Helm, copiare e incollare il codice YAML seguente in un `values.yaml`file denominato. Sostituire i `# {ENDPOINT_URI}` commenti `# {API_KEY}` e con i propri valori.

```yaml
# These settings are deployment specific and users can provide customizations

recognizeText:
  enabled: true
  image:
    name: cognitive-services-recognize-text
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-recognize-text
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se i `billing` valori `apikey` e non vengono specificati, i servizi scadranno dopo 15 minuti. Analogamente, la verifica avrà esito negativo perché i servizi non saranno disponibili.

Creare una cartella *templates* sotto la directory *Text-Recognizer* . Copiare e incollare il codice YAML seguente in un file `deployment.yaml`denominato. Il `deployment.yaml` file fungerà da modello Helm.

> I modelli generano file manifesto, che sono descrizioni di risorse in formato YAML che Kubernetes può comprendere. [-Guida ai modelli del grafico Helm][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: text-recognizer
spec:
  template:
    metadata:
      labels:
        app: text-recognizer-app
    spec:
      containers:
      - name: {{.Values.recognizeText.image.name}}
        image: {{.Values.recognizeText.image.registry}}{{.Values.recognizeText.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.recognizeText.image.args.eula}}
        - name: billing
          value: {{.Values.recognizeText.image.args.billing}}
        - name: apikey
          value: {{.Values.recognizeText.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.recognizeText.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: text-recognizer
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: text-recognizer-app
```

Il modello specifica un servizio di bilanciamento del carico e la distribuzione del contenitore/immagine per il riconoscimento del testo.

***

### <a name="the-kubernetes-package-helm-chart"></a>Pacchetto di Kubernetes (grafico Helm)

Il *grafico Helm* contiene la configurazione dell'immagine o delle immagini Docker da estrarre dal `containerpreview.azurecr.io` registro contenitori.

> Un [grafico Helm][helm-charts] è una raccolta di file che descrivono un set correlato di risorse Kubernetes. Un singolo grafico può essere usato per distribuire elementi semplici, ad esempio un pod Memcache, o un elemento complesso, ad esempio uno stack di app Web completo con server HTTP, database, cache e così via.

I *grafici Helm* forniti tirano le immagini docker del servizio visione artificiale e il servizio corrispondente dal `containerpreview.azurecr.io` registro contenitori.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installare il grafico Helm nel cluster Kubernetes

# <a name="readtabread"></a>[Lettura](#tab/read)

Per installare il *grafico Helm*, è necessario eseguire il [`helm install`][helm-install-cmd] comando. Assicurarsi di eseguire il comando install dalla directory sopra la `read` cartella.

```console
helm install read --name read
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
λ kubectl get all
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

# <a name="recognize-texttabrecognize-text"></a>[Riconoscimento del testo](#tab/recognize-text)

Per installare il *grafico Helm*, è necessario eseguire il [`helm install`][helm-install-cmd] comando. Assicurarsi di eseguire il comando install dalla directory sopra la `text-recognizer` cartella.

```console
helm install text-recognizer --name text-recognizer
```

Di seguito è riportato un esempio di output che può essere visualizzato dopo una corretta esecuzione dell'installazione:

```console
NAME:   text-recognizer
LAST DEPLOYED: Thu Aug 22 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                              READY  STATUS             RESTARTS  AGE
text-recognizer-57cb76bcf7-45sdh  0/1    ContainerCreating  0         0s

==> v1/Service
NAME             TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
text-recognizer  LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME             READY  UP-TO-DATE  AVAILABLE  AGE
text-recognizer  0/1    1           0          0s
```

Per il completamento della distribuzione di Kubernetes possono essere necessari diversi minuti. Per verificare che i pod e i servizi siano distribuiti e disponibili correttamente, eseguire il comando seguente:

```console
kubectl get all
```

Dovrebbe essere visualizzato un output simile al seguente:

```console
λ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/text-recognizer-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/text-recognizer   LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/text-recognizer   1/1     1            1           17s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/text-recognizer-57cb76bcf7   1         1         1       17s
```

***

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altri dettagli sull'installazione di applicazioni con Helm in Azure Kubernetes Service (AKS), [visitare qui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contenitori di servizi cognitivi][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-preview-access]: computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
