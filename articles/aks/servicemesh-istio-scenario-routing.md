---
title: Routing intelligente e versioni canary con Istio nel servizio Azure Container (AKS)
description: Informazioni su come usare Istio per fornire il routing intelligente e distribuire versioni canary in un cluster del servizio Azure Container (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78272995"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usare il routing intelligente e le versioni canary con Istio nel servizio Azure Container (AKS)

[Istio][istio-github] è una rete mesh di servizi open source che offre un set di funzionalità chiave per i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per maggiori informazioni su Istio, vedere la documentazione ufficiale [Cos'è Istio?][istio-docs-concepts].

Questo articolo illustra come usare la funzionalità di gestione traffico di Istio. Un'app di voto di esempio del servizio Azure Container viene usata per esplorare il routing intelligente e le versioni canary.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Distribuire l'applicazione
> * Aggiornare l'applicazione
> * Distribuire una versione canary dell'applicazione
> * Finalizzare l'implementazione

## <a name="before-you-begin"></a>Prima di iniziare

> [!NOTE]
> Questo scenario è stato testato rispetto `1.3.2`alla versione Istio.

I passaggi descritti in questo articolo presuppongono che sia stato `1.13` creato un cluster AKS (Kubernetes e versioni successive, con RBAC abilitato) e che sia stata stabilita una `kubectl` connessione con il cluster. È inoltre necessario che Istio sia installato nel cluster.

Se hai bisogno di aiuto per uno di questi elementi, consulta la [guida introduttiva][aks-quickstart] di AKS e [Installa Istio nelle][istio-install] linee guida di AKS.

## <a name="about-this-application-scenario"></a>Informazioni su questo scenario di applicazione

L'app di voto AKS di esempio fornisce due opzioni di voto (**Cats** o **Dogs**) agli utenti. È presente un componente di archiviazione che rende persistente il numero di voti per ogni opzione. Inoltre, è presente un componente di analitica che fornisce i dettagli per i voti per ogni opzione.

In questo scenario dell'applicazione, `1.0` si inizia distribuendo la versione dell'app di voto e la versione `1.0` del componente di analisi. Il componente di analitica fornisce semplici conteggi per il numero di voti. L'app di voto e `1.0` il componente di analisi interagiscono con la versione del componente di archiviazione, supportata da Redis.

Il componente di analisi `1.1`viene aggiornato alla versione , che fornisce i conteggi e ora i totali e le percentuali.

Un sottoinsieme di `2.0` utenti testa la versione dell'app tramite una versione canarino. Questa nuova versione utilizza un componente di archiviazione supportato da un database MySQL.

Quando si è certi `2.0` che la versione funziona come previsto `2.0` sul sottoinsieme di utenti, si implementa la versione a tutti gli utenti.

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Èpossibile distribuire l'applicazione nel cluster del servizio Azure Container (AKS). Il diagramma seguente mostra le operazioni eseguite `1.0` entro la fine di questa sezione, ovvero la versione di tutti i componenti con richieste in ingresso gestite tramite il gateway in ingresso Istio:

![Componenti dell’app di voto e routing del servizio Azure Container.](media/servicemesh/istio/scenario-routing-components-01.png)

Gli artefatti necessari per seguire le procedure in questo articolo sono disponibili nel repository GitHub [Azure-Samples/aks--app voting][github-azure-sample]. È possibile scaricare gli artefatti o clonare il repository come indicato di seguito:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Passare alla cartella seguente nel repository scaricato o clonato ed eseguire tutti i passaggi successivi da questa cartella:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Creare innanzitutto uno spazio dei nomi nel cluster AKS per l'app di voto AKS di esempio denominata come segue:First, create a namespace in your AKS cluster for the sample AKS voting app named `voting` as follows:

```console
kubectl create namespace voting
```

Etichettare lo spazio dei nomi con `istio-injection=enabled`. Questa etichetta indica a Istio di inserire automaticamente i proxy di Istio come collaterali a tutti i pod in questo spazio dei nomi.

```console
kubectl label namespace voting istio-injection=enabled
```

A questo punto è possibile creare i componenti per l'app di voto del servizio Azure Kubernetes. Creare questi componenti `voting` nello spazio dei nomi creato in un passaggio precedente.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

L'output di esempio seguente mostra le risorse create:The following example output shows the resources being created:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio ha alcuni requisiti specifici per i pod e i servizi. Per altre informazioni, vedere la documentazione[Requisiti di Istio per i servizi e i pod][istio-requirements-pods-and-services].

Per visualizzare i pod creati, usare il comando [kubectl get pods][kubectl-get] come segue:

```console
kubectl get pods -n voting --show-labels
```

L'output di esempio seguente mostra `voting-app` che sono presenti tre `voting-analytics` `voting-storage` istanze del pod e una singola istanza di entrambi i pod e . Ogni pod ha due contenitori. Uno di questi contenitori è il `istio-proxy`componente e l'altro è il :

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Per visualizzare le informazioni sul contenitore, useremo il comando [del contenitore kubectl describe][kubectl-describe] con i selettori di etichetta per selezionare il `voting-analytics` contenitore. Filtramo l'output per mostrare i dettagli dei due contenitori presenti nel contenitore:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Non è possibile connettersi all'app di voto fino a quando non si crea il [gateway][istio-reference-gateway] e il [servizio virtuale][istio-reference-virtualservice]. Queste risorse Istio instradano il traffico dal gateway di traffico in ingresso di Istio predefinito all'applicazione.

> [!NOTE]
> Un **Gateway** è un componente al margine della rete mesh di servizi che riceve il traffico HTTP e TCP in entrata o in uscita.
> 
> Un **servizio virtuale** definisce un set di regole di routing per uno o più servizi di destinazione.

Utilizzare `kubectl apply` il comando per distribuire lo yaml Gateway e Servizio virtuale. Ricordarsi di specificare lo spazio dei nomi in cui vengono distribuite queste risorse.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

L'output di esempio seguente mostra il nuovo gateway e il nuovo servizio virtuale in fase di creazione:The following example output shows the new Gateway and Virtual Service being created:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Ottenere l'indirizzo IP del gateway in ingresso di Istio usando il comando seguente:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Il seguente output di esempio mostra gli indirizzi IP del gateway di ingresso:

```output
20.188.211.19
```

Aprire un browser e incollare l'indirizzo IP. Viene visualizzata l'app di voto di esempio del servizio Azure Container.

![L'app di voto del servizio Azure Container in esecuzione nel cluster del servizio Azure Container abilitato da Istio.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Le informazioni nella parte inferiore dello schermo `1.0` mostrano che l'app usa la versione `voting-app` e la versione `1.0` di `voting-storage` (Redis).

## <a name="update-the-application"></a>Aggiornare l'applicazione

Dissolviamo una nuova versione del componente di analisi. Questa nuova `1.1` versione visualizza i totali e le percentuali oltre al conteggio per ogni categoria.

Il diagramma seguente mostra cosa verrà eseguito alla `1.1` fine `voting-analytics` di questa sezione: `voting-app` solo la versione del componente ha traffico instradato dal componente. Anche se `1.0` la `voting-analytics` versione del componente continua a `voting-analytics` essere eseguita e fa riferimento al servizio, i proxy Istio non consentono il traffico da e verso di esso.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/servicemesh/istio/scenario-routing-components-02.png)

Distribuire la `voting-analytics` versione `1.1` del componente. Creare questo componente `voting` nello spazio dei nomi:Create this component in the namespace:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

L'output di esempio seguente mostra le risorse create:The following example output shows the resources being created:

```output
deployment.apps/voting-analytics-1-1 created
```

Aprire l'esempio dell’app del servizio Azure Container in un browser usando l'indirizzo IP del gateway in ingresso di Istio ottenuto nel passaggio precedente.

Il browser alterna le due visualizzazioni illustrate di seguito. Poiché si utilizza un servizio Kubernetes per il [Service][kubernetes-service] `voting-analytics` componente`app: voting-analytics`con un solo selettore di etichetta ( ), Kubernetes utilizza il comportamento predefinito di round robin tra i pod che corrispondono a tale selettore. In questo caso, è `1.0` `1.1` sia `voting-analytics` la versione che i pod.

![Versione 1.0 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Versione 1.1 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/servicemesh/istio/scenario-routing-update-app-01.png)

È possibile visualizzare il passaggio tra `voting-analytics` le due versioni del componente come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

L'output di esempio seguente mostra la parte pertinente del sito Web restituito mentre il sito passa da una versione all’altra:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloccare il traffico per la versione 1.1 dell'applicazione

Ora si blocca il traffico `1.1` solo `voting-analytics` alla versione `1.0` del `voting-storage` componente e alla versione del componente. È quindi possibile definire le regole di routing per tutti gli altri componenti.

> * Un **servizio virtuale** definisce un set di regole di routing per uno o più servizi di destinazione.
> * Una **regola di destinazione ** definisce i criteri di traffico e i criteri specifici per la versione.
> * Un **criterio** definisce i metodi di autenticazione che possono essere accettati nel carico di lavoro.

Utilizzare `kubectl apply` il comando per sostituire la `voting-app` definizione del servizio virtuale in ambiente e aggiungere [le regole][istio-reference-destinationrule] di destinazione e i [servizi virtuali][istio-reference-virtualservice] per gli altri componenti. Si aggiungerà [Policy][istio-reference-policy] un `voting` criterio allo spazio dei nomi per garantire che tutti i messaggi di comunicazione tra i servizi siano protetti tramite Mutual TLS e certificati client.

* Il criterio `peers.mtls.mode` è `STRICT` impostato per garantire che TLS reciproco `voting` viene applicato tra i servizi all'interno dello spazio dei nomi.
* Abbiamo anche `trafficPolicy.tls.mode` impostato `ISTIO_MUTUAL` il per in tutte le nostre regole di destinazione. Istio fornisce servizi avanzati con identità forti e protegge le comunicazioni tra servizi con TLS reciproco e i certificati client che Istio gestisce in modo trasparente.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

L'output di esempio seguente mostra i nuovi criteri, regole di destinazione e servizi virtuali in fase di aggiornamento/creazione:The following example output shows the new Policy, Destination Rules, and Virtual Services being updated/created:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se si apre nuovamente l'app di voto AKS `voting-analytics` in un browser, solo `voting-app` la nuova versione `1.1` del componente viene utilizzata dal componente.

![Versione 1.1 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/servicemesh/istio/scenario-routing-update-app-01.png)

È possibile visualizzare che ora si `1.1` è `voting-analytics` indirizzati solo alla versione del componente come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

L'output di esempio seguente mostra la parte pertinente del sito Web restituito:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Confermiamo ora che Istio sta usando Mutual TLS per proteggere le comunicazioni tra ciascuno dei nostri servizi. Per questo useremo il comando [authn tls-check][istioctl-authn-tls-check] sul file binario del `istioctl` client, che assume la seguente forma.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Questo set di comandi fornisce informazioni sull'accesso ai servizi specificati, da tutti i pod che si trovano in uno spazio dei nomi e corrispondono a un set di etichette:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Questo output di esempio seguente mostra che TLS reciproco viene applicato per ognuna delle query precedenti. L'output mostra anche le regole di criteri e destinazione che applica il TLS reciproco:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuire una versione canary dell'applicazione

A questo punto è `2.0` possibile `voting-app`distribuire `voting-analytics`una `voting-storage` nuova versione dei componenti , , e . Il `voting-storage` nuovo componente utilizza MySQL invece `voting-app` di `voting-analytics` Redis e i componenti `voting-storage` e vengono aggiornati per consentire loro di utilizzare questo nuovo componente.

Il `voting-app` componente supporta ora la funzionalità dei flag di funzionalità. Il flag delle funzionalità consente di testare la funzionalità della versione canary di Istio per un subset di utenti.

Il diagramma seguente mostra cosa sarà in esecuzione alla fine di questa sezione.

* La `1.0` versione `voting-app` del `1.1` componente, `voting-analytics` la `1.0` versione `voting-storage` del componente e la versione del componente sono in grado di comunicare tra loro.
* La `2.0` versione `voting-app` del `2.0` componente, `voting-analytics` la `2.0` versione `voting-storage` del componente e la versione del componente sono in grado di comunicare tra loro.
* La `2.0` versione `voting-app` del componente è accessibile solo agli utenti che dispongono di un flag di funzionalità specifico impostato. Questa modifica viene gestita tramite un flag di funzionalità tramite un cookie.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/servicemesh/istio/scenario-routing-components-03.png)

Innanzitutto, aggiornare le regole di destinazione di Istio e i servizi virtuali in modo che tengano in considerazione questi nuovi componenti. Questi aggiornamenti assicurano che non si instradi il traffico in modo incorretto per i nuovi componenti e che gli utenti non ricevano accessi imprevisti:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

L'output di esempio seguente mostra le regole di destinazione e i servizi virtuali da aggiornare:The following example output shows the Destination Rules and Virtual Services being updated:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Successivamente, aggiungiamo gli oggetti Kubernetes per `2.0` i nuovi componenti della versione. È inoltre `voting-storage` possibile aggiornare `3306` il servizio per includere la porta per MySQL:You also update the service to include the port for MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

L'output di esempio seguente mostra che gli oggetti Kubernetes sono stati aggiornati o creati correttamente:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Attendere che tutti `2.0` i pod della versione siano in esecuzione. Usate il comando [kubectl get pods][kubectl-get] con l'interruttore `-w` watch `voting` per controllare le modifiche su tutti i pod nello spazio dei nomi:

```console
kubectl get pods --namespace voting -w
```

Ora dovresti essere in grado `1.0` di `2.0` passare dalla versione alla versione (canarino) dell'applicazione di voto. Il pulsante Attiva/disattiva flag funzionalità nella parte inferiore della schermata imposta un cookie. Questo cookie viene `voting-app` utilizzato dal servizio virtuale per `2.0`indirizzare gli utenti alla nuova versione .

![Versione 1.0 dell'app di voto del servizio Azure Conatiner: flag delle funzionalità NON IMPOSTATO.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Versione 2.0 dell'app di voto del servizio Azure Conatiner: flag delle funzionalità IMPOSTATO.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

I conteggi di voto sono diversi a seconda delle versioni dell'app. Questa differenza evidenzia l’utilizzo di due back-end di archiviazione diversi.

## <a name="finalize-the-rollout"></a>Finalizzare l'implementazione

Dopo aver testato correttamente la versione canarino, aggiornare il servizio virtuale per instradare tutto il `voting-app` traffico alla versione `2.0` del `voting-app` componente. Tutti gli utenti `2.0` vedrà quindi la versione dell'applicazione, indipendentemente dal fatto che il flag di funzionalità sia impostato o meno:

![Componenti dell’app di voto e routing del servizio Azure Container.](media/servicemesh/istio/scenario-routing-components-04.png)

Aggiornare tutte le regole di destinazione per rimuovere le versioni dei componenti che non si desidera più che siano attive. Quindi, aggiornare tutti i servizi virtuali per evitare che facciano riferimento a tali versioni.

Poiché non c’è più traffico verso le versioni precedenti dei componenti, ora è possibile eliminare in modo sicuro tutte le distribuzioni per tali componenti.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/servicemesh/istio/scenario-routing-components-05.png)

Nuova versione dell'app di voto del servizio Azure Conatiner implementata correttamente.

## <a name="clean-up"></a>Eseguire la pulizia 

È possibile rimuovere l'app di voto AKS usata `voting` in questo scenario dal cluster AKS eliminando lo spazio dei nomi come segue:You can remove the AKS voting app we used in this scenario on your AKS cluster by deleting the namespace as follows:

```console
kubectl delete namespace voting
```

L'output di esempio seguente mostra che tutti i componenti dell'app di voto AKS sono stati rimossi dal cluster AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Passaggi successivi

È possibile esplorare altri scenari usando l’[esempio di applicazione Bookinfo di Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
