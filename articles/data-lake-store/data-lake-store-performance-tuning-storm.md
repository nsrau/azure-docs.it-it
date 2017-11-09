---
title: Linee guida per l'ottimizzazione delle prestazioni di Storm in Azure Data Lake Store | Microsoft Docs
description: Linee guida per l'ottimizzazione delle prestazioni di Storm in Azure Data Lake Store
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
ms.openlocfilehash: 1dfa93643f45a96ded3fd022aa8b1c71d487acb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Linee guida per l'ottimizzazione delle prestazioni di Storm in HDInsight e di Azure Data Lake Store

È opportuno conoscere i fattori da tenere in considerazione quando si ottimizzano le prestazioni di una topologia di Storm in Azure. È importante, ad esempio, comprendere le caratteristiche del lavoro svolto da spout e bolt (se il lavoro è I/O o usa intensivamente la memoria). Questo articolo illustra una serie di linee guida per l'ottimizzazione delle prestazioni, inclusa la risoluzione dei problemi comuni.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Un cluster Azure HDInsight** con accesso a un account di Data Lake Store. Vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Assicurarsi di abilitare il Desktop remoto per il cluster.
* **Esecuzione di un cluster Storm in Data Lake Store**. Per altre informazioni, vedere [Storm in HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview).
* **Linee guida per l'ottimizzazione delle prestazioni in Data Lake Store**.  Per i concetti generali relativi alle prestazioni, vedere [Linee guida per l'ottimizzazione delle prestazioni in Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Ottimizzare il parallelismo della topologia

È possibile migliorare le prestazioni aumentando la concorrenza di I/O o da e verso Data Lake Store. Una topologia Storm consiste in un set di configurazioni che determinano il parallelismo:
* Numero di processi di lavoro (i ruoli di lavoro sono distribuiti uniformemente tra le macchine virtuali).
* Numero di istanze di executor di spout.
* Numero di istanze di executor di bolt.
* Numero di attività spout.
* Numero di attività bolt.

Ad esempio, in un cluster con 4 macchine virtuali e 4 processi di lavoro, 32 executor di spout e 32 attività spout e 256 executor di bolt e 512 attività bolt, tenere presente quanto segue:

Ogni supervisore, che è un ruolo di lavoro, ha un singolo processo di lavoro JVM (Java Virtual Machine). Questo processo JVM gestisce 4 thread di spout e 64 thread di bolt. In ogni thread le attività vengono eseguite in sequenza. Con la configurazione precedente, ogni thread di spout ha 1 attività e ogni thread di bolt ha 2 attività.

Ecco i vari componenti coinvolti in Storm e il relativo effetto sul livello di parallelismo:
* Il nodo head (denominato Nimbus in Storm) viene usato per inviare e gestire i processi. Questi nodi non hanno alcun impatto sul livello di parallelismo.
* I nodi supervisore. In HDInsight corrispondono a una macchina virtuale di Azure con nodo di lavoro.
* Le attività di lavoro sono processi di Storm in esecuzione sulle macchine virtuali. Ogni attività di lavoro corrisponde a un'istanza JVM. Storm distribuisce il numero di processi di lavoro che vengono specificati ai nodi di lavoro nel modo più uniforme possibile.
* Istanze di executor di spout e bolt. Ogni istanza dell'executor corrisponde a un thread in esecuzione nei ruoli di lavoro (JVM).
* Attività di Storm. Si tratta di attività logiche eseguite da ognuno di questi thread. Ciò non modifica il livello di parallelismo, pertanto è necessario valutare se è opportuno disporre di più attività per ogni executor.

### <a name="get-the-best-performance-from-data-lake-store"></a>Ottenere prestazioni ottimali da Data Lake Store

Quando si lavora con Data Lake Store, è possibile ottenere prestazioni ottimali procedendo come segue:
* Unire le piccole aggiunte in dimensioni maggiori (idealmente 4 MB).
* Eseguire il maggior numero possibile di richieste simultanee. Poiché ogni thread di bolt esegue il blocco delle letture, è opportuno avere un punto compreso nell'intervallo tra 8 e 12 thread per core. Questo favorisce un buon utilizzo della scheda di interfaccia di rete e della CPU. Una macchina virtuale più grande consente di eseguire un maggior numero di richieste simultanee.  

