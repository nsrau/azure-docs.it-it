---
title: Condivisione dei file per cluster DC/OS di Azure
description: Creare e montare una condivisione di file per un cluster DC/OS nel servizio contenitore di Azure
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Creare e montare una condivisione di file per un cluster DC/OS

Questa esercitazione illustra nei dettagli come creare una condivisione file in Azure e come montarla in ogni agente e nel master del cluster DC/OS. La configurazione della condivisione file semplifica la condivisione di file tra i cluster ad esempio configurazione, accesso, log e altro ancora. Nell'esercitazione verranno eseguite le attività seguenti:

> [!div class="checklist"]
> * Creare un account di archiviazione di Azure
> * Creare una condivisione file
> * Montare la condivisione nel cluster DC/OS

È necessario un cluster DC/OS del servizio contenitore di Azure per completare i passaggi in questa esercitazione. Se necessario, questo [script di esempio](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) può crearne uno appositamente.

Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Creare una condivisione file in Microsoft Azure

Prima di usare una condivisione file di Azure con un cluster DC/OS del servizio contenitore di Azure, è necessario creare l'account di archiviazione e la condivisione file. Eseguire lo script seguente per creare l'account di archiviazione e la condivisione file. Aggiornare i parametri con valori appropriati dall'ambiente in uso.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montare la condivisione nel cluster

La condivisione file deve poi essere montata in ogni macchina virtuale all'interno del cluster. Questa attività viene eseguita tramite lo strumento/protocollo CIFS. È possibile eseguire manualmente l'operazione di montaggio su ogni nodo del cluster o eseguendo uno script su ogni nodo del cluster.

In questo esempio, vengono eseguiti due script, uno per montare la condivisione file di Azure e il secondo per eseguire questo script in ogni nodo del cluster DC/OS.

Servono innanzitutto il nome dell'account di archiviazione di Azure e la chiave di accesso. Eseguire i comandi seguenti per ottenere queste informazioni. Prendere nota di tutti i valori perché vengono usati in un passaggio successivo.

Nome account di archiviazione:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Chiave di accesso dell'account di archiviazione:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Ottenere poi il nome di dominio completo del master DC/OS e archiviarlo in una variabile.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Copiare la chiave privata nel nodo master. Questa chiave è necessaria per creare una connessione SSH con tutti i nodi del cluster. Aggiornare il nome utente se è stato usato un valore non predefinito al momento della creazione del cluster. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Creare una connessione SSH con il master (o il primo master) del cluster basato su DC/OS. Aggiornare il nome utente se è stato usato un valore non predefinito al momento della creazione del cluster.

```azurecli-interactive
ssh azureuser@$FQDN
```

Creare un file denominato **cifsMount.sh** e copiare il contenuto seguente nel file. 

Questo script viene usato per montare la condivisione file di Azure. Aggiornare le variabili `STORAGE_ACCT_NAME` e `ACCESS_KEY` con le informazioni raccolte in precedenza.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Creare un secondo file denominato **getNodesRunScript.sh** e copiare il contenuto seguente nel file. 

Questo script individua tutti i nodi del cluster e quindi esegue lo script **cifsMount.sh** per montare la condivisione file in ogni nodo.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Eseguire lo script per montare la condivisione file di Azure in tutti i nodi del cluster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

La condivisione file è ora accessibile dal percorso `/mnt/share/dcosshare` in ogni nodo del cluster.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata resa disponibile una condivisione file di Azure per un cluster DC/OS tramite questi passaggi:

> [!div class="checklist"]
> * Creare un account di archiviazione di Azure
> * Creare una condivisione file
> * Montare la condivisione nel cluster DC/OS

Passare alla prossima esercitazione per informazioni sull'integrazione di Registro contenitori di Azure. con DC/OS in Azure.  

> [!div class="nextstepaction"]
> [Bilanciare il carico delle applicazioni](container-service-dcos-acr.md)
