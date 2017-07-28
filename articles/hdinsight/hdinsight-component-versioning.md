---
title: Componenti e versioni di Hadoop - Azure HDInsight | Microsoft Docs
description: Informazioni sui componenti e sulle versioni di Hadoop in HDInsight e sui livelli di servizio disponibili in questa distribuzione cloud di HortonWorks Data Platform.
keywords: versioni hadoop, componenti ecosistema hadoop, componenti hadoop, come controllare versione hadoop
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 54254203b79c6d69a2febc5987b0a24077a84939
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

 

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Componenti e versioni di Hadoop disponibili in HDInsight

Informazioni su componenti e versioni dell'ecosistema Hadoop in Azure HDInsight e sui livelli di servizio Standard e Premium. Informazioni su come controllare le versioni di componenti Hadoop in HDInsight. 

Ogni versione di HDInsight è una distribuzione cloud di una versione di HortonWorks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componenti di Hadoop disponibili con diverse versioni di HDInsight
Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti in tale distribuzione. La versione cluster predefinita usata da Azure HDInsight è attualmente la 3.5 e, a partire dal 17 febbraio 2017, basata su HDP 2.5.

Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente: 

> [!NOTE]
> La versione predefinita del servizio può cambiare senza preavviso. Se si dispone di una dipendenza della versione, è consigliabile specificare la versione quando si crea il cluster tramite l'SDK .NET, Azure PowerShell e l'interfaccia della riga di comando di Azure. 
>
>


| Componente | HDInsight versione 3.6 | HDInsight versione 3.5 (predefinita) | HDInsight versione 3.4 | HDInsight versione 3.3 | HDInsight versione 3.2 | HDInsight versione 3.1 | HDInsight versione 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop e YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive e HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez-Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (solo Linux) |1.6.2 + 2.0 (solo Linux) |1.6.0 (solo Linux) |1.5.2 (solo Linux/build sperimentale) |1.3.1 (solo Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|


## <a name="how-to-check-current-hadoop-component-version-information"></a>Procedura per controllare le informazioni sulle versioni correnti dei componenti Hadoop

Le versioni dei componenti dell'ecosistema Hadoop associate alle versioni cluster HDInsight potrebbero subire modifiche con gli aggiornamenti a HDInsight. Per controllare i componenti Hadoop e verificare le versioni in uso per un cluster, usare l'API REST Ambari. Il comando **GetComponentInformation** recupera informazioni sui componenti del servizio. Per informazioni dettagliate, vedere la [documentazione di Ambari][ambari-docs].

Solo per i cluster basati su Windows: un altro modo per determinare le versioni dei componenti è accedere a un cluster usando Desktop remoto ed esaminare direttamente il contenuto della directory "C:\apps\dist\".

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Ritiro di HDInsight su Windows](#hdi-version-33-nearing-retirement-date). 

### <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight
La tabella seguente include l'elenco delle versioni di HDInsight attualmente disponibili, le corrispondenti versioni HDP (Hortonworks Data Platform) usate e le date di rilascio. Se note, vengono indicate anche la data di scadenza del supporto e la data di ritiro. Tenere presente le seguenti informazioni sulle versioni:

* i cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per HDInsight 2.1 e versioni successive. Non sono disponibili per i cluster HDInsight 1.6.
* Dopo che il supporto per una particolare versione è scaduto, potrebbe non essere disponibile tramite il portale di Azure. Nella tabella seguente sono indicate le versioni disponibili sul portale di Azure classico. Le versioni dei cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) di Windows PowerShell e .NET SDK fino alla data di ritiro.

| Versione HDInsight | Versione HDP | Sistema operativo della macchina virtuale | Disponibilità elevata | Data di rilascio | Disponibile nel portale di Azure | Data di scadenza del supporto | Data di ritiro |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 |HDP 2.6 |Ubuntu 16 |Sì |04/06/2017 |Sì | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |Sì |9/30/2016 |Sì |07/05/2017 |05/31/2018 |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sì |29/03/2016 |Sì |12/29/2016 |1/9/2018 |
| HDI 3.3 |HDP 2.3 |Windows Server 2012 R2 |Sì |02/12/2015 |Sì |27/06/2016 |31/07/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |Sì |02/12/2015 |Sì |27/06/2016 |31/07/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012R2 |Sì |2/18/2015 |No |01/03/2016 |01/04/2017 |
| HDI 3.1 |HDP 2.1 |Windows Server 2012 R2 |Sì |6/24/2014 |No |18/05/2015 |30/06/2016 |
| HDI 3.0 |HDP 2.0 |Windows Server 2012 R2 |Sì |11/02/2014 |No |17/09/2014 |30/06/2015 |
| HDI 2.1 |HDP 1.3 |Windows Server 2012 R2 |Sì |28/10/2013 |No |12/05/2014 |31/05/2015 |
| HDI 1.6 |HDP 1.1 | |No |28/10/2013 |No |26/04/2014 |31/05/2015 |

