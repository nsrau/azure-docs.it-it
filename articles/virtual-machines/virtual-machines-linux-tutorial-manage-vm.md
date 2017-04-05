---
title: Gestire macchine virtuali Linux con l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: 'Esercitazione: Gestire macchine virtuali Linux con l&quot;interfaccia della riga di comando di Azure'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 14d3907d9ddbe42c9f919ce4d29ba7d58c7c5296
ms.lasthandoff: 03/29/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Gestire macchine virtuali Linux con l'interfaccia della riga di comando di Azure

In questa esercitazione viene creata una macchina virtuale e si eseguono attività di gestione comuni, come l'aggiunta di un disco, l'automazione dell'installazione di software e la creazione di uno snapshot della macchina virtuale. 

Per completare questa esercitazione, verificare di aver installato l'ultima versione dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="step-1--log-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Prima di tutto, aprire una finestra del terminale e accedere alla sottoscrizione di Azure usando il comando [az login](/cli/azure/#login).

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>Passaggio 2: Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. In questo esempio viene creato un gruppo di risorse denominato `myResourceGroup` nell'area `westeurope`. 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>Passaggio 3: Preparare la configurazione

Quando si distribuisce una macchina virtuale, è possibile usare **cloud-init** per automatizzare operazioni di configurazione come l'installazione di pacchetti, la creazione di file e l'esecuzione di script. In questa esercitazione vengono automatizzate le operazioni di configurazione di due elementi:

- Installazione di un server Web NGINX.
- Provisioning di un secondo disco nella macchina virtuale.

Dal momento che la configurazione **cloud-init** viene eseguita in fase di distribuzione della macchina virtuale, è necessario definire una configurazione **cloud-init** prima di creare la macchina virtuale.

Creare un file denominato `cloud-init.txt` e copiarvi il contenuto seguente. Questa configurazione installa il pacchetto NGINX ed esegue i comandi per formattare e montare il secondo disco.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>Passaggio 4: Creare la macchina virtuale

Crea una macchina virtuale usando il comando [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

Per la creazione di una macchina virtuale sono disponibili diverse opzioni, ad esempio l'immagine del sistema operativo, il ridimensionamento del disco e le credenziali amministrative. In questo esempio viene creata una macchina virtuale denominata `myVM` che esegue Ubuntu. Viene creato un disco da 50 GB e collegato alla macchina virtuale usando l'argomento `--data-disk-sizes-gb`. L'argomento `--custom-data` accetta la configurazione cloud-init e la inserisce temporaneamente nella macchina virtuale. Infine, se non esistono già vengono create anche le chiavi SSH.

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure restituisce le informazioni seguenti. Prendere nota dell'indirizzo IP pubblico, che viene usato quando si accede alla macchina virtuale. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

La macchina virtuale è stata distribuita, ma per completare la configurazione **cloud-init** potrebbero essere necessari alcuni minuti. 

## <a name="step-5--configure-firewall"></a>Passaggio 5: Configurare il firewall

Un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) di Azure consente di controllare il traffico in ingresso e in uscita per una o più macchine virtuali. Le regole di un gruppo di sicurezza di rete consentono o impediscono il traffico di rete su una porta specifica o un intervallo di porte. Queste regole possono includere un prefisso dell'indirizzo di origine, in modo che solo il traffico proveniente da un'origine specificata possa comunicare con una macchina virtuale.

Nella sezione precedente è stato installato il server Web NGINX. Senza una regola del gruppo di sicurezza di rete che consenta il traffico in ingresso sulla porta 80, il server Web non è accessibile da Internet. Questo passaggio illustra come creare la regola del gruppo di sicurezza di rete per consentire le connessioni in ingresso sulla porta 80.

### <a name="create-nsg-rule"></a>Creare una regola del gruppo di sicurezza di rete

Per creare una regola del gruppo di sicurezza di rete in ingresso, usare il comando [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port). L'esempio seguente apre la porta `80` per la macchina virtuale.

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

