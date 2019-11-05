---
title: Usare il contenitore di servizi vocali con Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando Kubernetes e Helm per definire le immagini del contenitore sintesi vocale e sintesi vocale, verrà creato un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: b413bc6d29f1b08949b50570cb5baa2eb758d779
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491013"
---
# <a name="use-speech-service-container-with-kubernetes-and-helm"></a>Usare il contenitore di servizi vocali con Kubernetes e Helm

Un'opzione per gestire i contenitori di riconoscimento vocale in locale consiste nell'usare Kubernetes e Helm. Usando Kubernetes e Helm per definire le immagini del contenitore sintesi vocale e sintesi vocale, verrà creato un pacchetto Kubernetes. Questo pacchetto verrà distribuito in un cluster Kubernetes locale. Infine, si esaminerà come testare i servizi distribuiti e le varie opzioni di configurazione. Per altre informazioni sull'esecuzione di contenitori Docker senza orchestrazione Kubernetes, vedere [installare ed eseguire i contenitori dei servizi vocali](speech-container-howto.md).

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti prima di usare i contenitori di riconoscimento vocale in locale:

|Obbligatoria|Finalità|
|--|--|
| Account Azure | Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free-azure-account] prima di iniziare. |
| Accesso Container Registry | Per consentire a Kubernetes di eseguire il pull delle immagini Docker nel cluster, sarà necessario accedere al registro contenitori. |
| INTERFACCIA della riga di comando Kubernetes | L' [interfaccia][kubernetes-cli] della riga di comando di Kubernetes è necessaria per gestire le credenziali condivise dal registro contenitori. Kubernetes è necessario anche prima di Helm, ovvero Kubernetes Package Manager. |
| INTERFACCIA della riga di comando | Come parte [dell'installazione dell'][tiller-install]interfaccia della riga di comando di [Helm][helm-install] , sarà anche necessario inizializzare Helm, che installerà il timone. |
|Risorsa vocale |Per usare questi contenitori, è necessario avere:<br><br>Una risorsa di Azure _vocale_ per ottenere la chiave di fatturazione e l'URI dell'endpoint di fatturazione associati. Entrambi i valori sono disponibili nelle pagine relative alla panoramica del **riconoscimento vocale** portale di Azure e alle pagine chiavi e sono necessari per avviare il contenitore.<br><br>**{API_KEY}** : chiave di risorsa<br><br>**{ENDPOINT_URI}** : esempio di URI dell'ENDPOINT: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Configurazione del computer host consigliata

Vedere i dettagli del [computer host del contenitore di servizi vocali][speech-container-host-computer] come riferimento. Questo *grafico Helm* calcola automaticamente i requisiti di CPU e memoria in base al numero di decodificazioni (richieste simultanee) specificate dall'utente. Inoltre, si adatta a seconda che le ottimizzazioni per l'input audio/testo siano configurate come `enabled`. Per impostazione predefinita, il grafico Helm, due richieste simultanee e la disabilitazione dell'ottimizzazione.

| Servizio | CPU/contenitore | Memoria/contenitore |
|--|--|--|
| **Riconoscimento vocale** | un decodificatore richiede almeno 1.150 millicore. Se la `optimizedForAudioFile` è abilitata, sono necessari 1.950 millicore. (impostazione predefinita: due decodificatori) | Obbligatorio: 2 GB<br>Limitato: 4 GB |
| **Sintesi vocale** | una richiesta simultanea richiede un minimo di 500 millicore. Se la `optimizeForTurboMode` è abilitata, sono necessari 1.000 millicore. (impostazione predefinita: due richieste simultanee) | Obbligatorio: 1 GB<br> Limitato: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al cluster Kubernetes

Il computer host dovrebbe avere un cluster Kubernetes disponibile. Per informazioni concettuali su come distribuire un cluster Kubernetes in un computer host, vedere questa esercitazione sulla [distribuzione di un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) .

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Condivisione di credenziali Docker con il cluster Kubernetes

Per consentire al cluster Kubernetes di `docker pull` le immagini configurate dal registro contenitori `mcr.microsoft.com`, è necessario trasferire le credenziali Docker nel cluster. Eseguire il comando [`kubectl create`][kubectl-create] riportato di seguito per creare un *segreto del registro Docker* in base alle credenziali fornite dal prerequisito di accesso del registro contenitori.

Dall'interfaccia della riga di comando desiderata, eseguire il comando seguente. Assicurarsi di sostituire la `<username>`, `<password>`e `<email-address>` con le credenziali del registro contenitori.

