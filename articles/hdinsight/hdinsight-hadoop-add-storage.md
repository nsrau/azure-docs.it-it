---
title: Aggiungere altri account di Archiviazione di Azure a HDInsight
description: Informazioni su come aggiungere altri account di Archiviazione di Azure a un cluster HDInsight esistente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665484"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Aggiungere altri account di archiviazione a HDInsight

Informazioni su come usare le azioni script per aggiungere altri *account* di archiviazione di Azure a HDInsight. La procedura descritta in questo documento consente di aggiungere un *account* di archiviazione a un cluster HDInsight basato su Linux esistente. Questo articolo si applica agli *account* di archiviazione (non all'account di archiviazione del cluster predefinito) e non a una risorsa di archiviazione aggiuntiva, ad esempio [Azure Data Lake storage Gen1](hdinsight-hadoop-use-data-lake-store.md) e [Azure Data Lake storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Le informazioni contenute in questo documento illustrano come aggiungere altri account di archiviazione a un cluster dopo che è stato creato. Per informazioni sull'aggiunta di account di archiviazione durante la creazione del cluster, vedere [Configurare cluster in HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chiave dell'account di archiviazione. Vedere [gestire le impostazioni dell'account di archiviazione nel portale di Azure](../storage/common/storage-account-manage.md).
* [Nome del cluster con case corretta](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Se si usa PowerShell, è necessario il modulo AZ.  Vedere [Panoramica di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Se l'interfaccia della riga di comando di Azure non è stata installata, vedere [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Se si usa bash o un prompt dei comandi di Windows, è necessario anche **JQ**, un processore JSON da riga di comando.  Vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Per bash in Ubuntu in Windows 10, vedere la [Guida all'installazione del sottosistema Windows per Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Funzionamento

Lo script accetta i parametri seguenti:

* __Nome dell'account di archiviazione di Azure__: nome dell'account di archiviazione da aggiungere al cluster HDInsight. Dopo l'esecuzione dello script, HDInsight è in grado di leggere e scrivere i dati archiviati in questo account di archiviazione.

* __Chiave dell'account di archiviazione di Azure__: chiave che concede l'accesso all'account di archiviazione.

* __-p__ (facoltativo): se specificato, la chiave non è crittografata e viene archiviata nel file core-site. XML come testo normale.

Durante l'elaborazione, lo script esegue le azioni seguenti:

* Se la chiave dell'account di archiviazione esiste già nella configurazione del file core-site.xml per il cluster, lo script viene chiuso e non vengono eseguite altre azioni.

* Verifica che l'account di archiviazione esista e sia accessibile tramite la chiave.

* Crittografa la chiave mediante le credenziali del cluster,

* Aggiunge l'account di archiviazione al file core-site.xml.

* Arrestare e riavviare i servizi [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) e [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html). L'arresto e l'avvio di questi servizi consente di usare il nuovo account di archiviazione.

> [!WARNING]  
> L'uso di un account di archiviazione in una località diversa rispetto al cluster HDInsight non è supportato.

## <a name="the-script"></a>Lo script

__Percorso dello script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisiti__: lo script deve essere applicato ai __nodi head__. Non è necessario contrassegnare questo script come __reso permanente__perché aggiorna direttamente la configurazione di Ambari per il cluster.

## <a name="to-use-the-script"></a>Per usare lo script

Questo script può essere usato dalla Azure PowerShell, dall'interfaccia della riga di comando di Azure o dall'portale di Azure.

### <a name="powershell"></a>PowerShell

Utilizzando [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Sostituire `CLUSTERNAME`, `ACCOUNTNAME`e `ACCOUNTKEY` con i valori appropriati.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Uso [di AZ HDInsight script-Action Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Sostituire `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`e `ACCOUNTKEY` con i valori appropriati.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Portale di Azure

Vedere [applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Problemi noti

### <a name="storage-firewall"></a>Firewall di archiviazione

Se si sceglie di proteggere l'account di archiviazione con le restrizioni relative a **firewall e reti virtuali** nelle **reti selezionate**, assicurarsi di abilitare l'eccezione **Consenti servizi Microsoft attendibili...** in modo che HDInsight possa accedere alla risorsa di archiviazione account.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Account di archiviazione non visualizzati nel portale di Azure o negli strumenti

Quando si Visualizza il cluster HDInsight nel portale di Azure, selezionando la voce __account di archiviazione__ in __Proprietà__ non vengono visualizzati gli account di archiviazione aggiunti tramite questa azione script. Azure PowerShell e l'interfaccia della riga di comando di Azure non visualizzano l'account di archiviazione aggiuntivo.

Le informazioni di archiviazione non vengono mostrate perché lo script modifica solo la configurazione di core-site.xml per il cluster. Queste informazioni non vengono usate quando si recuperano le informazioni del cluster usando le API di gestione di Azure.

Per visualizzare le informazioni sull'account di archiviazione aggiunte al cluster tramite lo script, usare l'API REST Ambari. Usare i comandi seguenti per recuperare queste informazioni per il cluster:

### <a name="powershell"></a>PowerShell

Sostituire `CLUSTERNAME` con il nome del cluster con maiuscole/minuscole appropriato. Sostituire `ACCOUNTNAME` con i nomi effettivi. Quando richiesto, immettere la password di accesso del cluster.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Sostituire `CLUSTERNAME` con il nome del cluster con maiuscole/minuscole appropriato. Sostituire `PASSWORD` con la password amministratore del cluster. Sostituire `STORAGEACCOUNT` con il nome dell'account di archiviazione effettivo.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Sostituire `CLUSTERNAME` con il nome del cluster con maiuscole/minuscole correttamente in entrambi gli script. Identificare prima di tutto la versione di configurazione del servizio in uso immettendo il comando seguente:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Sostituire `ACCOUNTNAME` con il nome dell'account di archiviazione effettivo. Sostituire quindi `4` con la versione di configurazione del servizio effettiva e immettere il comando:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Le informazioni restituite da questo comando sono simili al testo seguente:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Questo testo è un esempio di chiave crittografata, usata per accedere all'account di archiviazione.

### <a name="unable-to-access-storage-after-changing-key"></a>Non è possibile accedere alla risorsa di archiviazione dopo la modifica della chiave

Se si modifica la chiave per un account di archiviazione, HDInsight non potrà più accedere all'account di archiviazione. HDInsight usa una copia memorizzata nella cache della chiave in core-site.xml per il cluster. Questa copia memorizzata nella cache deve essere aggiornata in modo che corrisponda alla nuova chiave.

La ripetizione dell'esecuzione dell'azione script __non__ aggiorna la chiave, perché lo script verifica se esiste già una voce per l'account di archiviazione. Se una voce esiste già, non viene apportata alcuna modifica.

Per risolvere il problema, è necessario rimuovere la voce esistente per l'account di archiviazione, Eseguire i seguenti passaggi per rimuovere la voce esistente:

> [!IMPORTANT]  
> La rotazione della chiave di archiviazione per l'account di archiviazione primario collegato a un cluster non è supportata.

1. In un Web browser aprire l'interfaccia utente Web di Ambari per il cluster HDInsight. L'URI è `https://CLUSTERNAME.azurehdinsight.net`. Sostituire `CLUSTERNAME` con il nome del cluster.

    Quando richiesto, immettere l'utente e la password di accesso HTTP per il cluster.

2. Dall'elenco di servizi nella parte sinistra della pagina selezionare __HDFS__. Selezionare quindi la scheda __Configs__ (Configurazioni) al centro della pagina.

3. Nel campo __Filter__ (Filtro) immettere il valore __fs.azure.account__. Vengono restituite le voci per eventuali account di archiviazione aggiunti al cluster. Sono disponibili due tipi di voci, ovvero __keyprovider__ e __key__. Entrambi i tipi contengono il nome dell'account di archiviazione come parte del nome della chiave.

    Le voci di esempio seguenti sono relative a un account di archiviazione denominato __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Dopo aver identificato le chiavi per l'account di archiviazione che è necessario rimuovere, usare l'icona '-' rossa a destra della voce per eliminarla. Usare quindi il pulsante __Salva__ per salvare le modifiche apportate.

5. Dopo il salvataggio delle modifiche, usare l'azione script per aggiungere l'account di archiviazione e il nuovo valore della chiave al cluster.

### <a name="poor-performance"></a>Prestazioni non ottimali

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che le prestazioni non siano ottimali. L'accesso ai dati in un'area diversa comporta l'invio di traffico di rete all'esterno del data center di Azure di un'area specifica e la trasmissione tramite Internet pubblico e ciò può introdurre latenza.

### <a name="additional-charges"></a>Costi aggiuntivi

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che la fatturazione di Azure includa addebiti di uscita aggiuntivi. Viene applicato un addebito di uscita quando i dati escono dal data center di un'area, anche se il traffico è destinato a un altro data center di Azure in un'area diversa.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come aggiungere altri account di archiviazione a un cluster HDInsight esistente. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md)
