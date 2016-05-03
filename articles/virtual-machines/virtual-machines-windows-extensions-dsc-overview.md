<properties
   pageTitle="Panoramica di DSC (Desired State Configuration) per Azure | Microsoft Azure"
   description="Panoramica sull'uso del gestore dell'estensione di Microsoft Azure per PowerShell DSC (Desired State Configuration). Inclusi prerequisiti, architettura, cmdlet e così via."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="zachal"/>

# Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

L'agente di macchine virtuali di Azure e le relative estensioni associate fanno parte dei servizi di infrastruttura di Microsoft Azure. Le estensioni della macchina virtuale sono componenti software che estendono la funzionalità della macchina virtuale e semplificano varie operazioni di gestione delle macchine virtuali. Ad esempio, l'estensione VMAccess permette di reimpostare la password di una macchina virtuale e l'estensione di script personalizzato permette di eseguire uno script nella macchina virtuale.

Questo articolo illustra l'estensione DSC (Desired State Configuration) PowerShell per le VM di Azure in Azure PowerShell SDK. È possibile usare i nuovi cmdlet per caricare e applicare una configurazione DSC PowerShell in una macchina virtuale di Azure abilitata con l'estensione DSC PowerShell. L'estensione DSC PowerShell esegue una chiamata in PowerShell DSC per applicare la configurazione DSC ricevuta nella macchina virtuale. Questa funzionalità è disponibile anche tramite l'interfaccia utente del portale di anteprima.

## Prerequisiti ##
**Computer locale**: per interagire con l'estensione della macchina virtuale di Azure è necessario usare l'interfaccia utente del portale di anteprima o Azure PowerShell SDK.

**Agente guest**: la macchina virtuale di Azure in cui applicare la configurazione DSC deve essere un sistema operativo che supporta Windows Management Framework 4.0 o 5.0. L'elenco completo delle versioni dei sistemi operativi supportati è disponibile nella cronologia delle versioni dell'estensione DSC.

## Termini e concetti ##
Questa guida presuppone che si abbia familiarità con i concetti seguenti:

Configurazione: documento di configurazione DSC.

Nodo: destinazione per una configurazione DSC. In questo documento, "nodo" fa sempre riferimento a una macchina virtuale di Azure.

Dati di configurazione: file con estensione psd1 contenente i dati ambientali per una configurazione.

## Panoramica dell'architettura ##

L'estensione DSC di Azure usa il framework dell'agente di Azure per recapitare, applicare e generare report sulle configurazioni DSC in esecuzione nelle macchine virtuali di Azure. L'estensione DSC prevede un file ZIP contenente almeno un documento di configurazione e un set di parametri fornito tramite Azure PowerShell SDK oppure tramite l'interfaccia utente del portale.

Quando viene chiamata per la prima volta, l'estensione esegue il processo di installazione di una versione di Windows Management Framework, come specificato di seguito:

1. Se il sistema operativo della macchina virtuale di Azure è Windows Server 2016, non viene eseguita alcuna azione. In Windows Server 2016 è già installata la versione più recente di PowerShell.
2. Se la proprietà `wmfVersion` è specificata, viene installata la versione di Windows Management Framework corrispondente a meno che non sia incompatibile con il sistema operativo della macchina virtuale.
3. Se la proprietà `wmfVersion` non è specificata, viene installata la versione più recente applicabile di Windows Management Framework.

L'installazione di Windows Management Framework richiede il riavvio del sistema. Dopo il riavvio, l'estensione scarica il file ZIP specificato nella proprietà `modulesUrl`. Se tale percorso si trova in un archivio BLOB di Azure, è possibile specificare un token di firma di accesso condiviso nella proprietà `sasToken` per accedere al file. Dopo aver scaricato e decompresso il file ZIP, la funzione di configurazione definita in `configurationFunction` viene eseguita per generare il file MOF. L'estensione esegue quindi `Start-DscConfiguration -force` nel file MOF generato, acquisisce l'output e lo riscrive nel canale di stato di Azure. Da questo momento, Gestione configurazione locale DSC gestisce il monitoraggio e la correzione come di consueto.

## Cmdlet PowerShell ##

I cmdlet di PowerShell possono essere usati con Azure Resource Manager o con Gestione dei servizi di Azure per aggiungere a un pacchetto, pubblicare e monitorare le distribuzioni dell'estensione DSC. I cmdlet elencati di seguito sono i moduli di Gestione dei servizi di Azure, ma è possibile sostituire "Azure" con "AzureRM" per usare il modello di Azure Resource Manager. Ad esempio, `Publish-AzureVMDscConfiguration` usa Gestione dei servizi di Azure, mentre `Publish-AzureRmVMDscConfiguration` usa Azure Resource Manager.

`Publish-AzureVMDscConfiguration` riceve un file di configurazione, lo analizza per cercare risorse DSC dipendenti e crea un file ZIP contenente la configurazione e le risorse DSC necessarie per applicare la configurazione. Crea il pacchetto in locale usando il parametro `-ConfigurationArchivePath` oppure pubblica il file ZIP nell'archivio BLOB di Azure e lo protegge con un token di firma di accesso condiviso.

