<properties
	pageTitle="Aggiornare lo script di PowerShell per il progetto di gruppo di risorse di Azure | Microsoft Azure"
	description="Descrive i passaggi necessari per aggiornare manualmente lo script di PowerShell fornito con un progetto di distribuzione del gruppo di risorse di Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/20/2015"
	ms.author="tarcher" />

# Aggiornare lo script di PowerShell per il progetto di gruppo di risorse di Azure

Il progetto di distribuzione del gruppo di risorse di Azure in Visual Studio usa uno script di Azure PowerShell per preparare e distribuire gli asset in Azure da Visual Studio. La versione di Azure PowerShell usata dallo script nel progetto è la 0.9.8. È stata tuttavia rilasciata una nuova versione di Azure PowerShell, la versione 1.0.

La nuova versione di Azure PowerShell offre nuove funzionalità e ottimizza l'esperienza d'uso di Gestione risorse di Azure, ma include anche modifiche a diverse funzioni dei cmdlet che rendono incompatibile la versione precedente dello script. È tuttavia possibile modificare lo script per correggere questi problemi e quindi eseguire lo script all'esterno di Visual Studio in una finestra di comando di PowerShell. Questo articolo fornisce informazioni sulle correzioni da apportare e in che punto applicarle. Oltre alle correzioni specifiche, alla fine di questo articolo è disponibile una copia dello script completamente modificato.

## Soluzioni alternative

Una volta installato il nuovo Azure PowerShell, i problemi possono manifestarsi in due modi nel progetto di gruppo di risorse di Azure.

- Poiché la finestra di dialogo di distribuzione in Visual Studio non potrà trovare i nuovi cmdlet di Azure, verrà richiesto di installarli. I moduli sono stati rinominati nella nuova versione dei cmdlet, perciò Visual Studio non potrà trovare i nuovi moduli. Di conseguenza, se si tenta di installarli da Visual Studio si potrebbe rimanere bloccati in un ciclo.

- Se si esegue lo script all'esterno di Visual Studio nella finestra di comando di PowerShell, è possibile che venga visualizzato l'errore:

	*Il termine "Switch-AzureMode" non vene riconosciuto come nome di cmdlet, funzione, file di script o programma eseguibile. Controllare l'ortografia del nome, o se è stato incluso un percorso, verificare che il percorso sia corretto e riprovare.*

Se il nuovo Azure PowerShell impedisce di distribuire il progetto di gruppo di risorse di Azure, ecco di seguito le opzioni per risolvere il problema.

- È possibile disinstallare la versione più recente di Azure PowerShell e reinstallare quella precedente (0.9.8) tramite l'[Installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).

- È possibile apportare modifiche specifiche e mirate allo script di PowerShell del progetto di distribuzione per risolvere i problemi e quindi eseguire lo script manualmente nella finestra di comando di PowerShell. Non si potrà eseguire lo script dalla voce di menu **Distribuisci** in Visual Studio. La procedura per aggiornare lo script di PowerShell è elencata più avanti in questo articolo. Viene fornita anche una copia dello script completamente aggiornato.

## Aggiornare lo script di Azure PowerShell
Queste istruzioni fanno riferimento ai numeri di riga. Per abilitare i numeri di riga in Visual Studio, vedere [Procedura: visualizzare i numeri di riga nell'editor](https://msdn.microsoft.com/library/ms165340.aspx).

1. Nella riga 61 sostituire il codice seguente:

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	con:

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. Nella riga 87 sostituire il codice seguente:

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	con:

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. Nella riga 95 sostituire il codice:

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	con il codice seguente:

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-ErrorAction Stop 	

	New-AzureRMResourceGroupDeployment
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	    -ResourceGroupName $ResourceGroupName `
	    -TemplateFile $TemplateFile `
	    -TemplateParameterFile $TemplateParametersFile `
	    @OptionalParameters `
		-Verbose -Force
	```

## Script aggiornato
Di seguito è riportato lo script modificato con applicati tutti gli aggiornamenti precedenti.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

Test-AzureRmResourceGroupDeployment `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-ErrorAction Stop 	

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $TemplateFile `
    -TemplateParameterFile $TemplateParametersFile `
    @OptionalParameters `
	-Verbose -Force

```

<!---HONumber=AcomDC_1223_2015-->