### <a name="example-topology"></a>Topologia di esempio

Si supponga di avere un cluster con 8 nodi di lavoro e una macchina virtuale di Azure D13v2. Questa macchina virtuale ha 8 core, quindi negli 8 nodi di lavoro il totale è di 64 core.

Si supponga di eseguire 8 thread bolt per core. Essendo i core 64, si vuole un totale di 512 istanze dell'executor (ovvero di thread) di bolt. In questo caso, si inizia con una JVM per ogni macchina virtuale e si usa principalmente la concorrenza tra thread nella JVM per ottenere una concorrenza. Ciò significa che sono necessari 8 attività di lavoro (uno per ogni macchina virtuale di Azure) e 512 executor bolt. Data questa configurazione, Storm prova a distribuire i ruoli di lavoro in modo uniforme tra i nodi di lavoro (chiamati anche nodi supervisore), assegnando a ogni nodo di lavoro 1 JVM. A questo punto nei supervisor Storm prova a distribuire gli executor in modo uniforme tra i supervisori, assegnando a ogni supervisore (ovvero a ogni JVM) 8 thread.

## <a name="tune-additional-parameters"></a>Ottimizzare i parametri aggiuntivi
Dopo aver creato la topologia di base, è possibile valutare se modificare uno dei parametri:
* **Number of JVMs per worker node** (Numero di JVM per nodo di lavoro). Se si ha una struttura dei dati di grandi dimensioni (ad esempio, tabella di ricerca) che si vuole ospitare in memoria, ogni JVM richiede una copia separata. In alternativa, è possibile usare la struttura dei dati in diversi thread se si hanno meno JVM. Per l'I/O del bolt, il numero di JVM non definisce una differenza significativa come il numero di thread aggiunti su tali JVM. Per semplicità, è consigliabile avere una sola JVM per ruolo di lavoro. A seconda delle operazioni eseguite dal bolt o dall'elaborazione dell'applicazione richiesta, potrebbe tuttavia essere necessario modificare questo numero.
* **Number of spout executors** (Numero di executor spout). Poiché l'esempio precedente usa bolt per la scrittura in Data Lake Store, il numero di spout non è strettamente correlato alle prestazioni del bolt. Tuttavia, a seconda della quantità di elaborazione o I/O in corso nello spout, è consigliabile ottimizzare gli spout per ottenere le massime prestazioni. Assicurarsi di avere sufficienti spout per poter mantenere occupati i bolt. Le velocità di output degli spout devono corrispondere alla velocità effettiva dei bolt. La configurazione effettiva varia a seconda dello spout.
* **Number of tasks** (Numero di attività). Ogni bolt viene eseguito come thread singolo. Attività aggiuntive sui bolt non forniscono concorrenza aggiuntiva. Esse risultano utili solo se il processo di riconoscimento della tupla occupa una porzione importante del tempo di esecuzione del bolt. È consigliabile raggruppare più tuple in un'aggiunta di maggiori dimensioni prima di inviare un acknowledgement dal bolt. Nella maggior parte dei casi quindi più attività non offrono vantaggi aggiuntivi.
* **Local or shuffle grouping** (Raggruppamento locale o casuale). Quando questa impostazione è abilitata, le tuple vengono inviate ai bolt nello stesso processo di lavoro. In questo modo si riduce il numero di comunicazioni tra processi e chiamate di rete. Tale operazione è consigliata per la maggior parte delle topologie.

Questo scenario di base è un buon punto di partenza. Usare i propri dati per un test per perfezionare i parametri precedenti e raggiungere così prestazioni ottimali.

## <a name="tune-the-spout"></a>Ottimizzare lo spout

È possibile modificare le impostazioni seguenti per ottimizzare lo spout.

- **Tuple timeout: topology.message.timeout.secs** (Timeout tuple: topology.message.timeout.secs). Questa impostazione determina il tempo impiegato da un messaggio per completare e ricevere l'acknowledgement prima che questo abbia esito negativo.