## <a name="hdinsight-windows-retirement"></a>Ritiro di HDInsight in Windows

Microsoft Azure HDInsight (HDI) versione 3.3 è stata l'ultima versione di HDInsight in Windows e il 31 luglio 2018 verrà ritirato. Se si dispone di cluster HDI in esecuzione in Windows (3.3 o versione precedente), è necessario migrare HDInsight in Linux (HDI 3.5 o versione successiva) prima del 31 luglio 2018 per mantenere la possibilità di creare o ridimensionare i cluster HDI. Il supporto per HDI 3.3 su Windows è scaduto il 27 giugno 2016. 
 
A partire da HDInsight versione 3.4, Microsoft ha rilasciato HDInsight solo per il sistema operativo Linux. Alcuni dei componenti all'interno di HDInsight sono quindi disponibili solo per Linux: applicazioni Apache Ranger, Kafka, Interactive Hive, Spark, HDInsight e Azure Data Lake Store come file system primario. Le versioni future di HDInsight saranno disponibili solo per il sistema operativo Linux. Per il sistema operativo Windows non saranno più rilasciate versioni di HDInsight.

### <a name="faqs"></a>Domande frequenti

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Qual è la sequenza temporale per il ritiro di HDInsight su Windows?
31 luglio 2018 è la data di ritiro di HDInsight su Windows. Se la data di ritiro pianificata nella propria area geografica è diversa, si riceverà una notifica separata. 

### <a name="what-will-be-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Quale sarà l'impatto che il ritiro di HDInsight su Windows avrà sui clienti esistenti?
* Dopo la data del ritiro non sarà possibile creare un nuovo cluster HDI su Windows.
* Dopo la data del ritiro non sarà possibile ridimensionare un cluster HDInsight su Windows esistente. 
 * Le versioni future di HDInsight saranno disponibili solo per il sistema operativo Linux. Per il sistema operativo Windows non saranno più rilasciate versioni di HDInsight.
* Si noti che il supporto per HDInsight 3.3 è scaduto il 27 giugno 2016. Non sono pertanto disponibili supporto e correzioni di bug per HDInsight 3.3 o versioni precedenti. 
 
### <a name="which-versions-of-hdinsight-on-windows-are-impacted"></a>Quali versioni di HDInsight su Windows sono interessate?
Azure HDInsight versione 3.3 è stata l'ultima versione di HDInsight per Windows. Tutti i cluster HDInsight in esecuzione su Windows (3.3 o versione precedente) devono essere migrati a HDInsight su Linux (3.5 o versione successiva) prima della data di ritiro per mantenere la possibilità di ridimensionare i cluster HDI esistenti o crearne di nuovi. 

