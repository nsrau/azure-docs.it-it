---
title: Panoramica di DSC (Desired State Configuration) per Azure | Microsoft Docs
description: "Panoramica sull'uso del gestore dell'estensione di Microsoft Azure per PowerShell DSC (Desired State Configuration). Inclusi prerequisiti, architettura, cmdlet e così via."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

L'agente di macchine virtuali di Azure e le relative estensioni associate fanno parte dei servizi di infrastruttura di Microsoft Azure.
Le estensioni di VM sono componenti software che estendono la funzionalità di una VM e semplificano varie operazioni di gestione delle VM.

L'estensione DSC viene usata principalmente per il bootstrap di una macchina virtuale nel [servizio Azure Automation DSC](../../automation/automation-dsc-overview.md) che fornisce [vantaggi](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service) come la gestione continuativa della configurazione della macchina virtuale e l'integrazione con altri strumenti operativi, ad esempio Monitoraggio di Azure.

È anche possibile usare l'estensione DSC in modo indipendente dal servizio Azure Automation DSC. Tuttavia, si tratta di un'operazione singola che si verifica durante la distribuzione e non di attività di gestione o report continuative della configurazione tranne che a livello locale all'interno della macchina virtuale.

Questo articolo contiene informazioni relative a entrambi gli scenari: come usare l'estensione DSC per l'onboarding in Automazione di Azure e come usare l'estensione DSC come strumento per l'assegnazione di configurazioni a macchine virtuali tramite Azure SDK.

## <a name="prerequisites"></a>Prerequisiti

