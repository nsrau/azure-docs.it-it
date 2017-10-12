---
title: Aggiungere altri account di Archiviazione di Azure a HDInsight | Documentazione Microsoft
description: Informazioni su come aggiungere altri account di Archiviazione di Azure a un cluster HDInsight esistente.
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/04/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 0853e8605e07c28867676e9c13b89263ade67c88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Aggiungere altri account di archiviazione a HDInsight

Informazioni su come usare le azioni script per aggiungere altri account di archiviazione di Azure in HDInsight. Nella procedura descritta in questo documento viene aggiunto un account di archiviazione a un cluster HDInsight basato su Linux esistente.

> [!IMPORTANT]
> Le informazioni in questo documento illustrano come aggiungere altre risorse di archiviazione a un cluster dopo la creazione. Per informazioni sull'aggiunta di account di archiviazione durante la creazione del cluster, vedere [Configurare cluster in HDInsight con Hadoop, Spark, Kafka e altro](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="how-it-works"></a>Funzionamento

Lo script accetta i parametri seguenti:

* __Nome dell'account di archiviazione di Azure__: nome dell'account di archiviazione da aggiungere al cluster HDInsight. Dopo l'esecuzione dello script, HDInsight è in grado di leggere e scrivere i dati archiviati in questo account di archiviazione.

* __Chiave dell'account di archiviazione di Azure__: chiave che concede l'accesso all'account di archiviazione.

* __-p__ (facoltativo): se questo parametro è specificato, la chiave non viene crittografata e viene archiviata nel file core-site.xml come testo normale.

Durante l'elaborazione, lo script esegue le azioni seguenti:

* Se la chiave dell'account di archiviazione esiste già nella configurazione del file core-site.xml per il cluster, lo script viene chiuso e non vengono eseguite altre azioni.

* Verifica che l'account di archiviazione esista e sia accessibile tramite la chiave.

* Crittografa la chiave mediante le credenziali del cluster,

* Aggiunge l'account di archiviazione al file core-site.xml.

* Arresta e riavvia i servizi Oozie, YARN, MapReduce2 e Hadoop Distributed File System. L'arresto e l'avvio di questi servizi consente di usare il nuovo account di archiviazione.

> [!WARNING]
> L'uso di un account di archiviazione in una località diversa rispetto al cluster HDInsight non è supportato.

## <a name="the-script"></a>Lo script

__Percorso dello script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisiti__:

* Lo script deve essere applicato ai __nodi head__.

## <a name="to-use-the-script"></a>Per usare lo script

È possibile usare lo script tramite il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure 1.0. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

> [!IMPORTANT]
> Nella procedura descritta nel documento di personalizzazione usare le informazioni seguenti per applicare questo script:
>
> * Sostituire l'URI di esempio dell'azione script con l'URI per questo script (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh).
> * Sostituire eventuali parametri di esempio con il nome dell'account di archiviazione di Azure e la chiave dell'account di archiviazione da aggiungere al cluster. Se si usa il portale di Azure, questi parametri devono essere separati da uno spazio.
> * Non è necessario contrassegnare lo script come __Persistente__, perché aggiorna direttamente la configurazione Ambari per il cluster.

## <a name="known-issues"></a>Problemi noti

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Account di archiviazione non visualizzati nel portale di Azure o negli strumenti

Quando si visualizza il cluster HDInsight nel Portale di Azure, selezionando la voce __Account di archiviazione__ in __Proprietà__ non vengono mostrati gli account di archiviazione aggiunti tramite questa azione script. L'account di archiviazione aggiuntivo non viene inoltre visualizzato in Azure PowerShell e nell'interfaccia della riga di comando di Azure.

Le informazioni di archiviazione non vengono mostrate perché lo script modifica solo la configurazione di core-site.xml per il cluster. Queste informazioni non vengono usate durante il recupero delle informazioni del cluster tramite le API di gestione di Azure.

Per visualizzare le informazioni sull'account di archiviazione aggiunte al cluster tramite lo script, usare l'API REST Ambari. Usare i comandi seguenti per recuperare queste informazioni per il cluster:

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> Impostare `$clusterName` sul nome del cluster HDInsight. Impostare `$storageAccountName` sul nome dell'account di archiviazione. Quando richiesto, immettere (admin) e password di accesso al cluster.

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> Impostare `$PASSWORD` sulla password dell'account (admin) di accesso al cluster. Impostare `$CLUSTERNAME` sul nome del cluster HDInsight. Impostare `$STORAGEACCOUNTNAME` sul nome dell'account di archiviazione.
>
> Queste esempio usa [curl (http://curl.haxx.se/)](http://curl.haxx.se/) e [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) per recuperare e analizzare i dati JSON.

Quando si usa questo comando, sostituire __CLUSTERNAME__ con il nome del cluster HDInsight. Sostituire __PASSWORD__ con la password di accesso HTTP per il cluster. Sostituire __STORAGEACCOUNT__ con il nome dell'account di archiviazione aggiunto usando l'azione script. Le informazioni restituite da questo comando sono simili al testo seguente:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Questo testo è un esempio di chiave crittografata, usata per accedere all'account di archiviazione.

### <a name="unable-to-access-storage-after-changing-key"></a>Non è possibile accedere alla risorsa di archiviazione dopo la modifica della chiave

Se si modifica la chiave per un account di archiviazione, HDInsight non potrà più accedere all'account di archiviazione. HDInsight usa una copia memorizzata nella cache della chiave in core-site.xml per il cluster. Questa copia memorizzata nella cache deve essere aggiornata in modo che corrisponda alla nuova chiave.

La ripetizione dell'esecuzione dell'azione script __non__ aggiorna la chiave, perché lo script verifica se esiste già una voce per l'account di archiviazione. Se esiste già una voce, non viene apportata alcuna modifica.

Per risolvere il problema, è necessario rimuovere la voce esistente per l'account di archiviazione, Eseguire i seguenti passaggi per rimuovere la voce esistente:

1. In un Web browser aprire l'interfaccia utente Web di Ambari per il cluster HDInsight. L'URI è https://CLUSTERNAME.azurehdinsight.net. Sostituire __CLUSTERNAME__ con il nome del cluster.

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

> [!WARNING]
> L'uso di un account di archiviazione in un'area diversa rispetto al cluster HDInsight non è supportato.

### <a name="additional-charges"></a>Costi aggiuntivi

Se l'account di archiviazione si trova in un'area diversa rispetto al cluster HDInsight, è possibile che la fatturazione di Azure includa addebiti di uscita aggiuntivi. Viene applicato un addebito di uscita quando i dati escono dal data center di un'area, anche se il traffico è destinato a un altro data center di Azure in un'area diversa.

> [!WARNING]
> L'uso di un account di archiviazione in un'area diversa rispetto al cluster HDInsight non è supportato.

## <a name="next-steps"></a>Passaggi successivi

Fino a ora sono state date le informazioni su come aggiungere altri account di archiviazione a un cluster HDInsight esistente. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md)
