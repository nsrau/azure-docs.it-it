---
title: Configurazione di monitoraggio di Azure per il monitoraggio dello stato dei contenitori | Microsoft Docs
description: Questo articolo fornisce contenuto che descrive la configurazione dettagliata dei monitoraggi di integrità in monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: magoedte
ms.openlocfilehash: b782581318751830ec47b9fecb056fecefb353eb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134957"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Guida alla configurazione di monitoraggio integrità dei contenitori di monitoraggio di Azure

I monitoraggi sono l'elemento principale per misurare lo stato di integrità e rilevare gli errori in monitoraggio di Azure per i contenitori. Questo articolo consente di comprendere i concetti relativi alla misurazione dello stato di integrità e gli elementi che costituiscono il modello di integrità per monitorare e segnalare l'integrità del cluster Kubernetes con la [funzionalità di integrità](container-insights-health.md).

## <a name="monitors"></a>Monitoraggi

Un monitoraggio misura l'integrità di alcuni aspetti di un oggetto gestito. Ogni monitoraggio ha due o tre stati di integrità. Un monitoraggio si troverà in uno solo dei relativi stati potenziali in un determinato momento. Quando un monitoraggio viene caricato dall'agente in contenitori, viene inizializzato a uno stato integro. Lo stato viene modificato solo se vengono rilevate le condizioni specificate per un altro stato.

L'integrità complessiva di un determinato oggetto è determinata dall'integrità di ciascuno dei monitoraggi. Questa gerarchia è illustrata nel riquadro gerarchia di integrità di monitoraggio di Azure per i contenitori. I criteri per la modalità di rollup dell'integrità fanno parte della configurazione dei monitoraggi aggregati.

## <a name="types-of-monitors"></a>Tipi di monitoraggi

|Monitoraggio | DESCRIZIONE | 
|--------|-------------|
| Monitoraggio unità |Un monitoraggio unità misura alcuni aspetti di una risorsa o di un'applicazione. Questo potrebbe controllare un contatore delle prestazioni per determinare le prestazioni della risorsa o la relativa disponibilità. |
|Monitoraggio aggregato | I monitoraggi aggregati raggruppano più monitoraggi per fornire un unico stato di integrità aggregato. I monitoraggi unità vengono in genere configurati in un determinato monitoraggio aggregato. Un monitoraggio aggregato dei nodi, ad esempio, esegue il rollup dello stato del nodo utilizzo CPU, utilizzo memoria e stato nodo.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Criteri di rollup stato del monitoraggio aggregato

Ogni monitoraggio aggregato definisce criteri di rollup dello stato, ovvero la logica utilizzata per determinare lo stato del monitoraggio aggregato in base all'integrità dei monitoraggi sottoposti. I possibili criteri di rollup dello stato per un monitoraggio aggregato sono i seguenti:

#### <a name="worst-state-policy"></a>Criterio di stato peggiore

Lo stato del monitoraggio aggregato corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore. Questo è il criterio più comune utilizzato dai monitoraggi aggregati.

