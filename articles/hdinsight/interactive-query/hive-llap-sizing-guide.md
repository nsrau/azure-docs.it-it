---
title: Guida al dimensionamento di cluster Interactive Query in HDInsight (LLAP)
description: Guida al dimensionamento di LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663689"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Guida al dimensionamento di cluster Interactive Query in HDInsight (Hive LLAP)

Questo documento descrive il dimensionamento del cluster Interactive Query in HDInsight (Hive LLAP) per ottenere prestazioni ragionevoli con un carico di lavoro tipico. Si noti che le raccomandazioni fornite in questo documento sono linee guida generiche e che carichi di lavoro specifici possono richiedere  un'ottimizzazione specifica.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Tipi di VM di Azure predefiniti per il cluster Interactive Query in HDInsight (LLAP)**

| Tipo di nodo      | Istanza | Dimensione     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB di RAM, SSD da 400 GB   |
| Worker   | **D14 v2**        | **16 vCPU, 112 GB di RAM, SSD da 800 GB**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB di RAM, SSD da 40 GB       |

***Nota: tutti i valori di configurazione raccomandati sono basati sul nodo di lavoro di tipo D14 V2***  

### <a name="configuration"></a>**Configurazione:**    
| Chiave di configurazione      | Valore consigliato  | Descrizione |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memoria totale fornita, in MB, per tutti i contenitori YARN in un nodo | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | L'allocazione massima per ogni richiesta di contenitori in Resource Manager, in MB. Le richieste di memoria superiori a questo valore non avranno effetto |
| yarn.scheduler.maximum-allocation-vcores | 12 |Il numero massimo di core di CPU per ogni richiesta di contenitori in Resource Manager. Le richieste superiori a questo valore non avranno effetto. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Numero di core di CPU per NodeManager che è possibile allocare per i contenitori. |
| yarn.scheduler.capacity.root.llap.capacity | 80 (%) | Allocazione di capacità YARN per la coda LLAP  |
| tez.am.resource.memory.mb | 4096 (MB) | La quantità di memoria, in MB, che dovrà usare Tez AppMaster |
| hive.server2.tez.sessions.per.default.queue | <numero_di_nodi_di_lavoro> |Il numero di sessioni per ogni coda specificata in hive.server2.tez.default.queues. Questo numero corrisponde al numero di coordinatori di query (Tez AM) |
| hive.tez.container.size | 4096 (MB) | Dimensioni del contenitore Tez specificato, in MB |
| hive.llap.daemon.num.executors | 12 | Numero di executor per ogni daemon LLAP | 
| hive.llap.io.threadpool.size | 12 | Dimensioni del pool di thread per gli executor |
| hive.llap.daemon.yarn.container.mb | 77824 (MB) | Memoria totale, in MB, usata dai singoli daemon LLAP (memoria per daemon)
| hive.llap.io.memory.size | 235520 (MB) | Dimensioni della cache, in MB, per il daemon LLAP, purché la cache SSD sia abilitata |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | Dimensioni della memoria, in MB, per eseguire un join di mappa |

### <a name="llap-daemon-size-estimations"></a>**Stime delle dimensioni dei daemon LLAP:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Determinazione dell'allocazione di memoria totale a YARN per tutti i contenitori di un nodo**    
Configurazione: ***yarn.nodemanager.resource.memory-mb***  

Questo valore indica la somma massima di memoria, in MB, che possono usare i contenitori YARN in ogni nodo. Il valore specificato dovrà essere minore della quantità totale di memoria fisica disponibile in tale nodo.   
Memoria totale di tutti i contenitori YARN in un nodo = [Memoria fisica totale] - [Memoria per sistema operativo + altri servizi]  
Impostare questo valore su circa il 90% delle dimensioni della RAM disponibile.  
Per D14 v2, il valore consigliato è  **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Determinazione della quantità massima di memoria per ogni richiesta di contenitori YARN**  
Configurazione: ***yarn.scheduler.maximum-allocation-mb***

