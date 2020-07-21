---
title: Acquisire un'immagine gestita di una macchina virtuale Linux usando l'interfaccia della riga di comando di Azure
description: Acquisire un'immagine gestita di una macchina virtuale di Azure da usare per le distribuzioni di massa tramite l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 26c8a8746fe748585f36e5282ad6e694cdcef1f6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494463"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Come creare un'immagine gestita di una macchina virtuale o di un disco rigido virtuale

Per creare più copie di una macchina virtuale da usare in Azure per lo sviluppo e il test, acquisire un'immagine gestita della macchina virtuale o del disco rigido virtuale del sistema operativo. Per creare, archiviare e condividere immagini su larga scala, vedere [Raccolte immagini condivise](../shared-images-cli.md).

Un'immagine gestita supporta fino a 20 distribuzioni simultanee. Il tentativo di creare più di 20 macchine virtuali contemporaneamente dalla stessa immagine gestita può causare timeout del provisioning dovuti alle limitazioni delle prestazioni di archiviazione di un singolo disco rigido virtuale. Per creare più di 20 macchine virtuali contemporaneamente, usare un'immagine delle [Raccolte immagini condivise](shared-image-galleries.md) configurata con 1 replica per ogni 20 distribuzioni di macchina virtuale simultanee.

Per creare un'immagine gestita, è necessario rimuovere le informazioni sull'account personale. Nei passaggi seguenti si eseguirà il deprovisioning e la deallocazione di una macchina virtuale esistente e si procederà alla creazione di un'immagine. È possibile usare questa immagine per creare macchine virtuali in qualsiasi gruppo di risorse all'interno della sottoscrizione.

