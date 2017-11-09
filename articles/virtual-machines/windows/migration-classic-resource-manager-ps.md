---
title: Eseguire la migrazione a Resource Manager con PowerShell | Microsoft Docs
description: Questo articolo illustra la migrazione di risorse IaaS supportata dalla piattaforma come macchine virtuali (VM), reti virtuali (VNET) e account di archiviazione dal modello di distribuzione classica ad Azure Resource Manager (ARM) tramite comandi di Azure PowerShell
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 489e6cc6bd3c5b36635f5f7e398d08fed681d2e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell
Questi passaggi mostrano come usare i comandi di Azure PowerShell per eseguire la migrazione di risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager.

È anche possibile eseguire la migrazione delle risorse usando l' [interfaccia della riga di comando di Azure](../linux/migration-classic-resource-manager-cli.md).

* Per informazioni di base su scenari di migrazione supportati, vedere [Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Per una guida approfondita e la procedura dettagliata di migrazione, vedere [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md)

<br>
Ecco un diagramma di flusso per identificare l'ordine di esecuzione dei passaggi durante un processo di migrazione

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Passaggio 1: Pianificare la migrazione
Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager:

* Leggere con attenzione le [funzionalità e configurazioni supportate e non supportate](migration-classic-resource-manager-overview.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, in base alle esigenze è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione.
* Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

> [!IMPORTANT]
> I gateway applicazione non sono attualmente supportati per la migrazione dal modello di distribuzione classica a Resource Manager. Per eseguire la migrazione di una rete virtuale classica con un gateway applicazione, rimuovere il gateway prima di eseguire un'operazione di preparazione dello spostamento della rete. Dopo aver completato la migrazione, riconnettere il gateway in Azure Resource Manager.
>
>Non è possibile eseguire la migrazione automatica di gateway ExpressRoute che si connettono a circuiti ExpressRoute in un'altra sottoscrizione. In tal caso, rimuovere il gateway ExpressRoute, eseguire la migrazione della rete virtuale e ricreare il gateway. Per altre informazioni, vedere [Eseguire la migrazione di circuiti ExpressRoute e delle reti virtuali associate dalla distribuzione classica al modello di distribuzione Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Passaggio 2: Installare la versione più recente di Azure PowerShell
Per l'installazione di Azure PowerShell sono previste due opzioni principali: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](http://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. PowerShell Gallery riceve aggiornamenti su base continua. Questo articolo si basa su Azure PowerShell versione 2.1.0.

Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Passaggio 3: Assicurarsi di essere un amministratore della sottoscrizione nel portale di Azure
Per eseguire la migrazione, è necessario essere aggiunti come coamministratori della sottoscrizione nel [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu Hub, selezionare **Sottoscrizione**. Se questa voce non viene visualizzata, selezionare **Altri servizi**.
3. Cercare la voce di sottoscrizione appropriata, quindi esaminare il campo **Ruolo personale**. Per un coamministratore, il valore deve essere _amministratore account_.

Se non è possibile aggiungere un coamministratore, contattare un amministratore o un coamministratore del servizio per essere aggiunti alla sottoscrizione.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passaggio 4: impostare la sottoscrizione e iscriversi per la migrazione
Avviare prima un prompt di PowerShell. Per la migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager.

Accedere con l'account per il modello di Resource Manager.

```powershell
    Login-AzureRmAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato il nome **My Azure Subscription** per la sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> La registrazione è un passaggio da eseguire una sola volta, ma è necessario provvedervi prima di tentare la migrazione. Senza la registrazione, verrà visualizzato il seguente messaggio di errore:
>
> *BadRequest: Subscription is not registered for migration* (Richiesta non valida: la sottoscrizione non è registrata per la migrazione)
>
>

Registrarsi con il provider di risorse di migrazione usando il comando seguente:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Attendere cinque minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Assicurarsi che RegistrationState sia `Registered` prima di procedere.

Accedere ora con l'account per il modello classico.

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

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passaggio 5: verificare che siano disponibili sufficienti memorie centrali delle macchine virtuali di Azure Resource Manager nell'area di Azure di cui fa parte la distribuzione corrente o la rete virtuale
È possibile usare il comando PowerShell seguente per controllare il numero corrente di memorie centrali in Azure Resource Manager. Per altre informazioni sulle quote di memoria centrale, vedere [Limiti e Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

In questo esempio viene verificata la disponibilità nell'area **Stati Uniti occidentali**. Sostituire il nome dell'area di esempio con il nome della propria area.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Passaggio 6: eseguire i comandi per la migrazione delle risorse IaaS
> [!NOTE]
> Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma tenterà di ripetere l'azione.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passaggio 6.1: Opzione 1 - Eseguire la migrazione delle macchine virtuali in un servizio cloud (non in una rete virtuale)
Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web o di lavoro, il comando restituisce un messaggio di errore.

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

* **Opzione 1. Eseguire la migrazione delle VM a una rete virtuale creata dalla piattaforma**

    Per prima cosa, verificare se è possibile eseguire la migrazione del servizio cloud usando i comandi seguenti:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile passare alla fase di **preparazione**:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opzione 2. Eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager**

    In questo esempio vengono impostati il nome **myResourceGroup** per il gruppo di risorse, il nome **myVirtualNetwork** per la rete virtuale e il nome **mySubNet** per la subnet. Sostituire i nomi dell'esempio con i nomi delle proprie risorse.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Per prima cosa, verificare se sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il seguente passaggio di preparazione:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Dopo aver completato la procedura di preparazione tramite una delle opzioni precedenti, eseguire una query dello stato di migrazione delle VM, verificando che sia `Prepared`.

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

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passaggio 6.1: Opzione 2 - Eseguire la migrazione delle macchine virtuali in un una rete virtuale

Per eseguire la migrazione delle macchine virtuali in una rete virtuale, migrare la rete virtuale. Le macchine virtuali migreranno automaticamente con la rete virtuale. Selezionare la rete virtuale per cui si vuole eseguire la migrazione.
> [!NOTE]
> [Eseguire la migrazione della singola macchina virtuale classica](migrate-single-classic-to-resource-manager.md) creando una nuova macchina virtuale di Resource Manager con dischi gestiti usando i file VHD (sistema operativo e dati) della macchina virtuale.
<br>

> [!NOTE]
> Il nome della rete virtuale potrebbe essere diverso da quello mostrato nel nuovo portale. Il nuovo portale di Azure visualizza il nome come `[vnet-name]` ma il nome effettivo della rete virtuale è di tipo `Group [resource-group-name] [vnet-name]`. Prima della migrazione, controllare il nome effettivo della rete virtuale usando il comando `Get-AzureVnetSite | Select -Property Name` o visualizzarlo nel portale di Azure precedente. 

In questo esempio viene impostato il nome **myVnet** per la rete virtuale. Sostituire il nome della rete virtuale di esempio con il nome della propria rete virtuale.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se la rete virtuale contiene ruoli Web o di lavoro, o VM con configurazioni non supportate, viene visualizzato un messaggio di errore di convalida.
>
>

Per prima cosa, verificare se sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Il comando precedente visualizza eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il seguente passaggio di preparazione:

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

## <a name="step-62-migrate-a-storage-account"></a>Passaggio 6.2: Eseguire la migrazione di un account di archiviazione
Dopo aver completato la migrazione delle macchine virtuali, si consiglia di migrare gli account di archiviazione.

Prima di eseguire la migrazione dell'account di archiviazione, eseguire i controlli dei prerequisiti:

* **Eseguire la migrazione di macchine virtuali classiche con dischi archiviati nell'account di archiviazione**

    Il comando precedente restituisce le proprietà RoleName e DiskName di tutti i dischi delle VM classiche nell'account di archiviazione. RoleName è il nome della macchina virtuale a cui il disco è collegato. Se il comando precedente restituisce uno o più dischi, assicurarsi che le macchine virtuali a cui sono collegati tali dischi siano migrate prima della migrazione degli account di archiviazione.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Eliminare i dischi di VM classiche archiviati nell'account di archiviazione**

    Trovare i dischi di VM classiche nello spazio di archiviazione account tramiti il seguente comando:

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Se il comando sopra restituisce uno o più dischi, eliminare tali dischi tramite il seguente comando:

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Eliminare immagini di VM archiviate nell'account di archiviazione**

    Il comando precedente restituisce tutte le immagini di VM con il disco del sistema operativo archiviato nell'account di archiviazione.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     Il comando precedente restituisce tutte le immagini di VM con il disco dei dati archiviato nell'account di archiviazione.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Eliminare tutte le immagini di VM restituite dal comando precedente tramite il seguente comando:
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

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
