---
title: Limitare l'accesso alla rete alle risorse PaaS - Interfaccia della riga di comando di Azure | Microsoft Docs
description: In questo articolo viene descritto come limitare l'accesso di rete alle risorse di Azure, ad esempio Archiviazione di Azure e il database SQL di Azure, tramite endpoint servizio di rete virtuale con l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: e52829723b41f9274251ebe7432aa659251c0da4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695128"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Limitare l'accesso di rete alle risorse PaaS con gli endpoint servizio di rete virtuale usando l'interfaccia della riga di comando di Azure

Gli endpoint servizio di rete virtuale consentono di limitare l'accesso di rete ad alcune risorse dei servizi di Azure a una subnet della rete virtuale. È anche possibile rimuovere l'accesso Internet alle risorse. Gli endpoint di servizio forniscono la connessione diretta dalla rete virtuale ai servizi di Azure supportati, consentendo di usare lo spazio indirizzi privato della rete virtuale per accedere ai servizi di Azure. Il traffico destinato alle risorse di Azure tramite gli endpoint di servizio rimane sempre nella rete backbone di Microsoft Azure. In questo articolo viene spiegato come:

* Creare una rete virtuale con una subnet
* Aggiungere una subnet e abilitare un endpoint di servizio
* Creare una risorsa di Azure e consentire l'accesso di rete alla risorsa da una sola subnet
* Distribuire una macchina virtuale (VM) in ogni subnet
* Verificare che venga consentito l'accesso a una risorsa da una subnet
* Verificare che venga rifiutato l'accesso a una risorsa da una subnet e da Internet

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa guida introduttiva richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

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
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Abilitare un endpoint di servizio 

È possibile abilitare gli endpoint di servizio solo per i servizi che supportano tali endpoint. Visualizzare i servizi abilitati per gli endpoint di servizio in una località di Azure con [az network vnet list-endpoint-services](/cli/azure/network/vnet). L'esempio seguente restituisce un elenco di servizi abilitati per gli endpoint servizio nell'area *eastus*. L'elenco dei servizi restituiti aumenterà nel corso del tempo perché altri servizi di Azure verranno abilitati per gli endpoint di servizio.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Creare una subnet aggiuntiva nella rete virtuale con [az network vnet subnet create](/cli/azure/network/vnet/subnet). In questo esempio per la subnet viene creato un endpoint di servizio per *Microsoft.Storage*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
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