### <a name="what-do-i-need-to-do"></a>Cosa occorre fare?
Fare riferimento a [questo](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) documento per migrare prima del 31 luglio 2018 i cluster HDInsight per Windows verso un cluster HDInsight per Linux supportato. Per informazioni sulle versioni HDI supportate, visitare [qui](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Dove si può trovare il tipo di sistema operativo del cluster?
Nel portale di Azure andare alla pagina di panoramica del cluster HDInsight. In questa pagina è disponibile il tipo di cluster in Essentials, che indicherà il tipo di sistema operativo del cluster. 

### <a name="what-will-be-the-impact-to-my-hdinsight-windows-cluster-if-i-cant-migrate-to-a-linux-based-cluster-by-jul-31st-2018"></a>Quale sarà l'impatto sul cluster HDInsight su Windows in caso di impossibilità di migrarlo a un cluster basato su Linux entro il 31 luglio 2018?
Il cluster HDInsight su Windows verrà eseguito "così come è". Non sarà invece possibile creare un nuovo cluster HDInsight su Windows o ridimensionarne uno esistente. 

### <a name="my-cluster-has-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Il cluster include dipendenze .NET. Come si risolve questa dipendenza su Linux?
[Mono](http://www.mono-project.com/), un'implementazione open source di .NET, è disponibile nei cluster HDInsight su Linux. Per altre informazioni dettagliate, fare riferimento a [questo](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) documento. 

### <a name="i-am-a-new-customer-trying-to-create-an-hdinsight-windows-based-cluster-however-i-dont-see-the-option-in-the-azure-portal-or-i-am-unable-to-create-this-from-powershell-or-sdk-how-can-i-create-an-hdi-windows-based-cluster"></a>Un nuovo cliente tenta di creare un cluster HDInsight basato su Windows, ma l'opzione nel portale di Azure non è presente o non è possibile creare il cluster desiderato con PowerShell o l'SDK. Come è possibile creare un cluster HDI basato su Windows?
A partire dal 3 luglio 2017 solo i clienti di HDInsight per Windows esistenti possono creare nuovi cluster HDI basati su Windows (fino alla data di ritiro). È consigliabile creare un cluster HDI basato su Linux. 

### <a name="is-there-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Il passaggio da HDInsight su Windows a HDInsight su Linux influisce sui prezzi?
No, il piano tariffario è lo stesso per HDInsight su entrambi i sistemi operativi. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-hdinsight-only-on-the-linux-os"></a>Quali sono i vantaggi per i clienti che passano a HDInsight solo sul sistema operativo Linux?
* Tempi di realizzazione più veloci per le tecnologie Big Data open source tramite il servizio HDInsight
* Sono disponibili una community e un ecosistema di grandi dimensioni per il supporto
* La community del software open source per Hadoop offre uno sviluppo più attivo e tecnologie Big Data innovative

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-that-available-in-hdinsight-on-windows"></a>HDInsight su Linux fornisce funzionalità aggiuntive oltre a quelle disponibili in HDInsight su Windows?
A partire da HDInsight versione 3.4, MSFT ha rilasciato HDInsight solo per il sistema operativo Linux. Alcuni dei componenti all'interno di HDInsight sono quindi disponibili solo per Linux: applicazioni Apache Ranger, Kafka, Interactive Hive, Spark, HDInsight e Azure Data Lake Store come file system primario. 

## <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>Contratto di servizio per le versioni dei cluster HDInsight
Il Contratto di servizio viene definito come **finestra di supporto**. Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Supporto tecnico Microsoft. Un cluster HDInsight non è compreso nella finestra di supporto se la **data di scadenza del supporto** della versione ha superato la data corrente. Nella tabella precedente è disponibile un elenco di versioni di cluster HDInsight supportate. La data di scadenza del supporto per una determinata versione di HDInsight X (una volta che sarà disponibile una versione X+1 più recente) viene calcolata come l'ultima di:  

* Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
* Formula 2: aggiungere 90 giorni alla data in cui il cluster HDInsight versione X+1 (la versione successiva a X) diventa disponibile nel portale.

La **Data di ritiro** è la data dopo la quale non è possibile creare la versione del cluster su HDInsight. A partire dal 31 luglio 2017 non è possibile ridimensionare un cluster dopo la data di ritiro. 

> [!NOTE]
> I cluster HDInsight basati su Windows (incluse le versioni 2.1, 3.0, 3.1, 3.2 e 3.3) eseguono il sistema operativo guest di Azure Family 4, che usa la versione a 64 bit di Windows Server 2012 R2 e supporta .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Note sulla versione di Hortonworks associate alle versioni di HDInsight
* Il cluster HDInsight versione 3.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). 
* Il cluster HDInsight versione 3.5 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Si tratta del cluster Hadoop **predefinito** creato nel portale.
* Il cluster HDInsight versione 3.4 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). 
* Il cluster HDInsight versione 3.3 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Le note sulla versione di Apache Storm sono disponibili [qui](https://storm.apache.org/2015/11/05/storm0100-released.html).
  * Le note sulla versione di Apache Hive sono disponibili [qui](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).
* Il cluster HDInsight versione 3.2 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.2][hdp-2-2].  

  * Note sulla versione per componenti specifici di Apache: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. I cluster HDInsight 3.1 creati prima del 7/11/2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Il cluster HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Il cluster HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Il cluster HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium

