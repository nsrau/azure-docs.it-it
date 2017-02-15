---
title: Panoramica dello script di distribuzione del progetto del gruppo di risorse di Azure | Documentazione Microsoft
description: Illustra il funzionamento dello script PowerShell nel progetto di distribuzione del gruppo di risorse di Azure.
services: visual-studio-online
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: fecfb74f-363f-4cc8-9743-36e5ddd879c0
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a0695e51b595874ce3d4ccd2dca863e883e2840b


---
# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Panoramica dello script di distribuzione del progetto di gruppo di risorse di Azure
I progetti di distribuzione di gruppi di risorse di Azure semplificano lo staging e la distribuzione di file e di altri elementi in Azure. Quando si crea un progetto di distribuzione di Gestione risorse di Azure in Visual Studio, viene aggiunto al progetto uno script PowerShell denominato **Deploy-AzureResourceGroup.ps1** . Questo argomento fornisce informazioni dettagliate sul funzionamento dello script e su come eseguirlo all'interno e all'esterno di Visual Studio.

## <a name="what-does-the-script-do"></a>Funzionamento dello script
Lo script Deploy-AzureResourceGroup.ps1 esegue due operazioni importanti per il flusso di lavoro di distribuzione.

* Caricare eventuali file o elementi necessari per la distribuzione del modello
* Distribuire il modello

La prima parte dello script carica i file e gli elementi per la distribuzione e l'ultimo cmdlet dello script distribuisce effettivamente il modello. Ad esempio, se una macchina virtuale deve essere configurata con uno script, lo script di distribuzione carica prima di tutto in modo sicuro lo script di configurazione in un account di archiviazione di Azure. Lo script sarà quindi disponibile per Gestione risorse di Azure per la configurazione della macchina virtuale durante il provisioning.

Poiché non è necessario che tutte le distribuzioni di modelli includano elementi aggiuntivi da caricare, viene valutato un parametro opzionale denominato *uploadArtifacts* . Se è necessario caricare elementi, impostare l'opzione *uploadArtifacts* quando si chiama lo script. Si noti che non è necessario caricare il file principale del modello e il file dei parametri. Occorre caricare solo gli altri file, ad esempio script di configurazione, modelli di distribuzione annidati e file dell'applicazione.

## <a name="detailed-script-description"></a>Descrizione dettagliata dello script
La descrizione seguente illustra il funzionamento di alcune sezioni dello script Deploy-AzureResourceGroup.ps1 di Azure PowerShell.

> [!NOTE]
> Viene descritta la versione 1.0 dello script Deploy-AzureResourceGroup.ps1.
> 
> 

1. Dichiarare i parametri necessari al progetto di distribuzione di Gestione risorse di Azure. Alcuni parametri hanno valori predefiniti impostati durante la creazione del progetto. È possibile cambiare i valori predefiniti nello script o aggiungere valori di parametro diversi prima di eseguire lo script.
   
   ```
   Param(
   [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
   [string] $ResourceGroupName = 'AzureResourceGroup1',
   [switch] $UploadArtifacts,
   [string] $StorageAccountName,
   [string] $StorageAccountResourceGroupName,
   [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
   [string] $TemplateFile = '..\Templates\azuredeploy.json',
   [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
   [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
   [string] $AzCopyPath = '..\Tools\AzCopy.exe',
   [string] $DSCSourceFolder = '..\DSC'
   )
   ```
   
   | Parametro | Descrizione |
   | --- | --- |
   | $ResourceGroupLocation |Area o posizione del data center per il gruppo di risorse, ad esempio **West US** o **East Asia**. |
   | $ResourceGroupName |Nome del gruppo di risorse di Azure. |
   | $UploadArtifacts |Valore binario che indica se è necessario caricare elementi in Azure dal sistema. |
   | $StorageAccountName |Nome dell'account di archiviazione di Azure in cui vengono caricati gli elementi. |
   | $StorageAccountResourceGroupName |Nome del gruppo di risorse di Azure che include l'account di archiviazione. |
   | $StorageContainerName |Nome del contenitore di archiviazione usato per il caricamento degli elementi. |
   | $TemplateFile |Percorso del file di distribuzione (`<app name>.json`) nel progetto di gruppo di risorse di Azure. |
   | $TemplateParametersFile |Percorso del file di parametri (`<app name>.parameters.json`) nel progetto di gruppo di risorse di Azure. |
   | $ArtifactStagingDirectory |Percorso nel sistema in cui vengono caricati localmente gli elementi, inclusa la cartella radice dello script PowerShell. Questo percorso può essere assoluto o relativo al percorso dello script. |
   | $AzCopyPath |Percorso in cui lo strumento AzCopy.exe copia i rispettivi file con estensione zip, inclusa la cartella radice dello script PowerShell. Questo percorso può essere assoluto o relativo al percorso dello script. |
   | $DSCSourceFolder |Percorso della cartella di origine DSC (Desired State Configuration), inclusa la cartella radice dello script PowerShell. Questo percorso può essere assoluto o relativo al percorso dello script. Per altre informazioni, vedere il post di blog relativo all' [introduzione dell'estensione Azure PowerShell DSC (Desired State Configuration)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx). |
