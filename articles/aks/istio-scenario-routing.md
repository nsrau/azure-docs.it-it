---
title: Routing intelligente e versioni canary con Istio nel servizio Azure Container (AKS)
description: Informazioni su come usare Istio per fornire il routing intelligente e distribuire versioni canary in un cluster del servizio Azure Container (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893092"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usare il routing intelligente e le versioni canary con Istio nel servizio Azure Container (AKS)

[Istio][istio-github] è un servizio mesh open source che fornisce un set di chiavi di funzionalità tra i microservizi in un cluster Kubernetes. Queste funzionalità includono la gestione del traffico, l'identità e la sicurezza del servizio, l'applicazione dei criteri e l'osservabilità. Per altre informazioni su Istio, vedere la documentazione ufficiale [Cos'è Istio?][istio-docs-concepts].

Questo articolo illustra come usare la funzionalità di gestione traffico di Istio. Un'app di voto di esempio del servizio Azure Container viene usata per esplorare il routing intelligente e le versioni canary.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Distribuire l'applicazione
> * Aggiornare l'applicazione
> * Distribuire una versione canary dell'applicazione
> * Finalizzare l'implementazione

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster del servizio Kubernetes di Azure (Kubernetes 1.10 e versioni successive, cin RBAC attivato) e che sia stata stabilita una connessione `kubectl` al cluster. È anche necessario installare Istio nel cluster.

Se occorre assistenza con uno di questi elementi, consultare la [Guida introduttiva][aks-quickstart] e [installare Istio nel servizio Azure Container][istio-install].

## <a name="about-this-application-scenario"></a>Informazioni su questo scenario di applicazione

L'app di voto di esempio del servizio Azure Container offre due opzioni di voto (gatti o cani) agli utenti. È presente un componente di archiviazione che rende persistente il numero di voti per ogni opzione. Inoltre, è presente un componente di analitica che fornisce i dettagli per i voti per ogni opzione.

In questo articolo, si inizia con la distribuzione della versione *1.0* dell'app di voto e la versione *1.0* del componente di analitica. Il componente di analitica fornisce semplici conteggi per il numero di voti. L'app di voto e il componente di analitica interagiscono con la versione *1.0* del componente di archiviazione supportato da Redis.

Aggiornamento alla versione *1.1* del componente di analitica, che fornisce i conteggi, i totali e le percentuali.

Un subset di versione *2.0* di test per l’utente dell'app tramite una versione canary. Questa nuova versione utilizza un componente di archiviazione supportato da un database MySQL.

Una volta che si è certi che la versione *2.0* funzioni come previsto nel subset di utenti, distribuire la versione *2.0* a tutti gli utenti.

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Èpossibile distribuire l'applicazione nel cluster del servizio Azure Container (AKS). Il diagramma seguente illustra ciò che viene eseguito al termine di questa sezione: versione *1.0* di tutti i componenti con le richieste in ingresso elaborate tramite il gateway di traffico in ingresso di Istio:

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-01.png)

Gli artefatti necessari per seguire le procedure in questo articolo sono disponibili nel repository GitHub [Azure-Samples/aks--app voting][github-azure-sample]. È possibile scaricare gli artefatti o clonare il repository come indicato di seguito:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Passare alla cartella seguente nel repository scaricato o clonato ed eseguire tutti i passaggi successivi da questa cartella:

```console
cd scenarios/intelligent-routing-with-istio
```

Innanzitutto, creare uno spazio dei nomi nel cluster del servizio Azure Container per le app di voto del servizio Azure Container di esempio denominate *voto* come indicato di seguito:

```console
kubectl create namespace voting
```

Etichettare lo spazio dei nomi con `istio-injection=enabled`. Questa etichetta indica a Istio di inserire automaticamente i proxy di Istio come collaterali a tutti i pod in questo spazio dei nomi.

```console
kubectl label namespace voting istio-injection=enabled
```

A questo punto è possibile creare i componenti per l'app di voto AKS. Creare questi componenti nello spazio dei nomi *voto* creato nel passaggio precedente.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

L'output di esempio seguente mostra che le risorse sono state create correttamente:

```
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
kubectl get pods -n voting
```

L'output di esempio seguente mostra che sono disponibili tre istanze del pod dell’*app di voto* e una singola istanza di entrambi i pod dell’*analitica del voto* e dell’*archiviazione del voto*. Ogni pod ha due contenitori. Uno di questi contenitori è il componente e l'altro è il *proxy di Istio*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Per informazioni sui pod, usare [kubectl describe pod][kubectl-describe]. Sostituire il nome del pod con il nome di un pod del cluster del servzio Azure Conatiner dall'output precedente:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

Il contenitore del *proxy di Istio*  è stato inserito automaticamente da Istio per gestire il traffico di rete da e verso i componenti, come illustrato nell'output di esempio seguente:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

Non è possibile connettersi all'app di voto fino a quando non si crea il [gateway][istio-reference-gateway] e il [servizio virtuale][istio-reference-virtualservice]. Queste risorse Istio instradano il traffico dal gateway di traffico in ingresso di Istio predefinito all'applicazione.

