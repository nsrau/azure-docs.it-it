---
title: Usare Kubernetes in locale
titleSuffix: Azure Cognitive Services
description: Utilizzo (K8s) di Kubernetes e Helm per definire le immagini del contenitore per il riconoscimento vocale e sintesi vocale, si creerà un pacchetto di Kubernetes. Questo pacchetto verrà distribuito a Kubernetes cluster in locale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: dapine
ms.openlocfilehash: 1e3afc80abad5f5c1f9b4d57c52ca75449eeb755
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711483"
---
# <a name="use-kubernetes-on-premises"></a>Usare Kubernetes in locale

Utilizzo (K8s) di Kubernetes e Helm per definire le immagini del contenitore per il riconoscimento vocale e sintesi vocale, si creerà un pacchetto di Kubernetes. Questo pacchetto verrà distribuito a Kubernetes cluster in locale. Infine, verrà illustrato come testare i servizi distribuiti e diverse opzioni di configurazione.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale.

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][free-azure-account] prima di iniziare.
* Installare il [CLI Azure][azure-cli] (az).
* Installare il [Kubernetes CLI][kubernetes-cli] (kubectl).
* Installare il [Helm][helm-install] client, la gestione di pacchetti di Kubernetes.
    * Installazione del server, Helm [Tiller][tiller-install].
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con tali immagini del contenitore:
    * **Riconoscimento vocale** livelli solo di risorsa con F0 o prezzo Standard.
    * Risorsa **Servizi cognitivi** con piano tariffario S0.

## <a name="the-recommended-host-computer-configuration"></a>La configurazione del computer host consigliato

Vedere le [computer host del servizio di riconoscimento vocale contenitore][speech-container-host-computer] dettagli come riferimento. Ciò *grafico helm* calcola automaticamente i requisiti di CPU e memoria basati su quanti decodifica (richieste simultanee) specificato dall'utente. Inoltre, si adatterà base indica se le ottimizzazioni per l'input audio, testo o sono configurate come `enabled`. Il valore predefinito è, due richieste simultanee e la disabilitazione dell'ottimizzazione del grafico di helm.

| Service | CPU / contenitore | Memoria / contenitore |
|--|--|--|
| **Riconoscimento vocale** | un decodificatore richiede un minimo di 1,150 millicore. Se il `optimizedForAudioFile` è abilitato, quindi 1,950 millicore sono necessari. (predefinito: due decodificatori di immagini) | Obbligatorio: 2 GB<br>Limited:  4 GB |
| **Sintesi vocale** | una richiesta simultanea richiede almeno 500 millicore. Se il `optimizeForTurboMode` è abilitata, sono necessari 1.000 millicore. (predefinito: due richieste simultanee) | Obbligatorio: 1 GB<br> Limited: 2 GB |

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Inviare il [modulo di richiesta di contenitori vocale servizi cognitivi][speech-preview-access] per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al cluster Kubernetes

È previsto che il computer host dispone di un cluster Kubernetes disponibili. Vedere l'esercitazione sul [distribuisce un cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) per una comprensione concettuale dei come distribuire un cluster Kubernetes in un computer host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Condividere le credenziali di Docker con il cluster Kubernetes

