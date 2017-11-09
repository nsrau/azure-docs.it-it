---
title: "Disponibilità elevata con Apache Kafka: Azure HDInsight| Microsoft Docs"
description: "Informazioni su come garantire disponibilità elevata con Apache Kafka in Azure HDInsight. Questo articolo illustra come ribilanciare le repliche di partizione in Kafka in modo che siano incluse in domini di errore diversi all'interno dell'area di Azure contenente HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: fe4cb62e4ceba46b44bdc62b05be4a6b1e2afa3d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight"></a>Disponibilità elevata dei dati con Apache Kafka (anteprima) in HDInsight

Questo articolo illustra come configurare le repliche di partizione per gli argomenti Kafka in modo da sfruttare la configurazione rack hardware sottostante, che garantisce la disponibilità dei dati archiviati in Apache Kafka in HDInsight.

## <a name="fault-and-update-domains-with-kafka"></a>Domini di errore e di aggiornamento con Kafka

Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Le macchine virtuali e i dischi gestiti che implementano i nodi in un cluster HDInsight sono distribuiti tra i domini di errore. Questa architettura limita il potenziale impatto dei guasti dell'hardware fisico.

Ogni area di Azure include un numero specifico di domini di errore. Per un elenco dei domini e il numero dei domini di errore in essi contenuti, vedere la documentazione relativa ai [set di disponibilità](../../virtual-machines/windows/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> Kafka non rileva i domini di errore. Quando si crea un argomento in Kafka, tutte le partizioni di replica potrebbero essere archiviate nello stesso dominio di errore. Per risolvere il problema, è disponibile uno [strumento per il ribilanciamento delle partizioni Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Quando ribilanciare le repliche di partizione

Per garantire la massima disponibilità dei dati Kafka, è consigliabile ribilanciare le repliche di partizione per l'argomento nei momenti seguenti:

* Quando viene creato un nuovo argomento o una nuova partizione

* Quando si aumentano le prestazioni di un cluster

## <a name="replication-factor"></a>Fattore di replica

> [!IMPORTANT]
> È consigliabile usare un'area di Azure contenente tre domini di errore e un fattore di replica di 3.

Se si deve usare un'area che contiene solo due domini di errore, usare un fattore di replica di 4 per distribuire uniformemente le repliche tra i due domini di errore.

Per un esempio della creazione di argomenti e dell'impostazione del fattore di replica, vedere il documento su come [iniziare a usare Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Come ribilanciare le repliche di partizione

Usare lo [strumento per il ribilanciamento delle partizioni Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) per ribilanciare gli argomenti selezionati. Questo strumento deve essere eseguito da una sessione SSH al nodo head del cluster Kafka.

Per altre informazioni sulla connessione a HDInsight con SSH, vedere il documento su come [usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Passaggi successivi

* [Scalabilità di Kafka in HDInsight](apache-kafka-scalability.md)
* [Mirroring con Kafka in HDInsight](apache-kafka-mirroring.md)