Questo valore indica l'allocazione massima per ogni richiesta di contenitori in Resource Manager, in MB. Le richieste di memoria superiori al valore specificato non avranno effetto. Resource Manager può fornire memoria ai contenitori in incrementi di *yarn.scheduler.minimum-allocation-mb* e non può superare le dimensioni specificate da *yarn.scheduler.maximum-allocation-mb*. Il valore specificato non dovrà essere maggiore del totale della memoria fornita per tutti i contenitori nel nodo, specificata da *yarn.nodemanager.resource.memory-mb*.    
Per i nodi di lavoro D14 v2, il valore consigliato è **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Determinazione della quantità massima di vCore per ogni richiesta di contenitori YARN**  
Configurazione: ***yarn.scheduler.maximum-allocation-vcores***  

Questo valore indica il numero massimo di core di CPU virtuali per ogni richiesta di contenitori in Resource Manager. La richiesta di un numero di vCore superiore a questo valore non avrà effetto. Si tratta di una proprietà globale dell'utilità di pianificazione YARN. Per il contenitore di daemon LLAP, questo valore può essere impostato sul 75% del totale di vCore disponibili. Il rimanente 25% dovrà essere riservato per NodeManager, DataNode e altri servizi in esecuzione nei nodi di lavoro.  
Le VM D14 v2 includono 16 vCore e per il contenitore di daemon LLAP è possibile usare il 75% del totale di 16 vCore.  
Per D14 v2, il valore consigliato è **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Numero di query simultanee**  
Configurazione: ***hive.server2.tez.sessions.per.default.queue***

Questo valore di configurazione determina il numero di sessioni Tez che è possibile avviare in parallelo. Queste sessioni Tez verranno avviate per ogni coda specificata da "hive.server2.tez.default.queues". Il valore corrisponde al numero di Tez AM (coordinatori di query). È consigliabile che sia identico al numero di nodi di lavoro. Il numero di Tez AM può essere maggiore del numero di nodi di daemon LLAP. La principale responsabilità di Tez AM è coordinare l'esecuzione delle query e assegnare frammenti del piano di query ai daemon LLAP corrispondenti per l'esecuzione. Mantenere questo valore come multiplo del numero di nodi di daemon LLAP per aumentare la velocità effettiva.  

Il cluster di HDInsight predefinito include quattro daemon LLAP in esecuzione in quattro nodi di lavoro, quindi il valore consigliato è **4**.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Dimensioni di Tez Container e Tez Application Master**    
Configurazione: ***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb*: definisce le dimensioni di Tez Application Master.  
Il valore consigliato è **4096 MB**.
   
*hive.tez.container.size*: definisce la quantità di memoria assegnata a Tez Container. Questo valore deve essere impostato tra le dimensioni minime di contenitori YARN (*yarn.scheduler.minimum-allocation-mb*) e le dimensioni massime di contenitori YARN (*yarn.scheduler.maximum-allocation-mb*). Gli executor di daemon LLAP usano questo valore per limitare l'utilizzo di memoria per ogni executor.  
Il valore consigliato è **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Allocazione di capacità alla coda LLAP**   
Configurazione: ***yarn.scheduler.capacity.root.llap.capacity***  

Questo valore indica una percentuale della capacità assegnata alla coda LLAP. Le allocazioni di capacità possono avere valori diversi per carichi di lavoro diversi, a seconda della configurazione delle code YARN. Se il carico di lavoro è costituito da operazioni di sola lettura, l'impostazione fino al 90% della capacità dovrebbe funzionare. Se invece il carico di lavoro è costituito da una combinazione di operazioni di aggiornamento/eliminazione/unione usando tabelle gestite, è consigliabile assegnare l'80% della capacità alla coda LLAP. Il rimanente 20% della capacità può essere usato da altre attività, ad esempio compattazione e così via, per allocare contenitori dalla coda predefinita. In questo modo le attività della coda predefinita non tolgono risorse YARN.    
Per i nodi di lavoro D14v2, il valore consigliato per la coda LLAP è **80**.   
Per i carichi di lavoro di sola lettura, può essere aumentato a 90 se appropriato.  

#### <a name="7-llap-daemon-container-size"></a>**7. Dimensioni dei contenitori di daemon LLAP**    
Configurazione: ***hive.llap.daemon.yarn.container.mb***  
   
