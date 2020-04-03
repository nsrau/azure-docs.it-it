---
title: Gateway deep dive and best practices for Apache Hive in Azure HDInsight
description: Informazioni su come esplorare le procedure consigliate per l'esecuzione di query Hive sul gateway di Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586977"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Gateway deep dive and best practices for Apache Hive in Azure HDInsight

Il gateway di Azure HDInsight (gateway) è il front-end HTTPS per i cluster HDInsight.The Azure HDInsight gateway (Gateway) is the HTTPS frontend for HDInsight clusters. Il gateway è responsabile di: autenticazione, risoluzione host, individuazione dei servizi e altre funzionalità utili necessarie per un sistema distribuito moderno. Le funzionalità fornite dal gateway comportano un sovraccarico per il quale questo documento descrive le procedure consigliate per spostarsi. Vengono inoltre illustrate le tecniche di risoluzione dei problemi dei gateway.

## <a name="the-hdinsight-gateway"></a>Gateway HDInsight

Il gateway HDInsight è l'unica parte di un cluster HDInsight accessibile pubblicamente tramite Internet.The HDInsight gateway is the only part of an HDInsight cluster that is publicly accessible over the internet. È possibile accedere al servizio Gateway senza passare `clustername-int.azurehdinsight.net` attraverso la rete Internet pubblica utilizzando l'endpoint del gateway interno. L'endpoint del gateway interno consente di stabilire connessioni al servizio gateway senza uscire dalla rete virtuale del cluster. Il gateway gestisce tutte le richieste inviate al cluster e inoltra tali richieste ai componenti e agli host del cluster corretti.

Il diagramma seguente fornisce un'illustrazione approssimativa di come il gateway fornisce un'astrazione davanti a tutte le diverse possibilità di risoluzione host all'interno di HDInsight.The following diagram provides a rough illustration of how the Gateway provides an abstraction front of all the different host resolution possibilis within HDInsight.

![Diagramma di risoluzione host](./media/gateway-best-practices/host-resolution-diagram.png "Diagramma di risoluzione host")

## <a name="motivation"></a>Motivazione

La motivazione per inserire un gateway davanti ai cluster HDInsight consiste nel fornire un'interfaccia per l'individuazione dei servizi e l'autenticazione utente. I meccanismi di autenticazione forniti dal gateway sono particolarmente rilevanti per i cluster abilitati per ESP.

Per l'individuazione del servizio, il vantaggio del gateway è che è possibile `clustername.azurehdinsight.net/hive2`accedere a ogni componente `host:port` all'interno del cluster come endpoint diverso nel sito Web Gateway ( ), anziché con una moltitudine di abbinamenti.

Per l'autenticazione, il gateway `username:password` consente agli utenti di eseguire l'autenticazione utilizzando una coppia di credenziali. Per i cluster abilitati per ESP, questa credenziale corrisponde al nome utente e alla password di dominio dell'utente. L'autenticazione ai cluster HDInsight tramite il gateway non richiede al client di acquisire un ticket kerberos. Poiché il `username:password` gateway accetta le credenziali e acquisisce il ticket Kerberos dell'utente per conto dell'utente, è possibile stabilire connessioni protette al gateway da qualsiasi host client, inclusi i client aggiunti a domini AA-DDS diversi rispetto al cluster (ESP).

## <a name="best-practices"></a>Procedure consigliate

Il gateway è un singolo servizio (con bilanciamento del carico tra due host) responsabile dell'inoltro e dell'autenticazione delle richieste. Il gateway può diventare un collo di bottiglia della velocità effettiva per le query Hive che superano una determinata dimensione. La riduzione delle prestazioni delle query può essere osservata quando vengono eseguite query **SELECT** di grandi dimensioni nel gateway tramite ODBC o JDBC. "Molto grande" indica query che costituiscono più di 5 GB di dati in righe o colonne. Questa query può includere un lungo elenco di righe e un numero di colonne ampio.

La riduzione delle prestazioni del gateway intorno a query di dimensioni elevate è dovuto al fatto che i dati devono essere trasferiti dall'archivio dati sottostante (ADLS Gen2) a: HDInsight Hive Server, il gateway e infine tramite i driver JDBC o ODBC all'host client.

Nel diagramma seguente vengono illustrati i passaggi necessari in una query SELECT.

![Diagramma dei risultati](./media/gateway-best-practices/result-retrieval-diagram.png "Diagramma dei risultati")

Apache Hive è un'astrazione relazionale sopra un file system compatibile con HDFS. Questa astrazione significa che le istruzioni **SELECT** in Hive corrispondono alle operazioni **READ** nel file system. Le operazioni **READ** vengono convertite nello schema appropriato prima di essere segnalate all'utente. La latenza di questo processo aumenta con le dimensioni dei dati e gli hop totali necessari per raggiungere l'utente finale.

