---
title: Acquisire una VM Linux con l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come acquisire e generalizzare l&quot;immagine di una macchina virtuale di Azure basata su Linux usando i dischi gestiti creati con l&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8433f326f04d0585e538695c419e451323c7869b
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>Come generalizzare e acquisire una macchina virtuale Linux
Per riutilizzare le macchine virtuali distribuite e configurate in Azure, si acquisisce un'immagine della macchina virtuale. Il processo prevede inoltre la generalizzazione della macchina virtuale per rimuovere le informazioni personali dell'account prima di distribuire nuove macchine virtuali dall'immagine. Questo articolo descrive come acquisire un'immagine di macchina virtuale con l'interfaccia della riga di comando di Azure 2.0 tramite Azure Managed Disks. Questi dischi vengono gestiti dalla piattaforma Azure e non richiedono alcuna pianificazione o alcuna posizione per l'archiviazione. Per altre informazioni, vedere [Azure Managed Disks Overview](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Panoramica di Azure Managed Disks). Questo articolo illustra come acquisire una VM Linux tramite l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!TIP]
> Per creare una copia della VM Linux esistente con il relativo stato specializzato per il backup o il debug, vedere [Creare una copia di una macchina virtuale Linux in esecuzione su Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per caricare un disco rigido virtuale Linux da una macchina virtuale locale, vedere [Caricare e creare una VM Linux da un'immagine disco personalizzata](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  


## <a name="before-you-begin"></a>Prima di iniziare
Accertarsi che siano soddisfatti i prerequisiti seguenti:

* **Macchina virtuale Azure creata nel modello di distribuzione Resource Manager**: se non è stata creata una VM Linux, è possibile usare il [portale](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), l'[interfaccia della riga di comando di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oppure i [modelli di Resource Manager](cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Configurare la macchina virtuale in base alle esigenze. Ad esempio, [aggiungere dischi dati](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), applicare aggiornamenti e installare applicazioni. 

È necessario anche aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi base per acquisire l'immagine di una macchina virtuale Linux in Azure. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, a partire da [qui](#detailed-steps). Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. Alcuni esempi di nomi dei parametri sono `myResourceGroup`, `myVM` e `myImage`.

1. Eseguire il deprovisioning della macchina virtuale di origine:

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. Deallocare la macchina virtuale con [az vm deallocate](/cli/azure/vm#deallocate):

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Generalizzare la macchina virtuale con [az vm generalize](/cli/azure/vm#generalize):
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. Creare un'immagine dalla risorsa macchina virtuale con [az image create](/cli/azure/image#create):
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. Creare una macchina virtuale dalla risorsa di immagine con [az vm create](/cli/azure/vm#create):

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>Procedura dettagliata
Nei passaggi seguenti vengono eseguiti il deprovisioning di una macchina virtuale esistente, la deallocazione e la generalizzazione della risorsa macchina virtuale e la creazione di un'immagine. È possibile usare questa immagine per creare macchine virtuali in qualsiasi gruppo di risorse all'interno della sottoscrizione. Questo processo dà ad [Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) un vantaggio rispetto ai dischi non gestiti. Con i dischi non gestiti, si crea una copia di BLOB del disco rigido virtuale sottostante ed è possibile solamente creare macchine virtuali nello stesso account di archiviazione in cui si trova il BLOB del disco rigido virtuale copiato. Con i dischi gestiti, si crea una risorsa immagine che può essere distribuita nell'intera sottoscrizione.

## <a name="step-1-remove-the-azure-linux-agent"></a>Passaggio 1: Rimuovere l'agente Linux di Azure
Per preparare la macchina virtuale alla generalizzazione, si esegue il deprovisioning della macchina virtuale usando l'agente di macchine virtuali di Azure per eliminare file e dati. Eseguire il comando **waagent** con il parametro **deprovision** sulla macchina virtuale Linux di destinazione. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Connettersi alla VM Linux tramite un client SSH.
2. Nella finestra SSH digitare il comando seguente:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Eseguire questo comando solo su una VM che si intende acquisire come immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione.
 
3. Digitare **y** per continuare. È possibile aggiungere il parametro **-force** per evitare questo passaggio di conferma.
4. Dopo aver eseguito il comando, digitare **exit**. Questo passaggio chiude il client SSH.

## <a name="step-2-capture-the-vm"></a>Passaggio 2: Acquisire la macchina virtuale
Usare l'interfaccia della riga di comando di Azure 2.0 per generalizzare e acquisire la macchina virtuale. Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono **myResourceGroup**, **myVnet** e **myVM**.

1. Deallocare la macchina virtuale su cui è stato eseguito il deprovisioning con [az vm deallocate](/cli//azure/vm#deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Generalizzare la macchina virtuale con [az vm generalize](/cli//azure/vm#generalize). L'esempio seguente generalizza la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. Creare ora un'immagine della risorsa macchina virtuale con [az image create](/cli//azure/image#create). Nell'esempio seguente viene creata un'immagine denominata `myImage` nel gruppo di risorse `myResourceGroup` usando la risorsa macchina virtuale denominata `myVM`:
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > L'immagine viene creata nello stesso gruppo di risorse della macchina virtuale di origine. Con questa immagine è possibile creare macchine virtuali in qualsiasi gruppo di risorse all'interno della sottoscrizione. Da una prospettiva di gestione, si consiglia di creare un gruppo di risorse specifico per le risorse e le immagini della macchina virtuale.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passaggio 3: Distribuire una VM dall'immagine acquisita
Creare una macchina virtuale usando l'immagine creata con [az vm create](/cli/azure/vm#create). L'esempio seguente crea una macchina virtuale denominata `myVMDeployed` dall'immagine `myImage`:

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

Con i dischi gestiti, è possibile creare macchine virtuali da un'immagine in qualsiasi gruppo di risorse all'interno della sottoscrizione. Questo comportamento è diverso rispetto ai dischi non gestiti. In quel caso si poteva solamente creare macchine virtuali nello stesso account di archiviazione del disco rigido virtuale di origine. Per creare una macchina virtuale in un gruppo di risorse diverso rispetto all'immagine, specificare l'ID di risorsa completa per l'immagine. Usare [az image list](/cli/azure/image#list) per visualizzare un elenco di immagini. L'output è simile all'esempio seguente:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

L'esempio seguente usa **az vm create** per creare una macchina virtuale in un gruppo di risorse diverso rispetto all'immagine di origine, specificando l'ID di risorsa immagine:

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>Verificare la distribuzione
Connettersi ora con SSH alla macchina virtuale creata per verificare la distribuzione e iniziare a usare la nuova VM. Per connettersi tramite SSH, trovare l'indirizzo IP o il nome di dominio completo della macchina virtuale con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>Passaggi successivi
È possibile creare più macchine virtuali da un'immagine di macchina virtuale di origine. Se è necessario apportare modifiche all'immagine: 

- Creare una macchina virtuale da un'immagine dell'utente.
- Apportare aggiornamenti o modifiche alla configurazione.
- Seguire nuovamente i passaggi per eseguire il deprovisioning, deallocare, generalizzare e creare un'immagine.
- Usare la nuova immagine per le distribuzioni future. Se si desidera, eliminare l'immagine originale.

Per altre informazioni sulla gestione delle macchine virtuali con l'interfaccia della riga di comando, vedere [Interfaccia della riga di comando di Azure 2.0](/cli/azure/overview).

