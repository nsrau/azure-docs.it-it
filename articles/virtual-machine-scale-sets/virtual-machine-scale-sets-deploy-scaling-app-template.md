---
title: Creare un set di scalabilità di macchine virtuali con un modello di Azure | Microsoft Docs
description: Informazioni su come creare rapidamente un set di scalabilità di macchine virtuali con un modello di Azure Resource Manager
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
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: fc5c4166464ec43833bd49ac68fa52e8da8892e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Creare un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questo articolo introduttivo viene creato un set di scalabilità di macchine virtuali usando un modello di Azure Resource Manager. È anche possibile creare un set di scalabilità con l'[interfaccia della riga di comando di Azure 2.0](quick-create-cli.md), [Azure PowerShell](quick-create-powershell.md) oppure il [portale di Azure](quick-create-portal.md).


## <a name="overview-of-templates"></a>Panoramica dei modelli
I modelli di Azure Resource Manager consentono di distribuire gruppi di risorse correlate. I modelli sono scritti in JavaScript Object Notation (JSON) e definiscono l'intero ambiente dell'infrastruttura di Azure per l'applicazione. In un singolo modello è possibile creare il set di scalabilità di macchine virtuali, installare le applicazioni e configurare le regole di scalabilità automatica. Con l'uso di variabili e parametri, questo modello può essere riutilizzato per creare altri set di scalabilità o aggiornare i set esistenti. È possibile distribuire i modelli tramite il portale di Azure, l'interfaccia della riga di comando di Azure 2.0 o Azure PowerShell oppure dalle pipeline di integrazione continua/distribuzione continua (CI/CD).

Per altre informazioni sui modelli, vedere [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)


## <a name="define-a-scale-set"></a>Definire un set di scalabilità
Un modello definisce la configurazione di ciascun tipo di risorsa. Un tipo di risorsa del set di scalabilità di macchine virtuali è simile a una singola VM. Le parti principali del tipo di risorsa del set di scalabilità di macchine virtuali sono le seguenti:

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

 Il frammento di codice seguente illustra la definizione delle risorse principali del set di scalabilità in un modello. Per sintetizzare l'esempio, la configurazione della scheda di interfaccia di rete virtuale Per personalizzare un modello di set di scalabilità, è possibile modificare le dimensioni delle macchine virtuali o la capacità iniziale oppure usare una piattaforma diversa o un'immagine personalizzata.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
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


## <a name="install-an-application"></a>Installare un'applicazione
Quando si distribuisce un set di scalabilità, le estensioni di VM possono fornire attività di configurazione e automazione post-distribuzione, ad esempio l'installazione di un'app. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. Per applicare un'estensione al set di scalabilità, si aggiunge la sezione *extensionProfile* all'esempio di risorsa precedente. Il profilo di estensione definisce in genere le proprietà seguenti:

- Tipo di estensione
- Server di pubblicazione dell'estensione
- Versione dell'estensione
- Percorso degli script di configurazione o installazione
- Comandi da eseguire nelle istanze di macchina virtuale

Di seguito sono illustrate due modalità per installare un'applicazione con estensioni: con l'estensione di script personalizzati per installare un'app Python in Linux o con l'estensione DSC di PowerShell per installare un'app ASP.NET in Windows.

### <a name="python-http-server-on-linux"></a>Server HTTP Python in Linux
Il modello [Server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) usa l'estensione di script personalizzati per installare [Bottle](http://bottlepy.org/docs/dev/), un framework Web Python, e un semplice server HTTP. 

Sono definiti due script in *fileUris* - *installserver.sh* e *workserver.py*. Questi file vengono scaricati da GitHub, quindi *commandToExecute* definisce `bash installserver.sh` per l'installazione e la configurazione dell'app:

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

### <a name="aspnet-application-on-windows"></a>Applicazione ASP.NET in Windows
Il modello di esempio [Applicazione ASP.NET in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) usa l'estensione DSC di PowerShell per installare un'app ASP.NET MVC eseguita in IIS. 

Viene scaricato uno script di installazione da GitHub, come definito in *url*. L'estensione esegue quindi *InstallIIS* dallo script *IISInstall.ps1*, come definito in *function* e *Script*. L'app ASP.NET viene fornita come pacchetto di distribuzione Web scaricato da GitHub, come definito in *WebDeployPackagePath*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Distribuire il modello
Il modo più semplice per distribuire il modello [Server HTTP Python in Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) o [Applicazione ASP.NET MVC in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) consiste nell'usare il pulsante **Distribuisci in Azure** presente nei file Leggimi in GitHub.  È inoltre possibile usare PowerShell o l'interfaccia della riga di comando di Azure per distribuire i modelli di esempio.

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
È possibile usare l'interfaccia della riga di comando di Azure 2.0 per installare il server HTTP Python in Linux come segue:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Per vedere l'app in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show) come segue:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser nel formato *http://<publicIpAddress>:9000/do_work*. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web predefinita in NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
È possibile usare Azure PowerShell per installare l'applicazione ASP.NET in Windows come segue:

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

Per vedere l'app in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) come segue:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser nel formato *http://<publicIpAddress>/MyApp*. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Esecuzione del sito IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Quando il gruppo di risorse, il set di scalabilità e tutte le risorse correlate non sono più necessari, è possibile usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuoverli come segue:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi
