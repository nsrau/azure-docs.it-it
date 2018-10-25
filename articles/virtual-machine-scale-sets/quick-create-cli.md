---
title: Avvio rapido - Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare rapidamente un set di scalabilità di macchine virtuali con Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/18
ms.author: zarhoads
ms.openlocfilehash: b306766a300af1b6b4d5b85c29ce8c06d1888222
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471068"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Avvio rapido: Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'uso delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. Un servizio Azure Load Balancer distribuisce quindi il traffico alle istanze di macchina virtuale nel set di scalabilità. In questa guida introduttiva viene creato un set di scalabilità di macchine virtuali e viene distribuita un'applicazione di esempio con l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Per poter creare un set di scalabilità, è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Si può ora creare un set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss#az_vmss_create). L'esempio seguente crea un set di scalabilità denominato *myScaleSet* che è impostato per l'aggiornamento automatico man mano che vengono applicate modifiche e genera le chiavi SSH se non esistono in *~/.ssh/id_rsa*. Queste chiavi SSH vengono usate per l'accesso alle istanze di macchina virtuale. Per usare invece un set di chiavi SSH esistente, usare il parametro `--ssh-key-value` e specificare il percorso delle chiavi.

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


## <a name="deploy-sample-application"></a>Distribuire l'applicazione di esempio
Per testare il set di scalabilità, installare un'applicazione Web di base. Usare l'estensione script personalizzati di Azure per scaricare ed eseguire uno script che installa un'applicazione nelle istanze di macchina virtuale. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/linux/extensions-customscript.md).

Usare l'estensione script personalizzati per installare un server NGINX di base. Applicare l'estensione script personalizzati che installa NGINX con [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) nel modo seguente:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Consentire il traffico verso l'applicazione
Quando è stato creato il set di scalabilità, è stato distribuito automaticamente un servizio Azure Load Balancer. Il servizio di bilanciamento del carico distribuisce il traffico alle istanze di macchina virtuale nel set di scalabilità. Per consentire al traffico di raggiungere l'applicazione Web di esempio, creare una regola del servizio di bilanciamento del carico con [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Nell'esempio seguente viene creata una regola denominata *myLoadBalancerRuleWeb*:

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


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per vedere il set di scalabilità in azione, accedere all'applicazione Web di esempio in un Web browser. Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web predefinita in NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse, il set di scalabilità e tutte le risorse correlate non sono più necessari, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuoverli, come segue. Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato creato un set di scalabilità di base ed è stata usata l'estensione script personalizzati per installare un server Web NGINX di base nelle istanze di macchina virtuale. Per altre informazioni, passare all'esercitazione su come creare e gestire i set di scalabilità di macchine virtuali di Azure.

> [!div class="nextstepaction"]
> [Creare e gestire i set di scalabilità di macchine virtuali di Azure](tutorial-create-and-manage-cli.md)

