---
title: Eseguire processi Apache Sqoop con Azure HDInsight (Apache Hadoop)
description: Informazioni su come usare Azure PowerShell da una workstation per eseguire importazioni ed esportazioni con Sqoop tra un cluster Hadoop e un database SQL di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129400"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Usare Apache Sqoop con Hadoop in HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Apache Sqoop in HDInsight per importare ed esportare dati tra un cluster HDInsight e un database SQL di Azure.

Anche se Apache Hadoop è una scelta naturale per l'elaborazione dei dati non strutturati e semi-strutturati, ad esempio log e file, è anche possibile necessario elaborare i dati strutturati archiviati nei database relazionali.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) è uno strumento progettato per trasferire dati tra i cluster Hadoop e database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL Server, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Apache Hive e quindi esportarli nuovamente in un sistema RDBMS. In questo articolo, si usa un database di SQL Server per il database relazionale.

> [!IMPORTANT]  
> Questo articolo si configura un ambiente di test per eseguire il trasferimento dei dati. È quindi scegliere un metodo di trasferimento dati per questo ambiente da uno dei metodi nella sezione [processi Sqoop eseguire](#run-sqoop-jobs), più avanti.

Per le versioni di Sqoop supportate nei cluster HDInsight, vedere [novità introdotta in versioni di cluster incluse in HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Informazioni sullo scenario

Il cluster HDInsight include alcuni dati di esempio. Usare i due esempi seguenti:

* Un file di log Log4j di Apache, che si trova in `/example/data/sample.log`. Dal file verranno estratti i log seguenti:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Una tabella Hive denominata `hivesampletable`, che fa riferimento al file di dati che si trova in `/hive/warehouse/hivesampletable`. La tabella contiene alcuni dati relativi al dispositivo mobile.
  
  | Campo | Tipo di dati |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | country |string |
  | querydwelltime |Double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

In questo articolo usare questi due set di dati per testare Sqoop importazione ed esportazione.

## <a name="create-cluster-and-sql-database"></a>Configurare l'ambiente di test
Il cluster, database SQL e altri oggetti vengono creati tramite il portale di Azure usando un modello di Azure Resource Manager. Il modello è reperibile nel [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Il modello di Resource Manager chiama un pacchetto bacpac per distribuire gli schemi di tabella in un database SQL.  Il pacchetto bacpac è disponibile in un contenitore BLOB pubblico, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se si vuole usare un contenitore privato per i file bacpac, usare i valori seguenti nel modello:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> L'importazione tramite un modello o il portale di Azure supporta solo l'importazione di un file BACPAC dall'archiviazione BLOB di Azure.

1. Selezionare l'immagine seguente per aprire il modello di Resource Manager nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere le seguenti proprietà:

    |Campo |Value |
    |---|---|
    |Sottoscrizione |Selezionare la sottoscrizione di Azure nell'elenco a discesa.|
    |Gruppo di risorse |Selezionare il gruppo di risorse nell'elenco a discesa o crearne uno nuovo|
    |Località |Selezionare un'area nell'elenco a discesa.|
    |Cluster Name |Immettere un nome per il cluster Hadoop. Usare solo una lettera minuscola.|
    |Nome utente dell'account di accesso del cluster |Mantenere il valore già popolato `admin`.|
    |Password di accesso al cluster |Immettere una password.|
    |Nome utente SSH |Mantenere il valore già popolato `sshuser`.|
    |SSH Password |Immettere una password.|
    |Account di accesso amministratore SQL |Mantenere il valore già popolato `sqluser`.|
    |Password amministratore SQL |Immettere una password.|
    |Artifacts location | Usare il valore predefinito a meno che non si vuole usare un file bacpac in un percorso diverso.|
    |Artifacts location Sas Token |Lasciare vuoto.|
    |Nome del File Bacpac |Usare il valore predefinito a meno che non si vuole usare un file bacpac.|
    |Località |Usare il valore predefinito.|

    Il nome di Server SQL di Azure sarà `<ClusterName>dbserver`. Il nome del database saranno `<ClusterName>db`. Il nome di account di archiviazione predefinito sarà `e6qhezrh2pdqu`.

3. Selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare **Acquisto**. Viene visualizzato un nuovo riquadro denominato Invio della distribuzione per Distribuzione modello. La creazione del cluster e del database SQL richiede circa 20 minuti.

## <a name="run-sqoop-jobs"></a>Eseguire processi Sqoop

HDInsight è in grado di eseguire processi Sqoop in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo** se si desidera... | ...una shell **interattiva** | ...elaborazione**batch** | ...da questo **sistema operativo client** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X o Windows |
| [.NET SDK per Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (per ora) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitazioni

* Esportazione di massa: con HDInsight basato su Linux, attualmente il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti di massa.
* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache Hive con HDInsight](../hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig.md)
* [Caricare dati in HDInsight](../hdinsight-upload-data.md): trovare altri metodi per il caricamento di dati in HDInsight o nell'archiviazione BLOB di Azure.
