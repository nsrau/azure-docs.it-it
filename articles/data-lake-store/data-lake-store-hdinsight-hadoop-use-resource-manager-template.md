---
title: Usare modelli di Azure per creare cluster HDInsight con Azure Data Lake Storage Gen1 | Microsoft Docs
description: Usare i modelli di Azure Resource Manager per creare e usare cluster HDInsight con Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161379"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Creare un cluster HDInsight con Data Lake Storage Gen1 usando un modello di Azure Resource Manager
> [!div class="op_single_selector"]
> * [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso di PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso di PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilizzo di Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare Azure PowerShell per configurare un cluster HDInsight con Azure Data Lake Storage Gen1 **come risorsa di archiviazione aggiuntiva**.

Per i tipi di cluster supportati, Data Lake Storage Gen1 può essere usato come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo. Quando Data Lake Storage Gen1 viene usato come risorsa di archiviazione aggiuntiva, l'account di archiviazione predefinito per i cluster saranno i BLOB del servizio di archiviazione di Azure (WASB) e i file correlati ai cluster (ad esempio log e così via) vengono scritti nella risorsa di archiviazione predefinita, mentre i dati da elaborare possono essere archiviati in un account Data Lake Storage Gen1. L'uso di Data Lake Storage Gen1 come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere/scrivere nella risorsa di archiviazione dal cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Uso di Data Lake Storage Gen1 per l'archiviazione di cluster HDInsight

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Storage Gen1:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Storage Gen1 come risorsa di archiviazione predefinita è disponibile per le versioni 3.5 e 3.6 di HDInsight.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva è disponibile per HDInsight versione 3.2, 3.4, 3.5 e 3.6.

In questo articolo si effettuerà il provisioning di un cluster Hadoop con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva. Per istruzioni su come creare un cluster Hadoop con Data Lake Storage Gen1 come risorsa di archiviazione predefinita, vedere [Creare un cluster HDInsight con Data Lake Storage Gen1 tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
* **Entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure AD. Tuttavia, è necessario essere un amministratore di Azure AD per creare un'entità servizio. Se si è un amministratore di Azure AD, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    **Se non si è un amministratore di Azure AD**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In tal caso, l'amministratore di Azure AD deve creare un'entità servizio prima di creare un cluster HDInsight con Data Lake Storage Gen1. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Creare un cluster HDInsight con Data Lake Storage Gen1
Il modello di Resource Manager e i prerequisiti per l'uso del modello sono disponibili in GitHub in [Deploy a HDInsight Linux cluster with new Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) (Distribuire un cluster HDInsight Linux con Data Lake Storage Gen1). Seguire le istruzioni riportate in questa pagina per creare un cluster HDInsight con Data Lake Storage Gen1 come risorsa di archiviazione aggiuntiva.

Le istruzioni indicate nella suddetta pagina richiedono PowerShell. Prima di mettere in pratica queste istruzioni, assicurarsi di accedere al proprio account Azure. Sul desktop aprire una nuova finestra di Azure PowerShell e immettere i frammenti di codice seguenti. Quando viene chiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Il modello distribuisce i tipi di risorse seguenti:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Caricare dati di esempio in Data Lake Storage Gen1
Il modello di Resource Manager crea un nuovo account Data Lake Storage Gen1 e lo associa al cluster HDInsight. È ora necessario caricare alcuni dati di esempio in Data Lake Storage Gen1. Questi dati saranno necessari più avanti nell'esercitazione per eseguire i processi da un cluster HDInsight che accede ai dati nell'account Data Lake Storage Gen1. Per istruzioni su come caricare i dati, vedere [Caricare un file nell'account Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Impostare ACL rilevanti per i dati di esempio
Per assicurarsi che i dati di esempio caricati siano accessibili dal cluster HDInsight, è necessario verificare che l'applicazione Azure AD usata per stabilire l'identità tra il cluster HDInsight e Data Lake Storage Gen1 disponga dell'accesso al file o alla cartella a cui si sta tentando di accedere. A questo scopo, eseguire i passaggi seguenti.

1. Trovare il nome dell'applicazione Azure AD associata al cluster HDInsight e all'account Data Lake Storage Gen1. Per ricercare il nome è possibile aprire il pannello del cluster HDInsight creato usando il modello di Resource Manager, fare clic sulla scheda **Identità AAD del cluster** e cercare il valore **Nome visualizzato dell'entità servizio**.
2. A questo punto, consentire all'applicazione di Azure AD di accedere al file o alla cartella a cui si desidera accedere dal cluster HDInsight. Per impostare gli elenchi di controllo di accesso (ACL) corretti nel file o nella cartella in Data Lake Storage Gen1, vedere [Protezione dei dati Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Eseguire i processi di test nel cluster HDInsight per usare Data Lake Storage Gen1
Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test nel cluster per verificare che il cluster HDInsight possa accedere a Data Lake Storage Gen1. A questo scopo, verrà eseguito un processo Hive di esempio che crea una tabella con i dati di esempio caricati in precedenza nell'account Data Lake Storage Gen1.

In questa sezione si accede tramite SSH a un cluster Linux HDInsight e si esegue una query Hive di esempio. Se si usa un client Windows, è consigliabile usare **PuTTY**, che può essere scaricato da [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Dopo la connessione, avviare l'interfaccia della riga di comando di Hive mediante il comando seguente:

   ```
   hive
   ```
2. Usando l'interfaccia della riga di comando, immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   L'output dovrebbe essere simile al seguente:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Accedere a Data Lake Storage Gen1 tramite comandi HDFS
Dopo aver configurato il cluster HDInsight perché funzioni con Data Lake Storage Gen1, è possibile usare i comandi della shell HDFS per accedere all'archivio.

In questa sezione si accede tramite SSH a un cluster Linux HDInsight e si eseguono comandi HDFS. Se si usa un client Windows, è consigliabile usare **PuTTY**, che può essere scaricato da [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per visualizzare i file nell'account Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Dovrebbe essere visualizzato il file precedentemente caricato in Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

È anche possibile usare il comando `hdfs dfs -put` per caricare alcuni file in Data Lake Storage Gen1 e quindi usare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.


## <a name="next-steps"></a>Passaggi successivi
* [Copiare i dati dai BLOB di Archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Usare Data Lake Storage Gen1 con cluster Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
