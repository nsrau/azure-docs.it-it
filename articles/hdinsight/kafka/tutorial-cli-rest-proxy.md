---
title: "Esercitazione: Creare un cluster abilitato per proxy REST Apache Kafka in HDInsight con l'interfaccia della riga di comando di Azure"
description: Informazioni su come eseguire operazioni di Apache Kafka usando un proxy REST Kafka in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 44951fc19f36bb6652caf79ded96484bcc4b38f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503141"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Esercitazione: Creare un cluster abilitato per proxy REST Apache Kafka in HDInsight con l'interfaccia della riga di comando di Azure

Questa esercitazione illustra come creare un cluster [abilitato per proxy REST](./rest-proxy.md) Apache Kafka in Azure HDInsight con l'interfaccia della riga di comando di Azure. Azure HDInsight è un servizio di analisi open source, gestito e ad ampio spettro per le aziende. Apache Kafka è una piattaforma di streaming open source distribuita. Viene spesso usata come broker di messaggi perché offre funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione. Il proxy REST Kafka consente di interagire con il cluster Kafka attraverso un'[API REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) tramite HTTP. L'interfaccia della riga di comando di Azure è l'esperienza di riga di comando multipiattaforma Microsoft per la gestione delle risorse di Azure.

Possono accedere all'API Apache Kafka solo risorse interne alla stessa rete virtuale. È possibile accedere al cluster direttamente tramite SSH. Per connettere altri servizi, reti o macchine virtuali ad Apache Kafka, è necessario prima di tutto creare una rete virtuale e quindi creare le risorse all'interno della rete. Per altre informazioni, vedere [Connettersi ad Apache Kafka da una rete locale](./apache-kafka-connect-vpn-gateway.md).

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Prerequisiti per il proxy REST Kafka
> * Creare un cluster Apache Kafka con l'interfaccia della riga di comando di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un'applicazione registrata con Azure AD. Le applicazioni client scritte per interagire con il proxy REST Kafka useranno l'ID e il segreto di questa applicazione per l'autenticazione con Azure. Per altre informazioni, vedere [Registrare un'applicazione con Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md).

* Un gruppo di sicurezza di Azure AD con l'applicazione registrata come membro. Questo gruppo di sicurezza verrà usato per controllare quali applicazioni sono autorizzate a interagire con il proxy REST. Per ulteriori informazioni sulla creazione di gruppi di Azure AD, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Interfaccia della riga di comando di Azure. Assicurarsi che sia installata almeno la versione 2.0.79. Vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Creare un cluster Apache Kafka

1. Accedere alla sottoscrizione di Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Impostare le variabili di ambiente. L'uso delle variabili in questa esercitazione si basa su Bash. Per altri ambienti saranno necessarie piccole modifiche.

    |Variabile | Descrizione |
    |---|---|
    |resourceGroupName|Sostituire RESOURCEGROUPNAME con il nome del nuovo gruppo di risorse.|
    |posizione|Sostituire LOCATION con l'area in cui verrà creato il cluster. Per un elenco delle località valide, usare il comando `az account list-locations`|
    |clusterName|Sostituire CLUSTERNAME con un nome univoco a livello globale per il nuovo cluster.|
    |storageAccount|Sostituire STORAGEACCOUNTNAME con il nome del nuovo account di archiviazione.|
    |httpPassword|Sostituire PASSWORD con la password dell'account di accesso del cluster, **admin**.|
    |sshPassword|Sostituire PASSWORD con una password per il nome utente Secure Shell, **sshuser**.|
    |securityGroupName|Sostituire SECURITYGROUPNAME con il nome del gruppo di sicurezza AAD client per il proxy REST Kafka. La variabile verrà passata al parametro `--kafka-client-group-name` per `az-hdinsight-create`.|
    |securityGroupID|Sostituire SECURITYGROUPID con l'ID del gruppo di sicurezza AAD client per il proxy REST Kafka. La variabile verrà passata al parametro `--kafka-client-group-id` per `az-hdinsight-create`.|
    |storageContainer|Contenitore di archiviazione che verrà usato dal cluster. Per questa esercitazione, lasciare il valore invariato. Questa variabile verrà impostata con il nome del cluster.|
    |workernodeCount|Numero di nodi di lavoro nel cluster. Per questa esercitazione, lasciare il valore invariato. Per garantire una disponibilità elevata, Kafka richiede un minimo di 3 nodi di lavoro|
    |clusterType|Tipo del cluster HDInsight. Per questa esercitazione, lasciare il valore invariato.|
    |clusterVersion|Versione del cluster HDInsight. Per questa esercitazione, lasciare il valore invariato. Per il proxy REST Kafka, la versione minima richiesta per il cluster è la versione 4.0.|
    |componentVersion|Versione di Kafka. Per questa esercitazione, lasciare il valore invariato. Per il proxy REST Kafka, la versione minima richiesta per il componente è la versione 2.1.|

    Aggiornare le variabili con i valori desiderati. Immettere quindi i comandi dell'interfaccia della riga di comando per impostare le variabili di ambiente.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Creare il gruppo di risorse](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) immettendo il comando seguente:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Creare un account di archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) immettendo il comando seguente:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Estrarre la chiave primaria](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) dall'account di archiviazione di Azure e archiviarla in una variabile immettendo il comando seguente:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Creare un contenitore di archiviazione di Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) immettendo il comando seguente:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Creare il cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). Prima di immettere il comando, osservare i parametri seguenti:

    1. Parametri obbligatori per i cluster Kafka:

        |Parametro | Descrizione|
        |---|---|
        |--type|Il valore deve essere **Kafka**.|
        |--workernode-data-disks-per-node|Numero di dischi dati da usare per ogni nodo di lavoro. HDInsight Kafka è supportato solo con i dischi dati. In questa esercitazione viene usato il valore **2**.|

    1. Parametri obbligatori per il proxy REST Kafka:

        |Parametro | Descrizione|
        |---|---|
        |--kafka-management-node-size|Dimensioni del nodo. In questa esercitazione viene usato il valore **Standard_D4_v2**.|
        |--kafka-client-group-id|ID del gruppo di sicurezza AAD client per il proxy REST Kafka. Il valore viene passato dalla variabile **$securityGroupID**.|
        |--kafka-client-group-name|Nome del gruppo di sicurezza AAD client per il proxy REST Kafka. Il valore viene passato dalla variabile **$securityGroupName**.|
        |--version|La versione del cluster HDInsight deve essere almeno 4.0. Il valore viene passato dalla variabile **$clusterVersion**.|
        |--component-version|La versione di Kafka deve essere almeno 2.1. Il valore viene passato dalla variabile **$componentVersion**.|
    
        Se si vuole creare il cluster senza proxy REST, eliminare `--kafka-management-node-size`, `--kafka-client-group-id`e `--kafka-client-group-name` dal comando `az hdinsight create`.

    1. Se si dispone di una rete virtuale esistente, aggiungere i parametri `--vnet-name` e `--subnet` e i relativi valori.

    Immettere il comando seguente per creare il cluster:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Il processo di creazione del cluster può richiedere alcuni minuti, in genere circa 15.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'articolo, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Immettere tutti o alcuni dei comandi seguenti per rimuovere le risorse:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato un cluster abilitato per proxy REST Apache Kafka in Azure HDInsight usando l'interfaccia della riga di comando di Azure, usare il codice Python per interagire con il proxy REST:

> [!div class="nextstepaction"]
> [Creare l'applicazione di esempio](./rest-proxy.md#client-application-sample)
