---
title: Utilizzare il contenitore Visione artificiale con Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore Visione artificiale in un'istanza del contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458019"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Utilizzare il contenitore Visione artificiale con Kubernetes e Helm

Un'opzione per gestire i contenitori di Visione artificiale in locale consiste nell'utilizzare Kubernetes e Helm. Usando Kubernetes e Helm per definire un'immagine del contenitore Computer Vision, creeremo un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale. Infine, verrà illustrato come testare i servizi distribuiti. Per ulteriori informazioni sull'esecuzione di contenitori Docker senza orchestrazione Kubernetes, vedere [installare ed eseguire contenitori Computer Vision](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Prerequisiti

Prerequisiti seguenti prima di usare i contenitori di Visione artificiale in locale:The following prerequisites before using Computer Vision containers on-premises:

| Obbligatoria | Scopo |
|----------|---------|
| Account Azure | Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free-azure-account] prima di iniziare. |
| Kubernetes CLI | [L'interfaccia della riga di comando di Kubernetes][kubernetes-cli] è necessaria per la gestione delle credenziali condivise dal Registro di sistema del contenitore. Kubernetes è anche necessario prima di Helm, che è il gestore di pacchetti Kubernetes. |
| Interfaccia della riga di comando di Helm | Installare [l'interfaccia della riga di comando Helm,][helm-install]utilizzata per installare un grafico helm (definizione del pacchetto contenitore). |
| Risorsa Visione artificiale |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **di Azure Computer Vision** e la chiave API associata l'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica e Chiavi per la risorsa e sono necessari per avviare il contenitore.<br><br>**API_KEY :Una**delle due chiavi di risorsa disponibili nella pagina **Chiavi**<br><br>**ENDPOINT_URI :** l'endpoint come indicato nella pagina **Panoramica**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al cluster Kubernetes

Si prevede che il computer host disponga di un cluster Kubernetes disponibile. Vedere questa [esercitazione sulla distribuzione di un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) per informazioni concettuali su come distribuire un cluster Kubernetes in un computer host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Condivisione delle credenziali Docker con il cluster Kubernetes

Per consentire al cluster Kubernetes `docker pull` di configurare `containerpreview.azurecr.io` le immagini dal Registro di sistema del contenitore, è necessario trasferire le credenziali docker nel cluster. Eseguire [`kubectl create`][kubectl-create] il comando seguente per creare un *segreto docker-registry* in base alle credenziali fornite dal prerequisito di accesso al Registro di sistema del contenitore.

Dall'interfaccia della riga di comando scelta, eseguire il comando seguente. Assicurarsi di `<username>`sostituire `<password>`, `<email-address>` e con le credenziali del Registro di sistema del contenitore.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se si ha già `containerpreview.azurecr.io` accesso al Registro di sistema del contenitore, è possibile creare un segreto Kubernetes utilizzando invece il flag generico. Si consideri il comando seguente che viene eseguito sul JSON di configurazione Docker.Consider the following command that executes against your Docker configuration JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

L'output seguente viene stampato nella console quando il segreto è stato creato correttamente.

```console
secret "containerpreview" created
```

Per verificare che il segreto [`kubectl get`][kubectl-get] sia `secrets` stato creato, eseguire l'oggetto con il flag .

```console
kubectl get secrets
```

L'esecuzione `kubectl get secrets` dei segreti configurati consente di stampare tutti i segreti configurati.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurare i valori del grafico Helm per la distribuzione

Iniziare creando una cartella denominata *read*, quindi incollare il seguente contenuto YAML in un nuovo file denominato *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Per configurare i valori predefiniti del grafico Helm, copiare `values.yaml`e incollare il seguente YAML in un file denominato . Sostituire `# {ENDPOINT_URI}` i `# {API_KEY}` commenti e con i propri valori.

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
> Se `billing` i `apikey` valori e non vengono forniti, i servizi scadranno dopo 15 min. Allo stesso modo, la verifica avrà esito negativo in quanto i servizi non saranno disponibili.

Creare una cartella di *modelli* nella directory *di lettura.* Copiare e incollare il seguente `deployment.yaml`YAML in un file denominato . Il `deployment.yaml` file fungerà da modello Helm.

> I modelli generano file manifesto, ovvero descrizioni delle risorse in formato YAML che Kubernetes è in grado di comprendere. [- Guida al modello di Helm Chart][chart-template-guide]

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

### <a name="the-kubernetes-package-helm-chart"></a>Il pacchetto Kubernetes (grafico Helm)

Il *grafico Helm* contiene la configurazione delle immagini docker `containerpreview.azurecr.io` da estrarre dal Registro di sistema del contenitore.

> Un [grafico Helm][helm-charts] è una raccolta di file che descrivono un set correlato di risorse Kubernetes. Un singolo grafico può essere usato per distribuire qualcosa di semplice, come un pod memcached o qualcosa di complesso, come uno stack di app Web completo con server HTTP, database, cache e così via.

I *grafici Helm* forniti estraggono le immagini della finestra `containerpreview.azurecr.io` mobile del servizio di visione artificiale e il servizio corrispondente dal Registro di sistema del contenitore.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installare il grafico Helm nel cluster Kubernetes

Per installare il *grafico helm,* è [`helm install`][helm-install-cmd] necessario eseguire il comando. Assicurarsi di eseguire il comando `read` di installazione dalla directory sopra la cartella.

```console
helm install read ./read
```

Di seguito è riportato un output di esempio che ci si potrebbe aspettare di vedere da un'esecuzione di installazione riuscita:Here is an example output you might expect to see from a successful install execution:

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

Il completamento della distribuzione di Kubernetes può richiedere alcuni minuti. Per verificare che sia i pod che i servizi siano distribuiti correttamente e siano disponibili, eseguire il comando seguente:

```console
kubectl get all
```

Si dovrebbe aspettare di vedere qualcosa di simile al seguente output:

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

Per ulteriori informazioni sull'installazione di applicazioni con Helm nel servizio Azure Kubernetes (AKS), [visitare il sito Web all'indirizzo .][installing-helm-apps-in-aks]

> [!div class="nextstepaction"]
> [Contenitori di servizi cognitivi][cog-svcs-containers]

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