- **Max memory per worker process: worker.childopts** (Memoria massima per processo di lavoro: worker.childopts). Questa impostazione consente di specificare i parametri della riga di comando aggiuntivi per ruoli di lavoro Java. L'impostazione più comunemente usata in questo caso è XmX, che determina la memoria massima allocata nell'heap di JVM.

- **Max spout pending: topology.max.spout.pending** (Spout massimo in sospeso: topology.max.spout.pending). Questa configurazione determina il numero di tuple che possono essere in esecuzione (non ancora riconosciute in tutti i nodi della topologia) per thread di spout in qualsiasi momento.

 Un buon calcolo da eseguire è la stima delle dimensioni di ogni tupla. Quindi capire di quanta memoria dispone un thread spout. La memoria totale allocata in un thread, divisa per questo valore, fornisce il limite superiore per il parametro di spout massimo in sospeso.

## <a name="tune-the-bolt"></a>Ottimizzare il bolt
Durante la scrittura in Data Lake Store, impostare un criterio di sincronizzazione delle dimensioni (buffer sul lato client) pari a 4 MB. L'operazione di scaricamento o hsync() viene quindi eseguita solo quando la dimensione del buffer è uguale a questo valore. Il driver Data Lake Store nel ruolo di lavoro della macchina virtuale esegue automaticamente il buffering, a meno che non si esegua in modo esplicito hsync().

Il bolt Data Lake Store di Storm predefinito ha un parametro dei criteri di sincronizzazione delle dimensioni (fileBufferSize) che può essere usato per ottimizzare questo parametro.

Nelle topologie intensive I/O, è consigliabile che ogni thread bolt scriva in un proprio file e impostare un criterio di rotazione del file (fileRotationSize). Quando il file raggiunge una determinata dimensione, il flusso viene allineato automaticamente e viene scritto su un nuovo file. La dimensione del file consigliata per la rotazione è 1 GB.

### <a name="handle-tuple-data"></a>Gestire i dati delle tuple

In Storm lo spout trattiene la tupla fino a quando non viene esplicitamente riconosciuta dal bolt. Se una tupla è stata letta dal bolt, ma non è ancora stata riconosciuta, lo spout potrebbe non essere permanente nel back-end di Data Lake Store. Dopo l'acknowledgment di una tupla, la persistenza dello spout può essere garantita dal bolt e lo spout può quindi eliminare i dati di origine da qualsiasi origine vengano letti.  

Per prestazioni ottimali in Data Lake Store, 4 MB del buffer del bolt devono essere disponibili per i dati delle tuple. Scrivere quindi nel back-end di Data Lake Store con un'unica operazione di scrittura da 4 MB. Dopo aver scritto correttamente i dati nell'archivio, chiamando hflush(), il bolt può riconoscere i dati nello spout. Il bolt di esempio qui fornito si comporta in questo modo. È accettabile anche trattenere un numero maggiore di tuple prima che venga chiamato hflush() e le tuple vengano riconosciute. In questo modo, tuttavia, aumenta il numero di tuple in esecuzione che lo spout deve contenere e quindi aumenta la quantità di memoria necessaria per JVM.

> [!NOTE]
Per altre cause non strettamente correlate alle prestazioni, è possibile che le applicazioni presentino un requisito di frequenza più elevata per l'acknowledgment delle tuple (in caso di dimensioni dei dati inferiori a 4 MB). Questo potrebbe tuttavia influire sulla velocità effettiva di I/O nel back-end di archiviazione. Considerare con attenzione questo compromesso in relazione alle prestazioni di I/O del bolt.

Se la velocità in ingresso delle tuple non è elevata e quindi il riempimento del buffer da 4 MB richiede molto tempo, considerare la possibilità di risolvere il problema in questo modo:
* Riducendo il numero di bolt, per poter avere un numero minore di buffer da riempire.
* Con un criterio basato sul tempo o sul conteggio, in cui hflush() viene attivato ogni x scaricamenti oppure ogni y millisecondi e le tuple accumulate fino a quel momento vengono riconosciute.