Azure HDInsight presenta le offerte cloud per i Big Data in due categorie: **Standard** e **Premium**. La tabella nella sezione seguente elenca le funzionalità disponibili **solo come parte della categoria Premium**. Le funzionalità non indicate esplicitamente nella tabella di seguito sono disponibili come parte della categoria Standard.

> [!NOTE]
> Attualmente l’offerta HDInsight Premium è disponibile in anteprima e solo per i cluster Linux.
>
>

| Funzionalità HDInsight Premium | Description |
| --- | --- |
| Cluster HDInsight aggiunti al dominio |Aggiungere i cluster HDInsight ai domini di Azure Active Directory (AAD) per ottenere una sicurezza di livello aziendale. È ora possibile configurare un elenco di dipendenti dell'azienda autorizzati a eseguire l'autenticazione tramite Azure Active Directory per accedere al cluster HDInsight. L'amministratore dell'organizzazione può anche configurare il controllo di accesso in base al ruolo per la sicurezza di Hive usando [Apache Ranger](http://hortonworks.com/apache/ranger/), limitando così l'accesso ai dati solo ai ruoli interessati. Infine, l'amministratore può controllare l'accesso ai dati da parte dei dipendenti e le eventuali modifiche apportate ai criteri di controllo degli accessi, ottenendo in questo modo un elevato livello di governance delle risorse aziendali. Per altre informazioni, vedere [Configure domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configurare i cluster HDInsight aggiunti al dominio). |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>Tipi di cluster supportati per HDInsight Premium
Nella tabella seguente sono elencati il tipo di cluster HDInsight e la matrice di supporto Premium.

| Tipo di cluster | Standard | Premium (anteprima) |
| --- | --- | --- |
| Hadoop |Sì |Sì (solo HDInsight 3.5 e 3.6) |
| Spark |Sì |No |
| HBase |Sì |No |
| Storm |Sì |No |
| R Server  |Sì |No |
| Interactive Hive (anteprima) |Sì |No |
| Kafka (anteprima)|Sì|No| 

Questa tabella viene aggiornata quando vengono inclusi altri tipi di cluster in HDInsight Premium.

### <a name="features-not-supported-for-hdinsight-premium"></a>Funzionalità non supportate per HDInsight Premium

Le seguenti funzionalità non sono attualmente supportate per i cluster HDInsight Premium.

* **Azure Data Lake Store non è supportato come risorsa di archiviazione principale**. È comunque possibile usare Azure Data Lake Store come risorsa di archiviazione aggiuntiva con i cluster HDInsight Premium.

### <a name="pricing-and-sla"></a>Prezzi e contratto di servizio
Per informazioni su prezzi e contratto di servizio per HDInsight Premium, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster
La tabella seguente elenca le dimensioni di VM predefinite per i cluster HDInsight:

> [!IMPORTANT]
> Se si prevedono più di 32 nodi di lavoro in un cluster, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM.
>
>

* Tutte le aree supportate tranne Brasile meridionale e Giappone occidentale:

  | Tipo di cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Head: dimensioni VM predefinite |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Head: dimensioni VM consigliate |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Ruolo di lavoro: dimensioni VM predefinite |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Ruolo di lavoro: dimensioni VM consigliate |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: dimensioni VM predefinite | |A3 |A2 | | |
  | ZooKeeper: dimensioni VM consigliate | |A3, A4, A5 |A2, A3, A4 | | |
  | Edge: dimensioni VM predefinite | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: dimensioni VM consigliate | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Solo Brasile meridionale e Giappone occidentale (non sono disponibili dimensioni v2):

  | Tipo di cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Head: dimensioni VM predefinite |D3 |D3 |A3 |D12 |D12 |
  | Head: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Ruolo di lavoro: dimensioni VM predefinite |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Ruolo di lavoro: dimensioni VM consigliate |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | ZooKeeper: dimensioni VM predefinite | |A2 |A2 | | |
  | ZooKeeper: dimensioni VM consigliate | |A2, A3, A4 |A2, A3, A4 | | |
  | Edge: dimensioni VM predefinite | | | | |Windows: D12; Linux: D4 |
  | Edge: dimensioni VM consigliate | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> Il nodo Head è chiamato *Nimbus* per il tipo di cluster Storm. Il ruolo di lavoro è chiamato *Area* per il tipo di cluster HBase e *Supervisore* per il tipo di cluster Storm.

## <a name="next-steps"></a>Passaggi successivi
- [Configurazione del cluster per Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Lavorare da un computer Windows in Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

