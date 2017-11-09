---
title: "Distribuire un'applicazione in un set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Informazioni su come distribuire applicazioni nelle istanze di macchine virtuali Linux e Windows in un set di scalabilità"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 7e03d5e2bbdb1b3b206fa7fa455f7dce7951f02b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuire l'applicazione nei set di scalabilità delle macchine virtuali
Per eseguire applicazioni nelle istanze di macchine virtuali (VM) in un set di scalabilità, è necessario prima installare i componenti dell'applicazione e i file necessari. Questo articolo descrive come creare un'immagine personalizzata di macchina virtuale per le istanze in un set di scalabilità o eseguire automaticamente gli script di installazione nelle istanze di macchine virtuali esistenti. Si apprenderà anche come gestire gli aggiornamenti delle applicazioni o del sistema operativo in un set di scalabilità.


## <a name="build-a-custom-vm-image"></a>Creare un'immagine personalizzata della macchina virtuale
Quando si usa una delle immagini della piattaforma Azure per creare le istanze nel set di scalabilità, non viene installato o configurato alcun software aggiuntivo. È possibile automatizzare l'installazione di questi componenti; tuttavia ciò si aggiunge al tempo necessario per eseguire il provisioning delle istanze di macchine virtuali nei set di scalabilità. Se si applicano numerose modifiche di configurazione alle istanze di macchine virtuali, si crea un sovraccarico di gestione con queste attività e script di configurazione.

Per ridurre la gestione della configurazione e il tempo per eseguire il provisioning di una macchina virtuale, è possibile creare un'immagine personalizzata della macchina virtuale pronta per eseguire l'applicazione non appena viene eseguito il provisioning di un'istanza nel set di scalabilità. Il processo generale per creare un'immagine personalizzata della macchina virtuale per le istanze del set di scalabilità è il seguente:

1. Per creare un'immagine personalizzata della macchina virtuale per le istanze nel set di scalabilità, creare e accedere a una macchina virtuale, quindi installare e configurare l'applicazione. È possibile usare Packer per definire e creare un'immagine della macchina virtuale [Linux](../virtual-machines/linux/build-image-with-packer.md) o [Windows](../virtual-machines/windows/build-image-with-packer.md). In alternativa è possibile creare e configurare la macchina virtuale manualmente:

    - Creare una macchina virtuale Linux con l'[interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md) o il [portale](../virtual-machines/linux/quick-create-portal.md).
    - Creare una macchina virtuale Windows con [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), l'[interfaccia della riga di comando di Azure 2.0](../virtual-machines/windows/quick-create-cli.md) o il [portale](../virtual-machines/windows/quick-create-portal.md).
    - Accedere a una macchina virtuale [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) o [Windows](../virtual-machines/windows/connect-logon.md).
    - Installare e configurare le applicazioni e gli strumenti necessari. Se sono necessarie versioni specifiche di una libreria o di un runtime, un'immagine personalizzata della macchina virtuale consente di definire una versione. 

2. Acquisire la macchina virtuale con l'[interfaccia della riga di comando di Azure 2.0](../virtual-machines/linux/capture-image.md) o [Azure PowerShell](../virtual-machines/windows/capture-image.md). Questo passaggio crea l'immagine personalizzata della macchina virtuale che viene usata per distribuire le istanze in un set di scalabilità.

3. [Creare un set di scalabilità](virtual-machine-scale-sets-create.md) e specificare l'immagine personalizzata della macchina virtuale creata nei passaggi precedenti.


## <a name="already-provisioned"></a>Installare un'app con l'estensione dello script personalizzata
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure. 

Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Uso di Azure PowerShell
PowerShell usa una tabella hash per archiviare i file da scaricare e il comando da eseguire. L'esempio seguente:

- Indica alle istanze di macchine virtuali di scaricare uno script da GitHub: *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Imposta l'estensione in modo da eseguire uno script di installazione: `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- Ottiene informazioni su un set di scalabilità con [Get AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Applica l'estensione alle istanze di macchine virtuali con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