- **Computer locale**: per interagire con l'estensione della macchina virtuale di Azure, è necessario usare il portale di Azure o Azure PowerShell SDK.
- **Agente guest**: la macchina virtuale di Azure configurata tramite l'estensione DSC deve essere un sistema operativo che supporta Windows Management Framework (WMF) 4.0 o versione successiva. L'elenco completo delle versioni dei sistemi operativi supportati è disponibile nella [cronologia delle versioni dell'estensione DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termini e concetti

Questa guida presuppone che si abbia familiarità con i concetti seguenti:

- **Configurazione**: documento di configurazione DSC.
- **Nodo**: destinazione di una configurazione DSC. In questo documento, "nodo" fa sempre riferimento a una VM di Azure.
- **Dati di configurazione**: file con estensione psd1 contenente i dati ambientali di una configurazione.

## <a name="architectural-overview"></a>Panoramica dell'architettura

L'estensione DSC di Azure usa il framework dell'agente VM di Azure per recapitare, applicare e generare report sulle configurazioni DSC in esecuzione nelle VM di Azure.
L'estensione DSC accetta un documento di configurazione e un set di parametri.
Se non viene fornito alcun file, uno [script di configurazione predefinito](###default-configuration-script) viene incorporato con l'estensione usata solo per l'impostazione dei metadati in [Gestione configurazione locale](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Alla prima chiamata, l'estensione installa una versione di Windows Management Framework (WMF) adottando la logica seguente:

1. Se il sistema operativo della macchina virtuale di Azure è Windows Server 2016, non viene eseguita alcuna azione. In Windows Server 2016 è già installata la versione più recente di PowerShell.
2. Se la proprietà `wmfVersion` è specificata, viene installata la versione di WMF corrispondente a meno che non sia incompatibile con il sistema operativo della VM.
3. Se la proprietà `wmfVersion` non è specificata, viene installata la versione più recente applicabile di WMF.

L'installazione di Windows Management Framework richiede il riavvio del sistema.
Dopo il riavvio, l'estensione scarica il file con estensione zip eventualmente specificato nella proprietà `modulesUrl`.
Se tale percorso si trova nell'archiviazione BLOB di Azure, è possibile specificare un token di firma di accesso condiviso nella proprietà `sasToken` per accedere al file.
Dopo aver scaricato e decompresso il file con estensione zip, la funzione di configurazione definita in `configurationFunction` viene eseguita per generare un file MOF.
L'estensione esegue quindi `Start-DscConfiguration -Force` usando il file MOF generato,
acquisisce l'output e lo scrive nel canale di stato di Azure.

### <a name="default-configuration-script"></a>Script di configurazione predefinito

L'estensione DSC di Azure include uno script di configurazione predefinito da usare per l'onboarding di una macchina virtuale nel servizio Azure Automation DSC.
I parametri dello script sono allineati con le proprietà configurabili di [Gestione configurazione locale](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
I parametri dello script sono riportati nella [documentazione](extensions-dsc-template.md##default-configuration-script) e lo script completo è disponibile in [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>Estensione DSC nei modelli ARM

Nella maggior parte degli scenari l'estensione DSC viene usata per lo più tramite i modelli di distribuzione di Azure Resource Manager (ARM).
Per informazioni ed esempi su come includere l'estensione DSC nei modelli di distribuzione ARM, vedere la pagina della documentazione di riferimento [Estensione DSC con modelli di Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlet PowerShell dell'estensione DSC

I cmdlet PowerShell per la gestione dell'estensione DSC sono la scelta ottimale per gli scenari di risoluzione interattiva dei problemi e raccolta di informazioni.
I cmdlet possono essere usati per creare pacchetti, pubblicare e monitorare le distribuzioni dell'estensione DSC.
Si noti che i cmdlet per l'estensione DSC non sono ancora stati aggiornati per funzionare con lo [script di configurazione predefinito](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration` riceve un file di configurazione, lo analizza per cercare risorse DSC dipendenti e crea un file .zip contenente la configurazione e le risorse DSC necessarie per applicare la configurazione.
Può anche creare il pacchetto in locale usando il parametro `-ConfigurationArchivePath`
oppure pubblica il file .zip nell'archiviazione BLOB di Azure e lo protegge con un token di firma di accesso condiviso.

Il file ZIP creato da questo cmdlet include lo script di configurazione con estensione ps1 nella radice della cartella di archiviazione.
Per le risorse, la cartella del modulo è posizionata nella cartella di archiviazione.

`Set-AzureRMVMDscExtension` inserisce le impostazioni necessarie per l'estensione DSC di PowerShell in un oggetto di configurazione VM.

`Get-AzureRMVMDscExtension` recupera lo stato dell'estensione DSC di una determinata VM.

`Get-AzureRMVMDscExtensionStatus` recupera lo stato della configurazione DSC applicata dal gestore dell'estensione DSC.
Questa azione può essere eseguita su una singola VM o su un gruppo di VM.

`Remove-AzureRMVMDscExtension` rimuove il gestore dell'estensione da una determinata macchina virtuale.
Questo cmdlet **non** rimuove la configurazione, non disinstalla WMF e non modifica le impostazioni applicate nella macchina virtuale.
Rimuove soltanto il gestore dell'estensione. 

Informazioni importanti riguardanti i cmdlet AzureRM dell'estensione DSC:

- I cmdlet di Azure Resource Manager sono sincroni,
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version e Location sono tutti parametri obbligatori.
- ArchiveResourceGroupName è un parametro facoltativo. Questo parametro può essere specificato quando l'account di archiviazione appartiene a un gruppo di risorse diverso da quello in cui viene creata la macchina virtuale.
- L'opzione AutoUpdate consente l'aggiornamento automatico del gestore dell'estensione alla versione più recente, non appena disponibile. Si noti che questo parametro potrebbe causare il riavvio della VM quando viene rilasciata una nuova versione di WMF.

### <a name="getting-started-with-cmdlets"></a>Introduzione ai cmdlet

L'estensione DSC di Azure è capace di usare documenti di configurazione DSC direttamente per configurare macchine virtuali di Azure durante la distribuzione, anche se questo non determinerà la registrazione del nodo in Automazione di Azure. Di conseguenza, il nodo **NON*- verrà gestito centralmente.

Di seguito è riportato un semplice esempio di configurazione.
Salvarlo in locale come "IisInstall.ps1":

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

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funzionalità del portale di Azure

Passare a una macchina virtuale. In Impostazioni -> Generale fare clic su "Estensioni".
Verrà creato un nuovo riquadro.
Fare clic su Add e selezionare PowerShell DSC.

Il portale richiede un input.
**Configuration Modules or Script** (Moduli o script di configurazione): questo campo è obbligatorio. Il modulo non è stato aggiornato per lo [script di configurazione predefinito](###default-configuration-script).
Richiede un file con estensione .ps1 contenente uno script di configurazione oppure un file .zip con uno script di configurazione con estensione ps1 nella directory radice e tutte le risorse dipendenti nelle cartelle del modulo all'interno del file .zip.
Può essere creato con il cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluso in Azure PowerShell SDK.
Il file .zip viene caricato nell'archiviazione BLOB dell'utente protetta da un token di firma di accesso condiviso.

**Configuration Data PSD1 File**(File PSD1 dati di configurazione): questo è un campo facoltativo.
Se la configurazione usata richiede un file di dati della configurazione con estensione .psd1, usare questo campo per selezionarlo e quindi caricarlo nell'archiviazione BLOB dell'utente, in cui sarà protetto da un token di firma di accesso condiviso.

**Module-Qualified Name of Configuration**: i file con estensione .ps1 possono avere più funzioni di configurazione.
Immettere il nome dello script di configurazione con estensione ps1 seguito da '\' e dal nome della funzione di configurazione.
Ad esempio, se lo script con estensione .ps1 ha il nome "configuration.ps1" e la configurazione è "IisInstall", immettere: `configuration.ps1\IisInstall`

**Configuration Arguments**: se la funzione di configurazione accetta argomenti, immetterli qui nel formato `argumentName1=value1,argumentName2=value2`.
Questo formato è diverso rispetto a quello in cui vengono accettati gli argomenti di configurazione tramite i cmdlet di PowerShell o i modelli di Resource Manager.

## <a name="logging"></a>Registrazione
I log vengono inseriti in:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Esaminare il [modello di Azure Resource Manager per l'estensione DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per trovare altre funzionalità che è possibile gestire con PowerShell DSC, [cercare in PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) altre risorse DSC.

Per altre informazioni sul passaggio di parametri sensibili nelle configurazioni, vedere l'articolo [Gestione sicura delle credenziali con il gestore estensione DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