```console
kubectl create secret docker-registry mcr \
    --docker-server=mcr.microsoft.com \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se si ha già accesso al registro contenitori di `mcr.microsoft.com`, è possibile creare un segreto Kubernetes usando invece il flag generico. Si consideri il comando seguente che viene eseguito sul file JSON di configurazione di Docker.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Il seguente output viene stampato nella console quando il segreto è stato creato correttamente.

```console
secret "mcr" created
```

Per verificare che la chiave privata sia stata creata, eseguire il [`kubectl get`][kubectl-get] con il flag di `secrets`.

```console
kuberctl get secrets
```

Eseguendo la `kubectl get secrets` vengono stampati tutti i segreti configurati.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurare i valori del grafico Helm per la distribuzione

Visita l' [Hub Microsoft Helm][ms-helm-hub] per tutti i grafici Helm disponibili pubblicamente offerti da Microsoft. Dall'hub Microsoft Helm è possibile trovare il **grafico locale di riconoscimento vocale di servizi cognitivi**. Il **riconoscimento vocale di servizi cognitivi in locale** è il grafico che verrà installato, ma è necessario prima creare un file di `config-values.yaml` con configurazioni esplicite. Per iniziare, aggiungere il repository Microsoft all'istanza di Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Successivamente, i valori del grafico Helm verranno configurati. Copiare e incollare il codice YAML seguente in un file denominato `config-values.yaml`. Per altre informazioni sulla personalizzazione del **grafico Helm di servizi cognitivi in locale**, vedere [personalizzare i grafici Helm](#customize-helm-charts). Sostituire il `# {ENDPOINT_URI}` e `# {API_KEY}` commenti con i propri valori.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speech-to-text
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
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se non vengono specificati i valori `billing` e `apikey`, i servizi scadranno dopo 15 minuti. Analogamente, la verifica avrà esito negativo perché i servizi non saranno disponibili.

### <a name="the-kubernetes-package-helm-chart"></a>Pacchetto di Kubernetes (grafico Helm)

Il *grafico Helm* contiene la configurazione dell'immagine o delle immagini Docker da estrarre dal registro contenitori `mcr.microsoft.com`.

> Un [grafico Helm][helm-charts] è una raccolta di file che descrivono un set correlato di risorse Kubernetes. Un singolo grafico può essere usato per distribuire elementi semplici, ad esempio un pod Memcache, o un elemento complesso, ad esempio uno stack di app Web completo con server HTTP, database, cache e così via.

I *grafici Helm* forniti estraggono le immagini docker del servizio di riconoscimento vocale, sia da sintesi vocale che da sintesi vocale, dal registro contenitori `mcr.microsoft.com`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installare il grafico Helm nel cluster Kubernetes

Per installare il *grafico Helm* è necessario eseguire il comando [`helm install`][helm-install-cmd] , sostituendo l'`<config-values.yaml>` con il percorso e l'argomento del nome file appropriati. Il grafico Helm `microsoft/cognitive-services-speech-onpremise` a cui si fa riferimento è disponibile nell' [Hub Helm di Microsoft qui][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Di seguito è riportato un esempio di output che può essere visualizzato dopo una corretta esecuzione dell'installazione:

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

Per il completamento della distribuzione di Kubernetes possono essere necessari diversi minuti. Per verificare che i pod e i servizi siano distribuiti e disponibili correttamente, eseguire il comando seguente:

```console
kubectl get all
```

Dovrebbe essere visualizzato un output simile al seguente:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Verificare la distribuzione Helm con i test Helm

I grafici Helm installati definiscono i *test Helm*, che fungeranno da praticità per la verifica. Questi test convalidano la conformità del servizio. Per verificare i servizi di **riconoscimento** vocale e sintesi **vocale,** verrà eseguito il comando [Helm test][helm-test] .

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Questi test avranno esito negativo se lo stato del POD non è `Running` o se la distribuzione non è elencata nella colonna `AVAILABLE`. Essere paziente perché questa operazione può richiedere più di dieci minuti.

Questi test restituiranno diversi risultati di stato:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

In alternativa all'esecuzione dei *test Helm*, è possibile raccogliere gli indirizzi *IP esterni* e le porte corrispondenti dal comando `kubectl get all`. Usando l'indirizzo IP e la porta, aprire un Web browser e passare a `http://<external-ip>:<port>:/swagger/index.html` per visualizzare la pagina o le pagine di spavalderia dell'API.

## <a name="customize-helm-charts"></a>Personalizzare i grafici Helm

I grafici Helm sono gerarchici. La gerarchia è consentita per l'ereditarietà dei grafici, ma anche per il concetto di specificità, in cui le impostazioni più specifiche sostituiscono le regole ereditate.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
