---
title: Guida al dimensionamento di cluster Interactive Query in Azure HDInsight
description: Guida al dimensionamento di Interactive Query in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663649"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Guida al dimensionamento di cluster Interactive Query in Azure HDInsight

Questo documento descrive il dimensionamento del cluster Interactive Query in HDInsight (LLAP) per ottenere prestazioni ragionevoli con un carico di lavoro tipico. Le raccomandazioni fornite in questo documento sono generiche e carichi di lavoro specifici possono richiedere un'ottimizzazione specifica.

## <a name="default-vm-types-for-interactive-query"></a>Tipi di VM predefiniti per Interactive Query

| Tipo di nodo | Istanza | Dimensione |
|---|---|---|
| Head | D13 v2 | 8 vCPU, 56 GB di RAM, SSD da 400 GB |
| Worker | D14 v2 | 16 vCPU, 112 GB di RAM, SSD da 800 GB |
| ZooKeeper | A4 v2 | 4 vCPU, 8 GB di RAM, SSD da 40 GB |

## <a name="recommended-configurations"></a>Configurazioni consigliate

I valori di configurazione consigliati sono basati sul nodo di lavoro di tipo D14 V2.

| Chiave | valore | Descrizione |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memoria totale assegnata, in MB, per tutti i contenitori YARN in un nodo. |
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | L'allocazione massima per ogni richiesta di contenitori in Resource Manager, in MB. Le richieste di memoria superiori a questo valore non avranno effetto. |
| yarn.scheduler.maximum-allocation-vcores | 12 |Il numero massimo di core di CPU per ogni richiesta di contenitori in Resource Manager. Le richieste superiori a questo valore non avranno effetto. |
| yarn.scheduler.capacity.root.llap.capacity | 90% | Allocazione di capacità YARN per la coda LLAP.  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |Il numero di sessioni per ogni coda specificata in hive.server2.tez.default.queues. Questo numero corrisponde al numero di coordinatori di query (Tez AM). |
| tez.am.resource.memory.mb | 4096 (MB) | La quantità di memoria, in MB, che dovrà usare Tez AppMaster. |
| hive.tez.container.size | 4096 (MB) | Dimensioni del contenitore Tez specificato, in MB. |
| hive.llap.daemon.num.executors | 12 | Numero di executor per ogni daemon LLAP. |
| hive.llap.io.threadpool.size | 12 | Dimensioni del pool di thread per gli executor. |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | Memoria totale, in MB, usata dai singoli daemon LLAP (memoria per daemon).|
| hive.llap.io.memory.size | 409600 (MB) | Dimensioni della cache, in MB, per il daemon LLAP, purché la cache SSD sia abilitata. |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | Dimensioni della memoria, in MB, per creare un join di mappa. |

## <a name="llap-daemon-size-estimations"></a>Stime delle dimensioni dei daemon LLAP  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

Questo valore indica la somma massima di memoria, in MB, che possono usare i contenitori YARN in ogni nodo. Specifica la quantità di memoria che può essere usata da YARN in questo nodo, quindi questo valore deve essere minore della memoria totale in quel nodo.

Impostare questo valore = [memoria fisica totale sul nodo] – [memoria per sistema operativo + altri servizi].

È consigliabile impostare questo valore su circa il 90% della RAM disponibile. Per D14 v2, il valore consigliato è **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

Questo valore indica l'allocazione massima per ogni richiesta di contenitori in Resource Manager, in MB. Le richieste di memoria superiori al valore specificato non avranno effetto. Resource Manager può concedere memoria solo ai contenitori con incrementi di `yarn.scheduler.minimum-allocation-mb` e non può superare le dimensioni specificate da `yarn.scheduler.maximum-allocation-mb`. Questo valore non deve superare la memoria totale assegnata del nodo, specificata da `yarn.nodemanager.resource.memory-mb`.

Per i nodi di lavoro D14 v2, il valore consigliato è **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

Questa configurazione indica il numero massimo di core di CPU virtuali per ogni richiesta di contenitori in Resource Manager. La richiesta di un valore più elevato rispetto a questa configurazione non avrà effetto. La configurazione è una proprietà globale dell'utilità di pianificazione YARN. Per il contenitore di daemon LLAP, questo valore può essere impostato sul 75% del totale di memorie centrali virtuali (vCore) disponibili. Il rimanente 25% dovrà essere riservato per NodeManager, DataNode e altri servizi in esecuzione nei nodi di lavoro.  