![Esempio di stato peggiore del rollup del monitoraggio aggregato](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Criterio percentuale

L'oggetto di origine corrisponde allo stato peggiore di un singolo membro di una percentuale specificata di oggetti di destinazione nello stato migliore. Questo criterio viene usato quando una determinata percentuale di oggetti di destinazione deve essere integra per l'oggetto di destinazione per essere considerata integro. I criteri di percentuale ordinano i monitoraggi in ordine decrescente di gravità dello stato e lo stato del monitoraggio aggregato viene calcolato come lo stato peggiore di N% (N è dettato dal parametro di configurazione *StateThresholdPercentage*).

Si supponga, ad esempio, che esistano cinque istanze di contenitore di un'immagine del contenitore e che i rispettivi Stati siano **critici**, **avvisi**, **integri**, **integri**, **integri.**  Lo stato del monitoraggio dell'utilizzo della CPU del contenitore sarà **critico**, poiché lo stato peggiore del 90% dei contenitori è **critico** quando viene ordinato in ordine decrescente di gravità.

## <a name="understand-the-monitoring-configuration"></a>Informazioni sulla configurazione di monitoraggio

Il monitoraggio di Azure per i contenitori include diversi scenari di monitoraggio principali configurati come indicato di seguito.

### <a name="unit-monitors"></a>Monitoraggi unità

|**Nome monitoraggio** | Tipo di monitoraggio | **Descrizione** | **Parametro** | **Valore** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilizzo memoria nodo |Monitoraggio unità |Questo monitoraggio valuta l'utilizzo della memoria di un nodo ogni minuto, usando i dati segnalati da cadvisor. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Utilizzo CPU nodo |Monitoraggio unità |Questo monitoraggio controlla l'utilizzo della CPU del nodo ogni minuto, usando i dati segnalati da cadvisor. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Stato del nodo |Monitoraggio unità |Questo monitoraggio controlla le condizioni del nodo segnalate da Kubernetes.<br> Attualmente vengono verificate le condizioni del nodo seguenti: pressione del disco, utilizzo della memoria, pressione del PID, disco esaurito, rete non disponibile, stato pronto per il nodo.<br> Una delle condizioni precedenti, se il *disco* o la rete non *disponibile* è **true**, il monitoraggio cambia in stato **critico** .<br> Se qualsiasi altra condizione è uguale a **true**, a parte lo stato **pronto** , il monitoraggio passa a uno stato di **avviso** . | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Utilizzo memoria contenitore |Monitoraggio unità |Questo monitoraggio segnala lo stato di integrità combinato dell'utilizzo della memoria (RSS) delle istanze del contenitore.<br> Esegue un confronto semplice che confronta ogni campione con una sola soglia e specificato dal parametro di configurazione **ConsecutiveSamplesForStateTransition**.<br> Il suo stato viene calcolato come lo stato peggiore del 90% delle istanze del contenitore (StateThresholdPercentage), ordinate in ordine decrescente di gravità dello stato di integrità del contenitore (ovvero, critico, avviso, integro).<br> Se non viene ricevuto alcun record da un'istanza del contenitore, lo stato di integrità dell'istanza del contenitore viene segnalato come **sconosciuto**e ha una precedenza maggiore nell'ordinamento sullo stato **critico** .<br> Lo stato di ogni singola istanza del contenitore viene calcolato utilizzando le soglie specificate nella configurazione. Se l'utilizzo supera la soglia critica (90%), l'istanza si trova in uno stato **critico** , se è inferiore alla soglia critica (90%) ma maggiore della soglia di avviso (80%), l'istanza si trova in uno stato di **avviso** . In caso contrario, lo stato è **integro** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Utilizzo CPU contenitore |Monitoraggio unità |Questo monitoraggio segnala lo stato di integrità combinato dell'utilizzo della CPU delle istanze del contenitore.<br> Esegue un confronto semplice che confronta ogni campione con una sola soglia e specificato dal parametro di configurazione **ConsecutiveSamplesForStateTransition**.<br> Il suo stato viene calcolato come lo stato peggiore del 90% delle istanze del contenitore (StateThresholdPercentage), ordinate in ordine decrescente di gravità dello stato di integrità del contenitore (ovvero, critico, avviso, integro).<br> Se non viene ricevuto alcun record da un'istanza del contenitore, lo stato di integrità dell'istanza del contenitore viene segnalato come **sconosciuto**e ha una precedenza maggiore nell'ordinamento sullo stato **critico** .<br> Lo stato di ogni singola istanza del contenitore viene calcolato utilizzando le soglie specificate nella configurazione. Se l'utilizzo supera la soglia critica (90%), l'istanza si trova in uno stato **critico** , se è inferiore alla soglia critica (90%) ma maggiore della soglia di avviso (80%), l'istanza si trova in uno stato di **avviso** . In caso contrario, lo stato è **integro** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Pod del carico di lavoro del sistema pronto |Monitoraggio unità |Questo monitoraggio segnala lo stato in base alla percentuale di Pod nello stato pronto in un determinato carico di lavoro. Lo stato è impostato su **critico** se il valore inferiore al 100% dei Pod è **integro** |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Stato dell'API Kube |Monitoraggio unità |Questo monitoraggio segnala lo stato del servizio API Kube. Il monitoraggio è in stato critico nel caso in cui l'endpoint dell'API Kube non sia disponibile. Per questo particolare monitoraggio, lo stato è determinato dall'esecuzione di una query sull'endpoint ' nodes ' per il server Kube-API. Qualsiasi elemento diverso da un codice di risposta OK modifica il monitoraggio in uno stato **critico** . | Nessuna proprietà di configurazione |||

### <a name="aggregate-monitors"></a>Monitoraggi aggregati

|**Nome monitoraggio** | **Descrizione** | **Algoritmo** |
|-----------------|-----------------|---------------|
|Nodo |Questo monitoraggio è un'aggregazione di tutti i monitoraggi dei nodi. Corrisponde allo stato del monitoraggio figlio con il peggior stato di integrità:<br> Utilizzo CPU nodo<br> Utilizzo memoria nodo<br> Stato del nodo | Peggiore di|
|Pool di nodi |Questo monitoraggio segnala lo stato di integrità combinato di tutti i nodi nel pool di nodi *agentpool*. Si tratta di un monitoraggio a tre stati, il cui stato è basato sullo stato peggiore del 80% dei nodi nel pool di nodi, ordinati in ordine decrescente di gravità degli Stati dei nodi (ovvero, critico, avviso, integro).|Percentuale |
|Nodi (padre del pool di nodi) |Si tratta di un monitoraggio aggregato di tutti i pool di nodi. Il suo stato è basato sullo stato peggiore dei monitoraggi figlio (ovvero i pool di nodi presenti nel cluster). |Peggiore di |
|Cluster (elemento padre dei nodi/<br> Infrastruttura Kubernetes |Si tratta del monitoraggio padre che corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore, ovvero l'infrastruttura kubernetes e i nodi. |Peggiore di |
|Infrastruttura Kubernetes |Questo monitoraggio segnala lo stato di integrità combinato dei componenti dell'infrastruttura gestita del cluster. il suo stato viene calcolato come il ' peggiore di ' dei propri stati di monitoraggio figlio, ad esempio i carichi di lavoro del sistema e il server API. |Peggiore di|
|Carico di lavoro di sistema |Questo monitoraggio segnala lo stato di integrità di un carico di lavoro Kube-System. Questo monitoraggio corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore, ovvero i **Pod nel monitoraggio stato pronto** e i contenitori nel carico di lavoro. |Peggiore di |
|Contenitore: |Questo monitoraggio segnala lo stato di integrità generale di un contenitore in un determinato carico di lavoro. Questo monitoraggio corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore, ovvero i monitoraggi **utilizzo CPU** e **utilizzo memoria** . |Peggiore di |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla visualizzazione dello stato di integrità del cluster Kubernetes, vedere [monitorare l'integrità del cluster](container-insights-health.md) .