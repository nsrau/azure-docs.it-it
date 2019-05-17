---
title: Panoramica di DSC (Desired State Configuration) per Azure
description: Informazioni sull'uso del gestore dell'estensione di Microsoft Azure per PowerShell DSC (Desired State Configuration). L'articolo include prerequisiti, architettura e cmdlet.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 6ec85e840f8e61c46e86b0fa8fb947fb763a4265
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518852"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure

L'agente di macchine virtuali di Azure e le relative estensioni associate fanno parte dei servizi di infrastruttura di Microsoft Azure. Le estensioni di VM sono componenti software che estendono la funzionalità delle macchine virtuali e ne semplificano varie operazioni di gestione.

L'uso primario per l'estensione Azure Desired State Configuration (DSC) è possibile avviare una macchina virtuale per il [servizio Azure Automation State Configuration (DSC)](../../automation/automation-dsc-overview.md).
Il servizio offre [vantaggi](/powershell/dsc/metaconfig#pull-service) che includono la gestione continuativa della configurazione della macchina virtuale e l'integrazione con altri strumenti operativi, ad esempio il monitoraggio di Azure.
Uso dell'estensione per eseguire la registrazione della macchina virtuale per il servizio offre una soluzione flessibile che funziona anche sulle sottoscrizioni di Azure.

È possibile usare l'estensione DSC in modo indipendente dal servizio Automation DSC.
Tuttavia, questo invierà push solo una configurazione alla macchina virtuale.
Nessun report continuative è disponibile, diverso da in locale nella macchina virtuale.

Questo articolo fornisce informazioni relative a entrambi gli scenari: uso dell'estensione DSC per l'onboarding in Automazione e uso dell'estensione DSC come strumento per l'assegnazione di configurazioni a VM tramite Azure SDK.

## <a name="prerequisites"></a>Prerequisiti

- **Computer locale**: per interagire con l'estensione della macchina virtuale di Azure, è necessario usare il portale di Azure o Azure PowerShell SDK.
- **Agente guest**: la macchina virtuale di Azure configurata tramite l'estensione DSC deve essere un sistema operativo che supporta Windows Management Framework (WMF) 4.0 o versione successiva. Per l'elenco completo delle versioni dei sistemi operativi supportati, vedere la [cronologia delle versioni dell'estensione DSC](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Termini e concetti

Questa guida presuppone che si abbia familiarità con i concetti seguenti:

- **Configurazione**: documento di configurazione DSC.
- **Nodo**: destinazione di una configurazione DSC. In questo documento, *nodo* fa sempre riferimento a una VM di Azure.
- **Dati di configurazione**: file con estensione psd1 con i dati ambientali di una configurazione.

## <a name="architecture"></a>Architettura

L'estensione DSC di Azure usa il framework dell'agente VM di Azure per recapitare, applicare e generare report sulle configurazioni DSC in esecuzione nelle VM di Azure. L'estensione DSC accetta un documento di configurazione e un set di parametri. Se non viene fornito alcun file, uno [script di configurazione predefinito](#default-configuration-script) viene incorporato con l'estensione. Lo script di configurazione predefinito viene usato solo per l'impostazione dei metadati in [Gestione configurazione locale](/powershell/dsc/metaconfig).

Alla prima chiamata, l'estensione installa una versione di WMF adottando la logica seguente:

- Se il sistema operativo della macchina virtuale di Azure è Windows Server 2016, non viene eseguita alcuna azione. In Windows Server 2016 è già installata la versione più recente di PowerShell.
- Se la proprietà **wmfVersion** è specificata, viene installata la versione di WMF corrispondente, a meno che tale versione non sia incompatibile con il sistema operativo della VM.
- Se la proprietà **wmfVersion** non è specificata, viene installata la versione più recente applicabile di WMF.

L'installazione di WMF richiede un riavvio. Dopo il riavvio, l'estensione scarica il file ZIP eventualmente specificato nella proprietà **modulesUrl**. Se tale percorso si trova nell'archiviazione BLOB di Azure, è possibile specificare un token di firma di accesso condiviso nella proprietà **sasToken** per accedere al file. Dopo avere scaricato e decompresso il file ZIP, la funzione di configurazione definita in **configurationFunction** viene eseguita per generare un file MOF. L'estensione esegue quindi `Start-DscConfiguration -Force` usando il file con estensione mof generato, acquisisce l'output e lo scrive nel canale di stato di Azure.

### <a name="default-configuration-script"></a>Script di configurazione predefinito

L'estensione DSC di Azure include uno script di configurazione predefinito da usare per l'onboarding di una macchina virtuale nel servizio Automation DSC per Azure. I parametri dello script sono allineati con le proprietà configurabili di [Gestione configurazione locale](/powershell/dsc/metaconfig). Per i parametri dello script, vedere [Script di configurazione predefinito](dsc-template.md#default-configuration-script) in [Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager](dsc-template.md). Per lo script completo, vedere il [modello di avvio rapido di Azure in GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informazioni per la registrazione con il servizio Azure Automation State Configuration (DSC)

Quando si usa l'estensione DSC per registrare un nodo con il servizio di configurazione dello stato, saranno necessario specificare tre valori.

- RegistrationUrl - l'indirizzo https dell'account di automazione di Azure
- RegistrationKey - segreto condiviso usato per registrare i nodi con il servizio
- NodeConfigurationName - il nome di nodo di configurazione (MOF) per eseguire il pull dal servizio per configurare il ruolo del server

Queste informazioni possono essere visualizzate nei [portale di Azure](../../automation/automation-dsc-onboarding.md#azure-portal) oppure è possibile usare PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Per il nome di configurazione del nodo, verificare che si usa il nome del *configurazione del nodo* e non la configurazione.
Una configurazione è definita in uno script che viene usato [per compilare la configurazione di nodo (file MOF)](https://docs.microsoft.com/azure/automation/automation-dsc-compile).
Il nome sarà sempre la configurazione seguita da un punto `.` e il valore `localhost` o un nome di computer specifico.

## <a name="dsc-extension-in-resource-manager-templates"></a>Estensione DSC nei modelli di Resource Manager

Nella maggior parte degli scenari l'estensione DSC viene usata per lo più tramite i modelli di distribuzione Resource Manager. Per altre informazioni ed esempi su come includere l'estensione DSC nei modelli di distribuzione di Resource Manager, vedere [Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlet PowerShell dell'estensione DSC

I cmdlet PowerShell che vengono usati per la gestione dell'estensione DSC sono la scelta ottimale per gli scenari di risoluzione interattiva dei problemi e raccolta di informazioni. È possibile usare i cmdlet per creare pacchetti, pubblicare e monitorare le distribuzioni dell'estensione DSC. I cmdlet per l'estensione DSC non sono ancora aggiornati per funzionare con lo [script di configurazione predefinito](#default-configuration-script).

Il cmdlet **Publish-AzVMDscConfiguration** riceve un file di configurazione, lo analizza per cercare risorse DSC dipendenti e quindi crea un file con estensione zip. Il file ZIP contiene la configurazione e le risorse DSC necessarie per applicare la configurazione. Il cmdlet può anche creare il pacchetto in locale usando il parametro *-OutputArchivePath*. In alternativa, il cmdlet pubblica il file ZIP nell'archiviazione BLOB e quindi lo protegge con un token di firma di accesso condiviso.

Lo script di configurazione con estensione ps1 creato dal cmdlet è nel file ZIP nella radice della cartella di archiviazione. La cartella del modulo è posizionata nella cartella di archiviazione nelle risorse.

Il cmdlet **Set-AzVMDscExtension** inserisce le impostazioni necessarie per l'estensione DSC di PowerShell in un oggetto di configurazione di una macchina virtuale.

Il cmdlet **Get-AzVMDscExtension** recupera lo stato dell'estensione DSC di una macchina virtuale specifica.

Il cmdlet **Get-AzVMDscExtensionStatus** recupera lo stato della configurazione DSC applicata dal gestore dell'estensione DSC. Questa azione può essere eseguita su una singola VM o su un gruppo di VM.

Il cmdlet **Remove-AzVMDscExtension** rimuove il gestore dell'estensione da una macchina virtuale specifica. Questo cmdlet *non* rimuove la configurazione, non disinstalla WMF e non modifica le impostazioni applicate nella VM. Rimuove soltanto il gestore dell'estensione. 

Informazioni importanti sui cmdlet dell'estensione DSC di Resource Manager:

- I cmdlet di Azure Resource Manager sono sincroni,
- I parametri *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* e *Location* sono tutti obbligatori.
- *ArchiveResourceGroupName* è un parametro facoltativo. Questo parametro può essere specificato quando l'account di archiviazione appartiene a un gruppo di risorse diverso da quello in cui viene creata la VM.
- Usare l'opzione **AutoUpdate** per l'aggiornamento automatico del gestore dell'estensione alla versione più recente, quando disponibile. Questo parametro potrebbe causare il riavvio della macchina virtuale quando viene rilasciata una nuova versione di WMF.

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
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Il comando di Azure è utilizzabile per distribuire l'estensione DSC in una macchina virtuale esistente.

Per una macchina virtuale che esegue Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Per una macchina virtuale che esegue Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funzionalità del portale di Azure

Per configurare DSC nel portale:

1. Selezionare una macchina virtuale.
2. In **Impostazioni** selezionare **Estensioni**.
3. Nella nuova pagina creata selezionare **+ Aggiungi** e quindi **PowerShell DSC (Desired State Configuration)**.
4. Nella parte inferiore della pagina delle informazioni sulle estensioni, fare clic su **Crea**.

Il portale consente di raccogliere l'input seguente:

- **Configuration Modules or Script** (Moduli o script di configurazione): questo campo è obbligatorio. Il modulo non è stato aggiornato per lo [script di configurazione predefinito](#default-configuration-script). I moduli e gli script di configurazione richiedono un file con estensione ps1 contenente uno script di configurazione oppure un file ZIP con uno script di configurazione con estensione ps1 nella directory radice. Se si usa un file ZIP, tutte le risorse dipendenti devono essere incluse nelle cartelle del modulo all'interno del file ZIP. È possibile creare il file con estensione zip con il cmdlet **Publish-AzureVMDscConfiguration -OutputArchivePath** incluso in Azure PowerShell SDK. Il file ZIP viene caricato nell'archiviazione BLOB dell'utente e protetto da un token di firma di accesso condiviso.

- **Module-qualified Name of Configuration** (Nome della configurazione qualificato dal modulo): è possibile includere più funzioni di configurazione in un file con estensione ps1. Immettere il nome dello script di configurazione con estensione ps1 seguito da \\ e dal nome della funzione di configurazione. Ad esempio, se lo script con estensione ps1 ha il nome configuration.ps1 e la configurazione è **IisInstall**, immettere **configuration.ps1\IisInstall**.

- **Configuration Arguments** (Argomenti di configurazione): se la funzione di configurazione accetta argomenti, immetterli qui nel formato **argumentName1=value1,argumentName2=value2**. Questo è un formato diverso in cui vengono accettati gli argomenti di configurazione nei cmdlet di PowerShell o nei modelli di Resource Manager.

- **Configuration Data PSD1 File** (File psd1 dati di configurazione): Questo campo è facoltativo. Se la configurazione richiede un file di dati della configurazione con estensione psd1, usare questo campo per selezionare il file di dati e quindi caricarlo nell'archiviazione BLOB dell'utente. Il file di dati della configurazione è protetto da un token di firma di accesso condiviso nell'archiviazione BLOB.

- **WMF Version** (Versione WMF): Specifica la versione di Windows Management Framework (WMF) da installare nella macchina virtuale. Impostando questa proprietà su latest (più recente) verrà installata la versione più recente di WMF. Attualmente, gli unici valori possibili per questa proprietà sono 4.0, 5.0, 5.1 e latest. Questi valori possibili sono soggetti ad aggiornamenti. Il valore predefinito è **latest**.

- **Raccolta dati**: determina se l'estensione raccoglierà dati di telemetria. Per altre informazioni, vedere [Azure DSC extension data collection](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) (Raccolta di dati dell'estensione DSC di Azure).

- **Versione**: specifica la versione dell'estensione DSC da installare. Per informazioni sulle versioni, vedere [Cronologia delle versioni dell'estensione DSC (Desired State Configuration)](/powershell/dsc/azuredscexthistory).

- **Auto Upgrade Minor Version** (Aggiornamento automatico versione secondaria): questo campo viene associato allo switch **AutoUpdate** nei cmdlet e abilita l'estensione per eseguire l'aggiornamento automatico alla versione più recente durante l'installazione. **Sì** indicherà al gestore dell'estensione di usare la versione disponibile più recente e **No** imporrà l'installazione della **Versione** specificata. Non selezionare né **Sì** né **No** equivale alla selezione di **No**.

## <a name="logs"></a>Log

I log per l'estensione vengono archiviati nel percorso seguente: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su PowerShell DSC, passare al [centro di documentazione di PowerShell](/powershell/dsc/overview).
- Esaminare il [modello di Resource Manager per l'estensione DSC](dsc-template.md).
- Per altre funzionalità che è possibile gestire tramite PowerShell DSC e per altre risorse DSC, cercare in [PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Per altre informazioni sul passaggio di parametri sensibili nelle configurazioni, vedere [Gestire credenziali in modo sicuro con il gestore dell'estensione DSC](dsc-credentials.md).
