---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni sui componenti e le versioni di Apache Hadoop in Azure HDInsight.Learn the Apache Hadoop components and versions in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084771"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Componenti e versioni di Apache Hadoop disponibili in HDInsight.

Informazioni sui componenti e le versioni dell'ambiente [Apache Hadoop](https://hadoop.apache.org/) in Microsoft Azure HDInsight e sul pacchetto di sicurezza aziendale. Informazioni su come controllare le versioni di componenti Hadoop in HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componenti di Apache Hadoop disponibili con diverse versioni di HDInsight

Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Il 4 aprile 2017 la versione predefinita del cluster usata da Azure HDInsight è 3.6.On April 4, 2017, the default cluster version used by Azure HDInsight is 3.6.

Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente:

> [!NOTE]  
> La versione predefinita per il servizio HDInsight può essere modificata senza preavviso. Se si dispone di una dipendenza della versione, specificare la versione HDInsight quando si crea il cluster tramite l'SDK .NET con Azure PowerShell e l'interfaccia della riga di comando classica di Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (predefinito)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0 (cluster non ESP), 1.2.1 (cluster ESP)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (vedere la nota seguente) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> A causa di considerazioni sulle prestazioni di sistema, il supporto per Kafka versione 0.10 è scaduto nel mese di marzo 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Controllare le informazioni sulle versioni correnti dei componenti Hadoop

Le versioni dei componenti dell'ambiente Hadoop associate alle versioni del cluster HDInsight possono cambiare con gli aggiornamenti di HDInsight.The Hadoop environment component versions associated with HDInsight cluster versions can change with updates to HDInsight. Per controllare i componenti Hadoop e verificare le versioni in uso per un cluster, usare l'API REST Ambari. Il comando **GetComponentInformation** recupera informazioni sui componenti del servizio. Per informazioni dettagliate, vedere la [documentazione di Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Supporta la scadenza e il ritiro per le versioni HDInsightSupport expiration and retirement for HDInsight versions

**La scadenza del supporto** significa che Microsoft non fornirà più il supporto per la versione di HDInsight specificata. E non sarà più disponibile tramite il portale di Azure per la creazione di cluster. Tuttavia, queste versioni possono ancora essere create usando l'interfaccia della riga di comando di Azure o i vari SDK.

**Il ritiro** di una versione di HDInsight significa che i cluster esistenti continueranno a essere eseguiti così come sono. Tuttavia, i nuovi cluster di questa versione non possono essere creati con qualsiasi mezzo (inclusi CLI e SDK). Anche altre funzionalità del piano di controllo (ad esempio il ridimensionamento manuale e la scalabilità automatica) potrebbero non funzionare dopo il ritiro della versione. Il supporto non è disponibile per le versioni ritirate.

Le tabelle seguenti elencano le versioni di HDInsight. Se note, vengono indicate anche la data di scadenza del supporto e la data di ritiro.

### <a name="available-versions"></a>Versioni disponibili

Nella tabella seguente sono elencate le versioni di HDInsight disponibili nel portale di Azure e in altri metodi di distribuzione come PowerShell e .NET SDK.

| Versione HDInsight | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | Disponibilità elevata |  Disponibilità nel portale di AzureAvailability in the Azure portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 settembre 2018 | | |Sì |Sì |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 aprile 2017 | 31 dicembre 2020 |31 dicembre 2020 |Sì |Sì |

Spark 2.1, 2.2 & supporto di Kafka 1.0 scadrà il 30 giugno 2020.

> [!NOTE]  
> Se il supporto per una versione è scaduto, potrebbe non essere disponibile tramite il portale di Microsoft Azure. Tuttavia, le versioni dei cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) di Windows PowerShell e .NET SDK fino alla data di ritiro della versione.

### <a name="retired-versions"></a>Versioni ritirate

La tabella seguente elenca le versioni di HDInsight che non sono disponibili nel portale di Azure.The following table lists the versions of HDInsight that **aren't** available in the Azure portal.

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

