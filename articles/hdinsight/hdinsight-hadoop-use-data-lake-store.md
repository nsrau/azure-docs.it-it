---
title: Usare Data Lake Store con Hadoop in Azure HDInsight | Microsoft Docs
description: Informazioni su come eseguire query sui dati da Azure Data Lake Store e come archiviare i risultati dell'analisi.
keywords: archiviazione BLOB, hdfs, dati strutturati, dati non strutturati, Data Lake Store
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 28a836aff65636ef0031ac63f633d746436d7e4a
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Usare Data Lake Store con cluster Azure HDInsight

Per analizzare i dati in un cluster HDInsight, è possibile archiviarli in [Archiviazione di Azure](../storage/common/storage-introduction.md), in [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) o in entrambe le soluzioni. Entrambe le opzioni di archiviazione consentono l'eliminazione sicura dei cluster HDInsight usati per i calcoli, senza perdita di dati utente.

Questo articolo illustra come usare Data Lake Store con i cluster HDInsight. Per sapere come usare Archiviazione di Azure con i cluster HDInsight, vedere [Usare Archiviazione di Azure con cluster Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Per altre informazioni sulla creazione di un cluster HDInsight, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> L'accesso a Data Lake Store avviene sempre tramite un canale protetto, pertanto non è presente un nome di schema del file system `adls`. Viene usato sempre `adl`.
> 
> 

## <a name="availabilities-for-hdinsight-clusters"></a>Disponibilità per i cluster HDInsight

Hadoop supporta una nozione del file system predefinito. Il file system predefinito implica uno schema e un'autorità predefiniti e può essere usato anche per risolvere percorsi relativi. Durante il processo di creazione del cluster HDInsight è possibile specificare un contenitore BLOB in Archiviazione di Azure come file system predefinito. In alternativa, con HDInsight 3.5 e versioni successive è possibile selezionare Archiviazione di Azure o Azure Data Lake Store come file system predefinito, con alcune eccezioni. 

I cluster HDInsight possono usare Data Lake Store in due modi:

* Come risorsa di archiviazione predefinita
* Come risorsa di archiviazione aggiuntiva, con BLOB del servizio di archiviazione di Azure come risorsa predefinita.

Attualmente, solo alcuni dei tipi/versioni di cluster HDInsight supportano l'uso di Data Lake Store come account di archiviazione predefinito e di archiviazione aggiuntivo:

| Tipo di cluster HDInsight | Data Lake Store come risorsa di archiviazione predefinita | Data Lake Store come risorsa di archiviazione aggiuntiva| Note |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versione 3.6 | Sì | Sì | |
| HDInsight versione 3.5 | Sì | Sì | Ad eccezione di HBase|
| HDInsight versione 3.4 | No | Sì | |
| HDInsight versione 3.3 | No | No | |
| HDInsight versione 3.2 | No | Sì | |
| HDInsight Premium (livello)| No | No | |
| Storm | | |È possibile usare Data Lake Store per scrivere dati da una topologia Storm. È anche possibile usare Data Lake Store per archiviare dati di riferimento che possono essere letti da una topologia Storm.|

L'uso di Data Lake Store come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere o scrivere nella risorsa di archiviazione di Azure dal cluster.


## <a name="use-data-lake-store-as-default-storage"></a>Usare Data Lake Store come risorsa di archiviazione predefinita

Quando si distribuisce HDInsight con Data Lake Store come risorsa di archiviazione predefinita, i file legati al cluster vengono archiviati in Data Lake Store nel percorso seguente:

    adl://mydatalakestore/<cluster_root_path>/

dove `<cluster_root_path>` è il nome di una cartella creata in Data Lake Store. Specificando un percorso radice per ogni cluster, è possibile usare lo stesso account di Data Lake Store per più di un cluster. Pertanto, è possibile disporre di una configurazione in cui:

* Cluster1 può usare il percorso `adl://mydatalakestore/cluster1storage`
* Cluster2 può usare il percorso `adl://mydatalakestore/cluster2storage`

Si noti che entrambi i cluster usano lo stesso account Data Lake Store **mydatalakestore**. Ogni cluster ha accesso al proprio file system radice in Data Lake Store. Più nello specifico, l'esperienza di distribuzione del Portale di Azure richiede di utilizzare un nome di cartella come **/clusters/\<clustername>** per il percorso radice.

Per poter usare Data Lake Store come risorsa di archiviazione predefinita, è necessario concedere all'entità servizio l'accesso ai percorsi seguenti:

- Radice dell'account Data Lake Store,  ad esempio adl://mydatalakestore/.
- Cartella per tutte le cartelle del cluster,  ad esempio adl://mydatalakestore/clusters.
- Cartella per il cluster,  ad esempio adl://mydatalakestore/clusters/cluster1storage.

Per altre informazioni su come creare un'entità servizio e concedere l'accesso, vedere [Configurare l'accesso a Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Usare Data Lake Store come risorsa di archiviazione aggiuntiva

È anche possibile usare Data Lake Store come risorsa di archiviazione aggiuntiva per il cluster. In questi casi la risorsa di archiviazione predefinita del cluster può essere un BLOB del servizio di archiviazione di Azure o un account Data Lake Store. Se si eseguono processi di HDInsight con i dati archiviati in Data Lake Store come risorsa di archiviazione aggiuntiva, è necessario usare il percorso completo ai file. ad esempio:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Si noti che al momento non esiste alcun **cluster_root_path** nell'URL. Infatti, Data Lake Store non è una risorsa di archiviazione predefinita in questo caso. È sufficiente pertanto indicare il percorso ai file.

Per poter usare Data Lake Store come risorsa di archiviazione aggiuntiva, è necessario semplicemente concedere all'entità servizio l'accesso ai percorsi in cui sono archiviati i file.  ad esempio:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Per altre informazioni su come creare un'entità servizio e concedere l'accesso, vedere [Configurare l'accesso a Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Usare più di un account di Data Lake Store

Le operazioni di aggiunta di un account di Data Lake Store come risorsa di archiviazione aggiuntiva e di aggiunta di più account di Data Lake Store vengono eseguite assegnando al cluster HDInsight l'autorizzazione per i dati in uno o più account di Data Lake Store. Vedere [Configurare l'accesso a Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurare l'accesso a Data Lake Store

Per configurare l'accesso a Data Lake Store dal cluster HDInsight, è necessario disporre di un'entità servizio di Azure Active Directory (Azure AD). Solo un amministratore di Azure AD può creare un'entità servizio. L'entità servizio deve essere creata con un certificato. Per altre informazioni, vedere [Configurare l'accesso a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#configure-data-lake-store-access) e [Creare un'entità servizio con certificato autofirmato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Se si intende usare Azure Data Lake Store come risorsa di archiviazione aggiuntiva per il cluster HDInsight, è consigliabile eseguire questa operazione durante la creazione del cluster, come descritto in questo articolo. L'aggiunta di Azure Data Lake Store come ulteriore risorsa di archiviazione a un cluster HDInsight esistente è un processo complesso e soggetto a errori.
>

## <a name="access-files-from-the-cluster"></a>Accedere ai file dal cluster

Esistono diversi modi per accedere ai file in Data Lake Store da un cluster HDInsight.

* **Uso di nomi completi**. Con questo approccio viene fornito il percorso completo al file a cui si desidera accedere.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Uso del formato con percorso abbreviato**. Con questo approccio si sostituisce con adl:/// il percorso fino alla radice del cluster. Nell'esempio precedente, pertanto, è possibile sostituire `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` con `adl:///`.

        adl:///<file path>

* **Uso del percorso relativo**. Con questo approccio viene fornito unicamente il percorso relativo al file a cui si desidera accedere. Ad esempio, se il percorso completo del file è:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    È possibile accedere al medesimo file sample.log usando invece il percorso relativo.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Creare cluster HDInsight con accesso a Data Lake Store

Usare i collegamenti seguenti per informazioni dettagliate su come creare cluster HDInsight con accesso a Data Lake Store.

* [Uso del portale](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [Uso di PowerShell con Data Lake Store come risorsa di archiviazione predefinita](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Uso di PowerShell con Data Lake Store come risorsa di archiviazione aggiuntiva](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Uso di modelli di Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come usare Azure Data Lake Store compatibile con HDFS con HDInsight. In questo modo sarà possibile creare soluzioni scalabili di acquisizione e archiviazione a lungo termine dei dati e usare HDInsight per sbloccare le informazioni all'interno dei dati strutturati e non strutturati archiviati.

Per altre informazioni, vedere:

* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Introduzione ad Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati con HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

