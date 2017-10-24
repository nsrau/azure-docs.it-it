---
title: "Distribuire un'app nei set di scalabilità di macchine virtuali"
description: "Usare le estensioni per distribuire un'app nel set di scalabilità della macchina virtuale di Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 371295efea1eab66361b9aba21a55bbd2826c69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuire l'applicazione nei set di scalabilità delle macchine virtuali

Questo articolo descrive diverse modalità di installazione di software durante il provisioning del set di scalabilità.

È possibile esaminare l'articolo [Panoramica sulla progettazione del set di scalabilità](virtual-machine-scale-sets-design-overview.md) che descrive alcuni dei limiti imposti dal set di scalabilità della macchina virtuale.

## <a name="capture-and-reuse-an-image"></a>Acquisire e riusare un'immagine

È possibile usare una macchina virtuale disponibile in Azure per preparare un'immagine di base per il set di scalabilità. Questo processo crea un disco gestito nell'account di archiviazione, a cui è possibile fare riferimento come immagine di base per il set di scalabilità. 

Seguire anche questa procedura:

1. Creare una macchina virtuale di Azure
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. Connettersi da remoto alla macchina virtuale e personalizzare il sistema in base alle esigenze.

   Se lo si desidera, a questo punto è possibile installare l'applicazione. Tuttavia, tenere presente che per installare l'applicazione a questo punto potrebbe essere necessario un'operazione di aggiornamento più complessa perché potrebbe essere prima necessario rimuoverla. In alternativa, è possibile usare questo passaggio per installare i prerequisiti necessari all'applicazione, ad esempio una funzionalità specifica di runtime o del sistema operativo.

3. Seguire l'esercitazione "Acquisire una macchina" per [Linux][linux-vm-capture] o [Windows][windows-vm-capture].

4. Creare un [set di scalabilità della macchina virtuale][vmss-create] con l'URI dell'immagine acquisito nel passaggio precedente.

Per altre informazioni sui dischi, vedere [Panoramica di Managed Disks](../virtual-machines/windows/managed-disks-overview.md) e [Usare dischi dati collegati](virtual-machine-scale-sets-attached-disks.md).

## <a name="already-provisioned"></a>Installare quando viene eseguito il provisioning del set di scalabilità

Le estensioni della macchina virtuale possono essere applicate a un set di scalabilità della macchina virtuale. Con un'estensione della macchina virtuale, è possibile personalizzare le macchine virtuali in un set di scalabilità come gruppo intero. Per altre informazioni sulle estensioni, vedere [Estensioni della macchina virtuale](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

È possibile usare tre principali estensioni, a seconda che il sistema operativo si basi su Windows o Linux.

### <a name="windows"></a>Windows

Per un sistema operativo basato su Windows, usare l'estensione **Custom Script v1.8** o l'estensione **PowerShell DSC**.

#### <a name="custom-script"></a>Custom Script

L'estensione Custom Script esegue uno script in ogni istanza della macchina virtuale nel set di scalabilità. Un file di configurazione o una variabile indica i file che vengono scaricati nella macchina virtuale, quindi il comando eseguito. Questi vengono usati ad esempio per eseguire un programma di installazione, uno script, un file batch o un file eseguibile.

PowerShell usa una tabella hash per le impostazioni. Questo esempio configura l'estensione dello script personalizzato per eseguire uno script di PowerShell che consente di installare IIS.

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Usare lo switch `-ProtectedSetting` per tutte le impostazioni che potrebbero contenere informazioni riservate.

---------


L'interfaccia della riga di comando di Azure usa un file JSON per le impostazioni. Questo esempio configura l'estensione dello script personalizzato per eseguire uno script di PowerShell che consente di installare IIS. Salvare il file JSON seguente come _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

Quindi, eseguire questo comando dell'interfaccia della riga di comando di Azure.

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Usare lo switch `--protected-settings` per tutte le impostazioni che potrebbero contenere informazioni riservate.

### <a name="powershell-dsc"></a>PowerShell DSC

È possibile usare PowerShell DSC per personalizzare le istanze della macchina virtuale del set di scalabilità. L'estensione **DSC** pubblicata da **Microsoft.Powershell** distribuisce ed esegue la configurazione DSC indicata in ogni istanza della macchina virtuale. Un file di configurazione o una variabile indica l'estensione in cui si trova il pacchetto *.zip* e la combinazione di _funzione e script_ da eseguire.

PowerShell usa una tabella hash per le impostazioni. Questo esempio distribuisce un pacchetto DSC che consente di installare IIS.

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>Usare lo switch `-ProtectedSetting` per tutte le impostazioni che potrebbero contenere informazioni riservate.

