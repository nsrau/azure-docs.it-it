---
title: Limitare l'esfiltrazione dei dati ad Archiviazione di Azure - Interfaccia della riga di comando di AzureRestrict data exfiltration to Azure Storage - Azure CLI
description: In this article, you learn how to limit and restrict virtual network data exfiltration to Azure Storage resources with virtual network service endpoint policies using the Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271179"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gestire l'esfiltrazione dei dati agli account di archiviazione di Azure con i criteri degli endpoint del servizio di rete virtuale usando l'interfaccia della riga di comando di AzureManage data efiltration to Azure Storage accounts with virtual network service endpoint policies using the Azure CLI

I criteri degli endpoint del servizio di rete virtuale consentono di applicare il controllo di accesso agli account di Archiviazione di Azure dall'interno di una rete virtuale e agli endpoint del servizio. Si tratta di una chiave per proteggere i carichi di lavoro, gestire gli account di archiviazione consentiti e dove è consentita l'esfiltrazione dei dati.
In questo articolo vengono illustrate le operazioni seguenti:

* Creare una rete virtuale e aggiungere una subnet.
* Abilitare l'endpoint del servizio per Archiviazione di Azure.Enable service endpoint for Azure Storage.
* Creare due account di Archiviazione di Azure e consentire l'accesso di rete dalla subnet creata in precedenza.
* Creare criteri endpoint del servizio per consentire l'accesso solo a uno degli account di archiviazione.
* Distribuire una macchina virtuale (VM) nella subnet.
* Verificare l'accesso all'account di archiviazione consentito dalla subnet.
* Verificare che l'accesso sia negato all'account di archiviazione non consentito dalla subnet.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa guida introduttiva richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If]( /cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI. 

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nel percorso *eastus.*

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Creare una rete virtuale con una subnet con [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Abilitare un endpoint di servizio 

In questo esempio viene creato un endpoint del servizio per *Microsoft.Storage* per la subnet *Private:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Limitare l'accesso di rete per una subnet

Creare un gruppo di sicurezza di rete con il comando [az network nsg create](/cli/azure/network/nsg). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Associare la subnet del gruppo di sicurezza di rete alla subnet *privata* con [az network vnet subnet update](/cli/azure/network/vnet/subnet). L'esempio seguente associa il gruppo di sicurezza di rete *myNsgPrivate* alla subnet *privata*:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Creare le regole di sicurezza con [az network nsg rule create](/cli/azure/network/nsg/rule). La regola che segue consente l'accesso in uscita agli indirizzi IP pubblici assegnati al servizio Archiviazione di Azure: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Ogni gruppo di sicurezza di rete contiene diverse regole di [sicurezza predefinite.](security-overview.md#default-security-rules) La regola che segue esegue l'override di una regola di sicurezza predefinita che consente l'accesso in uscita a tutti gli indirizzi IP pubblici. L'opzione `destination-address-prefix "Internet"` nega l'accesso in uscita a tutti gli indirizzi IP pubblici. La regola precedente esegue l'override di questa regola, a causa della priorità più alta, che consente l'accesso agli indirizzi IP pubblici di Archiviazione di Azure.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

La regola seguente consente il traffico SSH in ingresso nella subnet da qualsiasi posizione. La regola esegue l'override di una regola di sicurezza predefinita che rifiuta tutto il traffico in ingresso da Internet. SSH è consentito alla subnet in modo che la connettività possa essere testata in un passaggio successivo.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Limitare l'accesso di rete agli account di Archiviazione di AzureRestrict network access to Azure Storage accounts

Questa sezione elenca i passaggi per limitare l'accesso alla rete per un account di Archiviazione di Azure dalla subnet specificata in una rete virtuale tramite l'endpoint del servizio.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare due account di archiviazione di Azure con [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Dopo aver creato gli account di archiviazione, recuperare la stringa di connessione per gli account di archiviazione in una variabile con [az storage account show-connection-string](/cli/azure/storage/account). La stringa di connessione viene usata per creare una condivisione file in un passaggio successivo.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visualizzare i contenuti della variabile e prendere nota del valore di **AccountKey** restituito nell'output, perché verrà usato in un passaggio successivo.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Creare una condivisione file nell'account di archiviazione

Creare una condivisione file nell'account di archiviazione con [az storage share create](/cli/azure/storage/share). In un passaggio successivo questa condivisione file verrà montata per verificare l'accesso di rete.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Negare l'accesso di rete all'account di archiviazioneDeny all network access to the storage account

Per impostazione predefinita, gli account di archiviazione accettano connessioni di rete dai client in qualsiasi rete. Per limitare l'accesso alle reti selezionate, modificare l'azione predefinita impostandola su *Deny* con [az storage account update](/cli/azure/storage/account). Dopo che l'accesso di rete è stato rifiutato, l'account di archiviazione non sarà accessibile da nessuna rete.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Abilitare l'accesso alla rete dalla subnet di rete virtualeEnable network access from virtual network subnet

Consentire l'accesso di rete all'account di archiviazione dalla subnet *privata* con [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Applicare i criteri per consentire l'accesso all'account di archiviazione validoApply policy to allow access to valid storage account

Azure Service Endpoint policies are only available for Azure Storage. Pertanto, verrà abilitato l'endpoint del servizio per *Microsoft.Storage* in questa subnet per questa configurazione di esempio.

I criteri degli endpoint del servizio vengono applicati agli endpoint del servizio. Inizieremo creando criteri endpoint del servizio. Creeremo quindi le definizioni dei criteri in base a questi criteri per gli account di archiviazione di Azure da elencare per questa subnet

Creare un criterio di endpoint di servizio

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Salvare l'URI della risorsa per l'account di archiviazione consentito in una variabile. Prima di eseguire il comando seguente, sostituire * \<il>-subscription-id* con il valore effettivo dell'ID sottoscrizione.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Creare & aggiungere una definizione di criteri per consentire l'account di archiviazione di Azure precedente ai criteri dell'endpoint del servizioCreate to add a policy definition for allowing the above Azure Storage account to the service endpoint policy

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

Aggiornare la subnet della rete virtuale da associare ai criteri dell'endpoint del servizio creati nel passaggio precedente

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Convalidare la restrizione di accesso agli account di archiviazione di AzureValidate access restriction to Azure Storage accounts

### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale

Per testare l'accesso di rete a un account di archiviazione, distribuire una macchina virtuale nella subnet.

Creare una macchina virtuale nella subnet *privata* con [az vm create](/cli/azure/vm). Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Dopo la creazione, prendere nota di **publicIpAddress** nell'output restituito. Questo indirizzo viene usato per accedere alla VM da Internet in un passaggio successivo.

### <a name="confirm-access-to-storage-account"></a>Verificare che venga consentito l'accesso a un account di archiviazione

Eseguire SSH nella VM *myVmPrivate*. Sostituire * \<publicIpAddress>* con l'indirizzo IP pubblico della macchina virtuale *myVmPrivate.Replace publicIpAddress*>with the public IP address of your myVmPrivate VM.

```bash 
ssh <publicIpAddress>
```

Creare una cartella per un punto di montaggio:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Montare la condivisione file di Azure nella directory creata. Prima di eseguire il comando riportato di seguito, sostituire * \<storage-account-key>* con il valore *AccountKey* di **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Viene visualizzato il prompt `user@myVmPrivate:~$`. La condivisione file di Azure è stata montata correttamente in */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Verificare che venga rifiutato l'accesso a un account di archiviazione

Dalla stessa macchina virtuale myVmPrivate , creare una directory per un punto di montaggio:From the same VM *myVmPrivate*, create a directory for a mount point:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Tentare di montare la condivisione file di Azure dall'account di archiviazione *notallowedstorageacc* alla directory creata. Questo articolo presuppone che sia stata distribuita la versione più recente di Ubuntu. Se si usano versioni meno recenti di Ubuntu, vedere [Eseguire il montaggio in Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per istruzioni aggiuntive sul montaggio di condivisioni file. 

Prima di eseguire il comando riportato di seguito, sostituire * \<storage-account-key>* con il valore *AccountKey* di **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Accesso negato e viene `mount error(13): Permission denied` visualizzato un errore perché questo account di archiviazione non è presente nell'elenco Consenti dei criteri dell'endpoint del servizio applicati alla subnet. 

Uscire dalla sessione SSH alla macchina virtuale *myVmPublic*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In this article, you applied a service endpoint policy over an Azure virtual network service endpoint to Azure Storage. Sono stati creati account di archiviazione di Azure e accesso limitato alla rete solo per determinati account di archiviazione (e quindi nesono negato altri) da una subnet di rete virtuale. Per altre informazioni sui criteri degli endpoint del servizio, vedere [Panoramica](virtual-network-service-endpoint-policies-overview.md)dei criteri degli endpoint del servizio.