Per consentire al cluster Kubernetes `docker pull` le immagini da configurati il `containerpreview.azurecr.io` registro contenitori, è necessario trasferire le credenziali di docker nel cluster. Eseguire la [ `kubectl create` ][kubectl-create] comando seguente per creare una *segreto del registro docker* basato sulle credenziali fornite dal contenitore [accesso al registro](#request-access-to-the-container-registry) sezione.

Dall'interfaccia della riga di comando di scelta, eseguire il comando seguente. Assicurarsi di sostituire il `<username>`, `<password>`, e `<email-address>` con le credenziali del registro contenitori.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se si hanno già accesso al `containerpreview.azurecr.io` registro contenitori, è possibile creare un segreto Kubernetes usando invece il flag generico. Prendere in considerazione il comando seguente che viene eseguito il file JSON di configurazione di Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

L'output seguente viene stampato nella console quando la chiave privata è stata creata correttamente.

```console
secret "containerpreview" created
```

Per verificare che la chiave privata è stata creata, eseguire la [ `kubectl get` ][kubectl-get] con il `secrets` flag.

```console
kuberctl get secrets
```

L'esecuzione di `kubectl get secrets` stampa tutti i segreti configurati.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurare i valori del grafico Helm per la distribuzione

Visitare il [Microsoft Helm Hub][ms-helm-hub] per tutti i grafici di helm disponibili pubblicamente offerti da Microsoft. Dall'Hub di Helm Microsoft, sono disponibili i **Cognitive Services vocale On-Premises grafico**. Il **Cognitive Services vocale On-Premises** è il grafico verrà installato, ma è necessario creare innanzitutto un `config-values.yaml` file con le configurazioni esplicitare. Per iniziare, aggiungere il repository di Microsoft per l'istanza di Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Successivamente, è possibile configurare i valori del grafico Helm. Copiare e incollare il codice YAML seguente in un file denominato `config-values.yaml`. Per altre informazioni sulla personalizzazione il **grafico Helm di On-Premises vocale di servizi cognitivi**, vedere [personalizzare i grafici helm](#customize-helm-charts). Sostituire il `billing` e `apikey` valori con quelli personalizzati.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Se il `billing` e `apikey` valori non sono specificati, i servizi scadrà dopo 15 minuti. Allo stesso modo, la verifica avrà esito negativo poiché i servizi non saranno disponibili.

### <a name="the-kubernetes-package-helm-chart"></a>Il pacchetto di Kubernetes (grafico di Helm)

Il *grafico Helm* contiene la configurazione delle quali immagini di docker per eseguire il pull dal `containerpreview.azurecr.io` registro contenitori.

> Oggetto [grafico Helm][helm-charts] è una raccolta di file che descrivono un set correlato di risorse di Kubernetes. Un singolo grafico potrebbe essere utilizzato per distribuire qualcosa di semplice, ad esempio un pod con memcache, o un elemento complesso, come uno stack di app web completo con i server HTTP, i database, cache e così via.

Fornito *i grafici Helm* effettuare il pull di immagini docker del servizio riconoscimento vocale, sintesi vocale sia i servizi di riconoscimento vocale dal `containerpreview.azurecr.io` registro contenitori.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installare il grafico Helm nel cluster Kubernetes

Per installare il *grafico helm* sarà necessario eseguire il [ `helm install` ][helm-install-cmd] comando, sostituendo il `<config-values.yaml>` con l'argomento relativo al nome file e percorso appropriato. Il `microsoft/cognitive-services-speech-onpremise` grafico Helm indicato di seguito è disponibile nel [Microsoft Helm Hub qui][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Ecco un esempio di output si aspetterebbe di vedere dall'esecuzione di una corretta installazione:

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

La distribuzione di Kubernetes completamento può richiedere diversi minuti. Per assicurarsi che sia i POD e i servizi siano correttamente distribuito e disponibile, eseguire il comando seguente:

```console
kubectl get all
```

Riceverai a qualcosa di simile all'output seguente:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Verificare la distribuzione di Helm con i test di Helm

Definiscono i grafici Helm installati *Helm test*, che fungono da una soluzione pratica per la verifica. Questi test di convalida della conformità del servizio. Verificare entrambe **per il riconoscimento vocale** e **sintesi vocale** services, è possibile eseguire il [test Helm][helm-test] comando.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Questi test avrà esito negativo se non è lo stato POD `Running` o se la distribuzione non è elencata sotto il `AVAILABLE` colonna. Essere paziente in quanto questa operazione può richiedere più di 10 minuti per il completamento.

Questi test restituirà vari risultati di stato:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Come alternativa all'esecuzione di *helm test*, puoi raccogliere il *IP esterno* indirizzi e porte corrispondente dal `kubectl get all` comando. Usando l'indirizzo IP e porta, aprire un web browser e passare a `http://<external-ip>:<port>:/swagger/index.html` alla visualizzazione dell'API swagger pag.

## <a name="customize-helm-charts"></a>Personalizzare i grafici Helm

I grafici Helm sono gerarchici. Being gerarchico consente l'ereditarietà del grafico, anche progettata per il concetto di specificità, in cui le impostazioni che sono più specifiche sostituiscono le regole ereditate.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'installazione di applicazioni con Helm in Azure Kubernetes Service (AKS) [visitare questa pagina][installing-helm-apps-in-aks].

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
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