Un comportamento simile può verificarsi durante l'esecuzione di istruzioni **CREATE** o **INSERT** di dati di grandi dimensioni, poiché questi comandi corrisponderanno alle operazioni **WRITE** nel file system sottostante. Valutare la possibilità di scrivere dati, ad esempio ORC non elaborati, nel file system o nel datalake anziché caricarli utilizzando **INSERT** o **LOAD**.

Nei cluster abilitati per Enterprise Security Pack, criteri Apache Ranger sufficientemente complessi possono causare un rallentamento del tempo di compilazione delle query, con conseguente timeout del gateway. Se si nota un timeout del gateway in un cluster ESP, è consigliabile ridurre o combinare il numero di criteri dei ranger.

## <a name="troubleshooting-techniques"></a>Tecniche di risoluzione dei problemi

Esistono più sedi per attenuare e comprendere i problemi di prestazioni soddisfatti come parte del comportamento precedente. Quando si verifica una riduzione delle prestazioni delle query sul gateway HDInsight, usare l'elenco di controllo seguente:Use the following checklist when experiencing query performance degradation over the HDInsight gateway:

* Utilizzare la clausola **LIMIT** durante l'esecuzione di query **SELECT** di grandi dimensioni. La clausola **LIMIT** ridurrà le righe totali segnalate all'host client. La clausola **LIMIT** influisce solo sulla generazione dei risultati e non modifica il piano di query. Per applicare la clausola **LIMIT** al `hive.limit.optimize.enable`piano di query, utilizzare la configurazione . **LIMIT** può essere combinato con un offset utilizzando il formato di argomento **LIMIT x,y**.

* Assegnare un nome alle colonne di interesse quando si eseguono query **SELECT** anziché **SELECT \* **. La selezione di un numero inferiore di colonne riduce la quantità di dati letti.

* Provare a eseguire la query di interesse tramite Apache Beeline. Se il recupero dei risultati tramite Apache Beeline richiede un periodo di tempo prolungato, prevedere ritardi durante il recupero degli stessi risultati tramite strumenti esterni.

* Testare una query Hive di base per assicurarsi che sia possibile stabilire una connessione al gateway HDInsight.Test a basic Hive query to ensure that a connection to the HDInsight Gateway can be established. Provare a eseguire una query di base da due o più strumenti esterni per assicurarsi che nessun singolo strumento stia incorrendo in problemi.

* Esaminare gli avvisi Apache Ambari per assicurarsi che i servizi HDInsight siano integri. Gli avvisi Ambari possono essere integrati con Monitoraggio di Azure per la creazione di report e l'analisi.

* Se si usa un Metastore Hive esterno, verificare che la DTU del database SQL di Azure per il metastore Hive non abbia raggiunto il limite. Se il DTU si sta avvicinando al limite, è necessario aumentare le dimensioni del database.

* Assicurarsi che gli strumenti di terze parti come PBI o Tableau utilizzino l'impaginazione per visualizzare tabelle o database. Per informazioni su questi strumenti sull'impaginazione, rivolgersi ai partner del supporto. Lo strumento principale utilizzato per l'impaginazione è il parametro JDBC. `fetchSize` Una dimensione di recupero ridotta può comportare una performance del gateway ridotta, ma una dimensione di recupero troppo grande può comportare un timeout del gateway. L'ottimizzazione delle dimensioni di recupero deve essere eseguita in base al carico di lavoro.

* Se la pipeline dei dati prevede la lettura di grandi quantità di dati dall'archiviazione sottostante del cluster HDInsight, è consigliabile usare uno strumento che si interfaccia direttamente con Archiviazione di Azure, ad esempio Azure Data FactoryIf your data pipeline involves reading large amount of data from the HDInsight cluster's underlying storage, consider using a tool that interfaces directly with Azure Storage such as Azure Data Factory

* Prendere in considerazione l'utilizzo di Apache Hive LLAP quando si eseguono carichi di lavoro interattivi, poiché LLAP può fornire un'esperienza più agevole per restituire rapidamente i risultati delle query

* Valutare la possibilità di aumentare il numero `hive.server2.thrift.max.worker.threads`di thread disponibili per il servizio Hive Metastore utilizzando . Questa impostazione è particolarmente rilevante quando un numero elevato di utenti simultanei invia query al cluster

* Ridurre il numero di tentativi utilizzati per raggiungere il gateway da qualsiasi strumento esterno. Se vengono utilizzati più tentativi, prendere in considerazione la possibilità di seguire un criterio di ripetizione dei tentativi di backup esponenzialeIf multiple retries are used, consider following an exponential back off retry policy

* Valutare la possibilità di `hive.exec.compress.output` abilitare la compressione Hive utilizzando le configurazioni e `hive.exec.compress.intermediate`.

## <a name="next-steps"></a>Passaggi successivi

* [Apache Beeline su HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Passaggi per la risoluzione dei problemi relativi al timeout del gateway HDInsightHDInsight Gateway Timeout Troubleshooting Steps](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Reti virtuali per HDInsightVirtual Networks for HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight con Percorso espresso](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)