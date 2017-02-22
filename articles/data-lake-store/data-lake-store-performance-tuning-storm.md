---
title: Linee guida per l&quot;ottimizzazione delle prestazioni di Storm in Azure Data Lake Store | Documentazione Microsoft
description: Linee guida per l&quot;ottimizzazione delle prestazioni di Storm in Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ebf876f946eceddce9c8c990d8b28fcb969bec23
ms.openlocfilehash: 112226028c053cc91f9fb2bc0e5978f7cb2343ed


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Linee guida per l'ottimizzazione delle prestazioni di Storm in HDInsight e di Azure Data Lake Store

Per l'ottimizzazione delle prestazioni di una topologia Storm, è necessario prendere in considerazione alcuni fattori.  È importante comprendere le caratteristiche del lavoro svolto da spout e bolt (se il lavoro è I/O o usa intensivamente la memoria).

## <a name="prerequisites"></a>Prerequisiti 

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md) 
* **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster. 
* **Esecuzione di cluster Storm in Azure Data Lake Store**.  Per altre informazioni, vedere [Introduzione ad Apache Storm in HDInsight: Analisi in tempo reale per Hadoop](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview). 
* **Linee guida per l'ottimizzazione delle prestazioni in ADLS**.  Per i concetti generali relativi alle prestazioni, vedere [Linee guida per l'ottimizzazione delle prestazioni in Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

**Ottimizzazione del parallelismo della topologia**

Il miglioramento delle prestazioni può essere ottenuto aumentando la concorrenza di I/O o da e verso Azure Data Lake Store.  
Una topologia Storm consiste in un set di configurazioni che determinano il parallelismo:
* Numero di processi di lavoro: i ruoli di lavoro sono distribuiti uniformemente tra le macchine virtuali.
* Numero di istanze di spout executor
* Numero di istanze di bolt executor
* Numero di attività spout
* Numero di attività bolt

Ad esempio, in un cluster con 4 macchine virtuali e 4 processi di lavoro, 32 spout executor e 32 attività spout, 256 esecutori bolt e 512 attività bolt:

Ogni supervisor, che è un nodo di lavoro, avrà un singolo processo JVM di lavoro che gestirà 4 thread spout e 64 thread bolt. All'interno di ogni thread, le attività vengono eseguite in sequenza. Con la configurazione precedente, ogni thread spout avrà un'attività e ogni thread bolt avrà 2 attività.

Ecco i vari componenti coinvolti in Storm e il relativo impatto sul livello di parallelismo:
* Il nodo head (denominato Nimbus in Storm) viene usato per inviare e gestire i processi. Questi nodi non hanno alcun impatto sul livello di parallelismo.
* I nodi supervisor: in HDInsight di Azure corrispondono a una macchina virtuale di Azure con nodo di lavoro.
* Le attività di lavoro sono processi di Storm in esecuzione sulle macchine virtuali. Ogni attività di lavoro corrisponde a un'istanza JVM di Java. Storm distribuisce il numero di processi di lavoro che vengono specificati ai nodi di lavoro nel modo più uniforme possibile.
* Istanze di spout e bolt executor: ogni istanza dell'executor corrisponde a un thread in esecuzione all'interno dei ruoli di lavoro (JVM).
* Attività Storm: si tratta di attività logiche eseguite da ognuno di questi thread. Ciò non modifica il livello di parallelismo, pertanto è necessario valutare se è opportuno disporre di più attività per ogni executor.

## <a name="guidance"></a>Indicazioni

Quando si lavora con Azure Data Lake, è possibile ottenere prestazioni migliori procedendo come segue:
* Unire le piccole aggiunte in dimensioni maggiori (idealmente di dimensioni da 4 MB)
* Eseguire il maggior numero possibile di richieste simultanee. Dal momento che ogni thread bolt esegue il blocco delle letture, si desidera possedere un punto compreso nell'intervallo tra 8 e 12 thread per core, al fine di favorire un buon uso della scheda di interfaccia di rete e della CPU.  Una macchina virtuale più grande consente di eseguire un maggior numero di richieste simultanee.  

