---
title: Configurazione di Monitoraggio di Azure per i monitoraggi dell'integrità dei contenitori Documenti Microsoft
description: Questo articolo fornisce contenuto che descrive la configurazione dettagliata dei monitoraggi dell'integrità in Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055712"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Guida alla configurazione del monitoraggio dell'integrità di Monitoraggio di Azure per i contenitoriAzure Monitor for containers health monitor configuration guide

I monitoraggi sono l'elemento principale per la misurazione dell'integrità e il rilevamento degli errori in Monitoraggio di Azure per i contenitori. Questo articolo illustra i concetti di come viene misurata l'integrità e gli elementi che costituiscono il modello di integrità per monitorare e creare report sull'integrità del cluster Kubernetes con la funzionalità [di integrità (anteprima).](container-insights-health.md)

>[!NOTE]
>La funzionalità Integrità è attualmente in anteprima pubblica.
>

## <a name="monitors"></a>Monitoraggi

Un monitoraggio consente di misurare lo stato di integrità di alcuni aspetti di un oggetto gestito. Ogni monitoraggio ha due o tre stati di integrità. In un determinato momento un monitoraggio si troverà in uno solo dei relativi stati possibili. Quando un monitoraggio caricato dall'agente containerizzato, viene inizializzato su uno stato integro. Lo stato cambia solo se vengono rilevate le condizioni specificate per un altro stato.

L'integrità generale di un determinato oggetto è determinata dall'integrità di ogni singolo monitoraggio. Questa gerarchia è illustrata nel riquadro Gerarchia di integrità in Monitoraggio di Azure per i contenitori. I criteri per il rollup dell'integrità fanno parte della configurazione dei monitoraggi aggregati.

## <a name="types-of-monitors"></a>Tipi di monitor

|Monitorare | Descrizione | 
|--------|-------------|
| Monitoraggio unità |Un monitoraggio unità misura alcuni aspetti di una risorsa o di un'applicazione. Ciò potrebbe controllare un contatore delle prestazioni per determinare le prestazioni della risorsa o la relativa disponibilità. |
|Monitoraggio aggregato | I monitoraggi aggregati raggruppano più monitoraggi per fornire un singolo stato di integrità aggregato di integrità. I monitoraggi unità vengono in genere configurati in un monitor aggregato specifico. Ad esempio, un monitoraggio aggregato nodo esegue il rollup dello stato dell'utilizzo della CPU del nodo, dell'utilizzo della memoria e dello stato Nodo.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Aggregare i criteri di rollup dell'integrità del monitoraggio

Ogni monitoraggio aggregato definisce un criterio di rollup dell'integrità, ovvero la logica utilizzata per determinare l'integrità del monitoraggio aggregato in base all'integrità dei monitoraggi al quale è stato. I possibili criteri di rollup dell'integrità per un monitoraggio aggregato sono i seguenti:

#### <a name="worst-state-policy"></a>Peggiore politica di stato

Lo stato del monitoraggio aggregato corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore. Si tratta dei criteri più comuni utilizzati dai monitoraggi aggregati.