Per i nodi di lavoro D14 V2 sono disponibili 16 vCore, di cui il 75% può essere assegnato. Il valore consigliato per il contenitore daemon LLAP è quindi **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

Questo valore di configurazione determina il numero di sessioni Tez che si devono avviare in parallelo per ogni coda specificata da `hive.server2.tez.default.queues`. Il valore corrisponde al numero di Tez AM (coordinatori di query). È consigliabile che sia identico al numero di nodi di lavoro per avere un Tez AM per nodo. Il numero di Tez AM può essere maggiore del numero di nodi di daemon LLAP. La principale responsabilità di Tez AM è coordinare l'esecuzione delle query e assegnare frammenti del piano di query ai daemon LLAP corrispondenti per l'esecuzione. È consigliabile mantenere questo valore come multiplo del numero di nodi di daemon LLAP per aumentare la velocità effettiva.  

Il cluster di HDInsight predefinito include quattro daemon LLAP in esecuzione in quattro nodi di lavoro, quindi il valore consigliato è **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb, hive.tez.container.size

`tez.am.resource.memory.mb` definisce le dimensioni di Tez Application Master.  
Il valore consigliato è **4096 MB**.

`hive.tez.container.size` definisce la quantità di memoria assegnata al contenitore Tez. Questo valore deve essere compreso tra le dimensioni minime del contenitore YARN (`yarn.scheduler.minimum-allocation-mb`) e le dimensioni massime del contenitore YARN (`yarn.scheduler.maximum-allocation-mb`).  
È consigliabile impostarlo su **4096 MB**.  

Una regola generale consiste nel mantenere il valore inferiore alla quantità di memoria per processore considerando un processore per ogni contenitore. Memoria `Rreserve` per il numero di Tez AM in un nodo prima di assegnare la memoria per il daemon LLAP. Ad esempio, se si usano due Tez AM (ognuno da 4 GB) per nodo, assegnare 82 GB su 90 GB per il daemon LLAP riservando 8 GB per due Tez AM.

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

Questo valore indica una percentuale della capacità assegnata alla coda LLAP. Il cluster Interactive query HDInsights assegna il 90% della capacità totale alla coda LLAP e il 10% rimanente è impostato sulla coda predefinita per altre allocazioni di contenitori.  
Per i nodi di lavoro D14v2, il valore consigliato per la coda LLAP è **90**.

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

Le dimensioni totali della memoria per il daemon LLAP dipende dai fattori seguenti:

* Configurazione delle dimensioni del contenitore YARN (`yarn.scheduler.maximum-allocation-mb`, `yarn.scheduler.maximum-allocation-mb`, `yarn.nodemanager.resource.memory-mb`)

* Memoria heap usata dagli executor (Xmx)

    Quantità di RAM disponibile dopo aver sottratto le dimensioni della capacità aggiuntiva.  
    Per D14 V2, HDI 4.0 - questo valore è pari a (86 GB - 6 GB) = 80 GB  
    Per D14 V2, HDI 3.6 - questo valore è pari a (84 GB - 6 GB) = 78 GB

* Cache in memoria off-heap per daemon (hive.llap.io.memory.size)

* Capacità aggiuntiva

    Si tratta di una parte della memoria off-heap usata per l'overhead delle VM Java (metaspazi, stack di thread, strutture dei dati di GC e così via). Questa parte rappresenta circa il 6% delle dimensioni dell'heap (Xmx). Per una maggiore tranquillità, il valore può essere calcolato come il 6% del totale della memoria dei daemon LLAP. Poiché è possibile se la cache SSD è abilitata, consentirà al daemon LLAP di usare tutto lo spazio disponibile in memoria solo per l'heap.  
    Per D14 v2, il valore consigliato è ceil(86 GB x 0,06) ~= **6 GB**.  

Memoria per daemon = [dimensioni della cache in memoria] + [dimensioni heap] + [capacità aggiuntiva].

Il valore può essere calcolato nel modo seguente:

