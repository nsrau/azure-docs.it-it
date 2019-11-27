---
title: Eseguire la migrazione a Gestione risorse con PowerShell
description: Questo articolo illustra la migrazione supportata dalla piattaforma di risorse IaaS, ad esempio macchine virtuali (VM), reti virtuali e account di archiviazione, dal modello classico al Azure Resource Manager usando Azure PowerShell comandi
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f87e7795416431305141de24497e9760eb03641e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484367"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Eseguire la migrazione di risorse IaaS dal modello classico al Azure Resource Manager usando PowerShell
Questi passaggi mostrano come usare i comandi di Azure PowerShell per eseguire la migrazione di risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager.

Se si vuole, è anche possibile eseguire la migrazione delle risorse usando l'interfaccia della riga di comando di [Azure](../linux/migration-classic-resource-manager-cli.md).

* Per informazioni di base su scenari di migrazione supportati, vedere [Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Per una guida approfondita e la procedura dettagliata di migrazione, vedere [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Esaminare gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md).

<br>
Di seguito è riportato un diagramma di flusso per identificare l'ordine in cui i passaggi devono essere eseguiti durante un processo di migrazione.

![Schermata che mostra i passaggi di migrazione](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Passaggio 1: Pianificare la migrazione
Di seguito sono riportate alcune procedure consigliate per valutare se eseguire la migrazione di risorse IaaS dal modello classico al Gestione risorse:

* Leggere con attenzione le [funzionalità e configurazioni supportate e non supportate](migration-classic-resource-manager-overview.md). Se sono presenti macchine virtuali che usano configurazioni o funzionalità non supportate, attendere che venga annunciata la configurazione o il supporto della funzionalità. In alternativa, in base alle esigenze è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione.
* Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

> [!IMPORTANT]
> I gateway applicazione non sono attualmente supportati per la migrazione dal modello classico al Gestione risorse. Per eseguire la migrazione di una rete virtuale con un gateway applicazione, rimuovere il gateway prima di eseguire un'operazione di preparazione per spostare la rete. Dopo aver completato la migrazione, riconnettere il gateway in Azure Resource Manager.
>
> Non è possibile eseguire la migrazione automatica dei gateway ExpressRoute di Azure che si connettono a circuiti ExpressRoute in un'altra sottoscrizione. In questi casi, rimuovere il gateway ExpressRoute, eseguire la migrazione della rete virtuale e ricreare il gateway. Per altre informazioni, vedere [eseguire la migrazione dei circuiti ExpressRoute e delle reti virtuali associate dal modello di distribuzione classica al modello Gestione risorse](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Passaggio 2: installare la versione più recente di PowerShell
Per l'installazione di Azure PowerShell sono previste due opzioni principali: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](https://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. PowerShell Gallery riceve aggiornamenti su base continua. Questo articolo si basa su Azure PowerShell versione 2.1.0.

Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Passaggio 3: assicurarsi di essere un amministratore per la sottoscrizione
Per eseguire questa migrazione, è necessario essere aggiunti come coamministratore per la sottoscrizione nel [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu **Hub** selezionare **sottoscrizione**. Se questa voce non viene visualizzata, selezionare **Tutti i servizi**.
3. Individuare la voce della sottoscrizione appropriata e quindi esaminare il campo **ruolo personale** . Per un coamministratore, il valore deve essere _amministratore account_.

Se non si è in grado di aggiungere un coamministratore, contattare un amministratore del servizio o un coamministratore per richiedere l'aggiunta della sottoscrizione.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passaggio 4: impostare la sottoscrizione e iscriversi per la migrazione
Avviare prima un prompt di PowerShell. Per la migrazione, configurare l'ambiente sia per la versione classica che per la Gestione risorse.

Accedere con l'account per il modello di Resource Manager.

```powershell
    Connect-AzAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato il nome **My Azure Subscription** per la sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> La registrazione è un passaggio singolo, ma è necessario eseguire una volta prima di tentare la migrazione. Senza la registrazione, verrà visualizzato il seguente messaggio di errore:
>
> *BadRequest: Subscription is not registered for migration* (Richiesta non valida: la sottoscrizione non è registrata per la migrazione)

Registrarsi con il provider di risorse di migrazione usando il comando seguente:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Attendere cinque minuti per il completamento della registrazione. Verificare lo stato dell'approvazione usando il comando seguente:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Assicurarsi che RegistrationState sia `Registered` prima di procedere.

A questo punto, accedere al proprio account per il modello di distribuzione classica.

```powershell
    Add-AzureAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato **My Azure Subscription** come sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-have-enough-resource-manager-vm-vcpus"></a>Passaggio 5: disporre di un numero sufficiente di Gestione risorse VM vCPU
Assicurarsi di disporre di un numero sufficiente Azure Resource Manager macchina virtuale vCPU nell'area di Azure della distribuzione o della rete virtuale corrente. È possibile usare il comando PowerShell seguente per controllare il numero corrente di CPU virtuali in Azure Resource Manager. Per altre informazioni sulle quote di CPU virtuali, vedere [Limiti e Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

In questo esempio viene verificata la disponibilità nell'area **Stati Uniti occidentali**. Sostituire il nome dell'area di esempio con il nome della propria area.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Passaggio 6: eseguire i comandi per la migrazione delle risorse IaaS
* [Eseguire la migrazione di macchine virtuali in un servizio cloud (non in una rete virtuale)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Eseguire la migrazione di macchine virtuali in una rete virtuale](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Eseguire la migrazione di un account di archiviazione](#step-62-migrate-a-storage-account)

> [!NOTE]
> Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma tenterà di ripetere l'azione.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passaggio 6.1: Opzione 1 - Eseguire la migrazione delle macchine virtuali in un servizio cloud (non in una rete virtuale)
Ottenere l'elenco dei servizi cloud usando il comando seguente. Quindi selezionare il servizio cloud di cui si vuole eseguire la migrazione. Se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web o di lavoro, il comando restituisce un messaggio di errore.

```powershell
    Get-AzureService | ft Servicename
```

Ottenere il nome della distribuzione per il servizio cloud. In questo esempio il nome del servizio è **My Service**. Sostituire il nome del servizio di esempio con il nome del proprio servizio.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

* **Opzione 1: eseguire la migrazione delle macchine virtuali a una rete virtuale creata dalla piattaforma.**

    Prima di tutto, verificare che sia possibile eseguire la migrazione del servizio cloud usando i comandi seguenti:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile passare al passaggio di preparazione.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opzione 2: eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Gestione risorse.**

    Questo esempio imposta il nome del gruppo di risorse su **myResourceGroup**, il nome della rete virtuale su **myVirtualNetwork**e il nome della subnet su **subnet**. Sostituire i nomi dell'esempio con i nomi delle proprie risorse.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Prima di tutto, verificare che sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il passaggio di preparazione seguente:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Dopo aver completato la procedura di preparazione tramite una delle opzioni precedenti, eseguire una query dello stato di migrazione delle VM, Verificare che si trovino nello stato `Prepared`.

In questo esempio viene impostato il nome **myVM** per la VM. Sostituire il nome di esempio con il nome della propria VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Controllare la configurazione per le risorse preparate tramite PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passaggio 6.1: Opzione 2 - Eseguire la migrazione delle macchine virtuali in un una rete virtuale

Per eseguire la migrazione delle macchine virtuali in una rete virtuale, migrare la rete virtuale. Le macchine virtuali migreranno automaticamente con la rete virtuale. Selezionare la rete virtuale per cui si vuole eseguire la migrazione.
> [!NOTE]
> [Eseguire la migrazione di una singola macchina virtuale](migrate-single-classic-to-resource-manager.md) creata usando il modello di distribuzione classica creando una nuova macchina virtuale Gestione risorse con Managed disks usando i file VHD (sistema operativo e dati) della macchina virtuale.
<br>

> [!NOTE]
> Il nome della rete virtuale potrebbe essere diverso da quello visualizzato nel nuovo portale. Il nuovo portale di Azure Visualizza il nome come `[vnet-name]`, ma il nome effettivo della rete virtuale è di tipo `Group [resource-group-name] [vnet-name]`. Prima di iniziare la migrazione, cercare il nome effettivo della rete virtuale usando il comando `Get-AzureVnetSite | Select -Property Name` o visualizzarlo nella portale di Azure precedente. 

In questo esempio viene impostato il nome **myVnet** per la rete virtuale. Sostituire il nome della rete virtuale di esempio con il nome della propria rete virtuale.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se la rete virtuale contiene ruoli Web o di lavoro, o VM con configurazioni non supportate, viene visualizzato un messaggio di errore di convalida.

Prima di tutto, verificare che sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il passaggio di preparazione seguente:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Controllare la configurazione per le macchine virtuali preparate usando Azure PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Passaggio 6,2: eseguire la migrazione di un account di archiviazione
Al termine della migrazione delle macchine virtuali, eseguire i controlli dei prerequisiti seguenti prima di eseguire la migrazione degli account di archiviazione.

> [!NOTE]
> Se all'account di archiviazione non è associato alcun disco o dati della macchina virtuale, è possibile passare direttamente alla sezione "convalida degli account di archiviazione e avvio della migrazione".

* Controlli dei prerequisiti se è stata eseguita la migrazione di qualsiasi macchina virtuale o l'account di archiviazione ha risorse disco:
    * Eseguire la migrazione di macchine virtuali i cui dischi sono archiviati nell'account di archiviazione.

        Il comando seguente restituisce le proprietà RoleName e DiskName di tutti i dischi delle macchine virtuali nell'account di archiviazione. RoleName è il nome della macchina virtuale a cui il disco è collegato. Se questo comando restituisce dischi, assicurarsi che le macchine virtuali a cui sono collegati questi dischi vengano migrate prima di eseguire la migrazione dell'account di archiviazione.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Eliminare i dischi delle macchine virtuali non collegati archiviati nell'account di archiviazione.

        Trovare i dischi delle macchine virtuali non collegati nell'account di archiviazione usando il comando seguente:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se il comando precedente restituisce dischi, eliminare questi dischi usando il comando seguente:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Eliminare le immagini di VM archiviate nell'account di archiviazione.

        Il comando seguente restituisce tutte le immagini di macchina virtuale con dischi del sistema operativo memorizzati nell'account di archiviazione.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Il comando seguente restituisce tutte le immagini di macchina virtuale con dischi dati archiviati nell'account di archiviazione.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Eliminare tutte le immagini di VM restituite dai comandi precedenti utilizzando questo comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Convalidare gli account di archiviazione e avviare la migrazione.

    Convalidare ogni account di archiviazione per la migrazione con il comando che segue. In questo esempio il nome dell'account di archiviazione è **myStorageAccount**. Sostituire il nome di esempio con il nome del proprio account di archiviazione.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Il passaggio successivo consiste nella preparazione dell'account di archiviazione per la migrazione.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Controllare la configurazione per l'account di archiviazione preparato tramite Azure PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Overview of platform-supported migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Panoramica sulla migrazione supportata dalla piattaforma per risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