![Esempio di stato peggiore del rollup del monitoraggio aggregato](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Politica percentuale

L'oggetto di origine corrisponde allo stato peggiore di un singolo membro di una percentuale specificata di oggetti di destinazione nello stato migliore. Questo criterio viene utilizzato quando una determinata percentuale di oggetti di destinazione deve essere integra affinché l'oggetto di destinazione venga considerato integro. I criteri percentuali ordinano i monitoraggi in ordine decrescente di gravità dello stato e lo stato del monitoraggio aggregato viene calcolato come lo stato peggiore di N% (N è dettato dal parametro di configurazione *StateThresholdPercentage*).

Si supponga, ad esempio, che siano presenti cinque istanze contenitore di un'immagine contenitore e che i relativi singoli stati siano **Critical**, **Warning**, **Healthy**, **Healthy**, **Healthy**.  Lo stato del monitoraggio dell'utilizzo della CPU del contenitore sarà **Critico**, poiché lo stato peggiore del 90% dei contenitori è **Critico** quando viene ordinato in ordine decrescente di gravità.

## <a name="understand-the-monitoring-configuration"></a>Comprendere la configurazione del monitoraggio

Monitoraggio di Azure per i contenitori include una serie di scenari di monitoraggio chiave configurati come segue.

### <a name="unit-monitors"></a>Monitor unità

|**Nome monitoraggio** | Tipo di monitoraggio | **Descrizione** | **Parametro** | **Valore** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilizzo della memoria del nodoNode Memory Utilization |Monitoraggio unità |Questo monitoraggio valuta l'utilizzo della memoria di un nodo ogni minuto, utilizzando i dati segnalati da cadvisor. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage (Percentuale) | 3<br> 90<br> 80  ||
|Utilizzo CPU nodo |Monitoraggio unità |Questo monitoraggio controlla l'utilizzo della CPU del nodo ogni minuto, utilizzando i dati segnalati da cadvisor. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage (Percentuale) | 3<br> 90<br> 80  ||
|Stato nodo |Monitoraggio unità |Questo monitoraggio controlla le condizioni del nodo segnalate da Kubernetes.<br> Attualmente vengono controllate le seguenti condizioni del nodo: Pressione del disco, Pressione memoria, Pressione PID, Disco fuori disco, Rete non disponibile, Stato Pronto per il nodo.<br> Delle condizioni di cui sopra, se *Su disco* esaurito o *Rete non disponibile* è **true**, il monitor passa allo stato **Critico.**<br> Se qualsiasi altra condizione è **uguale a true**, ad esempio lo stato **Pronto,** il monitoraggio passa a uno stato **di avviso.** | NodoConditionTypeForFailedState | outofdisk,networkunavailable ||
|Utilizzo della memoria del contenitore |Monitoraggio unità |Questo monitoraggio segnala lo stato di integrità combinato dell'utilizzo della memoria (RSS) delle istanze del contenitore.<br> Esegue un semplice confronto che confronta ogni campione con una singola soglia e specificato dal parametro di configurazione **ConsecutiveSamplesForStateTransition**.<br> Il relativo stato viene calcolato come lo stato peggiore del 90% delle istanze del contenitore (StateThresholdPercentage), ordinate in ordine decrescente di gravità dello stato di integrità del contenitore, ovvero Critico, Avviso, Integro.<br> Se non viene ricevuto alcun record da un'istanza del contenitore, lo stato di integrità dell'istanza del contenitore viene segnalato come **Sconosciuto**e ha una precedenza maggiore nell'ordinamento rispetto allo stato **Critico.**<br> Lo stato di ogni singola istanza contenitore viene calcolato utilizzando le soglie specificate nella configurazione. Se l'utilizzo supera la soglia critica (90%), l'istanza si trova in uno stato **Critico,** se è inferiore alla soglia critica (90%). ma maggiore della soglia di avviso (80%), l'istanza è in uno stato **di avviso.** In caso contrario, è in stato **Integro.Otherwise,** it is in Healthy state. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> Percentuale Di stato<br> WarnIfGreaterThanPercentage (Percentuale)| 3<br> 90<br> 90<br> 80 ||
|Utilizzo della CPU del contenitore |Monitoraggio unità |Questo monitoraggio segnala lo stato di integrità combinato dell'utilizzo della CPU delle istanze del contenitore.<br> Esegue un semplice confronto che confronta ogni campione con una singola soglia e specificato dal parametro di configurazione **ConsecutiveSamplesForStateTransition**.<br> Il relativo stato viene calcolato come lo stato peggiore del 90% delle istanze del contenitore (StateThresholdPercentage), ordinate in ordine decrescente di gravità dello stato di integrità del contenitore, ovvero Critico, Avviso, Integro.<br> Se non viene ricevuto alcun record da un'istanza del contenitore, lo stato di integrità dell'istanza del contenitore viene segnalato come **Sconosciuto**e ha una precedenza maggiore nell'ordinamento rispetto allo stato **Critico.**<br> Lo stato di ogni singola istanza contenitore viene calcolato utilizzando le soglie specificate nella configurazione. Se l'utilizzo supera la soglia critica (90%), l'istanza si trova in uno stato **Critico,** se è inferiore alla soglia critica (90%). ma maggiore della soglia di avviso (80%), l'istanza è in uno stato **di avviso.** In caso contrario, è in stato **Integro.Otherwise,** it is in Healthy state. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> Percentuale Di stato<br> WarnIfGreaterThanPercentage (Percentuale)| 3<br> 90<br> 90<br> 80 ||
|I pod del carico di lavoro del sistema sono pronti |Monitoraggio unità |Questo monitoraggio segnala lo stato in base alla percentuale di pod in stato pronto in un determinato carico di lavoro. Il suo stato è impostato su **Critico** se meno del 100% dei pod è in uno stato **Integro** |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Stato dell'API Kube |Monitoraggio unità |Questo monitoraggio segnala lo stato del servizio API Kube. Monitor è in stato critico nel caso in cui l'endpoint dell'API Kube non sia disponibile. Per questo particolare monitoraggio, lo stato viene determinato eseguendo una query all'endpoint 'nodes' per il server kube-api. Qualsiasi elemento diverso da un codice di risposta OK passa il monitoraggio a uno stato **critico.** | Nessuna proprietà di configurazione |||

### <a name="aggregate-monitors"></a>Monitoraggi aggregati

|**Nome monitoraggio** | **Descrizione** | **Algoritmo** |
|-----------------|-----------------|---------------|
|Nodo |Questo monitoraggio è un'aggregazione di tutti i monitoraggi dei nodi. Corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore:<br> Utilizzo della CPU del nodoNode CPU utilization<br> Utilizzo della memoria dei nodiNode memory utilization<br> Stato nodo | Peggiore dei|
|Pool di nodiNode pool |Questo monitoraggio segnala lo stato di integrità combinato di tutti i nodi nel pool di nodi *agentpool*. Si tratta di un monitoraggio a tre stati, il cui stato è basato sullo stato peggiore dell'80% dei nodi nel pool di nodi, ordinati in ordine decrescente di gravità degli stati dei nodi( (ovvero Critical, Warning, Healthy).|Percentuale |
|Nodi (padre del pool di nodi) |Si tratta di un monitoraggio aggregato di tutti i pool di nodi. Il suo stato si basa sullo stato peggiore dei relativi monitor figlio, ovvero i pool di nodi presenti nel cluster. |Peggiore dei |
|Cluster (padre di nodi/<br> infrastruttura Kubernetes) |Questo è il monitoraggio padre che corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore, ovvero kubernetes infrastruttura e nodi. |Peggiore dei |
|Infrastruttura Kubernetes |Questo monitoraggio segnala lo stato di integrità combinato dei componenti dell'infrastruttura gestita del cluster. il suo stato viene calcolato come il "peggiore" del monitor figlio indica, ad esempio, i carichi di lavoro del sistema kube e lo stato del server API. |Peggiore dei|
|Carico di lavoro del sistema |Questo monitoraggio segnala lo stato di integrità di un carico di lavoro del sistema kube. Questo monitoraggio corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore, ovvero **i Pod in stato pronto** (monitor e i contenitori nel carico di lavoro). |Peggiore dei |
|Contenitore |Questo monitoraggio segnala lo stato di integrità generale di un contenitore in un determinato carico di lavoro. Questo monitoraggio corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore, ovvero **l'utilizzo della CPU** e i monitoraggi **per l'utilizzo della memoria.** |Peggiore dei |

## <a name="next-steps"></a>Passaggi successivi

Visualizzare [l'integrità](container-insights-health.md) del cluster di monitoraggio per informazioni sulla visualizzazione dello stato di integrità del cluster Kubernetes.View monitor cluster health to learn about viewing the health status your Kubernetes cluster.