Passare all'indirizzo IP pubblico della macchina virtuale. Avendo impostato la regola del gruppo di sicurezza di rete, viene visualizzato il sito Web NGINX predefinito.

![Sito NGINX predefinito](./media/virtual-machines-linux-tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>Passaggio 6: Creare uno snapshot della macchina virtuale

Quando si crea uno snapshot del disco, viene creata una copia temporizzata di sola lettura del disco. In questo passaggio viene creato uno snapshot del disco del sistema operativo della macchina virtuale. Con uno snapshot del disco del sistema operativo, è possibile ripristinare rapidamente la macchina virtuale in uno stato specifico o creare una nuova macchina virtuale con uno stato identico.

### <a name="create-snapshot"></a>Creare uno snapshot

Prima di creare uno snapshot è necessario conoscere l'ID o il nome del disco. Usare il comando [az vm show](https://docs.microsoft.com/cli/azure/vm#show) per ottenere l'ID del disco. In questo esempio l'ID del disco viene archiviato in una variabile e usato in un passaggio successivo.

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Dopo aver ottenuto l'ID del disco, usare il comando seguente per creare lo snapshot.

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Creare un disco da uno snapshot

Lo snapshot può quindi essere convertito in un disco da usare per ricreare la macchina virtuale.

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Ripristinare una macchina virtuale da uno snapshot

Per illustrare il ripristino della macchina virtuale, eliminare la macchina virtuale esistente. 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

Quando si crea nuovamente la macchina virtuale, viene riutilizzata l'interfaccia di rete esistente. In questo modo vengono mantenute le configurazioni di sicurezza di rete.

Usare il comando [az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list) per ottenere il nome dell'interfaccia di rete. L'esempio inserisce il nome in una variabile denominata `nic`, che viene usata nel passaggio successivo.

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

Creare una nuova macchina virtuale dal disco dello snapshot.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

Prendere nota del nuovo indirizzo IP pubblico e passare a questo indirizzo con un browser Internet. Si noterà che NGINX è in esecuzione nella macchina virtuale ripristinata. 

### <a name="reconfigure-data-disk"></a>Riconfigurare il disco dati

Ora è possibile ricollegare il disco dati alla macchina virtuale. 

Usare il comando [az disk list](https://docs.microsoft.com/cli/azure/disk#list) per trovare il nome del disco dati. L'esempio inserisce il nome del disco in una variabile denominata `datadisk`, che viene usata nel passaggio successivo.

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Usare il comando [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) per collegare il disco.

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

Il disco deve anche essere montato nel sistema operativo. Per montare il disco, connettersi alla macchina virtuale ed eseguire `sudo mount /dev/sdc1 /datadrive` o un'altra operazione di montaggio del disco a scelta. 

## <a name="step-7--management-tasks"></a>Passaggio 7: Attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È consigliabile creare script per automatizzare le attività ripetitive o complesse. Usando l'interfaccia della riga di comando di Azure è possibile eseguire molte attività di gestione comuni dalla riga di comando o tramite script. 

### <a name="get-ip-address"></a>Ottenere l'indirizzo IP

Questo comando restituisce gli indirizzi IP pubblici e privati di una macchina virtuale.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>Ridimensionare la macchina virtuale

Per ridimensionare una macchina virtuale di Azure, è necessario conoscere il nome delle dimensioni disponibili nell'area di Azure scelta. Usare il comando [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes) per ottenere un elenco delle dimensioni.

```azurecli
az vm list-sizes --location westeurope --output table
```

Usare il comando [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize) per ridimensionare la macchina virtuale. 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>Arrestare la macchina virtuale

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>Avviare la macchina virtuale

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Esercitazione: [Creare macchine virtuali con bilanciamento del carico](./virtual-machines-linux-tutorial-load-balance-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Esempi: [Script di esempio dell'interfaccia della riga di comando di Azure](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