## <a name="example"></a>Esempio

Si supponga di avere un cluster con 8 nodi di lavoro e macchina virtuale di Azure D13v2.  Una macchina virtuale D13v2 dispone di 8 core. Pertanto, tra gli 8 nodi di lavoro, è disponibile un totale di 64 core.

Si supponga di eseguire 8 thread bolt per core. Disponendo di 64 core, si desidera un totale di 512 istanze dell'executor bolt (ad esempio thread). In questo caso, si inizia con una JVM per ogni macchina virtuale e si usa principalmente la concorrenza tra thread all'interno della JVM per ottenere una concorrenza. Ciò significa che sono necessari 8 attività di lavoro (uno per ogni macchina virtuale di Azure) e 512 executor bolt. Data questa configurazione, Storm tenterà di distribuire i ruoli di lavoro in modo uniforme tra i nodi di lavoro (ovvero sui nodi supervisor), assegnando a ciascun nodo di lavoro una JVM. A questo punto all'interno dei supervisor, Storm tenterà di distribuire gli executor in modo uniforme tra i supervisor, fornendo ogni supervisor (ad esempio JVM) 8 thread ciascuno.

## <a name="further-tuning"></a>Ottimizzazione aggiuntiva
Dopo aver creato la topologia di base, è possibile valutare se modificare uno dei parametri:
* **Number of JVMs per worker node** (Numero di JVM per ogni nodo di lavoro): se si dispone di una struttura di dati di grandi dimensioni (ad esempio, una tabella di ricerca) ospitata nella memoria, ogni JVM richiede una copia separata. Un numero minore di JVM consente di usare la struttura su molti thread. Per l'I/O del bolt, il numero di JVM non definisce una differenza significativa come il numero di thread aggiunti su tali JVM. Per semplicità, è consigliabile disporre di una JVM per ruolo lavoro. A seconda dell'attività del bolt o dall'elaborazione necessaria per l'applicazione, potrebbe essere necessario valutare se modificare questo numero.
* **Number of spout executors:** (Numero di executor spout) dal momento che questo esempio usa bolt per la scrittura in Azure Data Lake, il numero di spout non è strettamente correlato alle prestazioni del bolt. Tuttavia, a seconda della quantità di elaborazione o I/O in corso nello spout, sarà possibile ottimizzare gli spout al fine di ottenere prestazioni ottimali. In ultimo, si desidera accertarsi di disporre di un numero di spout sufficiente per mantenere i bolt occupati: ad esempio, la velocità di output degli spout deve corrispondere alla velocità effettiva dei bolt. La configurazione effettiva dipende dallo spout ed è esterna all'ambito di questo articolo.
* **Number of tasks** (Numero di attività):ogni bolt viene eseguito come thread singolo. Attività aggiuntive sui bolt non forniscono concorrenza aggiuntiva. Esse risultano utili solo se il processo di riconoscimento della tupla occupa una porzione importante del tempo di esecuzione del bolt. È consigliabile raggruppare numerose tuple in un'aggiunta più grande prima di inviare un riconoscimento dal bolt in modo tale che, nella che maggior parte dei casi, più attività non forniscono vantaggi aggiuntivi.
* **Local or shuffle grouping** (Raggruppamento locale o casuale): quando questa impostazione è abilitata, le tuple vengono inviate ai bolt all'interno dello stesso processo di lavoro. In questo modo si riduce il numero di comunicazioni tra processi e chiamate di rete. Tale operazione è consigliata per la maggior parte delle topologie.

Al fine di ottenere prestazioni ottimali, come approccio iniziale è consigliabile partire da scenari e test di base sui propri dati per modificare i parametri indicati in precedenza.

## <a name="tuning-the-spout"></a>Ottimizzazione dello spout

**Timeout tupla**

topology.message.timeout.secs: questa impostazione determina il tempo impiegato da un messaggio per completare/ricevere il riconoscimento prima che questo abbia esito negativo.

**Memoria massima per processo di lavoro**

Worker.childopts: questa impostazione consente di specificare i parametri della riga di comando aggiuntivi per ruoli di lavoro Java. L'impostazione più comunemente usata in questo caso è XmX, che determina la memoria massima allocata nell'heap di JVM.

