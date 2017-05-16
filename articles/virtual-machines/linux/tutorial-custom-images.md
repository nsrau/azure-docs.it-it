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
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 2ce92b3f0a21f80eb4294161d6d3a5275c992600
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando

In questa esercitazione verrà spiegato come definire un'immagine personalizzata di una macchina virtuale di Azure. Le immagini personalizzate consentono di creare VM usando un'immagine che si è già configurata. Le immagini personalizzate possono essere usate per l'avvio del precaricamento di file binari e applicazioni, configurazioni di applicazioni, dati di VM e altre configurazioni del sistema operativo. Quando si crea un'immagine personalizzata, vengono inclusi nell'immagine la VM che si personalizza e tutti i dischi associati.

Le procedure descritte in questa esercitazione possono essere completate usando l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.

## <a name="prepare-vm"></a>Preparare la VM

Per creare un'immagine di una macchina virtuale, è necessario preparare la macchina virtuale eseguendo il deprovisioning, la deallocazione e contrassegnare quindi la macchina virtuale di origine come generalizzata.

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
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

Infine, impostare lo stato della macchina virtuale come generalizzato tramite [az vm generalize](/cli//azure/vm#generalize) in modo che la piattaforma Azure riconosca che la macchina virtuale è stata generalizzata. È possibile creare solo un'immagine da una VM generalizzata.
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

## <a name="create-the-image"></a>Creare l'immagine

È ora possibile creare un'immagine della macchina virtuale con il comando [az image create](/cli//azure/image#create). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM denominata *myVM*.
   
```azurecli
az image create \
    --resource-group myResourceGroupImages \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Creare VM dall'immagine

Ora che è stata creata un'immagine, è possibile creare una o più nuove VM dall'immagine usando [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una VM denominata *myVMfromImage* dall'immagine denominata *myImage*.

```azurecli
az vm create \
    --resource-group myResourceGroupImages \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata descritta la creazione di immagini di macchine virtuali personalizzate. Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

[Creare macchine virtuali a disponibilità elevata](tutorial-availability-sets.md).


