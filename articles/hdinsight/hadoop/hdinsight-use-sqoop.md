---
title: Eseguire processi Apache Sqoop con Azure HDInsight (Apache Hadoop)
description: Informazioni su come usare Azure PowerShell da una workstation per eseguire importazioni ed esportazioni con Sqoop tra un cluster Hadoop e un database SQL di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951854"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Usare Apache Sqoop con Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Apache Sqoop in HDInsight per importare ed esportare dati tra un cluster HDInsight e un database SQL di Azure.Learn how to use Apache Sqoop in HDInsight to import and export data between an HDInsight cluster and an Azure SQL database.

Anche se Apache Hadoop è una scelta naturale per l'elaborazione di dati non strutturati e semistrutturati, ad esempio log e file, potrebbe essere necessario elaborare i dati strutturati archiviati in database relazionali.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) è uno strumento progettato per il trasferimento di dati tra cluster Hadoop e database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL Server, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Apache Hive e quindi esportarli nuovamente in un sistema RDBMS. In questo articolo viene utilizzato un database di SQL Server per il database relazionale.

> [!IMPORTANT]  
> Questo articolo consente di configurare un ambiente di test per eseguire il trasferimento dei dati. È quindi possibile scegliere un metodo di trasferimento dati per questo ambiente da uno dei metodi descritti nella sezione [Eseguire processi Sqoop](#run-sqoop-jobs), più avanti.

Per le versioni Sqoop supportate nei cluster HDInsight, vedere [Novità delle versioni del cluster fornite da HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Informazioni sullo scenario

Il cluster HDInsight include alcuni dati di esempio. Usare i due esempi seguenti:

* Un file di registro Apache Log4j, che si trova in `/example/data/sample.log`. Dal file verranno estratti i log seguenti:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Una tabella Hive denominata `hivesampletable`, che `/hive/warehouse/hivesampletable`fa riferimento al file di dati che si trova in . La tabella contiene alcuni dati relativi al dispositivo mobile.
  
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
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

In questo articolo vengono utilizzati questi due set di dati per testare l'importazione e l'esportazione di Sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Configurare l'ambiente di testSet up test environment

Il cluster, il database SQL e altri oggetti vengono creati tramite il portale di Azure usando un modello di Azure Resource Manager.The cluster, SQL database, and other objects are created through the Azure portal using an Azure Resource Manager template. Il modello è disponibile nei modelli di [avvio rapido](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)di Azure. Il modello di Resource Manager chiama un pacchetto bacpac per distribuire gli schemi di tabella in un database SQL.  Il pacchetto bacpac è disponibile in un contenitore BLOB pubblico, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se si vuole usare un contenitore privato per i file bacpac, usare i valori seguenti nel modello:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> L'importazione tramite un modello o il portale di Azure supporta solo l'importazione di un file BACPAC dall'archiviazione BLOB di Azure.

1. Selezionare l'immagine seguente per aprire il modello Resource Manager nel portale di Azure.Select the following image to open the Resource Manager template in the Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Immettere le seguenti proprietà:

    |Campo |valore |
    |---|---|
    |Subscription |Selezionare la sottoscrizione di Azure dall'elenco a discesa.|
    |Resource group |Selezionare il gruppo di risorse dall'elenco a discesa o crearne uno nuovo|
    |Location |Selezionare un'area dall'elenco a discesa.|
    |Cluster Name |Immettere un nome per il cluster Hadoop. Utilizzare solo lettere minuscole.|
    |Nome utente dell'account di accesso del cluster |Mantenere il valore `admin`prepopolato .|
    |Password di accesso al cluster |Immettere una password.|
    |Ssh Nome utente |Mantenere il valore `sshuser`prepopolato .|
    |Ssh Password |Immettere una password.|
    |Accesso amministratore SQLSql Admin Login |Mantenere il valore `sqluser`prepopolato .|
    |Password amministratore SQL |Immettere una password.|
    |_artifacts Posizione | Utilizzare il valore predefinito a meno che non si desideri utilizzare il proprio file bacpac in una posizione diversa.|
    |token Sas di posizione _artifacts |Lasciare vuoto.|
    |Nome file Bacpac |Utilizzare il valore predefinito a meno che non si desideri utilizzare il proprio file bacpac.|
    |Location |Usare il valore predefinito.|

    Il nome di Azure `<ClusterName>dbserver`SQL Server sarà . Il nome del `<ClusterName>db`database sarà . Il nome dell'account `e6qhezrh2pdqu`di archiviazione predefinito sarà .

3. Selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare **Acquista**. Viene visualizzato un nuovo riquadro denominato Invio della distribuzione per Distribuzione modello. La creazione del cluster e del database SQL richiede circa 20 minuti.

## <a name="run-sqoop-jobs"></a>Eseguire processi Sqoop

HDInsight è in grado di eseguire processi Sqoop in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo** se si desidera... | ...una shell **interattiva** | ... elaborazione **batch** | ...da questo **sistema operativo client** |
|:--- |:---:|:---:|:--- |:--- |
| [Ssh](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X o Windows |
| [.NET SDK per Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (per ora) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |WINDOWS |

## <a name="limitations"></a>Limitazioni

* Esportazione in blocco: con HDInsight basato su Linux, il connettore Sqoop utilizzato per esportare i dati in Microsoft SQL Server o database SQL di Azure non supporta attualmente gli inserimenti bulk.
* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi

Ora hai imparato a usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache Hive con HDInsight](../hdinsight-use-hive.md)
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.
* [Usare Apache Sqoop per importare ed esportare dati tra Apache Hadoop su HDInsight e un database SQL](./apache-hadoop-use-sqoop-mac-linux.md)