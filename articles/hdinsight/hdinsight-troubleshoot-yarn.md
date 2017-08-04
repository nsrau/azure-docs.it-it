---
title: 'Risoluzione dei problemi relativi a YARN: HDInsight di Azure | Microsoft Docs'
description: Usare le domande frequenti su YARN per ottenere risposte alle domande comuni su YARN nella piattaforma di Azure HDInsight.
keywords: Azure HDInsight, YARN, domande frequenti, guida alla risoluzione dei problemi, domande comuni
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="yarn-troubleshooting"></a>Risoluzione dei problemi di YARN

Questo articolo descrive i problemi principali e le relative soluzioni per l'uso di payload YARN in Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Modalità di creazione di una nuova coda YARN in un cluster

### <a name="issue"></a>Problema:

Creare una nuova coda YARN con allocazione della capacità nel cluster HDInsight.  

### <a name="resolution-steps"></a>Procedura per la risoluzione: 

Usare la procedura seguente con Ambari per creare una nuova coda YARN e bilanciare l'allocazione delle capacità tra tutte le code. 

In questo esempio per due code esistenti, una predefinita e l'altra thriftsvr, è stata modificata la capacità dal 50% al 25% consentendo alla nuova coda, Spark, di avere una capacità del 50%.
| Coda | Capacità | Max. Capacità |
| --- | --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| Spark | 50% | 50% |
1. Fare clic sull'icona Visualizzazioni di Ambari, sul motivo di griglia, e scegliere **Yarn Queue Manager** (Gestione code YARN).

    ![Fare clic sull'icona Visualizzazioni di Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. Selezionare la coda **predefinita**.

    ![Selezionare la coda predefinita](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. Modificare la **Capacità** dal 50% al 25% per la coda **predefinita** e al 25% per la coda **thriftsvr**.

    ![Modificare Capacità al 25% per le code predefinita e thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. Fare clic su **Aggiungi coda** per creare una nuova coda.

    ![Fare clic su Aggiungi coda](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. Assegnare un nome alla nuova coda.

    ![Assegnare alla coda il nome Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. Lasciare i valori della **Capacità** a 50% e fare clic sul pulsante **Azioni**.

    ![Fare clic sul pulsante Azioni](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. Scegliere **Save and Refresh Queues** (Salva e aggiorna le code).

    ![Scegliere Save and Refresh Queues (Salva e aggiorna le code)](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Queste modifiche saranno visibili immediatamente nell'interfaccia utente dell'utilità di pianificazione YARN.

### <a name="further-reading"></a>Altre informazioni:

- [Utilità di pianificazione della capacità di YARN](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Modalità per scaricare i log di Yarn da un cluster

#### <a name="issue"></a>Problema:

Si devono scaricare l'applicazione master YARN e altri log del contenitore dal cluster HDInsight.  

#### <a name="resolution-steps"></a>Procedura per la risoluzione: 

1. Connettersi al cluster HDInsight con un client Secure Shell (SSH). Fare riferimento alla sezione Altre informazioni riportata di seguito.
1. Elencare tutti gli ID applicazione delle applicazioni YARN attualmente in esecuzione con il comando seguente:

```apache
yarn top
```
Gli ID sono elencati nella colonna `APPLICATIONID` di cui è necessario scaricare i log.

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

Scaricare i log dei contenitori YARN per tutti gli schemi dell'applicazione con il comando seguente:
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

Verrà creato il file di log denominato `amlogs.txt` nel formato di testo. 

Scaricare i log dei contenitori YARN solo per gli schemi dell'applicazione più recenti con il comando seguente:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

Verrà creato il file di log denominato `latestamlogs.txt` nel formato di testo. 

Scaricare i log dei contenitori YARN per i primi due schemi dell'applicazione con il comando seguente:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

Verrà creato il file di log denominato `first2amlogs.txt` nel formato di testo. 

Scaricare tutti i log dei contenitori di YARN con il comando seguente:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

Verrà creato il file di log denominato `logs.txt` nel formato di testo. 

Scaricare i log dei contenitori di YARN per un determinato contenitore con il comando seguente:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

Verrà creato il file di log denominato `containerlogs.txt` nel formato di testo.

#### <a name="further-readings"></a>Altre informazioni:

- [Connettersi al cluster HDInsight Cluster con SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn concepts and applications](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/) (Concetti e applicazioni di Apache Hadoop Yarn)









