---
title: Creare immagini di macchine virtuali personalizzate con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: 'Esercitazione: creare un''immagine di macchina virtuale personalizzata tramite l''interfaccia della riga di comando di Azure.'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/21/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: d32980f05ad17a76793021d0a5355d597974a4e4
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando

Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. In questa esercitazione viene creata un'immagine personalizzata di una macchina virtuale di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Eseguire il deprovisioning e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.

## <a name="create-a-custom-image"></a>Creare un'immagine personalizzata

Per creare un'immagine di una macchina virtuale, è necessario preparare la macchina virtuale eseguendo il deprovisioning, la deallocazione e contrassegnare quindi la macchina virtuale di origine come generalizzata. Dopo aver preparato la macchina virtuale, è possibile creare un'immagine.

### <a name="deprovision-the-vm"></a>Eseguire il deprovisioning della macchina virtuale 

Il deprovisioning generalizza la macchina virtuale rimuovendo le informazioni specifiche del computer. La generalizzazione rende possibile distribuire più macchine virtuali da una singola immagine. Durante il deprovisioning, il nome host viene reimpostato su *hostlocale.dominiolocale*. Vengono eliminati anche i lease DHCP memorizzati nella cache, le chiavi host SSH, le configurazioni nameserver e le password radicele.

Per eseguire il deprovisioning della macchina virtuale, usare l'agente di macchine virtuali di Azure (waagent). L'agente di macchine virtuali di Azure viene installato sulla macchina virtuale e gestisce il provisioning e l'interazione con il controller di infrastruttura di Azure. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](agent-user-guide.md).

Connettersi alla macchina virtuale tramite SSH ed eseguire il comando per effettuare il deprovisioning della macchina virtuale. Con l'argomento `+user` vengono eliminati anche l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale.

Eseguire SSH sulla VM.
```bash
ssh azureuser@52.174.34.95
```
Eseguire il deprovisioning della VM.

```bash
sudo waagent -deprovision+user -force
```
Chiudere la sessione SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Deallocare e contrassegnare la macchina virtuale come generalizzata

Per creare un'immagine, è necessario deallocare la macchina virtuale. Deallocare la macchina virtuale con il comando [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Infine, impostare lo stato della macchina virtuale come generalizzato tramite [az vm generalize](/cli//azure/vm#generalize) in modo che la piattaforma Azure riconosca che la macchina virtuale è stata generalizzata. È possibile creare solo un'immagine da una VM generalizzata.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Creare l'immagine

È ora possibile creare un'immagine della macchina virtuale con il comando [az image create](/cli//azure/image#create). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM denominata *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Creare VM dall'immagine

Ora che è stata creata un'immagine, è possibile creare una o più nuove VM dall'immagine usando [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una VM denominata *myVMfromImage* dall'immagine denominata *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Gestione delle immagini 

Di seguito sono riportati alcuni esempi di attività comuni di gestione di immagini e la loro modalità di completamento tramite l'interfaccia della riga di comando di Azure.

Elencare tutte le immagini per nome in formato tabella.

```azurecli-interactive 
az image list \
  --resource-group myResourceGroup
```

Eliminare un'immagine. Questo esempio elimina l'immagine denominata *myOldImage* da *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale personalizzata. Si è appreso come:

> [!div class="checklist"]
> * Eseguire il deprovisioning e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Creare macchine virtuali a disponibilità elevata](tutorial-availability-sets.md).