Memoria di Tez AM per nodo = [ (Numero di Tez AM/Numero di nodi di daemon LLAP) x Dimensioni Tez AM ].
Dimensioni del contenitore del daemon LLAP = [ 90% della memoria max del contenitore YARN ] – [ memoria Tez AM per nodo ].

Per il nodo di lavoro D14 v2, HDI 4.0, il valore consigliato è (90 - (1/1 x 4 GB)) = **86 GB**.
(Per HDI 3.6, il valore consigliato è **84 GB** perché è consigliabile riservare circa 2 GB per Slider AM.)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

Questa configurazione è la quantità di memoria disponibile come cache per il daemon LLAP. I daemon LLAP possono usare unità SSD come cache. Se si imposta `hive.llap.io.allocator.mmap` = true, verrà abilitata la memorizzazione nella cache SSD. D14 v2 include circa 800 GB di unità SSD e la memorizzazione nella cache SSD è abilitata per impostazione predefinita per il cluster Interactive query (LLAP). È configurato per l'uso del 50% dello spazio SSD della cache off-heap.

Per D14 v2, il valore consigliato è **409600 MB**.  

Per altre macchine virtuali, senza la memorizzazione nella cache SSD abilitata, è utile assegnare parte della RAM disponibile per la memorizzazione nella cache di LLAP allo scopo di ottenere prestazioni migliori. Modificare le dimensioni totali della memoria per il daemon LLAP come segue:  

Totale memoria daemon LLAP = [dimensioni cache LLAP] + [dimensioni heap] + [capacità aggiuntiva].

È consigliabile modificare le dimensioni della cache e dell'heap scegliendo quelle più adatte per il proprio carico di lavoro.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Questa configurazione controlla il numero di executor che possono eseguire attività in parallelo per ogni daemon LLAP. Questo valore dipende dal numero di vCore disponibili, dalla quantità di memoria assegnata a ogni executor e dalla quantità totale di memoria disponibile per il daemon LLAP. In genere, questo valore deve essere il più vicino possibile al numero di core.

Per D14 V2 sono disponibili 16 vCore, tuttavia non è possibile assegnarle tutte. I nodi di lavoro eseguono anche altri servizi come NodeManager, DataNode e Metrics Monitor, che necessitano di una parte di vCore disponibili. Questo valore può essere configurato fino al 75% del totale di vCore disponibili in tale nodo.

Per D14 v2, il valore consigliato è (0,75 X 16) = **12**

È consigliabile riservare circa 6 GB di spazio heap per ogni executor. Modificare il numero di executor in base alle dimensioni disponibili del daemon LLAP e al numero di vCore disponibili per ogni nodo.  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

Questo valore specifica le dimensioni del pool di thread per gli executor. Poiché gli executor sono fissi, come specificato, il valore sarà identico al numero di executor per ogni daemon LAAP.

Per D14 v2, è consigliabile impostare questo valore su **12**.

Questa configurazione non può superare il valore di `yarn.nodemanager.resource.cpu-vcores`.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

Verificare che `hive.auto.convert.join.noconditionaltask` sia abilitato affinché questo parametro abbia effetto. Questa configurazione consente all'utente di specificare le dimensioni delle tabelle che possono rientrare in memoria per seguire un join di mappa. Se la somma delle dimensioni di n-1 `tables/partitions` per un join a n vie è inferiore al valore configurato, verrà scelto un join di mappa. Le dimensioni della memoria dell'executor LLAP dovranno essere usate per calcolare la soglia per la conversione automatica in join di mappa.

Per D14 v2, è consigliabile impostare questo valore su **2048 MB**.

È consigliabile modificare questo valore affinché si adatti al carico di lavoro, poiché l'impostazione di un valore troppo basso potrebbe impedire l'uso della funzionalità di conversione automatica. L'impostazione di un valore troppo alto può comportare una sospensione di GC, che può influire negativamente sulle prestazioni di esecuzione delle query.

## <a name="next-steps"></a>Passaggi successivi

* [Linee guida per il gateway](gateway-best-practices.md)
* [Spiegazione dell'ottimizzazione della memoria di Apache Tez - Procedura dettagliata](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Dimensionamento della memoria del join di mappa per LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - Panoramica dell'architettura di una pagina](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Approfondimenti su LLAP Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
