---
title: Come configurare la pipeline CI/CD per Azure Data Lake Analytics | Microsoft Docs
description: Informazioni su come configurare l'integrazione continua e la distribuzione continua per Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145010"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Come configurare la pipeline CI/CD per Azure Data Lake Analytics

Questo documento descrive come configurare la pipeline CI/CD per i processi e i database U-SQL.

## <a name="cicd-for-u-sql-job"></a>CI/CD per il processo U-SQL

Strumenti Azure Data Lake per Visual Studio offre il tipo di progetto U-SQL che consente di organizzare gli script U-SQL. L'uso del progetto U-SQL per gestire il codice U-SQL semplifica altri scenari di CI/CD.

## <a name="build-u-sql-project"></a>Compilare un progetto U-SQL

Il progetto U-SQL può essere compilato con MSBuild, passando i parametri corrispondenti. Attenersi alla procedura seguente per configurare il processo di compilazione per i progetti U-SQL.

### <a name="project-migration"></a>Migrazione del progetto

Prima di configurare le attività di compilazione del progetto U-SQL, assicurarsi di usare la versione più recente del progetto U-SQL. Aprire il file di progetto U-SQL nell'editor e verificare che siano presenti gli elementi di importazione seguenti:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Se non sono presenti, l'utente può eseguire la migrazione del progetto servendosi di due opzioni:

- Opzione 1: sostituire l'elemento di importazione precedente con quello indicato sopra.
- Opzione 2: aprire il progetto precedente in Strumenti Azure Data Lake per Visual Studio, con una versione successiva a 2.3.3000.0. Il modello di progetto precedente verrà aggiornato automaticamente alla versione più recente. Il nuovo progetto creato dopo la versione 2.3.3000.0 usa direttamente il nuovo modello.

### <a name="get-nuget-package"></a>Ottenere il pacchetto NuGet

MSBuild non offre supporto predefinito per il tipo di progetto U-SQL. Per aggiungere questa possibilità, è necessario aggiungere un riferimento per la soluzione al [pacchetto Nuget Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) che aggiunge il servizio di linguaggio necessario.

Per aggiungere il riferimento al pacchetto NuGet, fare clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**. Oppure è possibile aggiungere un file denominato `packages.config` nella cartella della soluzione inserendo i contenuti seguenti al suo interno.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Gestire i riferimenti al database U-SQL

Se gli script U-SQL nel progetto U-SQL contengono istruzioni di query per gli oggetti del database U-SQL, ad esempio, eseguire una query per una tabella U-SQL o fare riferimento a un assembly, è necessario fare riferimento al progetto di database U-SQL corrispondente che include la definizione di questi oggetti prima di compilare il progetto U-SQL.