Il file ZIP creato da questo cmdlet include lo script di configurazione con estensione ps1 nella radice della cartella di archiviazione. Per le risorse, la cartella del modulo è posizionata nella cartella di archiviazione.

`Set-AzureVMDscExtension` inserisce le impostazioni necessarie per l'estensione DSC PowerShell in un oggetto di configurazione di macchina virtuale, che poi può essere applicato a una macchina virtuale di Azure con `Update-AzureVm`.

`Get-AzureVMDscExtension` recupera lo stato dell'estensione DSC di una determinata macchina virtuale.

`Get-AzureVMDscExtensionStatus` recupera lo stato della configurazione DSC applicata dal gestore dell'estensione DSC a una macchina virtuale oppure a un gruppo di macchine virtuali.

`Remove-AzureVMDscExtension` rimuove il gestore dell'estensione da una determinata macchina virtuale. Questo cmdlet **non** rimuove la configurazione, non disinstalla Windows Management Framework e non modifica le impostazioni applicate nella macchina virtuale. Rimuove soltanto il gestore dell'estensione.

**Differenze principali tra i cmdlet di Azure Resource Manager e Gestione dei servizi di Azure**

- I cmdlet di Azure Resource Manager sono sincroni, mentre i cmdlet di Gestione dei servizi di Azure sono asincroni.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version e Location sono tutti nuovi parametri obbligatori.
- ArchiveResourceGroupName è un nuovo parametro facoltativo per Azure Resource Manager. Questo parametro può essere specificato quando l'account di archiviazione appartiene a un gruppo di risorse diverso da quello in cui viene creata la macchina virtuale.
- ConfigurationArchive è detto ArchiveBlobName in Azure Resource Manager.
- ContainerName è detto ArchiveContainerName in Azure Resource Manager.
- StorageEndpointSuffix è detto ArchiveStorageEndpointSuffix in Azure Resource Manager.
- L'opzione -AutoUpdate è stata aggiunta in Azure Resource Manager per consentire l'aggiornamento automatico del gestore dell'estensione alla versione più recente, non appena disponibile. Questo potrebbe causare il riavvio della macchina virtuale quando viene rilasciata una nuova versione di Windows Management Framework. 


## Funzionalità del portale di anteprima ##
Passare a una macchina virtuale. In Settings -> Manage fare clic su "extensions". Verrà creato un nuovo riquadro. Fare clic su Add e selezionare PowerShell DSC.

Nel portale è necessario l'input dell'utente. **Configuration Modules or Script**: questo è un campo obbligatorio. Richiede un file con estensione ps1 contenente uno script di configurazione oppure un file ZIP con uno script di configurazione con estensione ps1 nella directory radice e tutte le risorse dipendenti nelle cartelle del modulo all'interno del file ZIP. Può essere creato con il cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluso in Azure PowerShell SDK. Il file ZIP verrà caricato nell'archivio BLOB dell'utente protetto da un token di firma di accesso condiviso.

**Configuration Data PSD1 File**: questo è un campo facoltativo. Se la configurazione usata richiede un file di dati della configurazione con estensione psd1, usare questo campo per selezionarlo e quindi caricarlo nell'archivio BLOB dell'utente, in cui sarà protetto da un token di firma di accesso condiviso.
 
**Module-Qualified Name of Configuration**: i file con estensione ps1 possono avere più funzioni di configurazione. Immettere il nome dello script di configurazione con estensione ps1 seguito da "\" e dal nome della funzione di configurazione.

**Configuration Arguments**: se la funzione di configurazione accetta argomenti, immetterli qui nel formato `argumentName1=value1,argumentName2=value2`. Si noti che questo formato è diverso rispetto a quello in cui vengono accettati gli argomenti di configurazione tramite i cmdlet di PowerShell o i modelli di Azure Resource Manager.

## Introduzione ##

L'estensione DSC di Azure riceve i documenti di configurazione DSC e li applica nelle macchine virtuali di Azure. Di seguito è riportato un semplice esempio di configurazione. Salvarlo in locale come "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node ("localhost") 
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

La procedura seguente posiziona lo script IisInstall.ps1 nella macchina virtuale specificata, esegue la configurazione e invia un report sullo stato.
 
```powershell
#Requires Azure Powershell cmdlets
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -vm $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -verbose

#Azure Powershell batches commands until an update-AzureVM command is given
$demoVM | Update-AzureVM -Verbose

#check on status
get-azurevmdscextensionstatus -VM $demovm -verbose
```

## Registrazione ##

I log vengono inseriti in:

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[numero di versione]

## Passaggi successivi ##

Per altre informazioni su PowerShell DSC, vedere il [centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Per trovare altre funzionalità che è possibile gestire con PowerShell DSC, [cercare in PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) altre risorse DSC.

Per altre informazioni sul passaggio di parametri sensibili nelle configurazioni, vedere l'articolo relativo alla [gestione sicura delle credenziali con il gestore dell'estensione DSC](virtual-machines-windows-extensions-dsc-credentials.md).

<!---HONumber=AcomDC_0420_2016-->