2. Verificare se è necessario caricare elementi in Azure. Se non occorre caricare elementi, andare al passaggio 11. In caso contrario, seguire questa procedura.
3. Convertire eventuali variabili con percorsi relativi in percorsi assoluti. Ad esempio, cambiare un percorso quale `..\Tools\AzCopy.exe` in `C:\YourFolder\Tools\AzCopy.exe`. Inizializzare anche le variabili *ArtifactsLocationName* e *ArtifactsLocationSasTokenName* impostandole su Null. *ArtifactsLocation* e *SaSToken* possono essere parametri per il modello. Se i rispettivi valori sono Null dopo la lettura del file dei parametri, lo script genererà automaticamente valori per queste variabili.
   
   Gli strumenti di Azure usano i valori di parametro *_artifactsLocation* e *_artifactsLocationSasToken* nel modello per gestire gli elementi. Se lo script PowerShell trova parametri con questi nomi, ma non sono stati forniti valori per i parametri, lo script caricherà gli elementi e restituirà i valori appropriati per questi parametri. I valori vengono quindi passati al cmdlet tramite `@OptionsParameters`.
   
   | Variabile | Descrizione |
   | --- | --- |
   | ArtifactsLocationName |Percorso in cui si trovano gli elementi di Azure. |
   | ArtifactsLocationSasTokenName |Nome del token della firma di accesso condiviso (SAS, Shared Access Signature) usato dallo script per l'autenticazione al bus di servizio. Per altre informazioni, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](service-bus-messaging/service-bus-shared-access-signature-authentication.md) . |
   
   ```
   if ($UploadArtifacts) {
   # Convert relative paths to absolute paths if needed
   $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
   $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
   $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)
   
   Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
   Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly
   
   $OptionalParameters.Add($ArtifactsLocationName, $null)
   $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
   ```
4. In questa sezione si verifica se il file <app name>.parameters.json, indicato come "file di parametri", include un nodo padre denominato **parameters** nel blocco `else`. In caso contrario, non è presente alcun nodo padre. Entrambi i formati sono accettabili.
   
   ```
   if ($JsonParameters -eq $null) {
         $JsonParameters = $JsonContent
     }
     else {
         $JsonParameters = $JsonContent.parameters
     }
   ```
5. Scorrere la raccolta di parametri JSON. Se un valore di parametro è stato assegnato a *_artifactsLocation* o *_artifactsLocationSasToken*, impostare la variabile *$OptionalParameters* con i valori seguenti. In questo modo si impedirà allo script di sovrascrivere inavvertitamente eventuali valori di parametro forniti.
   
   ```
   $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
     $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name
   
     if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
         $OptionalParameters[$_.Name] = $ParameterValue.value
     }
   }
   ```
6. Ottenere la chiave dell'account di archiviazione e il contesto per la risorsa dell'account di archiviazione usata per contenere gli elementi per la distribuzione.
   
   ```
   $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
   
   $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
   ```
7. Se si usa PowerShell DSC per configurare una macchina virtuale, l'estensione DSC richiede che gli elementi si trovino in un singolo file con estensione ZIP. Creare quindi un file di archivio con estensione ZIP per la configurazione DSC. A questo scopo, verificare se la cartella $DSCSourceFolder è presente. Se esiste una configurazione DSC, rimuoverla e quindi creare un nuovo file compresso denominato dsc.zip.
   
   ```
   # Create DSC configuration archive
   if (Test-Path $DSCSourceFolder) {
   Add-Type -Assembly System.IO.Compression.FileSystem
     $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
     Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
     [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
   }
   ```
8. Se il file Parameters non include alcun elemento di Azure, impostare un percorso che possa essere usato dallo script PowerShell durante il caricamento degli elementi. A questo scopo, creare un percorso usando una combinazione del percorso dell'endpoint dell'account di archiviazione e del nome del contenitore di archiviazione. Aggiornare quindi il file Parameters specificando il nuovo percorso.
   
   ```
   # Generate the value for artifacts location if it is not provided in the parameter file
   $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
   if ($ArtifactsLocation -eq $null) {
     $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
     $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
   }
   ```