**Spout massimo in sospeso**

Topology.max.spout.Pending: questa configurazione determina il numero di tuple che possono essere in esecuzione (non ancora riconosciute in tutti i nodi della topologia) per thread spout in qualsiasi momento.

Un buon calcolo da eseguire è la stima delle dimensioni di ciascuna tupla. Quindi capire di quanta memoria dispone un thread spout. La memoria totale allocata in un thread, divisa per questo valore, fornisce il limite superiore per il parametro spout massimo in sospeso.

## <a name="tuning-the-bolt"></a>Ottimizzazione del bolt
Durante la scrittura in ADLS, è consigliabile impostare un criterio di sincronizzazione delle dimensioni (buffer sul lato client) pari a 4 MiB.  La sincronizzazione orizzontale o l'allineamento viene quindi eseguito solo quando la dimensione del buffer è uguale al valore sopracitato.  Il driver ADLS nel ruolo di lavoro della macchina virtuale esegue automaticamente il buffering, a meno che l'utente non esegua in modo esplicito una sincronizzazione orizzontale.

Il bolt ADLS di Storm predefinito ha un parametro dei criteri di sincronizzazione delle dimensioni (fileBufferSize) che può essere usato per ottimizzare questo parametro.

Nelle topologie intensive I/O, è consigliabile che ogni thread bolt scriva in un proprio file e imposti un criterio di rotazione del file (fileRotationSize).  Quando il file raggiunge una determinata dimensione, il flusso viene allineato automaticamente e viene scritto su un nuovo file.  La dimensione del file consigliata per la rotazione è 1 GB.

**Quando eseguire il riconoscimento:** in Storm lo spout trattiene la tupla fino a quando non viene esplicitamente riconosciuta dal bolt.  Se la tupla è stata letta dal bolt ma non è ancora stata riconosciuta, non è garantito che il bolt sia ancora presente nel back-end di Azure Data Lake Store.  Dopo aver riconosciuto la tupla, la persistenza dello spout può essere garantita dal bolt, in modo da eliminare i dati di origine da qualsiasi origine vengano letti.  

**Per ottenere prestazioni ottimali in ADLS:** è consigliabile che il bolt esegua il buffering di 4 MB dei dati tupla e quindi scriva nel back-end ADLS come scrittura da 4 MB. Dopo aver scritto correttamente i dati nell'archivio (chiamando un allineamento orizzontale), il bolt può quindi riconoscere i dati nello spout. Il bolt di esempio qui fornito si comporta in questo modo. È accettabile anche trattenere un numero maggiore di tuple prima che venga eseguita la chiamata di allineamento orizzontale e le tuple vengano riconosciute. In questo modo, tuttavia, aumenta il numero di tuple in esecuzione che lo spout deve contenere e quindi aumenta la quantità di memoria necessaria per JVM.

Per altre cause non strettamente correlate alle prestazioni, è possibile che le applicazioni presentino un requisito di frequenza più elevata per il riconoscimento (in caso di dimensioni dei dati inferiori a 4 MB). Questo, tuttavia, può avere un impatto sulla velocità effettiva di I/O per il back-end di archiviazione, in modo che il cliente deve valutare attentamente questo compromesso in confronto alle prestazioni di I/O del bolt.

Se la velocità in ingresso delle tuple non è abbastanza elevata e, quindi, il riempimento del buffer da 4 MB richiede molto tempo, è possibile considerare diverse opzioni per risolvere il problema:
* Ridurre il numero di bolt, in modo da avere un numero minore di buffer da 4MB da riempire.
* Avere un criterio basato sul tempo o sul conteggio, in cui l'allineamento orizzontale viene attivato per ogni x allineamenti o per ogni y millisecondi e le tuple accumulate fino a quel momento vengono riconosciute.

