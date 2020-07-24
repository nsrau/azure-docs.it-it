---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006535"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

## <a name="release-date-07132020"></a>Data di rilascio: 07/13/2020

Questa versione è valida per HDInsight 3.6 e 4.0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Supporto per Customer Lockbox per Microsoft Azure
Azure HDInsight supporta ora Azure Customer Lockbox. Fornisce un'interfaccia per consentire ai clienti di rivedere e approvare o rifiutare le richieste di accesso ai dati del cliente. Viene usato quando il tecnico Microsoft deve accedere ai dati dei clienti durante una richiesta di supporto. Per ulteriori informazioni, vedere [Customer Lockbox per Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Criteri dell'endpoint di servizio per l'archiviazione
I clienti possono ora usare i criteri di endpoint di servizio (SEP) nella subnet del cluster HDInsight. Altre informazioni sui [criteri dell'endpoint di servizio di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Deprecazione
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Deprecazione di Spark 2.1 e 2.2 in un cluster Spark HDInsight 3.6
A partire dal 1 2020 luglio, i clienti non possono creare nuovi cluster Spark con Spark 2,1 e 2,2 in HDInsight 3,6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.3 in HDInsight 3.6 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Deprecazione di Spark 2.3 nel cluster Spark HDInsight 4.0
A partire dal 1 2020 luglio, i clienti non possono creare nuovi cluster Spark con Spark 2,3 in HDInsight 4,0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.4 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Deprecazione di Kafka 1.1 nel cluster Kafka di HDInsight 4.0
A partire dal 1° luglio 2020, i clienti non potranno creare nuovi cluster Kafka con Kafka 1.1 in HDInsight 4.0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Kafka 2.1 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="behavior-changes"></a>Modifiche del comportamento
Non è necessario prestare attenzione alle modifiche del comportamento.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Possibilità di selezionare uno SKU Zookeeper diverso per i servizi Spark, Hadoop e ML
HDInsight attualmente non supporta la modifica dello SKU Zookeeper per i tipi di cluster Spark, Hadoop e ML Services. USA A2_v2 SKU/a2 per i nodi Zookeeper e i clienti non vengono addebitati. Nella prossima versione i clienti potranno modificare lo SKU Zookeeper per i servizi Spark, Hadoop e ML in base alle esigenze. Verranno addebitati i nodi Zookeeper con SKU diversi da A2_v2/a2. Lo SKU predefinito sarà ancora A2_V2/a2 e senza costi aggiuntivi.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Problema di connettore warehouse di hive fisso
Si è verificato un problema per l'usabilità del connettore del warehouse di hive nella versione precedente. Il problema è stato risolto. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Il notebook Zeppelin fisso tronca il problema degli zeri iniziali
Zeppelin ha troncato erroneamente gli zeri iniziali nell'output della tabella per il formato della stringa. Questo problema è stato risolto in questa versione.

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