[Altre informazioni sul progetto di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>Il progetto di database U-SQL è attualmente disponibile in anteprima pubblica. Se nel progetto sono disponibili istruzioni DROP, la compilazione ha esito negativo. L'istruzione DROP sarà disponibile a breve.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Compilare il progetto U-SQL con la riga di comando di MSBuild

Dopo aver eseguito la migrazione del progetto e aver ricevuto il pacchetto NuGet, è possibile chiamare la riga di comando di MSBuild standard con gli argomenti aggiuntivi seguenti per compilare il progetto U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

La definizione degli argomenti e i valori sono:

* USQLSDKPath = < pacchetto Nuget di U-SQL > \build\runtime: questo parametro fa riferimento al percorso di installazione del pacchetto NuGet per il servizio di linguaggio U-SQL.
* USQLTargetType=Merge o SyntaxCheck:
    * Merge: la modalità Merge consente di compilare file code-behind, ad esempio con estensione CS, PY e R e incorpora il file di libreria definito dall'utente risultante, ad esempio un file binario DLL, Python o di codice R, nello script U-SQL.
    * SyntaxCheck: la modalità SyntaxCheck unisce prima i file code-behind nello script U-SQL e quindi compila lo script U-SQL per convalidare il codice.
* DataRoot=<DataRoot path>: DataRoot è necessario solo per la modalità SyntaxCheck. Durante la compilazione dello script con la modalità SyntaxCheck, MSBuild verifica i riferimenti nello script agli oggetti di database. Assicurarsi di configurare un ambiente locale corrispondente che contiene gli oggetti di riferimento del database U-SQL nella cartella DataRoot del computer di compilazione prima della compilazione. È anche possibile gestire le dipendenze del database [facendo riferimento a un progetto di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Si noti che MSBuild controlla solo il riferimento a oggetti di database, non i file.
* EnableDeployment = true o false: EnableDeployment indica se è possibile distribuire database U-SQL di riferimento durante il processo di compilazione. Se si fa riferimento al progetto di database U-SQL e si utilizzano gli oggetti di database nello script U-SQL, impostare questo parametro su true.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Integrazione continua con Visual Studio Team Services

Oltre alla riga di comando, i clienti possono usare anche l'attività di Visual Studio Build o MSBuild per compilare i progetti U-SQL in Visual Studio Team Services. Per configurare l'attività di compilazione, assicurarsi di:

1.  Aggiungere l'attività di ripristino NuGet per ottenere il pacchetto NuGet a cui fa riferimento la soluzione incluso `Azure.DataLake.USQL.SDK`, in modo che MSBuild sia in grado di trovare le destinazioni del linguaggio U-SQL. Impostare **Avanzate > Directory di destinazione** come `$(Build.SourcesDirectory)/packages`, se si vuole usare l'esempio di argomenti di MSBuild direttamente nel passaggio 2.

    ![Attività di MSBuild CI CD per l'impostazione di Data Lake per il progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Attività di Nuget CI CD per l'impostazione di Data Lake per il progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Impostare gli argomenti di MSBuild: l'utente può impostare gli argomenti nell'attività di Visual Studio Build o MSBuild come indicato di seguito oppure può definire le variabili per questi argomenti nella definizione di compilazione di VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Variabili di MSBuild CI CD per l'impostazione di Data Lake per il progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Output di compilazione del progetto U-SQL

Dopo aver eseguito la compilazione, tutti gli script nel progetto U-SQL vengono compilati e restituiti in un file ZIP denominato `USQLProjectName.usqlpack`. La struttura della cartella nel progetto verrà mantenuta nell'output di compilazione compresso.

>[!NOTE]
>
>Il file code-behind per ogni script U-SQL verrà unito come istruzione inline all'output di compilazione dello script.
>

## <a name="test-u-sql-script"></a>Eseguire il test dello script U-SQL

Azure Data Lake offre progetti di test per lo script U-SQL e UDO/UDAG/UDF di C#:
* [Informazioni su come aggiungere test case per lo script U-SQL e il codice C# esteso](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Informazioni su come eseguire questi test case in Visual Studio Team Services](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Distribuzione del processo U-SQL

Dopo aver verificato il codice tramite il processo di compilazione e test, è possibile inviare i processi U-SQL direttamente da Visual Studio Team Services tramite l'**attività di Azure PowerShell**. È anche possibile distribuire lo script di Azure Data Lake Store/Archiviazione BLOB di Azure ed [eseguire i processi pianificati tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Inviare i processi U-SQL tramite Visual Studio Team Services

La output di compilazione del progetto U-SQL è un file ZIP denominato **USQLProjectName.usqlpack** che include tutti gli script U-SQL nel progetto. È possibile usare l'[attività di Azure PowserShell in Visual Studio Team Services](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) con lo script di esempio di PowserShell seguente per inviare i processi U-SQL direttamente dalla pipeline di versione o dalla compilazione di Visual Studio Team Services.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Distribuire i processi U-SQL tramite Azure Data Factory

Oltre a inviare direttamente i processi U-SQL da Visual Studio Team Services, è anche possibile caricare gli script compilati in Azure Data Lake Store/Archiviazione BLOB di Azure ed [eseguire i processi programmati tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Usare l'[attività di Azure PowerShell in Visual Studio Team Services](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) con l'esempio di script di PowerShell seguente per caricare gli script U-SQL nell'account di Azure Data Lake Store.

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>CI/CD per il database U-SQL

Strumenti Azure Data Lake per Visual Studio offre un modello di progetto di database U-SQL che consente agli sviluppatori di sviluppare, gestire e distribuire i database U-SQL in modo rapido e semplice. [Altre informazioni sul progetto del database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Compilare il progetto del database U-SQL

### <a name="get-nuget-package"></a>Ottenere il pacchetto NuGet

MSBuild non offre supporto predefinito per il tipo di progetto del database U-SQL. Per aggiungere questa possibilità, è necessario aggiungere un riferimento per la soluzione al [pacchetto Nuget Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) che aggiunge il servizio di linguaggio necessario.

Per aggiungere il riferimento al pacchetto NuGet, fare clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet** per la soluzione, quindi cercare e installare il pacchetto NuGet. Oppure è possibile aggiungere un file denominato "packages.config" nella cartella della soluzione inserendo i contenuti seguenti al suo interno.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Compilare il progetto del database U-SQL con la riga di comando di MSBuild

È possibile chiamare la riga di comando di MSBuild standard e passare come il riferimento al pacchetto NuGet dell'SDK U-SQL come argomento aggiuntivo, come indicato di seguito, per compilare il progetto del database U-SQL:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Gli argomenti `USQLSDKPath=<U-SQL Nuget package>\build\runtime` fanno riferimento al percorso di installazione del pacchetto NuGet per il servizio di linguaggio U-SQL.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Integrazione continua con Visual Studio Team Services

Oltre alla riga di comando, i clienti possono usare anche l'attività di **Visual Studio Build** o **MSBuild** per compilare i progetti del database U-SQL in Visual Studio Team Services. Per configurare l'attività di compilazione, assicurarsi di:

1.  Aggiungere l'attività di ripristino NuGet per ottenere il pacchetto NuGet a cui fa riferimento la soluzione incluso `Azure.DataLake.USQL.SDK`, in modo che MSBuild sia in grado di trovare le destinazioni del linguaggio U-SQL. Impostare **Avanzate > Directory di destinazione** come `$(Build.SourcesDirectory)/packages`, se si vuole usare l'esempio di argomenti di MSBuild direttamente nel passaggio 2.

    ![Attività di MSBuild CI CD per l'impostazione di Data Lake per il progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Attività di Nuget CI CD per l'impostazione di Data Lake per il progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Impostare gli argomenti di MSBuild: l'utente può impostare gli argomenti nell'attività di Visual Studio Build o MSBuild come indicato di seguito oppure può definire le variabili per questi argomenti nella definizione di compilazione di VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![Variabili di MSBuild CI CD per l'impostazione di Data Lake per il progetto del database U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Output della compilazione del progetto del database U-SQL

L'output della compilazione per il progetto del database U-SQL è un pacchetto di distribuzione del database U-SQL, denominato con il suffisso `.usqldbpack`. Il pacchetto `.usqldbpack` è un file ZIP che include tutte le istruzioni DDL in un unico script U-SQL nella cartella DDL e tutti i file con estensione DLL e i file aggiuntivi per gli assembly nella cartella Temp.

## <a name="test-table-valued-function-and-stored-procedure"></a>Funzione con valori di tabella e stored procedure di test

In questo momento, l'aggiunta diretta di test case per funzioni con valori di tabella e stored procedure non è supportata. In alternativa è possibile creare un progetto U-SQL con gli script U-SQL che richiamano tali funzioni e scrivere i relativi test case. Seguire le istruzioni di seguito per configurare i test case per le funzioni con valori di tabella e le stored procedure definite nel progetto del database U-SQL:

1.  Creare un progetto U-SQL a scopo di test e scrivere gli script U-SQL che richiamano le funzioni con valori di tabella e le stored procedure.
2.  Aggiungere il riferimento al database per questo progetto U-SQL. Per ottenere la definizione di funzione con valori di tabella e di stored procedure, è necessario fare riferimento al progetto del database che contiene l'istruzione DDL. [Altre informazioni sul riferimento a database](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Aggiungere i test case per gli script U-SQL che richiamano le stored procedure e le funzioni con valori di tabella. [Informazioni su come aggiungere test case per lo script U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Distribuire il database U-SQL tramite Visual Studio Team Services

`PackageDeploymentTool.exe` offre le interfacce di programmazione e della riga di comando che consentono di distribuire il pacchetto di distribuzione del database U-SQL (.usqldbpack). L'SDK è incluso nel [pacchetto NuGet dell'SDK di U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), che si trova in build/runtime/PackageDeploymentTool.exe. Usando `PackageDeploymentTool.exe` è possibile distribuire i database U-SQL sia in Azure Data Lake Analytics che nell'account locale.

>[!NOTE]
>
>Il supporto per la riga di comando di PowerShell e il supporto per l'attività della versione di Visual Studio Team Services per la distribuire del database U-SQL sono in via di sviluppo.
>

Seguire i passaggi seguenti per configurare l'attività di distribuzione del database in Visual Studio Team Services:

1. Aggiungere un'attività di script di PowerShell nella build o la pipeline di versione ed eseguire lo script di PowerShell seguente. Questa attività consente di ottenere le dipendenze di Azure SDK per `PackageDeploymentTool.exe` e `PackageDeploymentTool.exe`. È possibile impostare i parametri -AzureSDK e -DBDeploymentTool per caricare le dipendenze e lo strumento di distribuzione su alcune cartelle specifiche. Passare il percorso -AzureSDK su `PackageDeploymentTool.exe` come parametro -AzureSDKPath nel passaggio 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Aggiungere un'**attività della riga di comando** nella pipeline di compilazione o di versione e compilare lo script che chiama `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` si trova nella cartella $DBDeploymentTool definita. Lo script di esempio è il seguente: 

    * Distribuire il database U-SQL in locale

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Per distribuire il database U-SQL all'account di Azure Data Lake Analytics, usare la modalità di autenticazione interattiva:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Per distribuire il database U-SQL all'account di Azure Data Lake Analytics, usare l'autenticazione segreta:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Per distribuire il database U-SQL all'account di Azure Data Lake Analytics, usare l'autenticazione certFile:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Descrizione del parametro PackageDeploymentTool.exe:**

**Parametri comuni:**

|Parametro|Descrizione|Default Value|Obbligatoria|
|---------|-----------|-------------|--------|
|Pacchetto|Percorso del pacchetto di distribuzione del database U-SQL da distribuire|Null|true|
|Database|Nome del database da distribuire o creare|master|false|
|LogFile|Percorso del file di registrazione, output da predefinito o standard (console)|Null|false|
|LogLevel|Livello log: dettagliato, normale, avviso, errore|LogLevel.Normal|false|

**Parametro per la distribuzione locale:**

|Parametro|Descrizione|Default Value|Obbligatoria|
|---------|-----------|-------------|--------|
|DataRoot|Percorso della cartella DataRoot locale|Null|true|

**Parametro per la distribuzione di Azure Data Lake Analytics:**

|Parametro|Descrizione|Default Value|Obbligatoria|
|---------|-----------|-------------|--------|
|Account|Specifica l'account in cui eseguire la distribuzione di Azure Data Lake Analytics per nome dell'account|Null|true|
|ResourceGroup|Nome del gruppo di risorse di Azure per l'account Azure Data Lake Analytics|Null|true|
|SubscriptionId|ID della sottoscrizione di Azure per l'account Azure Data Lake Analytics|Null|true|
|Tenant|Nome del tenant, nome di dominio della directory AAD, è possibile trovarlo nella pagina di gestione della sottoscrizione del portale di Azure|Null|true|
|AzureSDKPath|Percorso in cui cercare gli assembly dipendenti in Azure SDK|Null|true|
|Interattività|Usare o meno la modalità interattiva per l'autenticazione|false|false|
|ClientId|ID dell'applicazione AAD per un'autenticazione non interattiva, necessaria per un'autenticazione non interattiva|Null|obbligatorio per un'autenticazione non interattiva|
|Secrete|Segreto/password per un'autenticazione non interattiva, da usare solo in un ambiente sicuro/attendibile|Null|obbligatorio per un'autenticazione non interattiva, in alternativa usare SecreteFile|
|SecreteFile|Il file salva il segreto o la password per un'autenticazione non interattiva, assicurarsi di mantenerlo solo leggibile per l'utente corrente|Null|obbligatorio per un'autenticazione non interattiva, in alternativa usare Secrete|
|CertFile|Il file salva la certificazione X.509 per un'autenticazione interattiva, l'impostazione predefinita prevede l'uso dell'autenticazione del segreto del client|Null|false|
|JobPrefix|Prefisso per il processo DLL di U-SQL per la distribuzione del database|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>Passaggi successivi

- [How to test your Azure Data Lake Analytics code](data-lake-analytics-cicd-test.md) (Come testare il codice di Azure Data Lake Analytics)
- [Eseguire lo script U-SQL in locale](data-lake-analytics-data-lake-tools-local-run.md)
- [Usare il progetto di database U-SQL per sviluppare il database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
