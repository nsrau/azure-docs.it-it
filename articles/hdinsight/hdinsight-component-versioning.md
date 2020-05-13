---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni sui componenti e le versioni di Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 9ce6df8491bf4ce7cbb0e12a08816e198f665beb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124653"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Componenti e versioni di Apache disponibili per Azure HDInsight

Questo articolo illustra i componenti e le versioni dell'ambiente [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight e il Enterprise Security Package. Si apprenderà anche come controllare le versioni del componente Hadoop in HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Componenti Apache disponibili con diverse versioni di HDInsight

Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Il 4 aprile 2017, la versione predefinita del cluster usata da Azure HDInsight è 3,6.

Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente.

> [!NOTE]
> La versione predefinita per il servizio HDInsight può essere modificata senza preavviso. Se si ha una dipendenza della versione, specificare la versione HDInsight quando si creano i cluster con .NET SDK con Azure PowerShell e l'interfaccia della riga di comando di Azure classico.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (predefinito)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 in ESP Interactive query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (vedere la nota).   |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Il supporto per Kafka versione 0,10 è scaduto nel marzo 2019 a causa di considerazioni sulle prestazioni del sistema.

## <a name="check-for-current-apache-component-version-information"></a>Verificare le informazioni sulla versione del componente Apache corrente

Le versioni dei componenti dell'ambiente Hadoop associate alle versioni del cluster HDInsight possono cambiare con gli aggiornamenti di HDInsight. Per controllare i componenti Hadoop e verificare le versioni in uso per un cluster, usare l'API REST Ambari. Il comando **GetComponentInformation** recupera informazioni sui componenti del servizio. Per ulteriori informazioni, vedere la [documentazione di Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione per le versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Supportare la scadenza e il ritiro per le versioni di HDInsight

La **scadenza del supporto** indica che Microsoft non fornisce più il supporto per la versione di HDInsight specifica. E non è più disponibile tramite il portale di Azure per la creazione del cluster. Queste versioni possono comunque essere create usando l'interfaccia della riga di comando di Azure o i vari SDK.

Il **ritiro** indica che i cluster esistenti di una versione di HDInsight continuano a funzionare così come sono. I nuovi cluster di questa versione non possono essere creati con alcun mezzo, che include l'interfaccia della riga di comando e gli SDK. Anche altre funzionalità del piano di controllo, come la scalabilità manuale e la scalabilità automatica, potrebbero non funzionare dopo il ritiro della versione. Il supporto non è disponibile per le versioni ritirate.

Le tabelle seguenti elencano le versioni di HDInsight. Le date di scadenza e di ritiro del supporto vengono fornite anche quando sono note.

### <a name="available-versions"></a>Versioni disponibili

Questa tabella elenca le versioni di HDInsight disponibili nell'portale di Azure e tramite altri metodi di distribuzione come PowerShell e .NET SDK.

| Versione HDInsight | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 settembre 2018 | | |Sì |Sì |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 aprile 2017 | 31 dicembre 2020 |31 dicembre 2020 |Sì |Sì |

Il supporto Spark 2,1, 2,2 e Kafka 1,0 scade il 30 giugno 2020.

> [!NOTE]
> Dopo la scadenza del supporto per una versione, potrebbe non essere disponibile tramite il portale di Azure. Le versioni del cluster continuano a essere disponibili usando il parametro **Version** nel comando [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) di Windows PowerShell e .NET SDK fino alla data di ritiro della versione.

### <a name="retired-versions"></a>Versioni ritirate

Questa tabella elenca le versioni di HDInsight che non sono disponibili nella portale di Azure.

| Versione HDInsight | Versione HDP | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 settembre 2016 |5 settembre 2017 |28 giugno 2018 |Sì |No |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 marzo 2016 |29 dicembre 2016 |9 gennaio 2018 |Sì |No |
| HDInsight 3.3 |HDP 2.3 |R2 per Windows Server 2012 |2 dicembre 2015 |27 giugno 2016 |31 luglio 2018 |Sì |No |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 dicembre 2015 |27 giugno 2016 |31 luglio 2017 |Sì |No |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012 R2 |18 febbraio 2015 |1° marzo 2016 |1° aprile 2017 |Sì |No |
| HDInsight 3.1 |HDP 2.1 |R2 per Windows Server 2012 |24 giugno 2014 |18 maggio 2015 |30 giugno 2016 |Sì |No |
| HDInsight 3.0 |HDP 2.0 |R2 per Windows Server 2012 |11 febbraio 2014 |17 settembre 2014 |30 giugno 2015 |Sì |No |
| HDInsight 2.1 |HDP 1.3 |R2 per Windows Server 2012 |28 ottobre 2013 |12 maggio 2014 |31 maggio 2015 |Sì |No |
| HDInsight 1.6 |HDP 1.1 | |28 ottobre 2013 |26 aprile 2014 |31 maggio 2015 |No |No |

> [!NOTE]
> I cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per HDInsight versione 2.1 e successive. Non sono disponibili per i cluster HDInsight versione 1,6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contratto di servizio per le versioni del cluster HDInsight

Il contratto di servizio è definito come finestra di _supporto_. Una finestra di supporto indica il periodo di tempo in cui una versione di HDInsight è supportata dal servizio supporto tecnico clienti Microsoft. Se la versione ha superato la _Data di scadenza del supporto_, il cluster HDInsight si trova al di fuori della finestra di supporto. La scadenza del supporto per HDInsight versione X (dopo la disponibilità di una versione più recente di X + 1) è la seguente:

- **Formula 1:** Aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
- **Formula 2:** Aggiungere 90 giorni alla data in cui il cluster HDInsight versione X + 1 viene reso disponibile nella portale di Azure.

La _Data di ritiro_ è la data dopo la quale non è possibile creare la versione del cluster in HDInsight. A partire dal 31 luglio 2017, non è possibile ridimensionare un cluster HDInsight dopo la data di ritiro.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster

Per altre informazioni sugli SKU di macchine virtuali da selezionare per il cluster, vedere [i dettagli di configurazione del cluster HDInsight di Azure](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
- [Note sulla versione di Hortonworks associate alle versioni di Azure HDInsight](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)