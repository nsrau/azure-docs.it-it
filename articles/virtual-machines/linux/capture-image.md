---
title: Acquisire un'immagine di una macchina virtuale Linux in Azure usando l'interfaccia della riga di comando 2.0 | Microsoft Docs
description: Acquisire un'immagine di una macchina virtuale di Azure da usare per le distribuzioni di massa tramite l'interfaccia della riga di comando di Azure 2.0.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 19b573f77f2ee84600955d00d30bdb16c84e3623
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Come creare un'immagine di una macchina virtuale o un disco rigido virtuale

<!-- generalize, image - extended version of the tutorial-->

Per creare più copie di una macchina virtuale da usare in Azure, acquisire un'immagine della macchina virtuale o il disco rigido virtuale del sistema operativo. Per creare un'immagine, è necessario rimuovere le informazioni sull'account personale, in modo da poter distribuire più volte in totale sicurezza. Nei passaggi seguenti si eseguirà il deprovisioning di una macchina virtuale esistente e quindi la deallocazione e la creazione di un'immagine. È possibile usare questa immagine per creare macchine virtuali in qualsiasi gruppo di risorse all'interno della sottoscrizione.

Per creare una copia della macchina virtuale Linux esistente per il backup o il debug o per caricare un disco rigido virtuale Linux specializzato da una macchina virtuale locale, vedere [Caricare e creare una VM Linux da un'immagine disco personalizzata](upload-vhd.md).  

È possibile anche usare **Packer** per creare una configurazione personalizzata. Per altre informazioni sull'uso di Packer, vedere [Come usare Packer per creare immagini di macchine virtuali di Linux in Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Prima di iniziare
Accertarsi che siano soddisfatti i prerequisiti seguenti:

* È necessario aver creato una macchina virtuale di Azure nel modello di distribuzione Resource Manager tramite l'utilizzo di dischi gestiti. Se non è stata creata una macchina virtuale Linux, è possibile usare il [portale](quick-create-portal.md), l'[interfaccia della riga di comando di Azure](quick-create-cli.md) o i [modelli di Resource Manager](create-ssh-secured-vm-from-template.md). Configurare la macchina virtuale in base alle esigenze. Ad esempio, [aggiungere dischi dati](add-disk.md), applicare aggiornamenti e installare applicazioni. 

* È necessario anche aver installato l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

## <a name="quick-commands"></a>Comandi rapidi

Per una versione semplificata di questo argomento e per informazioni sulle procedure di testing e valutazione o sulle macchine virtuali in Azure, vedere [Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Passaggio 1: Eseguire il deprovisioning della macchina virtuale
Eseguire il deprovisioning della macchina virtuale usando l'agente della macchina virtuale di Azure per eliminare file e dati specifici della macchina. Usare il comando `waagent` con il parametro *-deprovision+user* nella macchina virtuale Linux di origine. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../windows/agent-user-guide.md).

1. Connettersi alla VM Linux tramite un client SSH.
2. Nella finestra SSH digitare il comando seguente:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Eseguire questo comando solo su una VM che si intende acquisire come immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione. Il parametro *+user* rimuove anche l'ultimo account utente di cui è stato effettuato il provisioning. Se invece si vuole mantenere le credenziali dell'account nella macchina virtuale, usare semplicemente *-deprovision* per non rimuovere l'account utente.
 
3. Digitare **y** per continuare. È possibile aggiungere il parametro **-force** per evitare questo passaggio di conferma.
4. Dopo aver eseguito il comando, digitare **exit**. Questo passaggio chiude il client SSH.

## <a name="step-2-create-vm-image"></a>Passaggio 2: Creare l'immagine della macchina virtuale
Usare l'interfaccia della riga di comando di Azure 2.0 per contrassegnare la macchina virtuale come generalizzata e acquisire l'immagine. Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myVnet* e *myVM*.

1. Deallocare la macchina virtuale su cui è stato eseguito il deprovisioning con [az vm deallocate](/cli//azure/vm#deallocate). L'esempio seguente dealloca la VM denominata *myVM* nel gruppo di risorse *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Contrassegnare la macchina virtuale come generalizzata con il comando [az vm generalize](/cli//azure/vm#generalize). Nell'esempio seguente la macchina virtuale denominata *myVM* viene contrassegnata come generalizzata nel gruppo di risorse *myResourceGroup*:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Creare ora un'immagine della risorsa macchina virtuale con [az image create](/cli//azure/image#create). Nell'esempio seguente viene creata un'immagine denominata *myImage* nel gruppo di risorse denominato *myResourceGroup* usando la risorsa della macchina virtuale denominata *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > L'immagine viene creata nello stesso gruppo di risorse della macchina virtuale di origine. Con questa immagine è possibile creare macchine virtuali in qualsiasi gruppo di risorse all'interno della sottoscrizione. Da una prospettiva di gestione, si consiglia di creare un gruppo di risorse specifico per le risorse e le immagini della macchina virtuale.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passaggio 3: Distribuire una VM dall'immagine acquisita
Creare una macchina virtuale usando l'immagine creata con [az vm create](/cli/azure/vm#create). Nell'esempio seguente viene creata una macchina virtuale denominata *myVMDeployed* dall'immagine denominata *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Creazione della macchina virtuale in un altro gruppo di risorse 

È possibile creare macchine virtuali da un'immagine in qualsiasi gruppo di risorse all'interno della sottoscrizione. Per creare una macchina virtuale in un gruppo di risorse diverso rispetto all'immagine, specificare l'ID di risorsa completa per l'immagine. Usare [az image list](/cli/azure/image#list) per visualizzare un elenco di immagini. L'output è simile all'esempio seguente:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

L'esempio seguente usa [az vm create](/cli/azure/vm#create) per creare una macchina virtuale in un gruppo di risorse diverso rispetto all'immagine di origine, specificando l'ID di risorsa immagine:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passaggio 4: Verificare la distribuzione

Connettersi ora con SSH alla macchina virtuale creata per verificare la distribuzione e iniziare a usare la nuova VM. Per connettersi tramite SSH, trovare l'indirizzo IP o il nome di dominio completo della macchina virtuale con [az vm show](/cli/azure/vm#show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passaggi successivi
È possibile creare più macchine virtuali da un'immagine di macchina virtuale di origine. Se è necessario apportare modifiche all'immagine: 

- Creare una macchina virtuale da un'immagine dell'utente.
- Apportare aggiornamenti o modifiche alla configurazione.
- Seguire nuovamente i passaggi per eseguire il deprovisioning, deallocare, generalizzare e creare un'immagine.
- Usare la nuova immagine per le distribuzioni future. Se si desidera, eliminare l'immagine originale.

Per altre informazioni sulla gestione delle macchine virtuali con l'interfaccia della riga di comando, vedere [Interfaccia della riga di comando di Azure 2.0](/cli/azure/overview).