## <a name="enterprise-security-package-for-hdinsight"></a>Pacchetto di sicurezza aziendale per HDInsight

Si tratta di un pacchetto opzionale che è possibile aggiungere al cluster HDInsight come parte del flusso di lavoro di creazione del cluster. Il Pacchetto di sicurezza aziendale supporta:

- Integrazione con Active Directory per l'autenticazione.

    In passato, sono stati creati cluster HDInsight con utente amministratore locale e utente SSH locale. L'utente amministratore locale poteva accedere a tutti i file, cartelle, tabelle e colonne.  Con Enterprise Security Package, è possibile abilitare il controllo degli accessi in base al ruolo integrando HDInsight con Active Directory. Che include Active Directory locale, Servizi di dominio Azure Active Directory. O Active Directory nella macchina virtuale IaaS. L'amministratore di dominio nel cluster può concedere agli utenti di utilizzare il proprio nome utente e password aziendali (dominio).

    Per altre informazioni, vedere:

    - [Introduzione alla sicurezza Apache Hadoop con i cluster HDInsight aggiunti al dominio](./domain-joined/hdinsight-security-overview.md)
    - [Pianificare cluster Apache Hadoop aggiunti a un dominio di Azure in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurare l'ambiente sandbox aggiunto al dominio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorizzazione per i dati

  - Integrazione con Apache Ranger per l'autorizzazione per code Yarn, Hive e Spark SQL.
  - È possibile impostare il controllo di accesso per file e cartelle.

    Per altre informazioni, vedere:

  - [Configurare criteri Apache Hive in HDInsight aggiunto al dominio](./domain-joined/apache-domain-joined-run-hive.md)

- Visualizzare i log di controllo per monitorare gli accessi e i criteri configurati.

### <a name="supported-cluster-types"></a>Tipi di cluster supportati

Attualmente solo i tipi di cluster seguenti supportano il Pacchetto di sicurezza aziendale:

- Hadoop (solo HDInsight 3.6)
- Spark
- Kafka
- hbase
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Supporto per Azure Data Lake Storage

Enterprise Security Package supporta l'uso di Azure Data Lake Storage come risorsa di archiviazione sia primaria che aggiuntiva.

### <a name="pricing-and-service-level-agreement-sla"></a>Accordo sui prezzi e sul livello di servizio (SLA)

Per informazioni su prezzi e contratto di servizio per il Pacchetto di sicurezza aziendale, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contratto di servizio per le versioni dei cluster HDInsight

Il contratto di servizio (SLA) è definito come _una finestra_di supporto . Finestra di supporto è il periodo di `Microsoft Customer Service and Support`tempo in cui una versione di HDInsight è supportata da . Se alla versione è stata superata la data di scadenza del _supporto,_ il cluster HDInsight si trova all'esterno della finestra di supporto. La scadenza del supporto per HDInsight versione X (dopo la disponibilità di una versione più recente di X-1) è la successiva:Support expiration for HDInsight version X (after a newer X-1 version is available) is the later:  

- Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
- Formula 2: aggiungere 90 giorni alla data di rilascio del cluster HDInsight versione X+1 nel portale di Azure.

La data di _ritiro_ è la data dopo la quale non è possibile creare la versione del cluster in HDInsight.The retirement date is the date after which the cluster version can't be created on HDInsight. A partire dal 31 luglio 2017, non è possibile ridimensionare un cluster HDInsight dopo la data di pensionamento.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster

Per altre informazioni su quali SKU di macchine virtuali selezionare per il cluster, vedere Dettagli di configurazione del cluster Azure HDInsight.For more information on which virtual machine SKUs to select for your cluster, see [Azure HDInsight cluster configuration details.](hdinsight-supported-node-configuration.md)

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
- [Note sulla versione di Hortonworks associate alle versioni di Azure HDInsightHortonworks release notes associated with Azure HDInsight versions](./hortonworks-release-notes.md)