Si noti che in questo caso la velocità effettiva è inferiore, ma con una bassa frequenza di eventi, comunque, la velocità effettiva massima non è l'obiettivo principale.  Le riduzioni sopracitate consentono di limitare il tempo end-to-end impiegato da una tupla per fluire attraverso l'archivio. Questo aspetto può essere importante se si desidera una pipeline in tempo reale anche in caso di bassa frequenza di eventi.  Si noti anche che, se la frequenza di tuple in ingresso è bassa, è possibile modificare anche il parametro topology.message.timeout_secs, in modo che non si verifichi il timeout delle tuple durante la loro elaborazione o il buffering.

## <a name="interpreting-storm-ui"></a>Interpretazione dell'interfaccia utente di Storm  
Quando la topologia è in esecuzione, è possibile monitorarla nell'interfaccia utente di Storm. Quando si esamina l'interfaccia utente, i parametri principali da esaminare sono i seguenti:

* **Total process execution latency** (Latenza totale di esecuzione del processo): tempo medio che la tupla impiega per essere emessa dallo spout, elaborata dal bolt e riconosciuta.

* **Total bolt process latency**(Latenza totale del processo dei bolt): tempo medio impiegato dalla tupla per ricevere un riconoscimento nel bolt.

* **Total bolt execute latency**(Latenza totale di esecuzione dei bolt): tempo medio speso dal bolt nel metodo Execute.

* **Number of failures**(Numero di errori): numero di tuple la cui elaborazione non è stata completata prima del timeout.

* **Capacity**(Capacità): misura del carico di lavoro del sistema. Se questo numero è 1, i bolt stanno lavorando al massimo della velocità. Se è minore di 1, aumentare il parallelismo. Se è maggiore di&1;, ridurre il parallelismo.

## <a name="common-troubleshooting-scenarios"></a>Scenari comuni per la risoluzione dei problemi.
* **Numero elevato di tuple in time out**: esaminare ogni nodo della topologia per individuare il collo di bottiglia. La causa più comune è l'incapacità dei bolt di tenersi al passo con gli spout, che blocca le tuple nei buffer interni mentre attendono di essere elaborate. Considerare l'aumento del valore di timeout o la riduzione dello spout massimo in sospeso.

* **Elevata latenza totale di esecuzione del processo, ma bassa latenza del processo dei bolt**: in questo caso, entrambe le tuple non vengono elaborate abbastanza rapidamente. Verificare che esista un numero sufficiente di acker. Un'altra possibilità è che rimangano in attesa nella coda per troppo tempo prima che i bolt inizino la loro elaborazione. Ridurre lo spout massimo in sospeso.

* **Elevata latenza di esecuzione dei bolt**: indica che il metodo Execute() del bolt impiega troppo tempo. Ottimizzare il codice o esaminare le dimensioni della scrittura/il comportamento di allineamento.

## <a name="limitation"></a>Limitazione 
Limitazione ADLS: se si raggiungono i limiti di larghezza di banda di ADLS, le attività inizieranno ad avere esiti negativi. Ciò può essere constatato verificando la presenza di errori di limitazione nei log delle attività.  È possibile ridurre il parallelismo aumentando la dimensione del contenitore.  Se occorre maggiore concorrenza per il processo, contattare Microsoft.   
Per verificare la presenza di limitazioni, è necessario abilitare la registrazione di debug sul lato client. A tale scopo, è possibile procedere come segue:

1. Modificare il seguente in Ambari > Storm > Config > Advanced storm-worker-log4j.  Modificare &lt;root level="info"&gt; in &lt;root level="debug"&gt;.  Riavviare tutti i nodi o servizi per rendere effettiva la nuova configurazione.
2. Monitorare i registri della topologia Storm sui nodi di lavoro (in /var/log/storm/worker-artifacts/&lt;NomeTopologia&gt;/&lt;porta&gt;/worker.log) per le eccezioni alle limitazioni ADLS.

## <a name="additional-tuning"></a>Ottimizzazione aggiuntiva
L'ottimizzazione aggiuntiva delle prestazioni di Storm è reperibile in questo [blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

## <a name="examples-to-run"></a>Esempi di esecuzione
Questo esempio è disponibile in [github](https://github.com/hdinsight/storm-performance-automation).



<!--HONumber=Jan17_HO2-->


