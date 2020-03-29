---
title: Usare i contenitori del servizio di riconoscimento vocale con Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando Kubernetes e Helm per definire le immagini del contenitore di sintesi vocale e sintesi vocale, creeremo un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 6ad5a843c8cc287834305e09b48cd3fafe09ca51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474765"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Usare i contenitori del servizio di riconoscimento vocale con Kubernetes e Helm

Un'opzione per gestire i contenitori di riconoscimento vocale in locale consiste nell'usare Kubernetes e Helm. Usando Kubernetes e Helm per definire le immagini del contenitore di sintesi vocale e sintesi vocale, creeremo un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale. Infine, verrà illustrato come testare i servizi distribuiti e varie opzioni di configurazione. Per ulteriori informazioni sull'esecuzione di contenitori Docker senza orchestrazione Kubernetes, vedere [installare ed eseguire contenitori del servizio di riconoscimento vocale](speech-container-howto.md).

## <a name="prerequisites"></a>Prerequisiti

Prerequisiti seguenti prima di usare i contenitori di riconoscimento vocale in locale:The following prerequisites before using Speech containers on-premises:

| Obbligatoria | Scopo |
|----------|---------|
| Account Azure | Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free-azure-account] prima di iniziare. |
| Accesso al Registro di sistema del contenitoreContainer Registry | Affinché Kubernetes per estrarre le immagini docker nel cluster, sarà necessario accedere al registro contenitori. |
| Kubernetes CLI | [L'interfaccia della riga di comando di Kubernetes][kubernetes-cli] è necessaria per la gestione delle credenziali condivise dal Registro di sistema del contenitore. Kubernetes è anche necessario prima di Helm, che è il gestore di pacchetti Kubernetes. |
| Interfaccia della riga di comando di Helm | Installare [l'interfaccia della riga di comando Helm,][helm-install]utilizzata per installare un grafico helm (definizione del pacchetto contenitore). |
|Risorsa di riconoscimento vocale |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa _di Azure di riconoscimento vocale_ per ottenere la chiave di fatturazione associata e l'URI dell'endpoint di fatturazione. Entrambi i valori sono disponibili nelle pagine Panoramica **vocale** e Chiavi del portale di Azure e sono necessari per avviare il contenitore.<br><br>**API_KEY :** chiave di risorsa<br><br>: esempio di URI **dell'endpoint**è: ENDPOINT_URI`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Configurazione consigliata del computer host

Fare riferimento ai dettagli del computer host del [contenitore del servizio di riconoscimento vocale][speech-container-host-computer] come riferimento. Questo *grafico helm* calcola automaticamente i requisiti di CPU e memoria in base al numero di decodifica (richieste simultanee) specificate dall'utente. Inoltre, verrà regolato in base al fatto che le `enabled`ottimizzazioni per l'input audio/testo siano configurate come . Il grafico al timoniè viene impostato su base predefinita, due richieste simultanee e disabilitando l'ottimizzazione.

| Service | CPU / Contenitore | Memoria / Contenitore |
|--|--|--|
| **Sintesi vocale** | un decodificatore richiede un minimo di 1.150 millicore. Se `optimizedForAudioFile` l'opzione è abilitata, sono necessari 1.950 millicore. (impostazione predefinita: due decodificatori) | Obbligatorio: 2 GB<br>Limite: 4 GB |
| **Sintesi vocale** | una richiesta simultanea richiede un minimo di 500 millicore. Se `optimizeForTurboMode` l'opzione è abilitata, sono necessari 1.000 millicore. (impostazione predefinita: due richieste simultanee) | Obbligatorio: 1 GB<br> Limite: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al cluster Kubernetes

Si prevede che il computer host disponga di un cluster Kubernetes disponibile. Vedere questa [esercitazione sulla distribuzione di un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) per informazioni concettuali su come distribuire un cluster Kubernetes in un computer host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Condivisione delle credenziali Docker con il cluster Kubernetes

Per consentire al cluster Kubernetes `docker pull` di configurare `containerpreview.azurecr.io` le immagini dal Registro di sistema del contenitore, è necessario trasferire le credenziali docker nel cluster. Eseguire [`kubectl create`][kubectl-create] il comando seguente per creare un *segreto docker-registry* in base alle credenziali fornite dal prerequisito di accesso al Registro di sistema del contenitore.

Dall'interfaccia della riga di comando scelta, eseguire il comando seguente. Assicurarsi di `<username>`sostituire `<password>`, `<email-address>` e con le credenziali del Registro di sistema del contenitore.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se si ha già `containerpreview.azurecr.io` accesso al Registro di sistema del contenitore, è possibile creare un segreto Kubernetes utilizzando invece il flag generico. Si consideri il comando seguente che viene eseguito sul JSON di configurazione Docker.Consider the following command that executes against your Docker configuration JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

L'output seguente viene stampato nella console quando il segreto è stato creato correttamente.

```console
secret "mcr" created
```

Per verificare che il segreto [`kubectl get`][kubectl-get] sia `secrets` stato creato, eseguire l'oggetto con il flag .

```console
kubectl get secrets
```

L'esecuzione `kubectl get secrets` dei segreti configurati consente di stampare tutti i segreti configurati.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurare i valori del grafico Helm per la distribuzione

Visita microsoft [Helm Hub][ms-helm-hub] per tutti i grafici al timone disponibili pubblicamente offerti da Microsoft. Da Microsoft Helm Hub, è disponibili il grafico di **riconoscimento tecnico locale**di Servizi cognitivi . Riconoscimento vocale di **Servizi cognitivi locale** è il grafico che `config-values.yaml` verrà installato, ma è necessario innanzitutto creare un file con configurazioni esplicite. Iniziamo aggiungendo il repository Microsoft all'istanza di Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Successivamente, configureremo i valori del grafico Helm. Copiare e incollare il seguente `config-values.yaml`YAML in un file denominato . Per ulteriori informazioni sulla personalizzazione del grafico Helm di riconoscimento vocale di **Servizi cognitivi,** vedere [Personalizzare i grafici helm](#customize-helm-charts). Sostituire `# {ENDPOINT_URI}` i `# {API_KEY}` commenti e con i propri valori.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se `billing` i `apikey` valori e non vengono forniti, i servizi scadranno dopo 15 min. Allo stesso modo, la verifica avrà esito negativo in quanto i servizi non saranno disponibili.

### <a name="the-kubernetes-package-helm-chart"></a>Il pacchetto Kubernetes (grafico Helm)

Il *grafico Helm* contiene la configurazione delle immagini docker `containerpreview.azurecr.io` da estrarre dal Registro di sistema del contenitore.

> Un [grafico Helm][helm-charts] è una raccolta di file che descrivono un set correlato di risorse Kubernetes. Un singolo grafico può essere usato per distribuire qualcosa di semplice, come un pod memcached o qualcosa di complesso, come uno stack di app Web completo con server HTTP, database, cache e così via.

I *grafici Helm* forniti estraggono le immagini docker del servizio di riconoscimento `containerpreview.azurecr.io` vocale, sia di sintesi vocale che dei servizi di sintesi vocale dal Registro di sistema del contenitore.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installare il grafico Helm nel cluster Kubernetes

Per installare il *grafico timone* è [`helm install`][helm-install-cmd] necessario eseguire `<config-values.yaml>` il comando, sostituendo il con l'argomento path e nome file appropriato. Il `microsoft/cognitive-services-speech-onpremise` grafico Helm a cui si fa riferimento di seguito è disponibile in [Microsoft Helm Hub qui][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Di seguito è riportato un output di esempio che ci si potrebbe aspettare di vedere da un'esecuzione di installazione riuscita:Here is an example output you might expect to see from a successful install execution:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Il completamento della distribuzione di Kubernetes può richiedere alcuni minuti. Per verificare che sia i pod che i servizi siano distribuiti correttamente e siano disponibili, eseguire il comando seguente:

```console
kubectl get all
```

Si dovrebbe aspettare di vedere qualcosa di simile al seguente output:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Verificare la distribuzione di Helm con i test Helm

I grafici Helm installati definiscono i *test Helm,* che servono per comodità della verifica. Questi test convalidano la preparazione del servizio. Per verificare sia i servizi di **sintesi vocale** che i servizi di **sintesi vocale,** verrà eseguito il comando [Test Helm.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Questi test avranno esito `Running` negativo se lo stato POD `AVAILABLE` non è o se la distribuzione non è elencata nella colonna. Siate pazienti in quanto questo può richiedere più di dieci minuti per completare.

Questi test restituisceno vari risultati di stato:These tests will output various status results:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

In alternativa all'esecuzione dei *test del timone,* è possibile `kubectl get all` raccogliere gli indirizzi IP *esterni* e le porte corrispondenti dal comando. Utilizzando l'INDIRIZZO IP e la porta, aprire un browser Web e passare a `http://<external-ip>:<port>:/swagger/index.html` per visualizzare le pagine swagger API.

## <a name="customize-helm-charts"></a>Personalizzare i grafici Helm

I grafici Helm sono gerarchici. Essere gerarchico consente l'ereditarietà del grafico, si rivolge anche al concetto di specificità, in cui le impostazioni che sono regole ereditate di override più specifiche.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
