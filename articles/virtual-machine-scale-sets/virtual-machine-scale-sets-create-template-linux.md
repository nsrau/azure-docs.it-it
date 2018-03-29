---
title: Creare un set di scalabilità di macchine virtuali Linux con un modello di Azure | Microsoft Docs
description: Informazioni su come creare rapidamente un set di scalabilità di macchine virtuali Linux con un modello di Azure Resource Manager che distribuisce un'app di esempio e configura le regole di scalabilità automatica
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 16e9c0b30710d711ef2789f7781b17e72889d4da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2018
---
# <a name="create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Creare un set di scalabilità di macchine virtuali Linux con un modello di Azure
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questo articolo introduttivo viene creato un set di scalabilità di macchine virtuali Linux usando un modello di Azure Resource Manager. È anche possibile creare un set di scalabilità con l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) oppure il [portale di Azure](virtual-machine-scale-sets-create-portal.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="define-a-scale-set-in-a-template"></a>Definire un set di scalabilità in un modello
I modelli di Azure Resource Manager consentono di distribuire gruppi di risorse correlate. I modelli sono scritti in JavaScript Object Notation (JSON) e definiscono l'intero ambiente dell'infrastruttura di Azure per l'applicazione. In un singolo modello è possibile creare il set di scalabilità di macchine virtuali, installare le applicazioni e configurare le regole di scalabilità automatica. Con l'uso di variabili e parametri, questo modello può essere riutilizzato per creare altri set di scalabilità o aggiornare i set esistenti. È possibile distribuire i modelli tramite il portale di Azure, l'interfaccia della riga di comando di Azure 2.0 o Azure PowerShell oppure dalle pipeline di integrazione continua/distribuzione continua (CI/CD).

Per altre informazioni sui modelli, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)

Per creare un set di scalabilità con un modello, definire le risorse appropriate. Le parti principali del tipo di risorsa del set di scalabilità di macchine virtuali sono le seguenti:

| Proprietà                     | Descrizione della proprietà                                  | Valore di esempio del modello                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Tipo di risorsa di Azure da creare                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Nome del set di scalabilità                                       | myScaleSet                                |
| location                     | Posizione in cui creare il set di scalabilità                     | Stati Uniti orientali                                   |
| sku.name                     | Dimensioni delle VM per ogni istanza del set di scalabilità                  | Standard_A1                               |
| sku.capacity                 | Numero di istanze di macchina virtuale da creare inizialmente           | 2                                         |
| upgradePolicy.mode           | Modalità di aggiornamento dell'istanza di macchina virtuale in caso di modifiche              | Automatico                                 |
| imageReference               | Piattaforma o immagine personalizzata da usare per le istanze delle macchine virtuali | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Prefisso del nome per ogni istanza di macchina virtuale                     | myvmss                                    |
| osProfile.adminUsername      | Nome utente di ogni istanza di macchina virtuale                        | azureuser                                 |
| osProfile.adminPassword      | Password di ogni istanza di macchina virtuale                        | P@ssw0rd!                                 |

 L'esempio seguente illustra la definizione delle risorse principali del set di scalabilità di macchine virtuali. Per personalizzare un modello di set di scalabilità, è possibile modificare le dimensioni delle macchine virtuali o la capacità iniziale oppure usare una piattaforma diversa o un'immagine personalizzata.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Per sintetizzare l'esempio, la configurazione della scheda di interfaccia di rete virtuale e componenti aggiuntivi quali il servizio di bilanciamento del carico non vengono mostrati. Un esempio completo di set di scalabilità è disponibile [alla fine dell'articolo](#deploy-the-template).


## <a name="install-an-application"></a>Installare un'applicazione
Quando si distribuisce un set di scalabilità, le estensioni di VM possono fornire attività di configurazione e automazione post-distribuzione, ad esempio l'installazione di un'app. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. Per applicare un'estensione al set di scalabilità, si aggiunge la sezione *extensionProfile* all'esempio di risorsa precedente. Il profilo di estensione definisce in genere le proprietà seguenti:

- Tipo di estensione
- Server di pubblicazione dell'estensione
- Versione dell'estensione
- Percorso degli script di configurazione o installazione
- Comandi da eseguire nelle istanze di macchina virtuale

Il modello [Server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) usa l'estensione di script personalizzati per installare [Bottle](http://bottlepy.org/docs/dev/), un framework Web Python, e un semplice server HTTP. 

Sono definiti due script in **fileUris** - *installserver.sh* e *workserver.py*. Questi file vengono scaricati da GitHub, quindi *commandToExecute* esegue `bash installserver.sh` per installare e configurare l'app:

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
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Distribuire il modello
È possibile distribuire il modello [Server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) con il pulsante **Distribuisci in Azure** seguente. Questo pulsante apre il portale di Azure, carica il modello completo e richiede alcuni parametri quali il nome del set di scalabilità, il numero di istanze e le credenziali di amministratore.

[![Distribuire il modello in Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

È anche possibile usare l'interfaccia della riga di comando di Azure 2.0 per installare il server HTTP Python in Linux con [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) come segue:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Rispondere ai prompt per l'inserimento del nome del set di scalabilità, del numero di istanze e delle credenziali di amministratore per le istanze di macchina virtuale. Sono necessari alcuni minuti per creare il set di scalabilità e le risorse di supporto.


## <a name="test-your-sample-application"></a>Testare l'applicazione di esempio
Per vedere l'app in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show) come segue:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser nel formato *http://publicIpAddress:9000/do_work*. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web predefinita in NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse, il set di scalabilità e tutte le risorse correlate non sono più necessari, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuoverli come segue:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi
In questo articolo introduttivo è stato creato un set di scalabilità Linux con un modello di Azure ed è stata usata l'estensione di script personalizzati per installare un server Web Python di base nelle istanze di macchina virtuale. Per ottenere scalabilità e automazione maggiori, espandere il set di scalabilità con le procedure seguenti:

- [Distribuire l'applicazione nei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-deploy-app.md)
- È possibile applicare la scalabilità automatica con l'[interfaccia della riga di comando di Azure](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o il [portale di Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Usare gli aggiornamenti automatici del sistema operativo per le istanze di macchina virtuale del set di scalabilità](virtual-machine-scale-sets-automatic-upgrade.md)