9. Usare l'utilità **AzCopy**, inclusa nella cartella **Strumenti** del progetto di distribuzione di gruppi di risorse di Azure, per copiare eventuali file dal percorso di destinazione dell'archiviazione locale all'account di archiviazione online di Azure. Se questo passaggio ha esito negativo, chiudere lo script, perché è probabile che la distribuzione abbia esito negativo senza gli elementi richiesti.
   
   ```
   # Use AzCopy to copy files from the local storage drop path to the storage account container
   & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
   if ($LASTEXITCODE -ne 0) { return }
   ```
10. Se il token della firma di accesso condiviso per il percorso degli elementi non è disponibile nel file Parameters, crearne uno per fornire l'accesso temporaneo di sola lettura al contenitore di archiviazione online. Passare quindi il token della firma di accesso condiviso alla riga di comando come "optionalParameter". Si noti che eventuali parametri passati alla riga di comando avranno priorità rispetto ai valori forniti nel file di parametri.
    
    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
     # Create a SAS token for the storage container - this gives temporary read-only access to the container
     $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
     $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
     $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```
11. Creare il gruppo di risorse, se non esiste già, quindi verificare il modello e i file di parametri per trovare eventuali errori di convalida che impedirebbero la riuscita della distribuzione.
    
    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop
    
    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```
12. Distribuire infine il modello. Questo codice crea un nome univoco per la distribuzione usando un timestamp.
    
     ```
     New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
         -ResourceGroupName $ResourceGroupName `
         -TemplateFile $TemplateFile `
         -TemplateParameterFile $TemplateParametersFile `
         @OptionalParameters `
         -Force -Verbose
     ```

## <a name="deploy-the-resource-group"></a>Distribuire il gruppo di risorse
### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Per distribuire il gruppo di risorse in Visual Studio
1. Dal menu di scelta rapida del progetto di gruppo di risorse di Azure scegliere **Distribuisci** > **New Distribuisciment**.
   
    ![][0]
2. Nella finestra di dialogo **Distribuisci in gruppo di risorse** scegliere un gruppo di risorse esistente da distribuire dall'elenco a discesa oppure scegliere **&lt;Crea nuovo...&gt;**.  per creare un nuovo gruppo di risorse.
   
    ![][1]
3. Se richiesto, immettere un nome e una posizione per il gruppo di risorse nella finestra di dialogo **Crea gruppo di risorse**, quindi fare clic su **Crea**.
   
    ![][2]
4. Fare clic su **Modifica parametri** per visualizzare la finestra di dialogo **Modifica parametri** e quindi immettere eventuali valori di parametro mancanti.
   
    ![][3]
   
   > [!NOTE]
   > Se sono necessari valori per alcuni parametri, questa finestra di dialogo viene visualizzata automaticamente durante la distribuzione.
   > 
   > 
   
    ![][4]
5. Al termine dell'immissione dei valori di parametro, scegliere **Salva** e quindi **Distribuisci**.
   
    Lo script di distribuzione (Deploy-AzureResourceGroup.ps1) viene eseguito e il modello ed eventuali elementi vengono distribuiti in Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Per distribuire il gruppo di risorse usando PowerShell
Se si vuole eseguire lo script senza usare il comando Distribuisci e l'interfaccia utente di Visual Studio, dal menu di scelta rapida dello script scegliere **Apri con PowerShell ISE**.

![][5]

## <a name="command-deployment-examples"></a>Esempi di distribuzione di comandi
### <a name="deploy-using-default-values"></a>Distribuire con i valori predefiniti
Questo esempio illustra come eseguire lo script usando i valori predefiniti dei parametri. Poiché il parametro relativo alla posizione non ha alcun valore predefinito, sarà necessario specificarne uno.

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Distribuire con override dei valori predefiniti
Questo esempio illustra come eseguire lo script per distribuire i file di modello e di parametri che si differenziano dai valori predefiniti.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Distribuire con UploadArtifacts per staging
Questo esempio illustra come eseguire lo script per caricare elementi dalla cartella di rilascio e distribuire modelli non predefiniti.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Questo esempio illustra come eseguire lo script in un'attività di Azure PowerShell in Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Per altri esempi di uso dei progetti di gruppo di risorse di Azure, vedere [Deploy and manage Azure resources](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) (Distribuire e gestire risorse di Azure) dalla [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Per altre guide introduttive della demo HealthClinic.biz, vedere [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Guide introduttive agli strumenti di sviluppo di Azure).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png



<!--HONumber=Feb17_HO2-->