L'estensione dello script personalizzata viene applicata alle istanze di macchine virtuali *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*. Immettere nomi personalizzati come di seguito:

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Se il criterio di aggiornamento nel set di scalabilità è *manuale*, aggiornare le istanze di macchine virtuali con [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Questo cmdlet applica la configurazione aggiornata del set di scalabilità alle istanze di macchine virtuali e installa l'applicazione.


### <a name="use-azure-cli-20"></a>Usare l'interfaccia della riga di comando 2.0 di Azure
Per usare l'estensione dello script personalizzata con l'interfaccia della riga di comando di Azure, creare un file JSON che definisca i file da ottenere e i comandi da eseguire. Queste definizioni JSON possono essere riusate nelle distribuzioni del set di scalabilità per applicare installazioni di applicazioni coerenti.

Nella shell corrente creare un file denominato *customConfig.json* e incollare la configurazione seguente. Ad esempio, creare il file in Cloud Shell anziché nel computer locale. È possibile usare qualsiasi editor. Immettere `sensible-editor cloudConfig.json` per creare il file e visualizzare un elenco degli editor disponibili.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Applicare la configurazione dell'estensione dello script personalizzata alle istanze di macchine virtuali nel set di scalabilità con [az vmss extension set](/cli/azure/vmss/extension#set). Nell'esempio seguente viene applicata la configurazione *customConfig.json* alle istanze di macchine virtuali *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*. Immettere nomi personalizzati come di seguito:

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Se il criterio di aggiornamento nel set di scalabilità è *manuale*, aggiornare le istanze di macchine virtuali con [az vmss update-instances](/cli/azure/vmss#update-instances). Questo cmdlet applica la configurazione aggiornata del set di scalabilità alle istanze di macchine virtuali e installa l'applicazione.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installare un'app su una macchina virtuale Windows con PowerShell DSC
[PowerShell DSC (Desired State Configuration)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) è una piattaforma di gestione che permette di definire la configurazione dei computer di destinazione. Le configurazioni DSC definiscono che cosa installare nel computer e come configurare l'host. Un motore di Gestione configurazione locale viene eseguito in ogni nodo di destinazione che elabora le azioni necessarie in base alle configurazioni di cui è stato eseguito il push.

L'estensione PowerShell DSC consente di personalizzare le istanze di macchine virtuali in un set di scalabilità con PowerShell. L'esempio seguente:

- Indica alle istanze di macchine virtuali di scaricare un pacchetto DSC da GitHub - *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Imposta l'estensione in modo da eseguire uno script di installazione: `configure-http.ps1`
- Ottiene informazioni su un set di scalabilità con [Get AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Applica l'estensione alle istanze di macchine virtuali con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

Viene applicata l'estensione DSC alle istanze di macchine virtuali *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*. Immettere nomi personalizzati come di seguito:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Se il criterio di aggiornamento nel set di scalabilità è *manuale*, aggiornare le istanze di macchine virtuali con [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Questo cmdlet applica la configurazione aggiornata del set di scalabilità alle istanze di macchine virtuali e installa l'applicazione.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installare un'app su una VM Linux con cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/latest/) è un approccio diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Quando cloud-init viene eseguito durante il processo di avvio iniziale non vi sono altri passaggi o agenti necessari per applicare la configurazione.

Cloud-init funziona anche fra distribuzioni. Ad esempio, non si usa **apt-get install** o **yum install** per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. Cloud-init userà automaticamente lo strumento di gestione del pacchetto nativo per la distribuzione selezionata.

Per altre informazioni, incluso un file *cloud-init.txt* di esempio, vedere [Usare cloud-init per personalizzare le macchine virtuali di Azure](../virtual-machines/linux/using-cloud-init.md).

Per creare un set di scalabilità e usare un file cloud-init, aggiungere il parametro `--custom-data` al comando [az vmss create](/cli/azure/vmss#create) e specificare il nome di un file cloud-int. L'esempio seguente crea un set di scalabilità denominato *myScaleSet* in *myResourceGroup* e configura le istanze di macchine virtuali con un file denominato *cloud-init.txt*. Immettere nomi personalizzati come di seguito:

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


## <a name="install-applications-as-a-set-scales-out"></a>Installare le applicazioni quando un set scala orizzontalmente
I set di scalabilità consentono di aumentare il numero di istanze di macchine virtuali che eseguono l'app. Questo processo di scalabilità orizzontale può essere avviato manualmente o automaticamente in base a metriche, ad esempio l'utilizzo della CPU o della memoria.

Se è stata applicata l'estensione dello script personalizzata al set di scalabilità, l'applicazione viene installata in ogni nuova istanza della macchina virtuale. Se il set di scalabilità è basato su un'immagine personalizzata con l'applicazione preinstallata, ogni nuova istanza della macchina virtuale viene distribuita in uno stato utilizzabile. 

Se le istanze di macchine virtuali del set di scalabilità sono host contenitori, è possibile usare l'estensione dello script personalizzata per effettuare il pull ed eseguire le immagini contenitore necessarie. L'estensione dello script personalizzata è in grado anche di registrare la nuova istanza di macchina virtuale con un agente di orchestrazione, ad esempio il servizio contenitore di Azure.


## <a name="deploy-application-updates"></a>Distribuire gli aggiornamenti delle applicazioni
Se si aggiorna il codice, le librerie o i pacchetti dell'applicazione, è possibile effettuare il push dello stato più recente dell'applicazione nelle istanze di macchine virtuali in un set di scalabilità. Se si usa l'estensione dello script personalizzata, eseguire l'aggiornamento all'applicazione e non usare la distribuzione automatica. Modificare la configurazione di script personalizzata, ad esempio in modo che punti a uno script di installazione con il nome di una versione aggiornata. Nell'esempio precedente l'estensione dello script personalizzata usa uno script denominato *automate_nginx.sh*, come indicato di seguito:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Tutti gli aggiornamenti apportati all'applicazione non vengono esposti all'estensione dello script personalizzata, a meno che non venga modificato questo script di installazione. Un approccio consiste nell'includere un numero di versione che incrementa con le versioni dell'applicazione. L'estensione dello script personalizzata può ora referenziare *automate_nginx_v2.sh* come indicato di seguito:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

L'estensione dello script personalizzata viene ora eseguita con le istanze di macchine virtuali per applicare gli aggiornamenti più recenti delle applicazioni.


### <a name="install-applications-with-os-updates"></a>Installare le applicazioni con gli aggiornamenti del sistema operativo
Quando sono disponibili nuove versioni del sistema operativo, è possibile usare o creare una nuova immagine personalizzata e [distribuire gli aggiornamenti del sistema operativo](virtual-machine-scale-sets-upgrade-scale-set.md) a un set di scalabilità. Ogni istanza di macchina virtuale viene aggiornata all'immagine più recente specificata. È possibile usare un'immagine personalizzata con l'applicazione preinstallata, l'estensione dello script personalizzata o PowerShell DSC per rendere l'applicazione automaticamente disponibile quando si esegue l'aggiornamento. Potrebbe essere necessario pianificare la manutenzione dell'applicazione durante l'esecuzione di questo processo per verificare che non si siano verificati problemi di compatibilità delle versioni.

Se si usa un'immagine di macchina virtuale personalizzata con l'applicazione preinstallata, è possibile integrare gli aggiornamenti dell'applicazione con una pipeline di distribuzione per creare nuove immagini e distribuire gli aggiornamenti del sistema operativo nel set di scalabilità. Questo approccio consente alla pipeline di prelevare le compilazioni più recenti dell'applicazione, creare e convalidare un'immagine di macchina virtuale, quindi aggiornare le istanze di macchine virtuali nel set di scalabilità. Per eseguire una pipeline di distribuzione che crea e distribuisce gli aggiornamenti dell'applicazione tra le immagini di macchine virtuali personalizzate, è possibile usare [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/) o [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Passaggi successivi
Quando si compilano e distribuiscono applicazioni ai set di scalabilità, è possibile esaminare [Panoramica sulla progettazione di set di scalabilità](virtual-machine-scale-sets-design-overview.md). Per altre informazioni su come gestire il set di scalabilità, vedere [Usare PowerShell per gestire i set di scalabilità](virtual-machine-scale-sets-windows-manage.md).
