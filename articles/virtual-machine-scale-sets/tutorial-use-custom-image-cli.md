---
title: "Esercitazione: Usare un'immagine di VM personalizzata in un set di scalabilità con l'interfaccia della riga di comando di Azure"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare un'immagine VM personalizzata che si possa usare per distribuire un set di scalabilità di macchine virtuali
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: dd0cf450ca63349d29aba3d65f3c76f40a44be2a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503634"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Esercitazione: Creare e usare un'immagine personalizzata per i set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Quando si crea un set di scalabilità, si specifica un'immagine da usare quando vengono distribuite le istanze di macchina virtuale. Per ridurre il numero di attività dopo la distribuzione delle istanze di macchina virtuale, è possibile usare un'immagine di VM personalizzata. Questa immagine di VM personalizzata include le installazioni o le configurazioni delle applicazioni necessarie. Le istanze di macchina virtuale create nel set di scalabilità usano l'immagine di VM personalizzata e sono pronte per gestire il traffico delle applicazioni. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione di immagine specializzata
> * Creare una versione di immagine
> * Creare un set di scalabilità da un'immagine specializzata
> * Condividere una raccolta di immagini


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa esercitazione è necessario eseguire la versione 2.4.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Panoramica

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini di VM personalizzate con altri utenti. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. 

## <a name="create-and-configure-a-source-vm"></a>Creare e configurare una macchina virtuale di origine

Creare prima un gruppo di risorse con il comando [az group create](/cli/azure/group), quindi creare una VM con [az vm create](/cli/azure/vm). Questa macchina virtuale viene quindi usata come origine per l'immagine. L'esempio seguente crea una VM denominata *myVM* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> L'**ID** della VM viene mostrato nell'output del comando [az vm create](/cli/azure/vm). Copiare questo valore in un posto sicuro, in modo da poterlo usare più avanti in questa esercitazione.

Anche l'indirizzo IP pubblico della VM viene mostrato nell'output del comando [az vm create](/cli/azure/vm). Connettersi all'indirizzo IP pubblico della macchina virtuale tramite SSH, come segue:

```console
ssh azureuser@<publicIpAddress>
```

Per personalizzare la macchina virtuale, verrà installato un server Web di base. Quando l'istanza di macchina virtuale nel set di scalabilità verrà distribuita, avrà quindi tutti i pacchetti necessari per eseguire un'applicazione Web. Usare `apt-get` per installare *NGINX* come indicato di seguito:

```bash
sudo apt-get install -y nginx
```

Al termine, digitare `exit` per disconnettere la connessione SSH.

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. 

I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini.   I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [sig az create](/cli/azure/sig#az-sig-create). L'esempio seguente crea un gruppo di risorse denominato *myGalleryRG* nell'area *Stati Uniti orientali* e una raccolta denominata *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usate per gestire le informazioni sulle versioni di immagini create al loro interno. 

I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Assicurarsi che la definizione di immagine sia del tipo corretto. Se la VM è stata generalizzata, usando Sysprep per Windows o waagent -deprovision per Linux, è necessario creare una definizione di immagine generalizzata usando `--os-state generalized`. Se si vuole usare la VM senza rimuovere gli account utente esistenti, creare una definizione di immagine specializzata usando `--os-state specialized`.

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](../virtual-machines/linux/shared-image-galleries.md#image-definitions).

Creare una definizione di immagine nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In questo esempio la definizione di immagine è denominata *myImageDefinition* ed è relativa a un'immagine [specializzata](../virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images) del sistema operativo Linux. Per creare una definizione per le immagini usando un sistema operativo Windows, usare `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> L'**ID** della definizione di immagine viene mostrato nell'output del comando. Copiare questo valore in un posto sicuro, in modo da poterlo usare più avanti in questa esercitazione.


## <a name="create-the-image-version"></a>Creare una versione di immagine

Creare una versione di immagine dalla VM usando [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è*1.0.0* e verranno create una replica nell'area *Stati Uniti centro-meridionali* e una replica nell'area *Stati Uniti orientali 2*. Le aree di replica devono includere l'area in cui si trova la macchina virtuale di origine.

Sostituire il valore di `--managed-image` in questo esempio con l'ID della macchina virtuale del passaggio precedente.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> Quando si crea la versione dell'immagine, è anche possibile archiviare l'immagine nell'archiviazione Premium, aggiungendo `--storage-account-type  premium_lrs`, oppure nell'[archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md), aggiungendo `--storage-account-type  standard_zrs`.
>




## <a name="create-a-scale-set-from-the-image"></a>Creare un set di scalabilità dall'immagine
Creare un set di scalabilità dall'immagine specializzata usando [`az vmss create`](/cli/azure/vmss#az-vmss-create). 

Creare il set di scalabilità usando [`az vmss create`](/cli/azure/vmss#az-vmss-create) con il parametro --specialized per indicare che l'immagine è specializzata. 

Usare l'ID definizione immagine per `--image` per creare le istanze del set di scalabilità dalla versione più recente dell'immagine disponibile. È anche possibile creare le istanze del set di scalabilità da una versione specifica fornendo l'ID versione dell'immagine per `--image`. 

Creare un set di scalabilità denominato *myScaleSet*, la versione più recente dell'immagine *myImageDefinition* creata in precedenza.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per consentire al traffico di raggiungere il set di scalabilità e verificare che il server Web funzioni correttamente, creare una regola del servizio di bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule). Nell'esempio seguente viene creata una regola denominata *myLoadBalancerRuleWeb* che consente il traffico sulla porta *TCP* *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Per vedere il set di scalabilità in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Digitare l'indirizzo IP pubblico nel Web browser. La pagina Web NGINX predefinita viene visualizzata, come illustrato nell'esempio seguente:

![Nginx in esecuzione dall'immagine di macchina virtuale personalizzata](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>Condividere la raccolta

È possibile condividere immagini tra sottoscrizioni usando il Controllo di accesso in base al ruolo (RBAC). È possibile condividere immagini nella raccolta, definizioni di immagini o versioni di immagini. Qualsiasi utente che abbia autorizzazioni di lettura per una versione di immagine, anche tra sottoscrizioni diverse, potrà distribuire una VM usando la versione dell'immagine.

È consigliabile condividere con altri utenti a livello di raccolta. Per ottenere l'ID oggetto della raccolta, usare [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Usare l'ID oggetto come ambito, insieme a un indirizzo di posta elettronica e a [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), per concedere a un utente l'accesso alla raccolta di immagini condivise. Sostituire `<email-address>` e `<gallery iD>` con le proprie informazioni.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Per altre informazioni su come condividere risorse usando RBCA, vedere [Gestione dell'accesso usando RBCA e L'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [az group delete](/cli/azure/group). Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come creare e usare un'immagine di macchina virtuale personalizzata per il set di scalabilità con l'interfaccia della riga di comando di Azure:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione di immagine specializzata
> * Creare una versione di immagine
> * Creare un set di scalabilità da un'immagine specializzata
> * Condividere una raccolta di immagini

Passare all'esercitazione successiva per informazioni su come distribuire le applicazioni nel set di scalabilità.

> [!div class="nextstepaction"]
> [Distribuire le applicazioni nei set di scalabilità](tutorial-install-apps-cli.md)
