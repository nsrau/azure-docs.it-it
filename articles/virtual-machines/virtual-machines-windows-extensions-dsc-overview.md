---
title: Panoramica di DSC (Desired State Configuration) per Azure | Microsoft Docs
description: "Panoramica sull&quot;uso del gestore dell&quot;estensione di Microsoft Azure per PowerShell DSC (Desired State Configuration). Inclusi prerequisiti, architettura, cmdlet e così via."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: c2ce603e80243584fdc302c545e520b4503f5555
ms.openlocfilehash: ca2d8d4b277f48ec46156293f73b18b6c2967c51


---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

L'agente di macchine virtuali di Azure e le relative estensioni associate fanno parte dei servizi di infrastruttura di Microsoft Azure. Le estensioni di VM sono componenti software che estendono la funzionalità di una VM e semplificano varie operazioni di gestione delle VM. Ad esempio, l'estensione VMAccess consente di reimpostare la password dell'amministratore; l'estensione Script personalizzato invece può essere usata per eseguire uno script nella VM.

Questo articolo illustra l'estensione DSC (Desired State Configuration) PowerShell per le VM di Azure in Azure PowerShell SDK. È possibile usare i nuovi cmdlet per caricare e applicare una configurazione DSC PowerShell in una macchina virtuale di Azure abilitata con l'estensione DSC PowerShell. L'estensione DSC PowerShell esegue una chiamata in PowerShell DSC per applicare la configurazione DSC ricevuta nella VM. Questa funzionalità è disponibile anche tramite il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti
**Computer locale** : per interagire con l'estensione della VM di Azure, è necessario usare il portale di Azure o Azure PowerShell SDK. 

**Agente guest** : la VM di Azure cui applicare la configurazione DSC deve essere un sistema operativo che supporta Windows Management Framework 4.0 o 5.0. L'elenco completo delle versioni dei sistemi operativi supportati è disponibile nella [cronologia delle versioni dell'estensione DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termini e concetti
Questa guida presuppone che si abbia familiarità con i concetti seguenti:

Configurazione: documento di configurazione DSC. 

Nodo: destinazione per una configurazione DSC. In questo documento, "nodo" fa sempre riferimento a una VM di Azure.

Dati di configurazione: file con estensione psd1 contenente i dati ambientali per una configurazione.

## <a name="architectural-overview"></a>Panoramica dell'architettura
L'estensione DSC di Azure usa il framework dell'agente VM di Azure per recapitare, applicare e generare report sulle configurazioni DSC in esecuzione nelle VM di Azure. L'estensione DSC prevede un file ZIP contenente almeno un documento di configurazione e un set di parametri fornito tramite Azure PowerShell SDK oppure tramite il portale di Azure.

Quando viene chiamata per la prima volta, l'estensione esegue un processo di installazione. Tale processo installa una versione di Windows Management Framework adottando la logica seguente:

1. Se il sistema operativo della macchina virtuale di Azure è Windows Server 2016, non viene eseguita alcuna azione. In Windows Server 2016 è già installata la versione più recente di PowerShell.
2. Se la proprietà `wmfVersion` è specificata, viene installata la versione di WMF corrispondente a meno che non sia incompatibile con il sistema operativo della VM.
3. Se la proprietà `wmfVersion` non è specificata, viene installata la versione più recente applicabile di WMF.

L'installazione di Windows Management Framework richiede il riavvio del sistema. Dopo il riavvio, l'estensione scarica il file .zip specificato nella proprietà `modulesUrl`. Se tale percorso si trova nell'archiviazione BLOB di Azure, è possibile specificare un token di firma di accesso condiviso nella proprietà `sasToken` per accedere al file. Dopo aver scaricato e decompresso il file .zip, la funzione di configurazione definita in `configurationFunction` viene eseguita per generare il file MOF. L'estensione esegue quindi `Start-DscConfiguration -Force` nel file MOF generato, acquisisce l'output e lo riscrive nel canale di stato di Azure. Da questo momento, Gestione configurazione locale DSC gestisce il monitoraggio e la correzione come di consueto. 

## <a name="powershell-cmdlets"></a>Cmdlet PowerShell
I cmdlet di PowerShell possono essere usati con Azure Resource Manager o con il modello di distribuzione classica per creare pacchetti, pubblicare e monitorare le distribuzioni di estensione DSC. I cmdlet elencati di seguito sono i moduli di distribuzione classica, ma "Azure" può essere sostituito con "AzureRm" per usare il modello di Azure Resource Manager. Ad esempio, `Publish-AzureVMDscConfiguration` usa il modello di distribuzione classica, mentre `Publish-AzureRmVMDscConfiguration` usa Azure Resource Manager. 

`Publish-AzureVMDscConfiguration` riceve un file di configurazione, lo analizza per cercare risorse DSC dipendenti e crea un file .zip contenente la configurazione e le risorse DSC necessarie per applicare la configurazione. Può anche creare il pacchetto in locale usando il parametro `-ConfigurationArchivePath` oppure pubblica il file .zip nell'archiviazione BLOB di Azure e lo protegge con un token di firma di accesso condiviso.