Il daemon LLAP viene eseguito come contenitore YARN in ogni nodo di lavoro. Le dimensioni totali della memoria per i contenitori di daemon LLAP dipende dai fattori seguenti:    
*  Configurazioni delle dimensioni dei contenitori YARN (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Numero di Tez AM in un nodo
*  Memoria totale configurata per tutti i contenitori in un nodo e capacità della coda LLAP  

La memoria necessaria per Tez Application Master (Tez AM) può essere calcolata come segue.  
Per il cluster Interactive in HDInsight, per impostazione predefinita esiste un Tez AM per ogni nodo di lavoro che funge da coordinatore di query. Il numero di Tez AM può essere configurato in base al numero di query simultanee da gestire.
È consigliabile avere 4 GB di memoria per ogni Tez AM.

Memoria di Tez AM per nodo = [ ceil(Numero di Tez AM/Numero di nodi di daemon LLAP) ] x [ Dimensioni dei contenitori Tez AM ]  
Per D14 v2, la configurazione predefinita include quattro Tez AM e quattro nodi di daemon LLAP.  
Memoria del Tez AM per nodo = (ceil(4/4) x 4 GB) = 4 GB

La memoria totale disponibile per la coda LLAP per ogni nodo di lavoro può essere calcolata come segue: Questo valore dipende dalla quantità totale di memoria disponibile per tutti i contenitori YARN in un nodo (*yarn.nodemanager.resource.memory-mb*) e dalla percentuale di capacità configurata per la coda LLAP (*yarn.scheduler.capacity.root.llap.capacity*).  
Memoria totale per la coda LLAP nel nodo di lavoro = Memoria totale disponibile per tutti i contenitori YARN in un nodo x Percentuale di capacità per la coda LLAP.  
Per D14 v2, questo valore è [ 100 GB x 0,80 ] = 80 GB.

Le dimensioni del contenitore di daemon LLAP vengono calcolate come segue:

**Dimensioni del contenitore di daemon LLAP = [ Memoria totale disponibile per la coda LLAP] - [Memoria di Tez AM per nodo]**  
    
Per i nodi di lavoro D14 v2, HDI 4.0, il valore consigliato è (80 GB - 4 GB)) = **76 GB**   
Per HDI 3.6, il valore consigliato è **74 GB** perché è consigliabile riservare circa altri 2 GB per Slider AM.  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Determinazione del numero di executor per ogni daemon LLAP**  
Configurazione: ***hive.llap.daemon.num.executors***, ***hive.llap.io.threadpool.size***

***hive.llap.daemon.num.executors***:   
Questa configurazione controlla il numero di executor che possono eseguire attività in parallelo per ogni daemon LLAP. Questo valore dipende dal numero di vCore, dalla quantità di memoria assegnata a ogni executor e dalla quantità totale di memoria disponibile per il daemon LLAP. In genere, questo valore deve essere il più vicino possibile al numero di vCore.
Le VM D14 v2 includono 16 vCore. Tuttavia, non è possibile prendere tutti i vCore perché anche altri servizi, come NodeManager, DataNode, Metrics Monitor e così via, richiedono parte dei vCore disponibili. 

Se è necessario modificare il numero di executor, è consigliabile considerare 4 GB di memoria per ogni executor, come specificato da *hive.tez.container.size*, e assicurarsi che la memoria totale necessaria per tutti gli executor non superi la memoria totale disponibile per il contenitore di daemon LLAP.  
Questo valore può essere configurato fino al 75% del totale di vCore disponibili in tale nodo.  
Per D14 v2, il valore consigliato è (0,75 X 16) = **12**

***hive.llap.io.threadpool.size***:   
questo valore specifica le dimensioni del pool di thread per gli executor. Poiché gli executor sono fissi, come specificato, il valore sarà identico al numero di executor per ogni daemon LAAP.   
Per D14 v2, il valore consigliato è **12**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Determinazione delle dimensioni della cache dei daemon LLAP**  
Configurazione: ***hive.llap.io.memory.size***

La memoria del contenitore di daemon LLAP è costituita dai componenti seguenti:
*  Capacità aggiuntiva
*  Memoria heap usata dagli executor (Xmx)
*  Cache in memoria per ogni daemon (dimensioni della memoria off-heap, non applicabile se la cache SSD è abilitata)
*  Dimensioni dei metadati della cache in memoria (applicabile solo se la cache SSD è abilitata)

