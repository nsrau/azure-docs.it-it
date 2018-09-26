---
title: Esercitazione - Usare un'immagine VM personalizzata in un set di scalabilità con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare un'immagine VM personalizzata che si possa usare per distribuire un set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5eee55846bd6f5821be1e40b969a35f5e50bd205
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967371"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Esercitazione: Creare e usare un'immagine personalizzata per i set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Quando si crea un set di scalabilità, si specifica un'immagine da usare quando vengono distribuite le istanze di macchina virtuale. Per ridurre il numero di attività dopo la distribuzione delle istanze di macchina virtuale, è possibile usare un'immagine di VM personalizzata. Questa immagine di VM personalizzata include le installazioni o le configurazioni delle applicazioni necessarie. Le istanze di macchina virtuale create nel set di scalabilità usano l'immagine di VM personalizzata e sono pronte per gestire il traffico delle applicazioni. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e personalizzare una macchina virtuale
> * Effettuare il deprovisioning e generalizzare la macchina virtuale
> * Creare un'immagine di macchina virtuale personalizzata
> * Distribuire un set di scalabilità che usa l'immagine di macchina virtuale personalizzata

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Creare e configurare una macchina virtuale di origine

>[!NOTE]
> Questa esercitazione illustra in modo dettagliato il processo di creazione e di uso di un'immagine di macchina virtuale generalizzata. Non è supportata la creazione di un set di scalabilità da un'immagine di macchina virtuale specializzata.

Creare prima un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create), quindi creare una VM con [az vm create](/cli/azure/vm#az_vm_create). Questa macchina virtuale viene quindi usata come origine per un'immagine di macchina virtuale personalizzata. L'esempio seguente crea una VM denominata *myVM* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

L'indirizzo IP pubblico della VM viene visualizzato nell'output del comando [az vm create](/cli/azure/vm#az_vm_create). Connettersi all'indirizzo IP pubblico della macchina virtuale tramite SSH, come segue:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Per personalizzare la macchina virtuale, verrà installato un server Web di base. Quando l'istanza di macchina virtuale nel set di scalabilità verrà distribuita, avrà quindi tutti i pacchetti necessari per eseguire un'applicazione Web. Usare `apt-get` per installare *NGINX* come indicato di seguito:

```bash
sudo apt-get install -y nginx
```

Il passaggio finale per preparare la macchina virtuale e poterla usare come immagine personalizzata consiste nell'effettuarne il deprovisioning. Questo passaggio rimuove le informazioni specifiche del computer dalla macchina virtuale e consente di distribuire più macchine virtuali da una singola immagine. Quando viene effettuato il deprovisioning della VM, il nome host viene reimpostato su *hostlocale.dominiolocale*. Vengono eliminati anche i lease DHCP memorizzati nella cache, le chiavi host SSH, le configurazioni nameserver e le password radicele.

Per effettuare il deprovisioning della macchina virtuale, usare l'agente di macchine virtuali di Azure (*waagent*). L'agente di macchine virtuali di Azure viene installato in ogni macchina virtuale e usato per comunicare con la piattaforma Azure. Il parametro `-force` indica all'agente di accettare i prompt per reimpostare le informazioni specifiche del computer.

```bash
sudo waagent -deprovision+user -force
```

Chiudere la connessione SSH alla macchina virtuale:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Creare un'immagine di macchina virtuale personalizzata dalla macchina virtuale di origine
La macchina virtuale di origine viene ora personalizzata con il server Web Nginx installato. Verrà creata l'immagine di macchina virtuale personalizzata da usare con un set di scalabilità.

Per creare un'immagine, è necessario deallocare la macchina virtuale. Deallocare la macchina virtuale con [az vm deallocate](/cli//azure/vm#az_vm_deallocate). Impostare quindi lo stato della VM come generalizzato con [az vm generalize](/cli//azure/vm#az_vm_generalize) in modo che la piattaforma Azure riconosca che la VM è pronta per usare un'immagine personalizzata. È possibile creare solo un'immagine da una VM generalizzata:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Potrebbero essere necessari alcuni minuti per deallocare e generalizzare la macchina virtuale.

Creare ora un'immagine della macchina virtuale con [az image create](/cli//azure/image#az_image_create). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM:

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Creare un set di scalabilità dall'immagine di macchina virtuale personalizzata
Creare un set di scalabilità con [az vmss create](/cli/azure/vmss#az-vmss-create). Invece di un'immagine di una piattaforma, ad esempio *UbuntuLTS* o *CentOS*, specificare il nome dell'immagine di macchina virtuale personalizzata. L'esempio seguente crea un set di scalabilità denominato *myScaleSet* che usa l'immagine personalizzata denominata *myImage* del passaggio precedente:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per consentire al traffico di raggiungere il set di scalabilità e verificare che il server Web funzioni correttamente, creare una regola del servizio di bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule#create). Nell'esempio seguente viene creata una regola denominata *myLoadBalancerRuleWeb* che consente il traffico sulla porta *TCP* *80*:

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

Per vedere il set di scalabilità in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#show). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Digitare l'indirizzo IP pubblico nel Web browser. La pagina Web NGINX predefinita viene visualizzata, come illustrato nell'esempio seguente:

![Nginx in esecuzione dall'immagine di macchina virtuale personalizzata](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [az group delete](/cli/azure/group#az_group_delete). Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come creare e usare un'immagine di macchina virtuale personalizzata per il set di scalabilità con l'interfaccia della riga di comando di Azure:

> [!div class="checklist"]
> * Creare e personalizzare una macchina virtuale
> * Effettuare il deprovisioning e generalizzare la macchina virtuale
> * Creare un'immagine di macchina virtuale personalizzata
> * Distribuire un set di scalabilità che usa l'immagine di macchina virtuale personalizzata

Passare all'esercitazione successiva per informazioni su come distribuire le applicazioni nel set di scalabilità.

> [!div class="nextstepaction"]
> [Distribuire le applicazioni nei set di scalabilità](tutorial-install-apps-cli.md)