Si noti che in questo caso la velocità effettiva è inferiore, ma con una bassa frequenza di eventi, comunque, la velocità effettiva massima non è l'obiettivo principale. Queste mitigazioni consentono di ridurre il tempo totale impiegato da una tupla per il flusso fino alla risorsa di archiviazione. Questo potrebbe essere importante se è necessaria una pipeline in tempo reale anche con una frequenza di eventi bassa. Si noti anche che, se la frequenza di tuple in ingresso è bassa, è consigliabile modificare il parametro topology.message.timeout_secs, in modo che non si verifichi il timeout delle tuple durante l'elaborazione o il buffering.

## <a name="monitor-your-topology-in-storm"></a>Monitorare la topologia in Storm  
Quando la topologia è in esecuzione, è possibile monitorarla nell'interfaccia utente di Storm. Ecco i parametri principali da esaminare:

* **Total process execution latency** (Latenza totale di esecuzione del processo). Tempo medio che la tupla impiega per essere emessa dallo spout, elaborata dal bolt e riconosciuta.

* **Total bolt process latency** (Latenza totale del processo dei bolt). Tempo medio impiegato dalla tupla per ricevere un riconoscimento nel bolt.

* **Total bolt execute latency** (Latenza totale di esecuzione dei bolt). Tempo medio speso dal bolt nel metodo Execute.

* **Number of failures** (Numero di errori). Numero di tuple la cui elaborazione non è stata completata prima del timeout.

* **Capacity** (Capacità). Misura del carico di lavoro del sistema. Se questo numero è 1, i bolt stanno lavorando al massimo della velocità. Se è minore di 1, aumentare il parallelismo. Se è maggiore di 1, ridurre il parallelismo.

## <a name="troubleshoot-common-problems"></a>Risolvere i problemi comuni
Ecco alcuni scenari comuni per la risoluzione dei problemi.
* **È in corso il timeout di diverse tuple.** Esaminare ogni nodo della topologia per trovare il collo di bottiglia. La causa più comune è che i bolt non riescono a rimanere aggiornati con gli spout, di conseguenza le tuple bloccano i buffer interni mentre sono in attesa di essere elaborate. Considerare l'aumento del valore di timeout o la riduzione dello spout massimo in sospeso.

* **La latenza di esecuzione dei processi totale è elevata, ma la latenza dei processi bolt è bassa.** In questo caso è possibile che le tuple non siano riconosciute in modo sufficientemente veloce. Verificare che sia presente un numero sufficiente di elementi di acknowledgment. Un'altra possibilità è che rimangano in attesa nella coda per troppo tempo prima che i bolt inizino la loro elaborazione. Ridurre lo spout massimo in sospeso.

* **La latenza di esecuzione dei bolt è elevata.** Indica che il metodo execute() del bolt impiega troppo tempo. Ottimizzare il codice o esaminare le dimensioni della scrittura e il comportamento di scaricamento.

### <a name="data-lake-store-throttling"></a>Limitazione di Data Lake Store
Se si raggiungono i limiti di larghezza di banda di Data Lake Store, è possibile che le attività abbiano esito negativo. Cercare errori di limitazione nei log delle attività. È possibile ridurre il parallelismo aumentando la dimensione del contenitore.    

Per verificare la presenza di limitazioni, abilitare la registrazione di debug sul lato client:

1. In **Ambari** > **Storm** > **Config** (Configurazione)  > **Advanced storm-worker-log4j** (Storm-worker-log4j avanzato), sostituire **&lt;root level="info"&gt;** con **&lt;root level="debug"&gt;**. Riavviare tutti i nodi o servizi per rendere effettiva la nuova configurazione.
2. Monitorare i log della topologia Storm sui nodi di lavoro (in /var/log/storm/worker-artifacts/&lt;NomeTopologia&gt;/&lt;porta&gt;/worker.log) per le eccezioni alle limitazioni di Data Lake Store.

## <a name="next-steps"></a>Passaggi successivi
L'ottimizzazione aggiuntiva delle prestazioni di Storm è reperibile in questo [blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Per un esempio aggiuntivo da eseguire, vedere [questo in GitHub](https://github.com/hdinsight/storm-performance-automation).
