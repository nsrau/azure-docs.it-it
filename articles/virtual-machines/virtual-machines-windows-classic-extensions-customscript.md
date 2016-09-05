<properties
   pageTitle="Estensione Script personalizzato per una macchina virtuale di Windows | Microsoft Azure"
   description="Automatizzare le attività di configurazione della macchina virtuale di Azure utilizzando l'estensione dello Script personalizzato per eseguire gli script di PowerShell in una macchina virtuale di Windows remota"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Estensione Script personalizzato per le macchine virtuali di Windows

In questo articolo viene fornita una panoramica dell'uso dell'estensione Script personalizzato per le VM Windows tramite i cmdlet Azure PowerShell con le API Gestione dei servizi.

Le estensioni di macchine virtuali (VM) sono state sviluppate da Microsoft e da autori attendibili di terze parti per estendere la funzionalità della macchina virtuale. Per una panoramica delle estensioni di macchina virtuale, vedere 
[estensioni VM di Azure e funzionalità](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come [eseguire questa procedura con il modello di Resource Manager](virtual-machines-windows-extensions-customscript.md).

## Panoramica dell'estensione Script personalizzato

Con l'estensione Script personalizzato per Windows è possibile eseguire gli script PowerShell in una VM remota senza eseguire l'accesso. È possibile eseguire gli script dopo il provisioning della VM o in qualsiasi momento durante il ciclo di vita della VM senza aprire alcuna porta aggiuntiva. Il caso d'uso più comune per eseguire l'estensione Script personalizzato consiste nell'esecuzione, nell'installazione e nella configurazione di software aggiuntivo sul post-provisioning della VM.

### Prerequisiti per l'esecuzione dell'estensione Script personalizzato

1. Installare i <a href="http://azure.microsoft.com/downloads" target="_blank">cmdlet di Azure PowerShell</a> versione 0.8.0 o successiva.
2. Se si desidera che gli script vengano eseguiti in una VM esistente, assicurarsi che l'agente VM sia abilitato nella VM. Se non è installato, seguire queste [passaggi](virtual-machines-windows-classic-agents-and-extensions.md). Se la VM viene creata dal portale di Azure, l'agente VM è installato per impostazione predefinita.
3. Caricare gli script da eseguire sulla macchina virtuale in Archiviazione di Azure. Gli script possono provenire da uno o più contenitori di archiviazione.
4. Lo script deve essere creato in modo tale che lo script di ingresso che viene avviato dall'estensione a turno avvii altri script.

## Scenari dell'estensione Script personalizzato

### Caricare i file nel contenitore predefinito.

Nell'esempio seguente viene mostrato come eseguire gli script sulla VM se si trovano nel contenitore di archiviazione dell'account predefinito della sottoscrizione. Gli script vengono caricati in ContainerName. È possibile verificare l'account di archiviazione predefinito usando il comando **Get-AzureSubscription –Default**.

Nell'esempio seguente viene creata una nuova VM, ma lo stesso scenario può essere eseguito anche su una VM esistente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Caricare i file in un contenitore di archiviazione non predefinito

In questo caso d'uso viene illustrato come usare un contenitore di archiviazione non predefinito nella stessa sottoscrizione o in una sottoscrizione diversa per il caricamento di script e file. In questo esempio è mostrata una VM esistente, ma le stesse operazioni possono essere eseguite per la creazione di una nuova VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Caricare gli script in più contenitori per diversi account di archiviazione

  Se i file di script vengono archiviati in più contenitori, è necessario fornire l'URL completo delle firme di accesso condiviso (SAS) affinché i file eseguano gli script.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Aggiungere l'estensione Script personalizzato dal portale di Azure

Selezionare la VM nel <a href="https://portal.azure.com/ " target="_blank">portale di Azure</a> e aggiungere l'estensione specificando il file script da eseguire.

  ![Specificare il file di script][5]


### Disinstallazione dell'estensione Script personalizzato

È possibile disinstallare l'estensione Script personalizzato dalla VM usando il comando indicato di seguito.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Uso dell'estensione Script personalizzato con i modelli

Per informazioni su come usare l'estensione Script personalizzato con i modelli di Azure Resource Manager, vedere [Uso dell'estensione Script personalizzato per VM Windows con i modelli di Azure Resource Manager](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0824_2016-->