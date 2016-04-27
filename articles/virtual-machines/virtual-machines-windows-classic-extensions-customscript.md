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

In questo articolo viene fornita una panoramica dell'utilizzo dell'estensione Script personalizzato per le macchine virtuali di Windows tramite i cmdlet Azure Powershell.

Le estensioni di macchine virtuali (VM) sono state sviluppate da Microsoft e da autori attendibili di terze parti per estendere la funzionalità della macchina virtuale. Per una panoramica delle estensioni di macchina virtuale, vedere 
[estensioni VM di Azure e funzionalità](virtual-machines-windows-extensions-features.md).

Collegamento:
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-classic-extensions-customscript.md).


## Panoramica dell'estensione Script personalizzato

L'estensione Script personalizzato per Windows consente di eseguire gli script PowerShell in una macchina virtuale remota senza eseguire l'accesso. Gli script possono essere eseguiti dopo il provisioning della VM o in qualsiasi momento durante il ciclo di vita della VM senza la necessità di aprire alcuna porta aggiuntiva nella VM. Il caso di utilizzo più comune di Script personalizzato consiste nell'esecuzione dell'installazione e della configurazione di software aggiuntivo sul post-provisioning della macchina virtuale.

### Prerequisiti per l'esecuzione dell'estensione Script personalizzato

1. Installare i cmdlet di Azure PowerShell versione 0.8.0 o versione successiva da <a href="http://azure.microsoft.com/downloads" target="_blank">qui</a>.
2. Se gli script vengono eseguiti in una macchina virtuale esistente, assicurarsi che l'agente VM sia abilitato nella macchina virtuale; in caso contrario, fare riferimento a questo <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">articolo</a> per installarne uno. (Se si esegue il provisioning della macchina virtuale dalla raccolta di Azure, gli agenti della macchina virtuale sono abilitati per impostazione predefinita e non è necessario abilitarli)
3. Caricare gli script da eseguire sulla macchina virtuale in Archiviazione di Azure. Gli script possono provenire da uno o più contenitori di archiviazione.
4. Lo script deve essere creato in modo tale che lo script di ingresso che viene avviato dall'estensione a turno avvii altri script.

## Scenari dell'estensione Script personalizzato

### Caricare i file nel contenitore predefinito.

Se sono presenti script nel contenitore di archiviazione dell'account predefinito della sottoscrizione, il frammento di cmdlet riportato di seguito illustra come eseguirli nella macchina virtuale. ContainerName indica dove si caricano gli script. L'account di archiviazione predefinito può essere verificato utilizzando il comando **Get-AzureSubscription –Default**.

Nell'esempio seguente viene creata una nuova macchina virtuale, ma lo stesso scenario può essere eseguito anche su una macchina virtuale esistente.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Caricare i file in un contenitore di archiviazione non predefinito

In questo caso di utilizzo viene illustrato come utilizzare un archivio non predefinito nella stessa sottoscrizione o in una sottoscrizione diversa per il caricamento di script e file. Di seguito viene utilizzata una macchina virtuale esistente, ma le stesse operazioni possono essere eseguite per la creazione di una nuova macchina virtuale.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Caricare gli script in più contenitori per diversi account di archiviazione

  Se i file di script vengono archiviati in più contenitori, per eseguire tali script attualmente è necessario fornire l'URL SAS completo dei file.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Aggiungere l'estensione Script personalizzato dal portale di Azure

Selezionare la VM nel <a href="https://portal.azure.com/ " target="_blank">portale di Azure</a> e aggiungere l'estensione specificando il file script da eseguire.

  ![][5]


### Disinstallare l'estensione Script personalizzato

L'estensione Script personalizzato può essere disinstallata dalla macchina virtuale utilizzando il comando indicato di seguito.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Uso dell’estensione Script personalizzato con i modelli

Per ulteriori informazioni sull’utilizzo dell’estensione di script personalizzato con modelli di gestione risorse di Azure, vedere la documentazione [qui](virtual-machines-windows-classic-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0413_2016-->