> [!NOTE]
> Un *Gateway* è un componente al margine della rete mesh di servizi che riceve il traffico HTTP e TCP in entrata o in uscita.
>
> Un *servizio virtuale* definisce un set di regole di routing per uno o più servizi di destinazione.

Usare il client binario `istioctl` per distribuire il codice yaml del servizio virtuale e del Gateway. Come con il comando `kubectl apply`, ricordarsi di specificare lo spazio dei nomi in cui vengono distribuite queste risorse.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Ottenere l'indirizzo IP del gateway in ingresso di Istio usando il comando seguente:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Il seguente output di esempio mostra gli indirizzi IP del gateway di ingresso:

```
52.187.250.239
```

Aprire un browser e incollare l'indirizzo IP. Viene visualizzata l'app di voto di esempio del servizio Azure Container.

![L'app di voto del servizio Azure Container in esecuzione nel cluster del servizio Azure Container abilitato da Istio.](media/istio/deploy-app-01.png)

Le informazioni nella parte inferiore della schermata mostrano che l'app usa la versione *1.0* dell’*app di voto* e la versione *1.0* (Redis) come opzione di archiviazione.

## <a name="update-the-application"></a>Aggiornare l'applicazione

Ora distribuiamo una nuova versione del componente di analitica. Questa nuova versione *1.1* visualizza i totali e le percentuali oltre al conteggio per ogni categoria.

Il diagramma seguente illustra ciò che viene eseguito alla fine della sezione: solo la versione *1.1* del componente dell’*analitica di voto* include il traffico instradato dal componente dell’*app di voto*. Anche se la versione *1.0* del componente dell’*analitica di voto* continua a essere in esecuzione e fa riferimento al servizio di *analitica di voto*, i proxy di Istio rifiutano il traffico verso e da quest'ultimo.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-02.png)

È possibile distribuire la versione *1.1* del componente *analitica di voto*. Creare il componente nello spazio dei nomi *voto*:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Aprire l'esempio dell’app del servizio Azure Container in un browser usando l'indirizzo IP del gateway in ingresso di Istio ottenuto nel passaggio precedente.

Il browser alterna le due visualizzazioni illustrate di seguito. Poiché si sta usando un [servizio][kubernetes-service] di Kubernetes per il componente dell’*analitica di voto* con solo un selettore di etichetta singola (`app: voting-analytics`), Kubernetes usa il comportamento predefinito di round robin tra i pod che corrispondono a tale tipo di selettore. In questo caso, è sia la versione *1.0* e *1.1* del pod dell’*analitica di voto*.

![Versione 1.0 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/istio/deploy-app-01.png)

![Versione 1.1 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/istio/update-app-01.png)

È possibile visualizzare il passaggio tra le due versioni del componente dell’*analitica di voto* come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
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

Ora è possibile bloccare il traffico solo per la versione *1.1* del componente dell’*analitica di voto* e per la versione *1.0* del componente dell’*archiviazione di voto*. È quindi possibile definire le regole di routing per tutti gli altri componenti.

> * Un *servizio virtuale* definisce un set di regole di routing per uno o più servizi di destinazione.
> * Una *regola di destinazione*  definisce i criteri di traffico e i criteri specifici per la versione.
> * Un *criterio* definisce i metodi di autenticazione che possono essere accettati nel carico di lavoro.

Si utilizza il `istioctl` client binario per sostituire la definizione del servizio virtuale sull’*app di voto* e aggiungere le [regole di destinazione][istio-reference-destinationrule] e i [servizi virtuali][istio-reference-virtualservice] per gli altri componenti.

È anche possibile aggiungere un [criterio][istio-reference-policy] per lo spazio dei nomi *voto* per assicurarsi che tutte le comunicazioni tra i servizi siano protette tramite TLS reciproco e certificati client.

In quanto esiste una definizione del servizio virtuale esistente per l’*app di voto* da sostituire, usare il comando `istioctl replace` come segue:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

L'output di esempio seguente mostra i servizi virtuali di Istio aggiornati correttamente:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Successivamente, utilizzare il comando `istioctl create` per aggiungere i nuovi criteri e le nuove regole di destinazione e i servizi di virtuali per tutti gli altri componenti.

* Il criterio ha il valore `peers.mtls.mode` impostato su `STRICT` per garantire che venga applicato il TLS reciproco tra i servizi all'interno dello spazio dei nomi *voto*.
* È anche possibile impostare `trafficPolicy.tls.mode` su `ISTIO_MUTUAL` in tutte le regole di destinazione. Istio fornisce servizi avanzati con identità forti e protegge le comunicazioni tra servizi con TLS reciproco e i certificati client che Istio gestisce in modo trasparente.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

L'output di esempio seguente mostra che i nuovi criteri, le regole di destinazione e i servizi virtuali sono stati creati correttamente:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Se si apre l'app di voto del servizio Azure Container in un browser, solo la nuova versione *1.1* del componente dell’*analitica di voto* viene utilizzata dal componente dell’*app di voto*.

![Versione 1.1 del componente di analitica in esecuzione nell'app di voto del servizio Azure Container.](media/istio/update-app-01.png)

Ora è possibile visualizzare più facilmente che si è indirizzati solo alla versione *1.1* del componente dell’*analitica di voto* come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio.

Ora è possibile visualizzare che si è indirizzati solo alla versione *1.1* del componente dell’*analitica di voto* come indicato di seguito. Ricordarsi di usare l'indirizzo IP del proprio gateway di traffico in ingresso di Istio:

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

L'output di esempio seguente mostra la parte pertinente del sito Web restituito:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Confermare che Istio utilizza il TLS reciproco per proteggere le comunicazioni tra i servizi. I comandi seguenti controllano le impostazioni TLS per ogni servizio dell’*app di voto*:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

L’output di esempio seguente mostra che viene applicato il TLS reciproco per ciascuno dei servizi tramite i criteri e le regole di destinazione:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuire una versione canary dell'applicazione

Verrà ora distribuita una nuova versione *2.0* dei componenti dell’*app di voto*, dell’*analitica di voto* e dell’*archiviazione di voto*. Il nuovo componente dell’*archiviazione di voto* usa MySQL invece di Redis e i componenti dell’*app di voto* e dell’*analitica di voto* vengono aggiornati per consentire di usare questo nuovo componente di *archiviazione di voto*.

Il componente dell’*app di voto* supporta ora la funzionalità di flag delle funzioni. Il flag delle funzionalità consente di testare la funzionalità della versione canary di Istio per un subset di utenti.

Il diagramma seguente mostra ciò che viene eseguito alla fine di questa sezione.

* La versione *1.0* del componente dell’*app di voto*, la versione *1.1* del componente dell’*analitica di voto* e la versione *1.0* del componente di *archiviazione di voto* sono in grado di comunicare tra loro.
* La versione *2.0* del componente dell’*app di voto*, la versione *2.0* del componente dell’*analitica di voto* e la versione *2.0* del componente di *archiviazione di voto* sono in grado di comunicare tra loro.
* La versione *2.0* del componente dell’ *app di voto* è accessibile solo agli utenti che hanno un set di flag funzionalità specifiche. Questa modifica viene gestita tramite un flag di funzionalità tramite un cookie.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-03.png)

Innanzitutto, aggiornare le regole di destinazione di Istio e i servizi virtuali in modo che tengano in considerazione questi nuovi componenti. Questi aggiornamenti assicurano che non si instradi il traffico in modo incorretto per i nuovi componenti e che gli utenti non ricevano accessi imprevisti:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

L'output di esempio seguente mostra che le nuove regole di destinazione e i servizi virtuali sono stati creati correttamente:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Successivamente, aggiungere gli oggetti Kubernetes per i componenti della nuova versione *2.0*. È anche possibile aggiornare il servizio di *archiviazione di voto* per includere la porta *3306* per MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

L'output di esempio seguente mostra che gli oggetti Kubernetes sono stati aggiornati o creati correttamente:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Attendere finché tutti i pod della versione *2.0* sono in esecuzione. Usare il comando [kubectl get pods] [ kubectl-get] per visualizzare tutti i pod nello spazio dei nomi *voto*:

```azurecli-interactive
kubectl get pods --namespace voting
```

A questo punto dovrebbe essere possibile passare dalla versione *1.0* alla versione *2.0* (canary) dell'applicazione di voto. Il pulsante Attiva/disattiva flag funzionalità nella parte inferiore della schermata imposta un cookie. Questo cookie viene utilizzato dal servizio virtuale dell’*app di voto* per instradare gli utenti alla nuova versione *2.0*.

![Versione 1.0 dell'app di voto del servizio Azure Conatiner: flag delle funzionalità NON IMPOSTATO.](media/istio/canary-release-01.png)

![Versione 2.0 dell'app di voto del servizio Azure Conatiner: flag delle funzionalità IMPOSTATO.](media/istio/canary-release-02.png)

I conteggi di voto sono diversi a seconda delle versioni dell'app. Questa differenza evidenzia l’utilizzo di due back-end di archiviazione diversi.

## <a name="finalize-the-rollout"></a>Finalizzare l'implementazione

Dopo aver testato correttamente la versione canary, aggiornare il servizio virtuale dell’*app di voto* per indirizzare tutto il traffico alla versione *2.0* del componente dell’*app di voto*. Tutti gli utenti visualizzeranno quindi la versione *2.0* dell'applicazione, indipendentemente dal fatto che il flag delle funzionalità sia impostato o no:

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-04.png)

Aggiornare tutte le regole di destinazione per rimuovere le versioni dei componenti che non si desidera più che siano attive. Quindi, aggiornare tutti i servizi virtuali per evitare che facciano riferimento a tali versioni.

Poiché non c’è più traffico verso le versioni precedenti dei componenti, ora è possibile eliminare in modo sicuro tutte le distribuzioni per tali componenti.

![Componenti dell’app di voto e routing del servizio Azure Container.](media/istio/components-and-routing-05.png)

Nuova versione dell'app di voto del servizio Azure Conatiner implementata correttamente.

## <a name="next-steps"></a>Passaggi successivi

È possibile esplorare altri scenari usando l’[esempio di applicazione Bookinfo di Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
