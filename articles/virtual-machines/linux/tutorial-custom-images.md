---
title: Creare immagini di macchine virtuali personalizzate con l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: 'Esercitazione: creare un&quot;immagine di macchina virtuale personalizzata tramite l&quot;interfaccia della riga di comando di Azure.'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando

In questa esercitazione verrà spiegato come creare un'immagine personalizzata di una macchina virtuale di Azure. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. Quando si crea un'immagine personalizzata, la macchina virtuale e tutti i dischi sono inclusi nell'immagine. 

I passaggi descritti in questa esercitazione possono essere eseguiti usando la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.

## <a name="create-an-image"></a>Creare un'immagine

Per creare un'immagine di una macchina virtuale, è necessario preparare la macchina virtuale eseguendo il deprovisioning, la deallocazione e contrassegnare quindi la macchina virtuale di origine come generalizzata.

### <a name="deprovision-the-vm"></a>Eseguire il deprovisioning della macchina virtuale 

Il deprovisioning generalizza la macchina virtuale rimuovendo le informazioni specifiche del computer. La generalizzazione rende possibile distribuire più macchine virtuali da una singola immagine. Durante il deprovisioning, il nome host viene reimpostato su `localhost.localdomain`. Vengono eliminati anche i lease DHCP memorizzati nella cache, le chiavi host SSH, le configurazioni nameserver e le password radicele.

Per eseguire il deprovisioning della macchina virtuale, usare l'agente di macchine virtuali di Azure (waagent). L'agente di macchine virtuali di Azure viene installato sulla macchina virtuale e gestisce il provisioning e l'interazione con il controller di infrastruttura di Azure. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](agent-user-guide.md).

Connettersi alla macchina virtuale tramite SSH ed eseguire il comando per effettuare il deprovisioning della macchina virtuale. Con l'argomento `+user` vengono eliminati anche l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale.

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
```
Chiudere la sessione SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Deallocare e contrassegnare la macchina virtuale come generalizzata

Per creare un'immagine, è necessario deallocare la macchina virtuale. Deallocare la macchina virtuale con il comando [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

Infine, impostare lo stato della macchina virtuale come generalizzato con il comando [az vm generalize](/cli//azure/vm#generalize).
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>Acquisire l'immagine

È ora possibile creare un'immagine della macchina virtuale con il comando [az image create](/cli//azure/image#create). Nell'esempio seguente viene creata un'immagine denominata `myImage` dalla macchina virtuale denominata `myVM`.
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>Creare una macchina virtuale da un'immagine

È possibile creare una macchina virtuale usando un'immagine con il comando [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una macchina virtuale denominata `myVMfromImage` dall'immagine denominata `myImage`.

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata descritta la creazione di immagini di macchine virtuali personalizzate. Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

[Creare macchine virtuali a disponibilità elevata](tutorial-availability-sets.md).


