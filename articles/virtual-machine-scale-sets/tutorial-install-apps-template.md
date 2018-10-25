---
title: Esercitazione - Installare applicazioni in un set di scalabilità con i modelli di Azure | Microsoft Docs
description: Informazioni su come usare i modelli di Azure Resource Manager per installare applicazioni nei set di scalabilità di macchine virtuali con l'estensione Script personalizzato
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
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
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: dddd8ba98e2825d0e0396963e0f8c23059b97c55
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468637"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Esercitazione: Installare applicazioni in set di scalabilità di macchine virtuali con un modello di Azure
Per eseguire applicazioni nelle istanze di macchine virtuali (VM) in un set di scalabilità, è necessario prima installare i componenti dell'applicazione e i file necessari. In un'esercitazione precedente si è appreso come usare un'immagine di macchina virtuale personalizzata per distribuire le istanze di macchina virtuale. Questa immagine personalizzata includeva installazioni e configurazioni manuali di applicazioni. È anche possibile automatizzare l'installazione delle applicazioni in un set di scalabilità dopo la distribuzione di ogni istanza di macchina virtuale oppure aggiornare un'applicazione che è già in esecuzione in un set di scalabilità. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare automaticamente le applicazioni nel set di scalabilità
> * Usare l'estensione Script personalizzato di Azure
> * Aggiornare un'applicazione in esecuzione in un set di scalabilità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="what-is-the-azure-custom-script-extension"></a>Informazioni sull'estensione Script personalizzato di Azure
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione Script personalizzato si integra nei modelli di Azure Resource Manager e può anche essere usata con l'interfaccia della riga di comando di Azure, Azure PowerShell, il portale di Azure o l'API REST. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/linux/extensions-customscript.md).

Per vedere l'estensione Script personalizzato in azione, creare un set di scalabilità che installa il server Web NGINX e restituisce il nome host dell'istanza di macchina virtuale del set di scalabilità. La seguente definizione dell'estensione Script personalizzato scarica uno script di esempio da GitHub, installa i pacchetti richiesti, quindi scrive il nome host dell'istanza di macchina virtuale in una pagina HTML di base.


## <a name="create-custom-script-extension-definition"></a>Creare una definizione di estensione Script personalizzato
Quando si definisce un set di scalabilità di macchine virtuali con un modello di Azure, il provider di risorse *Microsoft.Compute/virtualMachineScaleSets* può includere una sezione sulle estensioni. *extensionsProfile* illustra in dettaglio che cosa viene applicato alle istanze di macchina virtuale in un set di scalabilità. Per usare l'estensione Script personalizzato, si specifica un'entità di pubblicazione di *Microsoft.Azure.Extensions* e un tipo di *CustomScript*.

La proprietà *fileUris* viene usata per definire gli script o i pacchetti di installazione di origine. Per avviare il processo di installazione, gli script necessari vengono definiti in *commandToExecute*. L'esempio seguente definisce uno script di esempio da GitHub che installa e configura il server Web NGINX:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Per un esempio completo di un modello di Azure che distribuisce un set di scalabilità e l'estensione Script personalizzato, vedere [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Questo modello di esempio viene usato nella sezione successiva.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Verrà usato il modello di esempio per creare un set di scalabilità e applicare l'estensione Script personalizzato. Creare prima un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare ora un set di scalabilità di macchine virtuali con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Quando richiesto, specificare il nome utente e la password usati come credenziali per ogni istanza di VM:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.

Ogni istanza di macchina virtuale nel set di scalabilità scarica ed esegue lo script da GitHub. In un esempio più complesso sarebbe possibile installare più componenti e file di applicazione. Se si aumentano le prestazioni del set di scalabilità, le nuove istanze di macchina virtuale applicano automaticamente la stessa definizione dell'estensione Script personalizzato e installano l'applicazione necessaria.


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per vedere il server Web in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [az network public-ip show](/cli/azure/network/public-ip#show). Nell'esempio seguente si ottiene l'indirizzo IP per *myScaleSetPublicIP* creato come parte del set di scalabilità:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web di base in Nginx](media/tutorial-install-apps-template/running-nginx.png)

Lasciare aperto il Web browser per poter visualizzare una versione aggiornata nel passaggio successivo.


## <a name="update-app-deployment"></a>Aggiornare la distribuzione dell'app
Nel ciclo di vita di un set di scalabilità potrebbe essere necessario distribuire una versione aggiornata dell'applicazione. Con l'estensione Script personalizzato è possibile fare riferimento a uno script di distribuzione aggiornato e quindi riapplicare l'estensione al set di scalabilità. Quando è stato creato il set di scalabilità in un passaggio precedente, *upgradePolicy` è stato impostato su *Automatic*. Questa impostazione consente di aggiornare automaticamente le istanze di macchina virtuale nel set di scalabilità e di applicare automaticamente la versione più recente dell'applicazione.

Per aggiornare la definizione dell'estensione Script personalizzato, modificare il modello per fare riferimento a un nuovo script di installazione. Per fare in modo che l'estensione Script personalizzato riconosca la modifica, deve essere usato un nuovo nome file. L'estensione Script personalizzato non esamina i contenuti dello script per determinare eventuali modifiche. La definizione seguente usa uno script di installazione aggiornato con *_v2* aggiunto al nome:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Applicare la configurazione dell'estensione Script personalizzato alle istanze di macchina virtuale nel set di scalabilità con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Questo modello *azuredeployv2.json* viene usato per applicare la versione aggiornata dell'applicazione. In pratica, si modifica il modello *azuredeploy.json* esistente per fare riferimento allo script di installazione aggiornato, come illustrato nella sezione precedente. Quando richiesto, immettere le stesse credenziali di nome utente e password usate quando si è creato il primo set di scalabilità:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Tutte le istanze di macchina virtuale nel set di scalabilità vengono aggiornate automaticamente con la versione più recente della pagina Web di esempio. Per visualizzare la versione aggiornata, aggiornare il sito Web nel browser:

![Pagina Web aggiornata in Nginx](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [az group delete](/cli/azure/group#az_group_delete). Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come installare e aggiornare automaticamente applicazioni nel set di scalabilità con i modelli di Azure:

> [!div class="checklist"]
> * Installare automaticamente le applicazioni nel set di scalabilità
> * Usare l'estensione Script personalizzato di Azure
> * Aggiornare un'applicazione in esecuzione in un set di scalabilità

Passare all'esercitazione successiva per informazioni su come ridimensionare automaticamente il set di scalabilità.

> [!div class="nextstepaction"]
> [Ridimensionare automaticamente i set di scalabilità](tutorial-autoscale-template.md)
