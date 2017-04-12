---
title: Usare modelli di Azure per creare istanze di HDInsight e Data Lake Store | Documentazione Microsoft
description: Usare un modello di Azure Resource Manager per creare e usare cluster HDInsight con Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: bc870d2ec2f81c439beb96878a68466ed94988f3
ms.lasthandoff: 04/05/2017


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Creare un cluster HDInsight con Data Lake Store usando un modello di Azure Resource Manager
> [!div class="op_single_selector"]
> * [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Uso di PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Uso di PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilizzo di Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare Azure PowerShell per configurare un cluster HDInsight con Azure Data Lake Store **come risorsa di archiviazione aggiuntiva**.

Per i tipi di cluster supportati, Data Lake Store deve essere usato come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo. Quando Data Lake Store viene usato come risorsa di archiviazione aggiuntiva, l'account di archiviazione predefinito per i cluster saranno i BLOB del servizio di archiviazione di Azure (WASB) e i file correlati ai cluster (ad esempio log e così via) vengono scritti nella risorsa di archiviazione predefinita, mentre i dati da elaborare possono essere archiviati in un account di Data Lake Store. L'uso di Archivio Data Lake come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere/scrivere nella risorsa di archiviazione dal cluster.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Udo di Data Lake Store per l'archiviazione di cluster HDInsight

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Store:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita è disponibile per HDInsight versione 3.5.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione aggiuntiva è disponibile per HDInsight versioni 3.2, 3.4 e 3.5.

* Per i cluster HBase (Windows e Linux), Data Lake Store **non è supportato** come opzione di archiviazione, per la risorsa di archiviazione predefinita o aggiuntiva.


In questo articolo si effettuerà il provisioning di un cluster Hadoop con Archivio Data Lake come risorsa di archiviazione aggiuntiva. Per istruzioni su come creare un cluster Hadoop con Data Lake Store come risorsa di archiviazione predefinita, vedere [Creare un cluster HDInsight con Data Lake Store tramite il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* **Entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure AD. Tuttavia, è necessario essere un amministratore di Azure AD per creare un'entità servizio. Se si è un amministratore di Azure AD, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    **Se non si è un amministratore di Azure AD**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In tal caso, l'amministratore di Azure AD deve creare un'entità servizio prima di creare un cluster HDInsight con l'archivio Data Lake Store. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Creare un cluster HDInsight con Azure Data Lake Store
Il modello di Resource Manager e i prerequisiti per l'uso del modello sono disponibili in GitHub alla sezione [Deploy a HDInsight Linux cluster with new Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) (Distribuzione di un cluster HDInsight Linux con il nuovo Data Lake Store). Seguire le istruzioni riportate in questa pagina per creare un cluster HDInsight con Azure Data Lake Store come spazio di archiviazione aggiuntivo.

Le istruzioni indicate nella suddetta pagina richiedono PowerShell. Prima di mettere in pratica queste istruzioni, assicurarsi di accedere al proprio account Azure. Sul desktop aprire una nuova finestra di Azure PowerShell e immettere i frammenti di codice seguenti. Quando viene richiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Caricare i dati di esempio in Azure Data Lake Store
Il modello di Resource Manager crea un nuovo account Data Lake Store e lo associa al cluster HDInsight. È ora necessario caricare alcuni dati di esempio in Data Lake Store. Questi dati saranno necessari più avanti nell'esercitazione per eseguire i processi da un cluster HDInsight che accede ai dati nell'Archivio Data Lake. Per istruzioni su come caricare i dati, vedere [Caricare dati in Archivio Data Lake di Azure](data-lake-store-get-started-portal.md#uploaddata). Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Impostare ACL rilevanti per i dati di esempio
Per assicurarsi che i dati di esempio caricati siano accessibili dal cluster HDInsight, è necessario assicurarsi che l'applicazione Azure AD usata per stabilire l'identità tra il cluster HDInsight e Data Lake Store disponga dell'accesso al file o alla cartella a cui si sta tentando di accedere. A questo scopo, eseguire i passaggi seguenti.

1. Trovare il nome dell'applicazione Azure AD associata al cluster HDInsight e Data Lake Store. Per ricercare il nome è possibile aprire il pannello del cluster HDInsight creato usando il modello di Resource Manager, fare clic sulla scheda **Identità AAD del cluster** e cercare il valore **Nome visualizzato dell'entità servizio**.
2. A questo punto, consentire all'applicazione di Azure AD di accedere al file o alla cartella a cui si desidera accedere dal cluster HDInsight. Per impostare gli ACL corretti sul file o sulla cartella in Data Lake Store, vedere [Protezione dei dati presenti in Archivio Data Lake di Azure](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Eseguire i processi di test sul cluster HDInsight per usare Archivio Data Lake.
Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa accedere ad Archivio Data Lake. A questo scopo, verrà eseguito un processo Hive di esempio che crea una tabella con i dati di esempio caricati in precedenza in Archivio Data Lake.

In questa sezione si accede tramite SSH a un cluster Linux HDInsight e si esegue una query Hive di esempio. Se si usa un client Windows, è consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Dopo la connessione, avviare l'interfaccia della riga di comando di Hive mediante il comando seguente:

   ```
   hive
   ```
2. Usando l'interfaccia della riga di comando, immettere le istruzioni seguenti per creare una nuova tabella denominata **vehicles** con i dati di esempio in Archivio Data Lake:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Accedere ad Archivio Data Lake tramite comandi HDFS
Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

In questa sezione si accede tramite SSH a un cluster Linux HDInsight e si eseguono comandi HDFS. Se si usa un client Windows, è consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per elencare i file nell'Archivio Data Lake.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.


## <a name="next-steps"></a>Passaggi successivi
* [Copiare i dati dai BLOB del servizio di archiviazione di Azure in Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)

