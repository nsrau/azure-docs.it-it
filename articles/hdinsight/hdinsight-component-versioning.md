---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni su componenti e versioni di Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 15d7b11ed49880a81e52c48a93a72e1491a5b645
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394330"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Componenti e versioni di Apache disponibili per Azure HDInsight

Questo articolo illustra i componenti e le versioni dell'ambiente [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight e in Enterprise Security Package. Verrà anche descritto come controllare le versioni dei componenti Hadoop in HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Componenti di Apache disponibili con versioni diverse di HDInsight

Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Il 4 aprile 2017 la versione predefinita del cluster usata da Azure HDInsight è la 3.6.

Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente.

> [!NOTE]
> La versione predefinita per il servizio HDInsight può essere modificata senza preavviso. Nel caso di una dipendenza da una versione, specificare la versione di HDInsight quando si creano i cluster con .NET SDK con Azure PowerShell e l'interfaccia della riga di comando classica di Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (predefinito)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 in ESP Interactive Query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0.5           | 0,4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0: Spark 2.4 e Kafka 2.1 sono completamente supportati. Tuttavia, i tipi di cluster Spark 2,3 e Kafka 1,1 non vengono serviti. HDInsight 3.6: Spark 2.3 e Kafka 1.1 sono completamente supportati.  

## <a name="check-for-current-apache-component-version-information"></a>Controllare le informazioni sulle versioni correnti dei componenti di Apache

Le versioni dei componenti dell'ambiente Hadoop associate alle versioni dei cluster HDInsight potrebbero subire modifiche con gli aggiornamenti di HDInsight. Per controllare i componenti Hadoop e verificare le versioni in uso per un cluster, usare l'API REST Ambari. Il comando **GetComponentInformation** recupera informazioni sui componenti del servizio. Per altre informazioni, vedere la [documentazione di Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Scadenza e ritiro del supporto per le versioni di HDInsight

Per **scadenza del supporto** si intende che Microsoft non fornisce più supporto per la versione specifica di HDInsight. Inoltre, questa versione non è più disponibile tramite il portale di Azure per la creazione di cluster. È comunque possibile continuare a creare queste versioni con l'interfaccia della riga di comando di Azure o i vari SDK.

**Ritiro** significa che i cluster esistenti di una versione di HDInsight continuano a essere eseguiti così come sono. Non è possibile creare in alcun modo nuovi cluster di questa versione, neanche tramite l'interfaccia della riga di comando e gli SDK. Anche altre funzionalità del piano di controllo, come la scalabilità manuale e automatica, potrebbero non funzionare dopo il ritiro della versione. Il supporto non è disponibile per le versioni ritirate.

Le tabelle seguenti elencano le versioni di HDInsight. Se note, vengono indicate anche le date di scadenza e di ritiro del supporto.

### <a name="available-versions"></a>Versioni disponibili

La tabella seguente elenca le versioni di HDInsight disponibili nel portale di Azure e tramite altri metodi di distribuzione, come PowerShell e .NET SDK.

| Versione HDInsight | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 settembre 2018 | | |Sì |Sì |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 aprile 2017      | * 30 giugno 2021 |30 giugno 2021 |Sì |Sì |

* Si sta estendendo l'intervallo di tempo del supporto per determinati tipi di cluster HDInsight 3,6

| Tipo di cluster                    | Versione del Framework | Scadenza supporto corrente        | Nuova data di scadenza del supporto |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 31 dicembre 2020                      | 30 giugno 2021               |
| HDInsight 3,6 Spark             | 2.3               | 31 dicembre 2020                      | 30 giugno 2021               |
| HDInsight 3,6 Spark             | 2.2               | Ritirato il 30 giugno 2020          |                             |
| HDInsight 3,6 Spark             | 2.1               | Ritirato il 30 giugno 2020          |                             |
| HDInsight 3,6 Kafka             | 1.1               | 31 dicembre 2020                      | 30 giugno 2021               |
| HDInsight 3,6 Kafka             | 1.0               | Ritirato il 30 giugno 2020.         |                             |
| HDInsight 3,6 HBase             | 1.1               | 31 dicembre 2020                      | 30 giugno 2021               |
| HDInsight 3,6-query interattiva | 2.1               | 31 dicembre 2020                      | 30 giugno 2021               |
| HDInsight 3,6 Storm             | 1.1               | 31 dicembre 2020                      | 30 giugno 2021               |
| Servizi HDInsight 3,6 ML      | 9.3               | 31 dicembre 2020                      | 31 dicembre 2020                |

> [!NOTE]
> Dopo la scadenza del supporto per una versione del cluster, è possibile che non sia disponibile tramite il portale di Azure. In alcuni casi, le versioni del cluster continuano a essere disponibili usando il parametro **Version** nel comando [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) di Windows PowerShell e .NET SDK fino alla data di ritiro della versione.

### <a name="retired-versions"></a>Versioni ritirate

La tabella seguente elenca le versioni di HDInsight attualmente non disponibili nel portale di Azure.

| Versione HDInsight | Versione HDP | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 settembre 2016 |5 settembre 2017 |28 giugno 2018 |Sì |No |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 marzo 2016 |29 dicembre 2016 |9 gennaio 2018 |Sì |No |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 dicembre 2015 |27 giugno 2016 |31 luglio 2018 |Sì |No |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 dicembre 2015 |27 giugno 2016 |31 luglio 2017 |Sì |No |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012 R2 |18 febbraio 2015 |1° marzo 2016 |1° aprile 2017 |Sì |No |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 giugno 2014 |18 maggio 2015 |30 giugno 2016 |Sì |No |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 febbraio 2014 |17 settembre 2014 |30 giugno 2015 |Sì |No |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 ottobre 2013 |12 maggio 2014 |31 maggio 2015 |Sì |No |
| HDInsight 1.6 |HDP 1.1 | |28 ottobre 2013 |26 aprile 2014 |31 maggio 2015 |No |No |

> [!NOTE]
> I cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per HDInsight versione 2.1 e successive. Non sono disponibili per i cluster HDInsight versione 1.6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contratto di servizio per le versioni dei cluster HDInsight

Il contratto di servizio viene definito come _finestra di supporto_. Il termine finestra di supporto indica il periodo di tempo in cui una versione del cluster HDInsight è supportata dal servizio clienti e dal supporto tecnico Microsoft. Se la versione supera la _data di scadenza del supporto_, il cluster HDInsight ricade al di fuori della finestra di supporto. La data di scadenza del supporto per una versione X di HDInsight (quando è disponibile una nuova versione X+1) viene calcolata come l'ultima delle date seguenti:

- **Formula 1:** aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
- **Formula 2:** aggiungere 90 giorni alla data in cui nel portale di Azure viene resa disponibile la versione X+1 del cluster HDInsight.

La _data di ritiro_ è la data dopo la quale non è possibile creare la versione del cluster in HDInsight. A partire dal 31 luglio 2017 non è possibile ridimensionare un cluster HDInsight dopo la data di ritiro.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster

Per altre informazioni sugli SKU di macchine virtuali da selezionare per il cluster, vedere [Dettagli di configurazione del cluster Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
- [Note sulla versione di Hortonworks associate alle versioni di Azure HDInsight](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)
