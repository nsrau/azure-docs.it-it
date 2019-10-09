---
title: Distribuire un'applicazione in un set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come distribuire applicazioni nelle istanze di macchine virtuali Linux e Windows in un set di scalabilità
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 996006c60e754437f8f863c7e7a72c929ed77f2c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166216"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuire l'applicazione nei set di scalabilità delle macchine virtuali

Per eseguire applicazioni nelle istanze di macchine virtuali (VM) in un set di scalabilità, è necessario prima installare i componenti dell'applicazione e i file necessari. Questo articolo descrive come creare un'immagine personalizzata di macchina virtuale per le istanze in un set di scalabilità o eseguire automaticamente gli script di installazione nelle istanze di macchine virtuali esistenti. Si apprenderà anche come gestire gli aggiornamenti delle applicazioni o del sistema operativo in un set di scalabilità.


## <a name="build-a-custom-vm-image"></a>Creare un'immagine personalizzata della macchina virtuale
Quando si usa una delle immagini della piattaforma Azure per creare le istanze nel set di scalabilità, non viene installato o configurato alcun software aggiuntivo. È possibile automatizzare l'installazione di questi componenti; tuttavia ciò si aggiunge al tempo necessario per eseguire il provisioning delle istanze di macchine virtuali nei set di scalabilità. Se si applicano numerose modifiche di configurazione alle istanze di macchine virtuali, si crea un sovraccarico di gestione con queste attività e script di configurazione.

Per ridurre la gestione della configurazione e il tempo per eseguire il provisioning di una macchina virtuale, è possibile creare un'immagine personalizzata della macchina virtuale pronta per eseguire l'applicazione non appena viene eseguito il provisioning di un'istanza nel set di scalabilità. Per altre informazioni su come creare e usare un'immagine di VM personalizzata con un set di scalabilità, vedere le esercitazioni seguenti:

- [Interfaccia della riga di comando di Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Installare un'app con l'estensione dello script personalizzata
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. Per altre informazioni su come creare e usare un'immagine di VM personalizzata con un set di scalabilità, vedere le esercitazioni seguenti:

- [Interfaccia della riga di comando di Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Modello di Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installare un'app su una macchina virtuale Windows con PowerShell DSC
[PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview) è una piattaforma di gestione che permette di definire la configurazione dei computer di destinazione. Le configurazioni DSC definiscono che cosa installare nel computer e come configurare l'host. Un motore di Gestione configurazione locale viene eseguito in ogni nodo di destinazione che elabora le azioni necessarie in base alle configurazioni di cui è stato eseguito il push.

L'estensione PowerShell DSC consente di personalizzare le istanze di macchine virtuali in un set di scalabilità con PowerShell. L'esempio seguente:

- Indica alle istanze di macchine virtuali di scaricare un pacchetto DSC da GitHub: *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Imposta l'estensione in modo da eseguire uno script di installazione: `configure-http.ps1`
- Ottiene informazioni su un set di scalabilità con [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Applica l'estensione alle istanze di macchine virtuali con [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

Viene applicata l'estensione DSC alle istanze di macchine virtuali *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*. Immettere i nomi personalizzati nel modo seguente:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Se il criterio di aggiornamento nel set di scalabilità è *manuale*, aggiornare le istanze di macchine virtuali con [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Questo cmdlet applica la configurazione aggiornata del set di scalabilità alle istanze di macchine virtuali e installa l'applicazione.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installare un'app su una VM Linux con cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) è un approccio diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Quando cloud-init viene eseguito durante il processo di avvio iniziale non vi sono altri passaggi o agenti necessari per applicare la configurazione.

Cloud-init funziona anche fra distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. Cloud-init userà automaticamente lo strumento di gestione del pacchetto nativo per la distribuzione selezionata.

Per altre informazioni, incluso un file *cloud-init.txt* di esempio, vedere [Usare cloud-init per personalizzare le macchine virtuali di Azure](../virtual-machines/linux/using-cloud-init.md).

Per creare un set di scalabilità e usare un file cloud-init, aggiungere il parametro `--custom-data` al comando [az vmss create](/cli/azure/vmss) e specificare il nome di un file cloud-int. L'esempio seguente crea un set di scalabilità denominato *myScaleSet* in *myResourceGroup* e configura le istanze di macchine virtuali con un file denominato *cloud-init.txt*. Immettere i nomi personalizzati nel modo seguente:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Installare le applicazioni con gli aggiornamenti del sistema operativo
Quando sono disponibili nuove versioni del sistema operativo, è possibile usare o creare una nuova immagine personalizzata e [distribuire gli aggiornamenti del sistema operativo](virtual-machine-scale-sets-upgrade-scale-set.md) a un set di scalabilità. Ogni istanza di macchina virtuale viene aggiornata all'immagine più recente specificata. È possibile usare un'immagine personalizzata con l'applicazione preinstallata, l'estensione dello script personalizzata o PowerShell DSC per rendere l'applicazione automaticamente disponibile quando si esegue l'aggiornamento. Potrebbe essere necessario pianificare la manutenzione dell'applicazione durante l'esecuzione di questo processo per verificare che non si siano verificati problemi di compatibilità delle versioni.

Se si usa un'immagine di macchina virtuale personalizzata con l'applicazione preinstallata, è possibile integrare gli aggiornamenti dell'applicazione con una pipeline di distribuzione per creare nuove immagini e distribuire gli aggiornamenti del sistema operativo nel set di scalabilità. Questo approccio consente alla pipeline di prelevare le compilazioni più recenti dell'applicazione, creare e convalidare un'immagine di macchina virtuale, quindi aggiornare le istanze di macchine virtuali nel set di scalabilità. Per eseguire una pipeline di distribuzione che crea e distribuisce gli aggiornamenti dell'applicazione tra le immagini di macchine virtuali personalizzate, è possibile [creare un'immagine Packer e distribuirla con Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset) oppure usare un'altra piattaforma come [Spinnaker](https://www.spinnaker.io/) o [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Passaggi successivi
Quando si compilano e distribuiscono applicazioni ai set di scalabilità, è possibile esaminare [Panoramica sulla progettazione di set di scalabilità](virtual-machine-scale-sets-design-overview.md). Per altre informazioni su come gestire il set di scalabilità, vedere [Usare PowerShell per gestire i set di scalabilità](virtual-machine-scale-sets-windows-manage.md).
