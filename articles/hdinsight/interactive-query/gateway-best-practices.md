---
title: Approfondimenti sul gateway e procedure consigliate per Apache Hive in Azure HDInsight
description: Informazioni su come esplorare le procedure consigliate per l'esecuzione di query hive sul gateway Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80586977"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Approfondimenti sul gateway e procedure consigliate per Apache Hive in Azure HDInsight

Il gateway di Azure HDInsight è il front-end HTTPS per i cluster HDInsight. Il gateway è responsabile di: autenticazione, risoluzione host, individuazione dei servizi e altre utili funzionalità necessarie per un sistema distribuito moderno. Le funzionalità fornite dal gateway comportano un sovraccarico per il quale questo documento descrive le procedure consigliate per spostarsi. Vengono inoltre illustrate le tecniche di risoluzione dei problemi del gateway.

## <a name="the-hdinsight-gateway"></a>Gateway HDInsight

Il gateway HDInsight è l'unica parte di un cluster HDInsight accessibile pubblicamente tramite Internet. È possibile accedere al servizio gateway senza passare attraverso la rete Internet pubblica usando l' `clustername-int.azurehdinsight.net` endpoint del gateway interno. L'endpoint del gateway interno consente di stabilire le connessioni al servizio gateway senza uscire dalla rete virtuale del cluster. Il gateway gestisce tutte le richieste inviate al cluster e le inoltra alle richieste per i componenti e gli host del cluster corretti.

Il diagramma seguente fornisce un'illustrazione approssimativa del modo in cui il gateway fornisce un'astrazione davanti a tutte le diverse possibilità di risoluzione host all'interno di HDInsight.

![Diagramma di risoluzione host](./media/gateway-best-practices/host-resolution-diagram.png "Diagramma di risoluzione host")

## <a name="motivation"></a>Motivazione

La motivazione per l'inserimento di un gateway davanti ai cluster HDInsight è fornire un'interfaccia per l'individuazione dei servizi e l'autenticazione utente. I meccanismi di autenticazione forniti dal gateway sono particolarmente rilevanti per i cluster abilitati per ESP.

Per l'individuazione dei servizi, il vantaggio del gateway è che è possibile accedere a ogni componente all'interno del cluster come endpoint diverso nel sito `clustername.azurehdinsight.net/hive2`Web del gateway (), a differenza di `host:port` numerose associazioni.

Per l'autenticazione, il gateway consente agli utenti di eseguire l' `username:password` autenticazione usando una coppia di credenziali. Per i cluster abilitati per ESP, questa credenziale corrisponde al nome utente e alla password del dominio dell'utente. Per l'autenticazione per i cluster HDInsight tramite il gateway non è necessario che il client acquisisca un ticket Kerberos. Poiché il gateway accetta `username:password` le credenziali e acquisisce il ticket Kerberos dell'utente per conto dell'utente, è possibile stabilire connessioni sicure al gateway da qualsiasi host client, inclusi i client aggiunti a diversi domini AA-DDS rispetto al cluster (ESP).

## <a name="best-practices"></a>Procedure consigliate

Il gateway è un singolo servizio (con bilanciamento del carico tra due host) responsabile dell'invio e dell'autenticazione delle richieste. Il gateway può diventare un collo di bottiglia della velocità effettiva per le query hive che superano una determinata dimensione. Il calo delle prestazioni delle query può essere osservato quando vengono eseguite query **Select** molto grandi sul gateway tramite ODBC o JDBC. "Molto grande" significa che le query che compongono più di 5 GB di dati in righe o colonne. Questa query può includere un lungo elenco di righe e un numero di colonne ampio.

Il calo delle prestazioni del gateway per le query di grandi dimensioni è dovuto al fatto che i dati devono essere trasferiti dall'archivio dati sottostante (ADLS Gen2) a: il server hive HDInsight, il gateway e infine tramite i driver JDBC o ODBC per l'host client.

Il diagramma seguente illustra i passaggi necessari in una query SELECT.

![Diagramma risultati](./media/gateway-best-practices/result-retrieval-diagram.png "Diagramma risultati")

Apache Hive è un'astrazione relazionale basata su un file system compatibile con HDFS. Questa astrazione significa che le istruzioni **Select** in hive corrispondono a operazioni di **lettura** nel file System. Le operazioni di **lettura** vengono convertite nello schema appropriato prima di essere segnalate all'utente. La latenza di questo processo aumenta con le dimensioni dei dati e gli hop totali necessari per raggiungere l'utente finale.

