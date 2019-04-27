---
title: Aggiungere altri account di Archiviazione di Azure a HDInsight
description: Informazioni su come aggiungere altri account di Archiviazione di Azure a un cluster HDInsight esistente.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 6b9577bcf8b527abb0cb7b8720ed83ec8321655b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098723"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Aggiungere altri account di archiviazione a HDInsight

Informazioni su come usare azioni script per aggiungere archiviazione di Azure aggiuntivi *account* per HDInsight. I passaggi descritti in questo documento aggiungono una risorsa di archiviazione *account* a un cluster HDInsight basati su Linux esistente. Questo articolo si applica all'archiviazione *conti* (non cluster account di archiviazione predefinito) e non ulteriore spazio di archiviazione, ad esempio [Gen1 di archiviazione di Azure Data Lake](hdinsight-hadoop-use-data-lake-store.md) e [Gen2 di archiviazione di Azure Data Lake ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Le informazioni in questo documento illustrano come aggiungere altre risorse di archiviazione a un cluster dopo la creazione. Per informazioni sull'aggiunta di account di archiviazione durante la creazione del cluster, vedere [Configurare cluster in HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Hadoop in HDInsight. Visualizzare [Introduzione a HDInsight su Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome account di archiviazione e la chiave. Visualizzare [gestire le impostazioni di account di archiviazione nel portale di Azure](../storage/common/storage-account-manage.md).
* [Nome del cluster in modo corretto maiuscole e minuscole](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Se si usa PowerShell, è necessario il modulo di AZ.  Visualizzare [Panoramica di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Se non è stato installato l'interfaccia CLI di Azure, vedere [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Se si usa bash o un prompt dei comandi di windows, è necessario anche **jq**, un processore JSON della riga di comando.  Vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Per bash in Ubuntu in Windows 10, vedere [sottosistema Windows per la Guida all'installazione di Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Funzionamento

Lo script accetta i parametri seguenti:

* __Nome dell'account di archiviazione di Azure__: nome dell'account di archiviazione da aggiungere al cluster HDInsight. Dopo l'esecuzione dello script, HDInsight è in grado di leggere e scrivere i dati archiviati in questo account di archiviazione.

* __Chiave dell'account di archiviazione di Azure__: chiave che concede l'accesso all'account di archiviazione.

* __-p__ (facoltativo): se questo parametro viene specificato, la chiave non viene crittografata e viene archiviata nel file core-site.xml come testo normale.

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

__Requisiti__:  Lo script deve essere applicato ai __nodi head__. Non è necessario contrassegnare lo script come __Persistente__, perché aggiorna direttamente la configurazione Ambari per il cluster.

## <a name="to-use-the-script"></a>Per usare lo script

Questo script può essere usato da Azure PowerShell, CLI Azure o il portale di Azure.

### <a name="powershell"></a>PowerShell

Usando [AzHDInsightScriptAction inviare](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Sostituire `CLUSTERNAME`, `ACCOUNTNAME`, e `ACCOUNTKEY` con i valori appropriati.

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

Usando [azione di script di az hdinsight eseguire](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Sostituire `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, e `ACCOUNTKEY` con i valori appropriati.

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

Visualizzare [applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Problemi noti

### <a name="storage-firewall"></a>Firewall di archiviazione

Se si sceglie di proteggere l'account di archiviazione con il **firewall e reti virtuali** restrizioni sulle **reti selezionate**, assicurarsi di abilitare l'eccezione **Consenti attendibili Microsoft servizi...**  in modo che HDInsight può accedere all'account di archiviazione.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Account di archiviazione non visualizzati nel portale di Azure o negli strumenti

Quando si visualizza il cluster HDInsight nel Portale di Azure, selezionando la voce __Account di archiviazione__ in __Proprietà__ non vengono mostrati gli account di archiviazione aggiunti tramite questa azione script. L'account di archiviazione aggiuntivo non viene inoltre visualizzato in Azure PowerShell e nell'interfaccia della riga di comando di Azure.

Le informazioni di archiviazione non vengono mostrate perché lo script modifica solo la configurazione di core-site.xml per il cluster. Queste informazioni non vengono usate durante il recupero delle informazioni del cluster tramite le API di gestione di Azure.

Per visualizzare le informazioni sull'account di archiviazione aggiunte al cluster tramite lo script, usare l'API REST Ambari. Usare i comandi seguenti per recuperare queste informazioni per il cluster:

### <a name="powershell"></a>PowerShell

Sostituire `CLUSTERNAME` con il nome del cluster in modo corretto maiuscole e minuscole. Prima di tutto identificare la versione di configurazione del servizio in uso immettendo il comando seguente:

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Sostituire `ACCOUNTNAME` con i nomi effettivi. Sostituire quindi `4` con l'effettivo servizio versione di configurazione e immettere il comando. Quando richiesto, immettere la password dell'account di accesso del cluster.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash
Sostituire `myCluster` con il nome del cluster in modo corretto maiuscole e minuscole.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Sostituire `myAccount` con il nome di account di archiviazione effettiva. Sostituire quindi `4` con l'effettivo servizio versione di configurazione e immettere il comando:

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Sostituire `CLUSTERNAME` con il nome del cluster in modo corretto maiuscole e minuscole in entrambi gli script. Prima di tutto identificare la versione di configurazione del servizio in uso immettendo il comando seguente:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Sostituire `ACCOUNTNAME` con il nome di account di archiviazione effettiva. Sostituire quindi `4` con l'effettivo servizio versione di configurazione e immettere il comando:

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

La ripetizione dell'esecuzione dell'azione script __non__ aggiorna la chiave, perché lo script verifica se esiste già una voce per l'account di archiviazione. Se esiste già una voce, non viene apportata alcuna modifica.

Per risolvere il problema, è necessario rimuovere la voce esistente per l'account di archiviazione, Eseguire i seguenti passaggi per rimuovere la voce esistente:

1. In un Web browser aprire l'interfaccia utente Web di Ambari per il cluster HDInsight. L'URI è `https://CLUSTERNAME.azurehdinsight.net`. Sostituire `CLUSTERNAME` con il nome del cluster.

    Quando richiesto, immettere l'utente e la password di accesso HTTP per il cluster.

2. Dall'elenco di servizi nella parte sinistra della pagina selezionare __HDFS__. Selezionare quindi la scheda __Configs__ (Configurazioni) al centro della pagina.

3. Nel campo __Filter__ (Filtro) immettere il valore __fs.azure.account__. Vengono restituite le voci per eventuali account di archiviazione aggiunti al cluster. Sono disponibili due tipi di voci, ovvero __keyprovider__ e __key__. Entrambi i tipi contengono il nome dell'account di archiviazione come parte del nome della chiave.

    Le voci di esempio seguenti sono relative a un account di archiviazione denominato __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Dopo avere identificato le chiavi per l'account di archiviazione da rimuovere, usare l'icona '-' rossa a destra della voce per eliminarla. Usare quindi il pulsante __Salva__ per salvare le modifiche apportate.

5. Dopo il salvataggio delle modifiche, usare l'azione script per aggiungere l'account di archiviazione e il nuovo valore della chiave al cluster.

### <a name="poor-performance"></a>Prestazioni non ottimali

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che le prestazioni non siano ottimali. L'accesso ai dati in un'area diversa comporta l'invio di traffico di rete all'esterno del data center di Azure di un'area specifica e la trasmissione tramite Internet pubblico e ciò può introdurre latenza.

### <a name="additional-charges"></a>Costi aggiuntivi

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che la fatturazione di Azure includa addebiti di uscita aggiuntivi. Viene applicato un addebito di uscita quando i dati escono dal data center di un'area, anche se il traffico è destinato a un altro data center di Azure in un'area diversa.

## <a name="next-steps"></a>Passaggi successivi

Fino a ora sono state date le informazioni su come aggiungere altri account di archiviazione a un cluster HDInsight esistente. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md)
