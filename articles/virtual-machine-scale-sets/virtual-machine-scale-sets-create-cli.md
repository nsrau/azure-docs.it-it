---
title: "Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs"
description: "Informazioni su come creare rapidamente un set di scalabilità di macchine virtuali con Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 222bfa1c3070fa4634cf5c48d934a6387c48a4b0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questo articolo introduttivo viene creato un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0. È anche possibile creare un set di scalabilità con [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) oppure il [portale di Azure](virtual-machine-scale-sets-create-portal.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Per poter creare un set di scalabilità, è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Si può ora creare un set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss#create). Nell'esempio seguente viene creato un set di scalabilità denominato *myScaleSet* e vengono generate le chiavi SSH, se non sono presenti:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="install-nginx-webserver"></a>Installare il server Web NGINX
Per testare il set di scalabilità, usare l'estensione di script personalizzati per scaricare ed eseguire uno script che installi NGINX nelle istanze di macchina virtuale. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/linux/extensions-customscript.md).

Applicare l'estensione di script personalizzati che installa NGINX nel modo seguente:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Consentire il traffico Web
Per consentire al traffico di raggiungere il server Web, creare una regola del servizio di bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule#create). Nell'esempio seguente viene creata una regola denominata *myLoadBalancerRuleWeb*:

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


## <a name="test-your-web-server"></a>Testare il server Web
Per vedere il server Web in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#show). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web predefinita in NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse, il set di scalabilità e tutte le risorse correlate non sono più necessari, è possibile usare il comando [az group delete](/cli/azure/group#delete) per rimuoverli come segue:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi
In questo articolo introduttivo è stato creato un set di scalabilità di base ed è stata usata l'estensione di script personalizzati per installare un server Web NGINX di base nelle istanze di macchina virtuale. Per ottenere scalabilità e automazione maggiori, espandere il set di scalabilità con le procedure seguenti:

- [Distribuire l'applicazione nei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-deploy-app.md)
- È possibile applicare la scalabilità automatica con l'[interfaccia della riga di comando di Azure](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o il [portale di Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Usare gli aggiornamenti automatici del sistema operativo per le istanze di macchina virtuale del set di scalabilità](virtual-machine-scale-sets-automatic-upgrade.md)