Potrebbe verificarsi un comportamento simile quando si eseguono istruzioni **create** o **Insert** di dati di grandi dimensioni, in quanto questi comandi corrispondono alle operazioni di **scrittura** nel file system sottostante. Prendere in considerazione la scrittura di dati, ad esempio un orco non elaborato, nel file System/datalake anziché caricarli usando **Insert** o **Load**.

Nei cluster Enterprise Security Pack abilitati, i criteri di Apache Ranger sufficientemente complessi possono causare un rallentamento nel tempo di compilazione delle query, il che può comportare un timeout del gateway. Se viene rilevato un timeout del gateway in un cluster ESP, valutare la possibilità di ridurre o combinare il numero di criteri Ranger.

## <a name="troubleshooting-techniques"></a>Tecniche di risoluzione dei problemi

Sono disponibili più sedi per attenuare e comprendere i problemi di prestazioni soddisfatti come parte del comportamento precedente. Usare l'elenco di controllo seguente quando si verifica un calo delle prestazioni delle query sul gateway HDInsight:

* Utilizzare la clausola **limit** durante l'esecuzione di query **SELECT** di grandi dimensioni. La clausola **limit** ridurrà il totale delle righe restituite all'host client. La clausola **limit** influisca solo sulla generazione dei risultati e non modifica il piano di query. Per applicare la clausola **limit** al piano di query, usare la configurazione `hive.limit.optimize.enable`. Il **limite** può essere combinato con un offset usando il formato di argomento **limite x, y**.

* Assegnare un nome alle colonne di interesse durante l'esecuzione di query **Select** anziché l'uso di **Select \* **. Se si seleziona un numero inferiore di colonne, la quantità di dati letti sarà ridotta.

* Provare a eseguire la query di interesse tramite Apache. Se il recupero dei risultati tramite Apache coeline richiede un periodo di tempo prolungato, prevedere ritardi durante il recupero degli stessi risultati tramite strumenti esterni.

* Testare una query hive di base per assicurarsi che sia possibile stabilire una connessione al gateway HDInsight. Provare a eseguire una query di base da due o più strumenti esterni per assicurarsi che non si verifichino problemi in nessun singolo strumento.

* Esaminare tutti gli avvisi di Apache Ambari per assicurarsi che i servizi HDInsight siano integri. Gli avvisi Ambari possono essere integrati con monitoraggio di Azure per la creazione di report e l'analisi.

* Se si usa un Metastore hive esterno, verificare che il database SQL di Azure DTU per il Metastore hive non abbia raggiunto il limite. Se il DTU si avvicina al limite, è necessario aumentare le dimensioni del database.

* Assicurarsi che gli strumenti di terze parti, ad esempio PBI o Tableau, utilizzino l'impaginazione per visualizzare tabelle o database. Per assistenza sulla paginazione, consultare i partner di supporto per questi strumenti. Lo strumento principale usato per la paginazione è `fetchSize` il parametro JDBC. Una dimensione di recupero ridotta può comportare un peggioramento delle prestazioni del gateway, ma una dimensione di recupero troppo grande può causare un timeout del gateway. L'ottimizzazione delle dimensioni del recupero deve essere eseguita in base a un carico di lavoro.

* Se la pipeline di dati prevede la lettura di grandi quantità di dati dall'archiviazione sottostante del cluster HDInsight, è consigliabile usare uno strumento che interfacce direttamente con archiviazione di Azure, ad esempio Azure Data Factory

* Si consiglia di usare Apache Hive LLAP durante l'esecuzione di carichi di lavoro interattivi, perché LLAP può offrire un'esperienza più uniforme per restituire rapidamente i risultati delle query

* Provare ad aumentare il numero di thread disponibili per il servizio Metastore hive `hive.server2.thrift.max.worker.threads`usando. Questa impostazione è particolarmente importante quando un numero elevato di utenti simultanei invia query al cluster

* Ridurre il numero di tentativi usati per raggiungere il gateway da qualsiasi strumento esterno. Se vengono usati più tentativi, valutare la possibilità di seguire i criteri di ripetizione dei tentativi di backup esponenziale

* Prendere in considerazione l'abilitazione di `hive.exec.compress.output` Compression `hive.exec.compress.intermediate`hive usando le configurazioni e.

## <a name="next-steps"></a>Passaggi successivi

* [Apache caeline su HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Passaggi per la risoluzione dei problemi di timeout del gateway HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Reti virtuali per HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight con Express Route](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)