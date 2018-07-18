---
title: Esercitazione - Installare applicazioni in un set di scalabilità con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure 2.0 per installare applicazioni nei set di scalabilità di macchine virtuali con l'estensione Script personalizzato
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
ms.openlocfilehash: fe1fd957176762c5cc04145f56559b50667c476c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606526"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Esercitazione: Installare applicazioni in set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0
Per eseguire applicazioni nelle istanze di macchine virtuali (VM) in un set di scalabilità, è necessario prima installare i componenti dell'applicazione e i file necessari. In un'esercitazione precedente si è appreso come usare un'immagine di macchina virtuale personalizzata per distribuire le istanze di macchina virtuale. Questa immagine personalizzata includeva installazioni e configurazioni manuali di applicazioni. È anche possibile automatizzare l'installazione delle applicazioni in un set di scalabilità dopo la distribuzione di ogni istanza di macchina virtuale oppure aggiornare un'applicazione che è già in esecuzione in un set di scalabilità. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare automaticamente le applicazioni nel set di scalabilità
> * Usare l'estensione Script personalizzato di Azure
> * Aggiornare un'applicazione in esecuzione in un set di scalabilità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="what-is-the-azure-custom-script-extension"></a>Informazioni sull'estensione Script personalizzato di Azure
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione Script personalizzato si integra nei modelli di Azure Resource Manager e può anche essere usata con l'interfaccia della riga di comando di Azure 2.0, Azure PowerShell, il portale di Azure o l'API REST. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/linux/extensions-customscript.md).

Per usare l'estensione dello script personalizzata con l'interfaccia della riga di comando di Azure, creare un file JSON che definisca i file da ottenere e i comandi da eseguire. Queste definizioni JSON possono essere riusate nelle distribuzioni del set di scalabilità per applicare installazioni di applicazioni coerenti.


## <a name="create-custom-script-extension-definition"></a>Creare una definizione di estensione Script personalizzato
Per vedere l'estensione Script personalizzato in azione, creare un set di scalabilità che installa il server Web NGINX e restituisce il nome host dell'istanza di macchina virtuale del set di scalabilità. La seguente definizione dell'estensione Script personalizzato scarica uno script di esempio da GitHub, installa i pacchetti richiesti, quindi scrive il nome host dell'istanza di macchina virtuale in una pagina HTML di base.

Nella shell corrente creare un file denominato *customConfig.json* e incollare la configurazione seguente. Ad esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. In Cloud Shell immettere `sensible-editor cloudConfig.json` per creare il file e visualizzare un elenco degli editor disponibili.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

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


## <a name="apply-the-custom-script-extension"></a>Applicare l'estensione Script personalizzato
Applicare la configurazione dell'estensione dello script personalizzata alle istanze di macchine virtuali nel set di scalabilità con [az vmss extension set](/cli/azure/vmss/extension#set). Nell'esempio seguente viene applicata la configurazione *customConfig.json* alle istanze di macchine virtuali *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Ogni istanza di macchina virtuale nel set di scalabilità scarica ed esegue lo script da GitHub. In un esempio più complesso sarebbe possibile installare più componenti e file di applicazione. Se si aumentano le prestazioni del set di scalabilità, le nuove istanze di macchina virtuale applicano automaticamente la stessa definizione dell'estensione Script personalizzato e installano l'applicazione necessaria.


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
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

Per vedere il server Web in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#show). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetLBPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web di base in Nginx](media/tutorial-install-apps-cli/running-nginx.png)

Lasciare aperto il Web browser per poter visualizzare una versione aggiornata nel passaggio successivo.


## <a name="update-app-deployment"></a>Aggiornare la distribuzione dell'app
Nel ciclo di vita di un set di scalabilità potrebbe essere necessario distribuire una versione aggiornata dell'applicazione. Con l'estensione Script personalizzato è possibile fare riferimento a uno script di distribuzione aggiornato e quindi riapplicare l'estensione al set di scalabilità. Quando è stato creato il set di scalabilità in un passaggio precedente, `--upgrade-policy-mode` è stato impostato su *automatic*. Questa impostazione consente di aggiornare automaticamente le istanze di macchina virtuale nel set di scalabilità e di applicare automaticamente la versione più recente dell'applicazione.

Nella shell corrente creare un file denominato *customConfigv2.json* e incollare la configurazione seguente. Questa definizione esegue una versione *v2* aggiornata dello script di installazione dell'applicazione:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Applicare la configurazione dell'estensione Script personalizzato alle istanze di macchina virtuale nel set di scalabilità con [az vmss extension set](/cli/azure/vmss/extension#set). Il file *customConfigv2.json* viene usato per applicare la versione aggiornata dell'applicazione:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Tutte le istanze di macchina virtuale nel set di scalabilità vengono aggiornate automaticamente con la versione più recente della pagina Web di esempio. Per visualizzare la versione aggiornata, aggiornare il sito Web nel browser:

![Pagina Web aggiornata in Nginx](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [az group delete](/cli/azure/group#az_group_delete). Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come installare e aggiornare automaticamente applicazioni nel set di scalabilità con l'interfaccia della riga di comando di Azure 2.0:

> [!div class="checklist"]
> * Installare automaticamente le applicazioni nel set di scalabilità
> * Usare l'estensione Script personalizzato di Azure
> * Aggiornare un'applicazione in esecuzione in un set di scalabilità

Passare all'esercitazione successiva per informazioni su come ridimensionare automaticamente il set di scalabilità.

> [!div class="nextstepaction"]
> [Ridimensionare automaticamente i set di scalabilità](tutorial-autoscale-cli.md)