Per creare una copia della macchina virtuale Linux esistente per il backup o il debug o per caricare un disco rigido virtuale Linux specializzato da una macchina virtuale locale, vedere [Caricare e creare una VM Linux da un'immagine disco personalizzata](upload-vhd.md).  

È possibile usare il servizio **Image Builder per macchine virtuali di Azure (Anteprima pubblica)** per creare l'immagine personalizzata. Non è necessario imparare a usare alcuno strumento o configurare pipeline di compilazione, basta inserire una configurazione dell'immagine e Image Builder la creerà. Per altre informazioni, vedere [Introduzione ad Image Builder per macchine virtuali di Azure](./image-builder-overview.md).

Prima di poter creare un'immagine, è necessario:

* Aver creato una macchina virtuale di Azure nel modello di distribuzione di Resource Manager che usa dischi gestiti. Se non è ancora stata creata una macchina virtuale Linux, è possibile usare il [portale](quick-create-portal.md), l'[interfaccia della riga di comando di Azure](quick-create-cli.md) o i [modelli di Resource Manager](create-ssh-secured-vm-from-template.md). Configurare la macchina virtuale in base alle esigenze. Ad esempio, [aggiungere dischi dati](add-disk.md), applicare aggiornamenti e installare applicazioni. 

* Aver installato l'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Si preferisce un'esercitazione?

Per una versione semplificata di questo articolo e per informazioni sulle procedure di testing e valutazione o sulle macchine virtuali in Azure, vedere [Creare un'immagine personalizzata di una macchina virtuale di Azure tramite l'interfaccia della riga di comando](tutorial-custom-images.md).  Altrimenti continuare a leggere qui per ottenere un'illustrazione completa.


## <a name="step-1-deprovision-the-vm"></a>Passaggio 1: Eseguire il deprovisioning della macchina virtuale
Per prima cosa, eseguire il deprovisioning della macchina virtuale usando l'agente della macchina virtuale di Azure per eliminare file e dati specifici della macchina. Usare il comando `waagent` con il parametro `-deprovision+user` nella macchina virtuale Linux di origine. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../extensions/agent-linux.md).

1. Connettersi alla macchina virtuale Linux con un client SSH.
2. Nella finestra SSH digitare il comando seguente:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Eseguire questo comando solo su una macchina virtuale che si intende acquisire come immagine. Questo comando non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione. Il parametro `+user` rimuove anche l'ultimo account utente di cui è stato effettuato il provisioning. Per mantenere le credenziali dell'account utente nella macchina virtuale, usare solo `-deprovision`.
 
3. Immettere **y** per continuare. È possibile aggiungere il parametro `-force` per evitare questo passaggio di conferma.
4. Dopo aver eseguito il comando, digitare **exit** per chiudere il client SSH.  A questo punto la macchina virtuale sarà ancora in esecuzione.

## <a name="step-2-create-vm-image"></a>Passaggio 2: Creare l'immagine della macchina virtuale
Usare l'interfaccia della riga di comando di Azure per contrassegnare la macchina virtuale come generalizzata e acquisire l'immagine. Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myVnet* e *myVM*.

1. Deallocare la macchina virtuale su cui è stato eseguito il deprovisioning con [az vm deallocate](/cli/azure/vm). L'esempio seguente dealloca la macchina virtuale denominata *myVM* nel gruppo di risorse denominato *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Attendere che la macchina virtuale venga completamente deallocata prima di procedere. Questa operazione richiederà qualche minuto.  La macchina virtuale si arresta durante la deallocazione.

2. Contrassegnare la macchina virtuale come generalizzata con il comando [az vm generalize](/cli/azure/vm). Nell'esempio seguente la macchina virtuale denominata *myVM* viene contrassegnata come generalizzata nel gruppo di risorse *myResourceGroup*.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Non è più possibile riavviare una macchina virtuale che è stata generalizzata.

3. Creare un'immagine della risorsa macchina virtuale con [az image create](/cli/azure/image#az-image-create). Nell'esempio seguente viene creata un'immagine denominata *myImage* nel gruppo di risorse denominato *myResourceGroup* usando la risorsa macchina virtuale denominata *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > L'immagine viene creata nello stesso gruppo di risorse della macchina virtuale di origine. Con questa immagine è possibile creare macchine virtuali in qualsiasi gruppo di risorse all'interno della sottoscrizione. Da una prospettiva di gestione, si consiglia di creare un gruppo di risorse specifico per le risorse e le immagini della macchina virtuale.
   >
   > Se si vuole archiviare l'immagine in una risorsa di archiviazione servizio di archiviazione resiliente nella zona, è necessario crearla in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `--zone-resilient true`.
   
Questo comando restituisce un JSON che descrive l'immagine della macchina virtuale. Salvare l'output come riferimento da usare in seguito.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passaggio 3: Creare una macchina virtuale dall'immagine acquisita
Creare una macchina virtuale usando l'immagine creata con [az vm create](/cli/azure/vm). Nell'esempio seguente viene creata una macchina virtuale denominata *myVMDeployed* dall'immagine denominata *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Creazione della macchina virtuale in un altro gruppo di risorse 

È possibile creare macchine virtuali da un'immagine in qualsiasi gruppo di risorse all'interno della sottoscrizione. Per creare una macchina virtuale in un gruppo di risorse diverso rispetto all'immagine, specificare l'ID di risorsa completa per l'immagine. Usare [az image list](/cli/azure/image#az-image-list) per visualizzare un elenco di immagini. L'output è simile all'esempio seguente:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

L'esempio seguente usa [az vm create](/cli/azure/vm#az-vm-create) per creare una macchina virtuale in un gruppo di risorse diverso rispetto all'immagine di origine, specificando l'ID di risorsa immagine.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passaggio 4: Verificare la distribuzione

Stabilire una connessione SSH alla macchina virtuale creata per verificare la distribuzione e iniziare a usare la nuova macchina virtuale. Per connettersi tramite SSH, trovare l'indirizzo IP o il nome di dominio completo della macchina virtuale con [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passaggi successivi
Per creare, archiviare e condividere immagini su larga scala, vedere [Raccolte immagini condivise](../shared-images-cli.md).