Il file ZIP creato da questo cmdlet include lo script di configurazione con estensione ps1 nella radice della cartella di archiviazione. Per le risorse, la cartella del modulo è posizionata nella cartella di archiviazione. 

`Set-AzureVMDscExtension` inserisce le impostazioni necessarie per l'estensione DSC di PowerShell in un oggetto di configurazione VM. Nel modello di distribuzione classica è necessario applicare le modifiche di una VM a una VM di Azure con `Update-AzureVM`. 

`Get-AzureVMDscExtension` recupera lo stato dell'estensione DSC di una determinata VM. 

`Get-AzureVMDscExtensionStatus` recupera lo stato della configurazione DSC applicata dal gestore dell'estensione DSC. Questa azione può essere eseguita su una singola VM o su un gruppo di VM.

`Remove-AzureVMDscExtension` rimuove il gestore dell'estensione da una determinata macchina virtuale. Questo cmdlet **non** rimuove la configurazione, non disinstalla WMF e non modifica le impostazioni applicate nella macchina virtuale. Rimuove soltanto il gestore dell'estensione. 

**Differenze principali tra cmdlet ASM e cmdlet di Azure Resource Manager**

* I cmdlet di Azure Resource Manager sono sincroni, mentre i cmdlet di Gestione dei servizi di Azure sono asincroni.
* ResourceGroupName, VMName, ArchiveStorageAccountName, Version e Location sono tutti parametri obbligatori in Azure Resource Manager.
* ArchiveResourceGroupName è un nuovo parametro facoltativo per Azure Resource Manager. Questo parametro può essere specificato quando l'account di archiviazione appartiene a un gruppo di risorse diverso da quello in cui viene creata la macchina virtuale.
* ConfigurationArchive è denominato ArchiveBlobName in Azure Resource Manager
* ContainerName è denominato ArchiveContainerName in Azure Resource Manager
* StorageEndpointSuffix è denominato ArchiveStorageEndpointSuffix in Azure Resource Manager
* L'opzione AutoUpdate è stata aggiunta in Azure Resource Manager per consentire l'aggiornamento automatico del gestore dell'estensione alla versione più recente, non appena disponibile. Si noti che questo parametro potrebbe causare il riavvio della VM quando viene rilasciata una nuova versione di WMF. 

## <a name="azure-portal-functionality"></a>Funzionalità del portale di Azure
Passare a una macchina virtuale. In Impostazioni -> Generale fare clic su "Estensioni". Verrà creato un nuovo riquadro. Fare clic su Add e selezionare PowerShell DSC.

Il portale richiede un input.
**Configuration Modules or Script**(Moduli o script di configurazione): questo è un campo obbligatorio. Richiede un file con estensione .ps1 contenente uno script di configurazione oppure un file .zip con uno script di configurazione con estensione ps1 nella directory radice e tutte le risorse dipendenti nelle cartelle del modulo all'interno del file .zip. Può essere creato con il cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluso in Azure PowerShell SDK. Il file .zip viene caricato nell'archiviazione BLOB dell'utente protetta da un token di firma di accesso condiviso. 

**Configuration Data PSD1 File**(File PSD1 dati di configurazione): questo è un campo facoltativo. Se la configurazione usata richiede un file di dati della configurazione con estensione .psd1, usare questo campo per selezionarlo e quindi caricarlo nell'archiviazione BLOB dell'utente, in cui sarà protetto da un token di firma di accesso condiviso. 

**Module-Qualified Name of Configuration**: i file con estensione .ps1 possono avere più funzioni di configurazione. Immettere il nome dello script di configurazione con estensione ps1 seguito da '\' e dal nome della funzione di configurazione. Ad esempio, se lo script con estensione .ps1 ha il nome "configuration.ps1" e la configurazione è "IisInstall", immettere: `configuration.ps1\IisInstall`

**Configuration Arguments**: se la funzione di configurazione accetta argomenti, immetterli qui nel formato `argumentName1=value1,argumentName2=value2`. Questo formato è diverso rispetto a quello in cui vengono accettati gli argomenti di configurazione tramite i cmdlet di PowerShell o i modelli di Resource Manager. 

## <a name="getting-started"></a>Introduzione
L'estensione DSC di Azure riceve i documenti di configurazione DSC e li applica nelle macchine virtuali di Azure. Di seguito è riportato un semplice esempio di configurazione. Salvarlo in locale come "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

La procedura seguente posiziona lo script IisInstall.ps1 nella VM specificata, esegue la configurazione e invia un report sullo stato.
###<a name="classic-model"></a>Modello classico
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Modello di Azure Resource Manager

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Registrazione
I log vengono inseriti in:

C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Numero versione]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Esaminare il [modello di Azure Resource Manager per l'estensione DSC](virtual-machines-windows-extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per trovare altre funzionalità che è possibile gestire con PowerShell DSC, [cercare in PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) altre risorse DSC.

Per altre informazioni sul passaggio di parametri sensibili nelle configurazioni, vedere l'articolo [Gestione sicura delle credenziali con il gestore estensione DSC](virtual-machines-windows-extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO2-->