Ogni gruppo di sicurezza di rete contiene numerosi [regole di sicurezza predefinite](security-overview.md#default-security-rules). La regola che segue esegue l'override di una regola di sicurezza predefinita che consente l'accesso in uscita a tutti gli indirizzi IP pubblici. Il `destination-address-prefix "Internet"` opzione Nega accesso in uscita a tutti gli indirizzi IP. La regola precedente esegue l'override di questa regola, a causa della priorità più alta, che consente l'accesso agli indirizzi IP pubblici di Archiviazione di Azure.

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

La regola seguente consente il traffico SSH in ingresso verso la subnet da qualsiasi posizione. La regola esegue l'override di una regola di sicurezza predefinita che rifiuta tutto il traffico in ingresso da Internet. SSH è consentita per la subnet in modo che la connettività può essere testata in un passaggio successivo.

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

## <a name="restrict-network-access-to-a-resource"></a>Limitare l'accesso di rete a una risorsa

I passaggi necessari per limitare l'accesso di rete alle risorse create tramite i servizi di Azure abilitati per gli endpoint di servizio variano a seconda dei servizi. Vedere la documentazione relativa ai singoli servizi per i passaggi specifici. La parte rimanente di questo articolo include, a titolo di esempio, i passaggi da eseguire per limitare l'accesso di rete per un account di archiviazione di Azure.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione di Azure con [az storage account create](/cli/azure/storage/account). Sostituire `<replace-with-your-unique-storage-account-name>` con un nome univoco per tutte le località di Azure, di lunghezza compresa tra 3 e 24 caratteri e costituito solo da numeri e lettere minuscole.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Dopo aver creato l'account di archiviazione, recuperare la stringa di connessione per l'account di archiviazione in una variabile con [az storage account show-connection-string](/cli/azure/storage/account). La stringa di connessione viene usata per creare una condivisione file in un passaggio successivo.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Visualizzare i contenuti della variabile e prendere nota del valore di **AccountKey** restituito nell'output, perché verrà usato in un passaggio successivo.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Creare una condivisione file nell'account di archiviazione

Creare una condivisione file nell'account di archiviazione con [az storage share create](/cli/azure/storage/share). In un passaggio successivo questa condivisione file verrà montata per verificare l'accesso di rete.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Negare l'accesso di rete a un account di archiviazione

Per impostazione predefinita, gli account di archiviazione accettano connessioni di rete da client di qualsiasi rete. Per limitare l'accesso alle reti selezionate, modificare l'azione predefinita impostandola su *Deny* con [az storage account update](/cli/azure/storage/account). Dopo che l'accesso di rete è stato negato, l'account di archiviazione non sarà accessibile da nessuna rete.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Abilitare l'accesso di rete da una subnet

Consentire l'accesso di rete all'account di archiviazione dalla subnet *privata* con [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Creare macchine virtuali

Per testare l'accesso di rete a un account di archiviazione, distribuire una VM in ogni subnet.

### <a name="create-the-first-virtual-machine"></a>Creare la prima macchina virtuale

Creare una macchina virtuale nella subnet *Public* con [az vm create](/cli/azure/vm). Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Dopo aver creato la macchina virtuale, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Prendere nota di **publicIpAddress** nell'output restituito. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo.

### <a name="create-the-second-virtual-machine"></a>Creare la seconda macchina virtuale

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Dopo la creazione, prendere nota di **publicIpAddress** nell'output restituito. Questo indirizzo viene usato per accedere alla macchina virtuale da Internet in un passaggio successivo.

## <a name="confirm-access-to-storage-account"></a>Verificare che venga consentito l'accesso a un account di archiviazione

Eseguire SSH nella VM *myVmPrivate*. Sostituire  *\<publicIpAddress >* con l'indirizzo IP pubblico del *myVmPrivate* della macchina virtuale.

```bash 
ssh <publicIpAddress>
```

Creare una cartella per un punto di montaggio:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Montare la condivisione file di Azure nella directory creata. Prima di eseguire il comando seguente, sostituire `<storage-account-name>` con il nome dell'account e `<storage-account-key>` con la chiave recuperata in [Creare un account di archiviazione](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Viene visualizzato il prompt `user@myVmPrivate:~$`. La condivisione file di Azure è stata montata correttamente in */mnt/MyAzureFileShare*.

Verificare che la VM non abbia connettività in uscita ad altri indirizzi IP pubblici:

```bash
ping bing.com -c 4
```

Non si ricevono risposte perché il gruppo di sicurezza di rete associato alla subnet *privata* non consente l'accesso in uscita a indirizzi IP pubblici diversi dagli indirizzi assegnati al servizio Archiviazione di Azure.

Uscire dalla sessione SSH alla macchina virtuale *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Verificare che venga negato l'accesso a un account di archiviazione

Usare il comando seguente per creare una sessione SSH con la VM *myVmPublic*. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico della macchina virtuale *myVmPublic*: 

```bash 
ssh <publicIpAddress>
```

Creare una directory per un punto di montaggio:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Provare a montare la condivisione file di Azure nella directory creata. Questo articolo presuppone che sia stata distribuita la versione più recente di Ubuntu. Se si usano versioni meno recenti di Ubuntu, vedere [Eseguire il montaggio in Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per istruzioni aggiuntive sul montaggio di condivisioni file. Prima di eseguire il comando seguente, sostituire `<storage-account-name>` con il nome dell'account e `<storage-account-key>` con la chiave recuperata in [Creare un account di archiviazione](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

L'accesso viene rifiutato e viene visualizzato un errore `mount error(13): Permission denied` perché la VM *myVmPublic* viene distribuita nella subnet *pubblica*. La subnet *pubblica* non ha un endpoint di servizio abilitato per Archiviazione di Azure e l'account di archiviazione consente l'accesso di rete solo dalla subnet *privata* e non dalla subnet *pubblica*.

Uscire dalla sessione SSH alla macchina virtuale *myVmPublic*.

Dal computer provare a visualizzare le condivisioni nell'account di archiviazione con [az storage share list](/cli/azure/storage/share?view=azure-cli-latest). Sostituire `<account-name>` e `<account-key>` con il nome e la chiave dell'account di archiviazione usati in [Creare un account di archiviazione](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

L'accesso viene rifiutato e viene visualizzato l'errore *This request is not authorized to perform this operation* (La richiesta non è autorizzata a eseguire questa operazione) perché il computer non si trova nella subnet *privata* della rete virtuale *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [az group delete](/cli/azure) per rimuoverli.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato abilitato un endpoint servizio per una subnet della rete virtuale. È stato illustrato che gli endpoint di servizio possono essere abilitati per le risorse distribuite con più servizi di Azure. È stato creato un account di archiviazione di Azure ed è stato limitato l'accesso di rete all'account di archiviazione solo alle risorse in una subnet della rete virtuale. Per altre informazioni sugli endpoint servizio, vedere [Panoramica degli endpoint servizio](virtual-network-service-endpoints-overview.md) e [Gestire le subnet](virtual-network-manage-subnet.md).

Se nell'account sono presenti più reti virtuali, può essere necessario connettere due reti virtuali per consentire alle risorse di ogni rete virtuale di comunicare tra loro. Per informazioni su come fare, vedere [Connettere reti virtuali](tutorial-connect-virtual-networks-cli.md).