**Dimensioni della capacità aggiuntiva**: indicano una parte della memoria off-heap usata per l'overhead delle VM Java (metaspazi, stack di thread, strutture dei dati di GC e così via). In genere, questo overhead è circa il 6% delle dimensioni heap (Xmx). Per una maggiore tranquillità, questo valore può essere calcolato come il 6% del totale della memoria dei daemon LLAP.  
Per D14 v2, il valore consigliato è ceil(76 GB x 0,06) =~**5 GB**.  

**Dimensioni heap (Xmx)** : si tratta di una quantità di memoria heap disponibile per tutti gli executor.
Totale dimensioni heap = Numero di executor x 4 GB  
Per D14 v2, questo valore è 12 x 4 GB = **48 GB**  

Se la cache SSD è disabilitata, la cache in memoria corrisponde alla quantità di memoria che rimane dopo aver tolto le dimensioni della capacità aggiuntiva e heap dalle dimensioni del contenitore di daemon LLAP.

Il calcolo delle dimensioni della cache è diverso se la cache SSD è abilitata.
Se si imposta *hive.llap.io.allocator.mmap* su true, la cache SSD verrà abilitata.
Se la cache SSD è abilitata, parte della memoria verrà usata per archiviare i relativi metadati. I metadati vengono archiviati in memoria ed è previsto che le relative dimensioni siano circa il 10% delle dimensioni della cache SSD.   
Dimensioni dei metadati in memoria della cache SSD = [ Dimensioni del contenitore di daemon LLAP ] - [ Capacità aggiuntiva + Dimensioni heap]  
Per D14 v2, con HDI 4.0, le dimensioni dei metadati in memoria della cache SSD sono uguali a [ 76 GB ] - [ 5 GB + 48 GB ] = **23 GB**  
Per D14 v2, con HDI 3.6, le dimensioni dei metadati in memoria della cache SSD sono uguali a [ 76 GB ] - [ 5 GB + 48 GB + 2 GB Slider ] = **21 GB**

Considerando le dimensioni della memoria disponibili per archiviare i metadati della cache SSD, è possibile calcolare le dimensioni della cache SSD supportabili.  
Dimensioni dei metadati in memoria per la cache SSD = 10% delle dimensioni della cache SSD       
Dimensioni della cache SSD = dimensioni dei metadati in memoria per la cache SSD x 10  

Per D14 v2 e HDI 4.0, le dimensioni consigliate della cache SSD sono uguali a 23 GB x 10 = **230 GB**  
Per D14 v2 e HDI 3.6, le dimensioni della cache SSD consigliate sono uguali a 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Regolazione della memoria per join di mappa**   
Configurazione: ***hive.auto.convert.join.noconditionaltask.size***

Assicurarsi che *hive.auto.convert.join.noconditionaltask* sia abilitato per rendere effettivo questo parametro.
Questa configurazione consente all'utente di specificare le dimensioni delle tabelle che possono rientrare in memoria per seguire un join di mappa. Se la somma delle dimensioni di n-1 tabelle o partizioni per un join a n vie è inferiore al valore configurato, verrà scelto un join di mappa. Le dimensioni della memoria dell'executor LLAP dovranno essere usate per calcolare la soglia per la conversione automatica in join di mappa.
Si presuppone che ogni executor abbia dimensioni heap di 4 GB, ma non tutti disponibili per il join di mappa. Parte della memoria heap verrà usata anche da altre operazioni per i buffer di ordinamento, i buffer di sequenze casuali, le tabelle hash e così via. Quindi, è possibile assegnare il 50% dei 4 GB di memoria heap per il join di mappa.  
Nota: questo valore potrebbe richiedere modifiche appropriate per il carico di lavoro. Se viene impostato un valore troppo basso, è possibile che la funzionalità di conversione automatica non venga usata. Se viene impostato un valore troppo alto si potrebbero generare eccezioni di memoria esaurita o pause di GC con conseguenze negative per le prestazioni.  
Per D14 v2, con 4 GB di memoria per executor, è consigliabile impostare questo valore su **2048 MB**.


#### <a name="next-steps"></a>**Passaggi successivi**
Se l'impostazione di questi valori non ha consentito di risolvere il problema, vedere una delle risorse seguenti.

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Altri riferimenti:**
  * [Configurare altre proprietà di LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Configurare le dimensioni heap del server Hive](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Dimensionamento della memoria del join di mappa per LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Proprietà del motore di esecuzione di Tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Approfondimenti su LLAP Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