-----------

L'interfaccia della riga di comando di Azure usa un file JSON per le impostazioni. Questo esempio distribuisce un pacchetto DSC che consente di installare IIS. Salvare il file JSON seguente come _settings.json_.

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

Quindi, eseguire questo comando dell'interfaccia della riga di comando di Azure.

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Usare lo switch `--protected-settings` per tutte le impostazioni che potrebbero contenere informazioni riservate.

### <a name="linux"></a>Linux

Durante la creazione Linux può usare l'estensione **Custom Script v2.0** o **cloud-init**.

Custom script è un'estensione semplice che scarica i file per istanze di macchine virtuali ed esegue un comando.

#### <a name="custom-script"></a>Custom Script

Salvare il file JSON seguente come _settings.json_.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Usare l'interfaccia della riga di comando di Azure per aggiungere questa estensione a un set di scalabilità della macchina virtuale esistente. Ogni macchina virtuale nel set di scalabilità esegue automaticamente l'estensione.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>Usare lo switch `--protected-settings` per tutte le impostazioni che potrebbero contenere informazioni riservate.

#### <a name="cloud-init"></a>cloud-init

Cloud-Init viene usata durante la creazione del set di scalabilità. Innanzitutto, creare un file locale denominato _cloud-init.txt_ e aggiungere la configurazione. Ad esempio, vedere [questo gist](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt)

Usare l'interfaccia della riga di comando di Azure per creare un set di scalabilità. Il campo `--custom-data` accetta il nome del file di uno script cloud-init.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>Gestione degli aggiornamenti dell'applicazione

Se l'applicazione è stata distribuita tramite un'estensione, modificare la definizione dell'estensione. Questa modifica fa sì che l'estensione venga ridistribuita a tutte le istanze della macchina virtuale. **È necessario** modificare alcuni elementi dell'estensione, ad esempio è possibile rinominare un file di riferimento, altrimenti Azure non vede la modifica apportata all'estensione.

Se è stato eseguito un backup dell'applicazione nell'immagine del sistema operativo, usare una pipeline di distribuzione automatica per gli aggiornamenti dell'applicazione. Progettare l'architettura per facilitare il passaggio rapido di un set di scalabilità temporaneo in produzione. Un buon esempio di questo approccio è rappresentato dall'[uso del driver Spinnaker di Azure](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

[Packer](https://www.packer.io/) e [Terraform](https://www.terraform.io/) supportano Azure Resource Manager; pertanto, è anche possibile definire le immagini "come codice" e compilarle in Azure, quindi usare il disco rigido virtuale nel set di scalabilità. Tuttavia, tale approccio diventerebbe problematico per le immagini Marketplace, in cui gli script personalizzati/le estensioni acquistano importanza in quanto i bit non vengono modificati direttamente da Marketplace.

## <a name="what-happens-when-a-scale-set-scales-out"></a>Cosa accade in caso di aumento della capacità di un set di scalabilità
Quando si aggiungono una o più macchine virtuali a un set di scalabilità, l'applicazione viene installata automaticamente. Se ad esempio il set di scalabilità ha estensioni definite, queste vengono eseguite ogni volta che viene creata una nuova macchina virtuale. Se il set di scalabilità è basato su un'immagine personalizzata, qualsiasi nuova macchina virtuale è una copia dell'immagine di origine personalizzata. Se le macchine virtuali del set di scalabilità sono host del contenitore, potrebbe essere necessario il codice di avvio per caricare i contenitori in un'estensione Custom Script. In alternativa, un'estensione potrebbe installare un agente che esegue la registrazione con un agente di orchestrazione del cluster, ad esempio il servizio contenitore di Azure.


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Come distribuire un aggiornamento del sistema operativo nei domini di aggiornamento
Si supponga di voler aggiornare un'immagine del sistema operativo mantenendo in esecuzione il set di scalabilità della macchina virtuale. PowerShell e l'interfaccia della riga di comando di Azure possono aggiornare le immagini della macchina virtuale, per una macchina virtuale alla volta. L'articolo [Aggiornare un set di scalabilità di macchine virtuali](./virtual-machine-scale-sets-upgrade-scale-set.md) include altre informazioni sulle opzioni disponibili per eseguire aggiornamenti del sistema operativo in un set di scalabilità della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

* [Usare PowerShell per gestire il set di scalabilità.](virtual-machine-scale-sets-windows-manage.md)
* [Creare un modello del set di scalabilità.](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md

