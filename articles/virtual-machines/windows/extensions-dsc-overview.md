---
title: Panoramica di DSC (Desired State Configuration) per Azure | Microsoft Docs
description: Informazioni sull'uso del gestore dell'estensione di Microsoft Azure per PowerShell DSC (Desired State Configuration). L'articolo include prerequisiti, architettura e cmdlet.
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
ms.openlocfilehash: 14d29223435e9a133b112a61f2ecdde0aad581a2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

L'agente di macchine virtuali di Azure e le relative estensioni associate fanno parte dei servizi di infrastruttura di Microsoft Azure. Le estensioni di VM sono componenti software che estendono la funzionalità delle macchine virtuali e ne semplificano varie operazioni di gestione.

Il caso d'uso principale per l'estensione DSC (Desired State Configuration) di Azure è il bootstrap di una macchina virtuale nel [servizio Automation DSC per Azure](../../automation/automation-dsc-overview.md). Il bootstrap di una macchina virtuale offre [vantaggi](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#pull-service) che includono la gestione continuativa della configurazione della macchina virtuale e l'integrazione con altri strumenti operativi, ad esempio Monitoraggio di Azure.

È possibile usare l'estensione DSC in modo indipendente dal servizio Automation DSC. Tuttavia, si tratta di un'operazione singola che si verifica durante la distribuzione. Non sono disponibili attività continuative di gestione della configurazione o report tranne che a livello locale all'interno della macchina virtuale.

Questo articolo fornisce informazioni relative a entrambi gli scenari: uso dell'estensione DSC per l'onboarding in Automazione e uso dell'estensione DSC come strumento per l'assegnazione di configurazioni a VM tramite Azure SDK.

## <a name="prerequisites"></a>prerequisiti

- **Computer locale**: per interagire con l'estensione della macchina virtuale di Azure, è necessario usare il portale di Azure o Azure PowerShell SDK.
- **Agente guest**: la macchina virtuale di Azure configurata tramite l'estensione DSC deve essere un sistema operativo che supporta Windows Management Framework (WMF) 4.0 o versione successiva. Per l'elenco completo delle versioni dei sistemi operativi supportati, vedere la [cronologia delle versioni dell'estensione DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termini e concetti

Questa guida presuppone che si abbia familiarità con i concetti seguenti:

- **Configurazione**: documento di configurazione DSC.
- **Nodo**: destinazione di una configurazione DSC. In questo documento, *nodo* fa sempre riferimento a una VM di Azure.
- **Dati di configurazione**: file con estensione psd1 con i dati ambientali di una configurazione.

## <a name="architecture"></a>Architecture

L'estensione DSC di Azure usa il framework dell'agente VM di Azure per recapitare, applicare e generare report sulle configurazioni DSC in esecuzione nelle VM di Azure. L'estensione DSC accetta un documento di configurazione e un set di parametri. Se non viene fornito alcun file, uno [script di configurazione predefinito](#default-configuration-script) viene incorporato con l'estensione. Lo script di configurazione predefinito viene usato solo per l'impostazione dei metadati in [Gestione configurazione locale](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Alla prima chiamata, l'estensione installa una versione di WMF adottando la logica seguente:

* Se il sistema operativo della macchina virtuale di Azure è Windows Server 2016, non viene eseguita alcuna azione. In Windows Server 2016 è già installata la versione più recente di PowerShell.
* Se la proprietà **wmfVersion** è specificata, viene installata la versione di WMF corrispondente, a meno che tale versione non sia incompatibile con il sistema operativo della VM.
* Se la proprietà **wmfVersion** non è specificata, viene installata la versione più recente applicabile di WMF.

L'installazione di WMF richiede un riavvio. Dopo il riavvio, l'estensione scarica il file ZIP eventualmente specificato nella proprietà **modulesUrl**. Se tale percorso si trova nell'archiviazione BLOB di Azure, è possibile specificare un token di firma di accesso condiviso nella proprietà **sasToken** per accedere al file. Dopo avere scaricato e decompresso il file ZIP, la funzione di configurazione definita in **configurationFunction** viene eseguita per generare un file MOF. L'estensione esegue quindi **Start-DscConfiguration -Force** usando il file MOF generato, acquisisce l'output e lo scrive nel canale di stato di Azure.

### <a name="default-configuration-script"></a>Script di configurazione predefinito

L'estensione DSC di Azure include uno script di configurazione predefinito da usare per l'onboarding di una macchina virtuale nel servizio Automation DSC per Azure. I parametri dello script sono allineati con le proprietà configurabili di [Gestione configurazione locale](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig). Per i parametri dello script, vedere [Script di configurazione predefinito](extensions-dsc-template.md#default-configuration-script) in [Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager](extensions-dsc-template.md). Per lo script completo, vedere il [modello di avvio rapido di Azure in GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Estensione DSC nei modelli di Resource Manager

Nella maggior parte degli scenari l'estensione DSC viene usata per lo più tramite i modelli di distribuzione di Resource Manager. Per altre informazioni ed esempi su come includere l'estensione DSC nei modelli di distribuzione di Resource Manager, vedere [Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlet PowerShell dell'estensione DSC

I cmdlet PowerShell che vengono usati per la gestione dell'estensione DSC sono la scelta ottimale per gli scenari di risoluzione interattiva dei problemi e raccolta di informazioni. È possibile usare i cmdlet per creare pacchetti, pubblicare e monitorare le distribuzioni dell'estensione DSC. Si noti che i cmdlet per l'estensione DSC non sono ancora aggiornati per funzionare con lo [script di configurazione predefinito](#default-configuration-script).

Il cmdlet **Publish-AzureRMVMDscConfiguration** riceve un file di configurazione, lo analizza per cercare risorse DSC dipendenti e quindi crea un file ZIP. Il file ZIP contiene la configurazione e le risorse DSC necessarie per applicare la configurazione. Il cmdlet può inoltre creare il pacchetto in locale usando il parametro *-ConfigurationArchivePath*. In alternativa, il cmdlet pubblica il file ZIP nell'archiviazione BLOB e quindi lo protegge con un token di firma di accesso condiviso.

Lo script di configurazione con estensione ps1 creato dal cmdlet è nel file ZIP nella radice della cartella di archiviazione. La cartella del modulo è posizionata nella cartella di archiviazione nelle risorse.

Il cmdlet **Set-AzureRMVMDscExtension** inserisce le impostazioni necessarie per l'estensione DSC di PowerShell in un oggetto di configurazione VM.

Il cmdlet **Get-AzureRMVMDscExtension** recupera lo stato dell'estensione DSC di una VM specifica.

Il cmdlet **Get-AzureRMVMDscExtensionStatus** recupera lo stato della configurazione DSC applicata dal gestore dell'estensione DSC. Questa azione può essere eseguita su una singola VM o su un gruppo di VM.

Il cmdlet **Remove-AzureRMVMDscExtension** rimuove il gestore dell'estensione da una VM specifica. Questo cmdlet *non* rimuove la configurazione, non disinstalla WMF e non modifica le impostazioni applicate nella VM. Rimuove soltanto il gestore dell'estensione. 

Informazioni importanti sui cmdlet dell'estensione DSC di Resource Manager:

- I cmdlet di Azure Resource Manager sono sincroni,
- I parametri *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* e *Location* sono tutti obbligatori.
- *ArchiveResourceGroupName* è un parametro facoltativo. Questo parametro può essere specificato quando l'account di archiviazione appartiene a un gruppo di risorse diverso da quello in cui viene creata la VM.
- Usare l'opzione **AutoUpdate** per l'aggiornamento automatico del gestore dell'estensione alla versione più recente, quando disponibile. Si noti che questo parametro potrebbe causare il riavvio della VM quando viene rilasciata una nuova versione di WMF.

### <a name="get-started-with-cmdlets"></a>Introduzione ai cmdlet

L'estensione DSC di Azure può usare documenti di configurazione DSC per configurare direttamente macchine virtuali di Azure durante la distribuzione. Questa operazione non determina tuttavia la registrazione del nodo in Automazione. Il nodo *non* è gestito centralmente.

Di seguito è riportato un semplice esempio di configurazione. Salvare la configurazione in locale come IisInstall.ps1.

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

I comandi seguenti inseriscono lo script IisInstall.ps1 nella VM specificata, eseguono la configurazione e quindi inviano un report sullo stato.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funzionalità del portale di Azure

Per configurare DSC nel portale:

1. Selezionare una macchina virtuale. 
2. In **Impostazioni** > **Generale** selezionare **Estensioni**. 
3. Nel nuovo riquadro che viene creato selezionare **Aggiungi** e quindi **PowerShell DSC**.

Il portale richiede l'input seguente:

* **Configuration Modules or Script** (Moduli o script di configurazione): questo campo è obbligatorio. Il modulo non è stato aggiornato per lo [script di configurazione predefinito](#default-configuration-script). I moduli e gli script di configurazione richiedono un file con estensione ps1 contenente uno script di configurazione oppure un file ZIP con uno script di configurazione con estensione ps1 nella directory radice. Se si usa un file ZIP, tutte le risorse dipendenti devono essere incluse nelle cartelle del modulo all'interno del file ZIP. È possibile creare il file ZIP con il cmdlet **Publish-AzureVMDscConfiguration -ConfigurationArchivePath** incluso in Azure PowerShell SDK. Il file ZIP viene caricato nell'archiviazione BLOB dell'utente e protetto da un token di firma di accesso condiviso.

* **Configuration Data PSD1 File**(File PSD1 dati di configurazione): questo è un campo facoltativo. Se la configurazione richiede un file di dati della configurazione con estensione psd1, usare questo campo per selezionare il file di dati e quindi caricarlo nell'archiviazione BLOB dell'utente. Il file di dati della configurazione è protetto da un token di firma di accesso condiviso nell'archiviazione BLOB.

* **Module-Qualified Name of Configuration** (Nome della configurazione qualificato dal modulo): è possibile includere più funzioni di configurazione in un file con estensione ps1. Immettere il nome dello script di configurazione con estensione ps1 seguito da \\ e dal nome della funzione di configurazione. Ad esempio, se lo script con estensione ps1 ha il nome configuration.ps1 e la configurazione è **IisInstall**, immettere **configuration.ps1\IisInstall**.

* **Configuration Arguments** (Argomenti di configurazione): se la funzione di configurazione accetta argomenti, immetterli qui nel formato **argumentName1=value1,argumentName2=value2**. Questo è un formato diverso in cui vengono accettati gli argomenti di configurazione nei cmdlet di PowerShell o nei modelli di Resource Manager.

## <a name="logs"></a>Log
Inserire i log in questo percorso:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su PowerShell DSC, passare al [centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
* Esaminare il [modello di Resource Manager per l'estensione DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Per altre funzionalità che è possibile gestire tramite PowerShell DSC e per altre risorse DSC, cercare in [PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* Per altre informazioni sul passaggio di parametri sensibili nelle configurazioni, vedere [Gestire credenziali in modo sicuro con il gestore dell'estensione DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

