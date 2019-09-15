---
title: Routing intelligente e versioni canary con Istio nel servizio Azure Container (AKS)
description: Informazioni su come usare Istio per fornire il routing intelligente e distribuire versioni canary in un cluster del servizio Azure Container (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 7baa2adbd615a449c73e70e1b96524fc1e18b25d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000164"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usare il routing intelligente e le versioni canary con Istio nel servizio Azure Container (AKS)

[Istio][istio-github] è una mesh di servizi open source che fornisce un set di chiavi di funzionalità tra i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per ulteriori informazioni su Istio, vedere la documentazione ufficiale [che cos'è Istio?][istio-docs-concepts] .

Questo articolo illustra come usare la funzionalità di gestione traffico di Istio. Un'app di voto di esempio del servizio Azure Container viene usata per esplorare il routing intelligente e le versioni canary.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Distribuire l'applicazione
> * Aggiornare l'applicazione
> * Distribuire una versione canary dell'applicazione
> * Finalizzare l'implementazione

## <a name="before-you-begin"></a>Prima di iniziare

> [!NOTE]
> Questo scenario è stato testato rispetto alla versione `1.1.3`di Istio.

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster `1.11` AKS (Kubernetes e versioni successive, con RBAC abilitato) `kubectl` e che sia stata stabilita una connessione con il cluster. È anche necessario che Istio sia installato nel cluster.

Per assistenza su uno di questi elementi, vedere la Guida [introduttiva di AKS][aks-quickstart] e [installare Istio in AKS][istio-install] .

## <a name="about-this-application-scenario"></a>Informazioni su questo scenario di applicazione

L'app di voto AKS di esempio offre due opzioni di voto (**gatti** o **cani**) per gli utenti. È presente un componente di archiviazione che rende persistente il numero di voti per ogni opzione. Inoltre, è presente un componente di analitica che fornisce i dettagli per i voti per ogni opzione.

In questo scenario di applicazione si inizia distribuendo la `1.0` versione dell'app votante e `1.0` la versione del componente Analytics. Il componente di analitica fornisce semplici conteggi per il numero di voti. Il componente app e analisi per il voto interagisce con la versione `1.0` del componente di archiviazione, supportata da Redis.

Si aggiorna il componente Analytics alla versione `1.1`, che fornisce conteggi e ora totali e percentuali.

Un subset di utenti prova la `2.0` versione dell'app tramite una versione Canary. Questa nuova versione utilizza un componente di archiviazione supportato da un database MySQL.

Quando si è certi che la `2.0` versione funziona come previsto nel sottoinsieme di utenti, è possibile distribuire `2.0` la versione a tutti gli utenti.

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Èpossibile distribuire l'applicazione nel cluster del servizio Azure Container (AKS). Il diagramma seguente illustra le operazioni eseguite alla fine di questa sezione-versione `1.0` di tutti i componenti con richieste in ingresso gestite tramite il gateway di ingresso Istio:

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-01.png)

Gli elementi che è necessario seguire insieme a questo articolo sono disponibili nel repository di GitHub [Azure-Samples/AKS-vote-app][github-azure-sample] . È possibile scaricare gli artefatti o clonare il repository come indicato di seguito:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Passare alla cartella seguente nel repository scaricato o clonato ed eseguire tutti i passaggi successivi da questa cartella:

```console
cd scenarios/intelligent-routing-with-istio
```

Per prima cosa, creare uno spazio dei nomi nel cluster AKS per l'app AKS `voting` vote di esempio denominata come indicato di seguito:

```azurecli
kubectl create namespace voting
```

Etichettare lo spazio dei nomi con `istio-injection=enabled`. Questa etichetta indica a Istio di inserire automaticamente i proxy di Istio come collaterali a tutti i pod in questo spazio dei nomi.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

A questo punto è possibile creare i componenti per l'app di voto del servizio Azure Kubernetes. Creare questi componenti nello `voting` spazio dei nomi creato in un passaggio precedente.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

L'output di esempio seguente mostra le risorse in fase di creazione:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio ha alcuni requisiti specifici per i pod e i servizi. Per ulteriori informazioni, vedere la [documentazione relativa ai requisiti di Istio per i pod e i servizi][istio-requirements-pods-and-services].

Per visualizzare i pod creati, usare il comando [kubectl Get Pod][kubectl-get] come indicato di seguito:

```azurecli
kubectl get pods -n voting
```

L'output di esempio seguente mostra che sono presenti tre istanze `voting-app` del Pod e una singola istanza dei `voting-analytics` pod e `voting-storage` . Ogni pod ha due contenitori. Uno di questi contenitori è il componente e l'altro è il `istio-proxy`seguente:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Per visualizzare le informazioni sul Pod, usare il [Pod kubectl descrivere][kubectl-describe]. Sostituire il nome del pod con il nome di un pod del cluster del servzio Azure Conatiner dall'output precedente:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

Il `istio-proxy` contenitore è stato inserito automaticamente da Istio per gestire il traffico di rete da e verso i componenti, come illustrato nell'output di esempio seguente:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Non è possibile connettersi all'app di voto fino a quando non si crea il [gateway][istio-reference-gateway] Istio e il [servizio virtuale][istio-reference-virtualservice]. Queste risorse Istio instradano il traffico dal gateway di traffico in ingresso di Istio predefinito all'applicazione.

> [!NOTE]
> Un **Gateway** è un componente al margine della rete mesh di servizi che riceve il traffico HTTP e TCP in entrata o in uscita.
> 
> Un **servizio virtuale** definisce un set di regole di routing per uno o più servizi di destinazione.

Usare il `kubectl apply` comando per distribuire il gateway e il servizio virtuale YAML. Ricordarsi di specificare lo spazio dei nomi in cui vengono distribuite queste risorse.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

L'output di esempio seguente mostra il nuovo gateway e il servizio virtuale in fase di creazione:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Ottenere l'indirizzo IP del gateway in ingresso di Istio usando il comando seguente:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Il seguente output di esempio mostra gli indirizzi IP del gateway di ingresso:

```
20.188.211.19
```

Aprire un browser e incollare l'indirizzo IP. Viene visualizzata l'app di voto di esempio del servizio Azure Container.

![L'app di voto del servizio Azure Container in esecuzione nel cluster del servizio Azure Container abilitato da Istio.](media/istio/deploy-app-01.png)

Le informazioni nella parte inferiore della schermata indicano che l'app usa la versione `1.0` di `voting-app` e la `1.0` versione `voting-storage` di (Redis).

## <a name="update-the-application"></a>Aggiornare l'applicazione

Viene ora distribuita una nuova versione del componente Analytics. Questa nuova versione `1.1` Visualizza i totali e le percentuali, oltre al conteggio per ogni categoria.

Il diagramma seguente mostra cosa verrà eseguito alla fine di questa versione `1.1` solo `voting-analytics` della sezione del componente. il `voting-app` traffico viene instradato dal componente. Anche se la `1.0` `voting-analytics` versione`voting-analytics` del componente continua a essere eseguita e a cui viene fatto riferimento dal servizio, i proxy Istio non consentono il traffico da e verso tale componente.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-02.png)

Viene ora distribuita `1.1` la `voting-analytics` versione del componente. Creare questo componente nello `voting` spazio dei nomi:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

L'output di esempio seguente mostra le risorse in fase di creazione:

```console
deployment.apps/voting-analytics-1-1 created
```

Aprire l'esempio dell’app del servizio Azure Container in un browser usando l'indirizzo IP del gateway in ingresso di Istio ottenuto nel passaggio precedente.

Il browser alterna le due visualizzazioni illustrate di seguito. Poiché si usa un [servizio][kubernetes-service] Kubernetes per il `voting-analytics` componente con un solo selettore con etichetta singola (`app: voting-analytics`), Kubernetes usa il comportamento predefinito del round robin tra i pod che corrispondono al selettore. In questo caso, si tratta sia `1.0` `1.1` della versione che dei `voting-analytics` pod.

![Versione 1.0 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/istio/deploy-app-01.png)

![Versione 1.1 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/istio/update-app-01.png)

È possibile visualizzare il cambio tra le due versioni del `voting-analytics` componente, come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

L'output di esempio seguente mostra la parte pertinente del sito Web restituito mentre il sito passa da una versione all’altra:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloccare il traffico per la versione 1.1 dell'applicazione

A questo punto, è possibile bloccare il traffico `1.1` solo con `voting-analytics` la versione del componente `1.0` e la `voting-storage` versione del componente. È quindi possibile definire le regole di routing per tutti gli altri componenti.

> * Un **servizio virtuale** definisce un set di regole di routing per uno o più servizi di destinazione.
> * Una **regola di destinazione**  definisce i criteri di traffico e i criteri specifici per la versione.
> * Un **criterio** definisce i metodi di autenticazione che possono essere accettati nel carico di lavoro.

Usare il `kubectl apply` comando per sostituire la definizione del servizio `voting-app` virtuale in e aggiungere le [regole di destinazione][istio-reference-destinationrule] e i [Servizi virtuali][istio-reference-virtualservice] per gli altri componenti. Si aggiungerà un [criterio][istio-reference-policy] allo `voting` spazio dei nomi per assicurarsi che tutte le comunicazioni tra i servizi siano protette tramite i certificati client e TLS reciproci.

* Il criterio è `peers.mtls.mode` impostato su `STRICT` per garantire che TLS reciproco venga applicato tra i servizi all'interno `voting` dello spazio dei nomi.
* Si imposta `trafficPolicy.tls.mode` anche su in `ISTIO_MUTUAL` tutte le regole di destinazione. Istio fornisce servizi avanzati con identità forti e protegge le comunicazioni tra servizi con TLS reciproco e i certificati client che Istio gestisce in modo trasparente.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

L'output di esempio seguente mostra il nuovo criterio, le regole di destinazione e i servizi virtuali da aggiornare o creare:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se si apre di nuovo l'app AKS vote in un browser, solo la nuova `1.1` versione `voting-analytics` del `voting-app` componente verrà usata dal componente.

![Versione 1.1 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/istio/update-app-01.png)

È possibile visualizzare che ora si viene indirizzati solo alla versione `1.1` `voting-analytics` del componente, come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

L'output di esempio seguente mostra la parte pertinente del sito Web restituito:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Ora è possibile verificare che Istio usi Mutual TLS per proteggere le comunicazioni tra i servizi. A tale proposito verrà usato il comando [AuthN TLS-check][istioctl-authn-tls-check] sul `istioctl` file binario client, che assume il formato seguente.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Questo set di comandi fornisce informazioni sull'accesso ai servizi specificati, da tutti i pod che si trovano in uno spazio dei nomi e corrispondono a un set di etichette:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

L'output di esempio seguente mostra che TLS reciproco viene applicato per ogni query precedente. L'output Mostra anche le regole di criteri e di destinazione che applicano il TLS reciproco:

```console
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

Verrà ora distribuita una nuova versione `2.0` `voting-app`dei componenti, `voting-analytics`e `voting-storage` . Il nuovo `voting-storage` componente usa MySQL invece di redis e i `voting-app` componenti e `voting-analytics` vengono aggiornati per consentire loro di usare questo nuovo `voting-storage` componente.

Il `voting-app` componente ora supporta la funzionalità dei flag funzionalità. Il flag delle funzionalità consente di testare la funzionalità della versione canary di Istio per un subset di utenti.

Il diagramma seguente Mostra gli elementi che sono in esecuzione alla fine di questa sezione.

* `1.1` `voting-analytics` `1.0` `voting-storage` La versione del componente, la versione del componente e la versione del componente sono in grado di comunicare tra loro. `1.0` `voting-app`
* `2.0` `voting-analytics` `2.0` `voting-storage` La versione del componente, la versione del componente e la versione del componente sono in grado di comunicare tra loro. `2.0` `voting-app`
* La `2.0` versione del `voting-app` componente è accessibile solo agli utenti per i quali è impostato un flag di funzionalità specifico. Questa modifica viene gestita tramite un flag di funzionalità tramite un cookie.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-03.png)

Innanzitutto, aggiornare le regole di destinazione di Istio e i servizi virtuali in modo che tengano in considerazione questi nuovi componenti. Questi aggiornamenti assicurano che non si instradi il traffico in modo incorretto per i nuovi componenti e che gli utenti non ricevano accessi imprevisti:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Nell'output di esempio seguente vengono illustrate le regole di destinazione e i servizi virtuali da aggiornare:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Successivamente, aggiungere gli oggetti Kubernetes per i nuovi componenti della versione `2.0` . Si aggiorna anche il `voting-storage` servizio per includere la `3306` porta per MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

L'output di esempio seguente mostra che gli oggetti Kubernetes sono stati aggiornati o creati correttamente:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Attendere che tutti i pod `2.0` di versione siano in esecuzione. Usare il comando [kubectl Get Pod][kubectl-get] per visualizzare tutti i pod nello `voting` spazio dei nomi:

```azurecli
kubectl get pods --namespace voting
```

A questo punto dovrebbe essere possibile passare tra la versione `1.0` e la `2.0` versione (Canary) dell'applicazione di voto. Il pulsante Attiva/disattiva flag funzionalità nella parte inferiore della schermata imposta un cookie. Questo cookie viene utilizzato dal `voting-app` servizio virtuale per indirizzare gli utenti alla nuova versione. `2.0`

![Versione 1.0 dell'app di voto del servizio Azure Conatiner: flag delle funzionalità NON IMPOSTATO.](media/istio/canary-release-01.png)

![Versione 2.0 dell'app di voto del servizio Azure Conatiner: flag delle funzionalità IMPOSTATO.](media/istio/canary-release-02.png)

I conteggi di voto sono diversi a seconda delle versioni dell'app. Questa differenza evidenzia l’utilizzo di due back-end di archiviazione diversi.

## <a name="finalize-the-rollout"></a>Finalizzare l'implementazione

Dopo aver testato correttamente la versione Canary, aggiornare il `voting-app` servizio virtuale per indirizzare tutto il traffico alla versione `2.0` del `voting-app` componente. Tutti gli utenti visualizzano `2.0` quindi la versione dell'applicazione, indipendentemente dal fatto che il flag funzionalità sia impostato o meno:

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-04.png)

Aggiornare tutte le regole di destinazione per rimuovere le versioni dei componenti che non si desidera più che siano attive. Quindi, aggiornare tutti i servizi virtuali per evitare che facciano riferimento a tali versioni.

Poiché non c’è più traffico verso le versioni precedenti dei componenti, ora è possibile eliminare in modo sicuro tutte le distribuzioni per tali componenti.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-05.png)

Nuova versione dell'app di voto del servizio Azure Conatiner implementata correttamente.

## <a name="clean-up"></a>Eseguire la pulizia 

È possibile rimuovere l'app di voto AKS usata in questo scenario dal cluster AKS eliminando lo `voting` spazio dei nomi come indicato di seguito:

```azurecli
kubectl delete namespace voting
```

L'output di esempio seguente mostra che tutti i componenti dell'app AKS vote sono stati rimossi dal cluster AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Passaggi successivi

È possibile esplorare altri scenari usando l' [esempio di applicazione BookInfo di Istio][istio-bookinfo-example].

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
[istio-install]: ./istio-install.md
