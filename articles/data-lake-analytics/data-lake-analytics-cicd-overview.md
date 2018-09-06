---
title: Come configurare una pipeline di CI/CD per Azure Data Lake Analytics
description: Informazioni su come configurare l'integrazione continua e la distribuzione continua per Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 49ac9f9603a1b8043b19c327d5a66015959b9dd1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045875"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Come configurare una pipeline di CI/CD per Azure Data Lake Analytics  

Questo articolo illustra come configurare una pipeline di integrazione continua e distribuzione continua (CI/CD) per i processi U-SQL e i database U-SQL.  

## <a name="use-cicd-for-u-sql-jobs"></a>Usare CI/CD per i processi U-SQL

Strumenti Azure Data Lake per Visual Studio offre il tipo di progetto U-SQL che consente di organizzare gli script U-SQL. L'uso del progetto U-SQL per gestire il codice U-SQL semplifica altri scenari di CI/CD.

## <a name="build-a-u-sql-project"></a>Compilare un progetto U-SQL

Un progetto U-SQL può essere compilato con Microsoft Build Engine (MSBuild), passando i parametri corrispondenti. Attenersi alla procedura in questo articolo per configurare un processo di compilazione per un progetto U-SQL.

### <a name="project-migration"></a>Migrazione del progetto

Prima di configurare un'attività di compilazione per un progetto U-SQL, assicurarsi di disporre della versione più recente del progetto U-SQL. Aprire il file di progetto U-SQL nell'editor e verificare che siano presenti gli elementi di importazione seguenti:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Se non sono presenti, l'utente può eseguire la migrazione del progetto servendosi di due opzioni:

- Opzione 1: sostituire l'elemento di importazione precedente con quello indicato sopra.
- Opzione 2: aprire il progetto precedente in Strumenti Azure Data Lake per Visual Studio. Usare una versione successiva alla 2.3.3000.0. Il modello di progetto precedente verrà aggiornato automaticamente alla versione più recente. I nuovi progetti creati con le versioni successive alla 2.3.3000.0 usano il nuovo modello.

### <a name="get-nuget"></a>Ottenere NuGet

MSBuild non offre supporto integrato per i progetti U-SQL. Per ottenere questo supporto, è necessario aggiungere un riferimento per la soluzione al pacchetto NuGet [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), che aggiunge il servizio di linguaggio necessario.

Per aggiungere il riferimento al pacchetto NuGet, fare clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni di Visual Studio e scegliere **Gestisci pacchetti NuGet**. In alternativa, è possibile aggiungere un file denominato `packages.config` nella cartella della soluzione e inserire i contenuti seguenti:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Gestire i riferimenti al database U-SQL

Gli script U-SQL in un progetto U-SQL potrebbero includere istruzioni di query per gli oggetti di database U-SQL. In tal caso, è necessario fare riferimento al progetto di database U-SQL corrispondente che include la definizione degli oggetti prima di compilare il progetto U-SQL. Ciò avviene, ad esempio, quando si esegue una query su una tabella U-SQL o si fa riferimento a un assembly. 

Leggere altre informazioni sul [progetto di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Il progetto di database U-SQL è attualmente disponibile in anteprima pubblica. Se nel progetto sono disponibili istruzioni DROP, la compilazione ha esito negativo. L'istruzione DROP sarà disponibile a breve.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Compilare il progetto U-SQL con la riga di comando di MSBuild

Prima di tutto, eseguire la migrazione del progetto e ottenere il pacchetto NuGet. Chiamare quindi la riga di comando di MSBuild standard con gli argomenti aggiuntivi seguenti per compilare il progetto U-SQL: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

La definizione e i valori degli argomenti sono i seguenti:

* **USQLSDKPath=<Pacchetto NuGet U-SQL>\build\runtime**. Questo parametro fa riferimento al percorso di installazione del pacchetto NuGet per il servizio di linguaggio U-SQL.
* **USQLTargetType=Merge o SyntaxCheck**:
    * **Merge**. La modalità Merge compila i file code-behind. Ad esempio, i file **.cs**, **.py** e **.r**. La libreria di codice definita dall'utente risultante viene inserita nello script U-SQL. Ad esempio, codice DLL binario, Python o R.
    * **SyntaxCheck**. La modalità SyntaxCheck prima unisce i file code-behind nello script U-SQL. Compila quindi lo script U-SQL per convalidare il codice.
* **DataRoot=<DataRoot path>**. DataRoot è necessario solo per la modalità SyntaxCheck. Durante la compilazione dello script con la modalità SyntaxCheck, MSBuild controlla i riferimenti agli oggetti di database nello script. Prima della compilazione, assicurarsi di configurare un ambiente locale corrispondente che contiene gli oggetti di riferimento del database U-SQL nella cartella DataRoot del computer di compilazione. È anche possibile gestire le dipendenze del database [facendo riferimento a un progetto di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild controlla solo i riferimenti agli oggetti di database, non i file.
* **EnableDeployment=true** o **false**. EnableDeployment indica se è consentita la distribuzione dei database U-SQL di riferimento durante il processo di compilazione. Se si fa riferimento a un progetto di database U-SQL e si utilizzano gli oggetti di database nello script U-SQL, impostare questo parametro su **true**.

### <a name="continuous-integration-with-visual-studio-team-services"></a>Integrazione continua con Visual Studio Team Services

Oltre alla riga di comando, è anche possibile usare un'attività di MSBuild o Visual Studio Build per compilare progetti U-SQL in Visual Studio Team Services (VSTS). Per configurare una pipeline di compilazione, assicurarsi di aggiungere due attività nella pipeline: un'attività di ripristino di NuGet e un'attività di MSBuild.

![Attività di MSBuild per un progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Aggiungere un'attività di ripristino di NuGet per ottenere il pacchetto NuGet a cui fa riferimento la soluzione che include `Azure.DataLake.USQL.SDK`, in modo che MSBuild sia in grado di trovare le destinazioni del linguaggio U-SQL. Impostare **Avanzate** > **Directory di destinazione** su `$(Build.SourcesDirectory)/packages`, se si vuole usare gli argomenti di esempio di MSBuild direttamente nel passaggio 2.

    ![Attività di ripristino di NuGet per un progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Impostare gli argomenti di MSBuild in Visual Studio Build Tools o in un'attività di MSBuild, come illustrato nell'esempio seguente. In alternativa, è possibile definire variabili per questi argomenti nella definizione di compilazione VSTS.

    ![Definire variabili di CI/CD di MSBuild per un progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>Output di compilazione del progetto U-SQL

Dopo avere eseguito una compilazione, tutti gli script nel progetto U-SQL vengono compilati e restituiti in un file ZIP denominato `USQLProjectName.usqlpack`. La struttura della cartella nel progetto viene mantenuta nell'output di compilazione compresso.

> [!NOTE]
>
> I file code-behind per ogni script U-SQL vengono uniti come istruzione inline nell'output di compilazione dello script.
>

## <a name="test-u-sql-scripts"></a>Testare gli script U-SQL

Azure Data Lake fornisce progetti di test per gli script U-SQL e funzioni, oggetti e aggregati C# definiti dall'utente:
* Leggere le informazioni su come [aggiungere test case per gli script U-SQL e il codice C# esteso](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Leggere le informazioni su come [eseguire test case in Visual Studio Team Services](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service).

## <a name="deploy-a-u-sql-job"></a>Distribuire un processo U-SQL

Dopo aver verificato il codice tramite il processo di compilazione e test, è possibile inviare i processi U-SQL direttamente da Visual Studio Team Services tramite un'attività di Azure PowerShell. È anche possibile distribuire lo script in Azure Data Lake Store o Archiviazione BLOB di Azure ed [eseguire i processi pianificati tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-services"></a>Inviare processi U-SQL tramite Visual Studio Team Services

L'output di compilazione del progetto U-SQL è un file ZIP denominato **USQLProjectName.usqlpack**. Il file ZIP include tutti gli script U-SQL nel progetto. È possibile usare l'[attività di Azure PowerShell](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) in Visual Studio Team Services con lo script di esempio di PowerShell seguente per inviare i processi U-SQL direttamente dalla pipeline di versione o di compilazione di Visual Studio Team Services.

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

È possibile inviare i processi U-SQL direttamente da Visual Studio Team Services. In alternativa, è possibile caricare gli script di compilazione in Azure Data Lake Store o Archiviazione BLOB di Azure ed [eseguire i processi pianificati tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Usare l'[attività di Azure PowerShell](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) in Visual Studio Team Services con lo script di esempio di PowerShell seguente per caricare gli script U-SQL in un account di Azure Data Lake Store:

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

## <a name="cicd-for-a-u-sql-database"></a>CI/CD per un database U-SQL

In Strumenti Azure Data Lake per Visual Studio sono disponibili modelli di progetto di database U-SQL che consentono di sviluppare, gestire e distribuire i database U-SQL. Leggere altre informazioni su un [progetto di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Compilare il progetto del database U-SQL

### <a name="get-the-nuget-package"></a>Ottenere il pacchetto NuGet

MSBuild non offre supporto integrato per i progetti di database U-SQL. Per ottenere questa funzionalità, è necessario aggiungere un riferimento per la soluzione al pacchetto NuGet [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), che aggiunge il servizio di linguaggio necessario.

Per aggiungere il riferimento al pacchetto NuGet, fare clic con il pulsante destro del mouse sulla soluzione in Esplora soluzioni di Visual Studio. Scegliere **Gestisci pacchetti NuGet**. Quindi cercare e installare il pacchetto NuGet. In alternativa, è possibile aggiungere un file denominato **packages.config** nella cartella della soluzione e inserire i contenuti seguenti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Compilare un progetto di database U-SQL con la riga di comando di MSBuild

Per compilare il progetto di database U-SQL, chiamare la riga di comando di MSBuild standard e passare il riferimento al pacchetto NuGet dell'SDK U-SQL come argomento aggiuntivo. Vedere l'esempio seguente: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

L'argomento `USQLSDKPath=<U-SQL Nuget package>\build\runtime` fa riferimento al percorso di installazione del pacchetto NuGet per il servizio di linguaggio U-SQL.

### <a name="continuous-integration-with-visual-studio-team-services"></a>Integrazione continua con Visual Studio Team Services

Oltre alla riga di comando, è anche possibile usare un'attività di MSBuild o Visual Studio Build per compilare progetti di database U-SQL in Visual Studio Team Services. Per configurare un'attività di compilazione, assicurarsi di aggiungere due attività nella pipeline di compilazione: un'attività di ripristino di NuGet e un'attività di MSBuild.

   ![Attività di CI/CD di MSBuild per un progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1.  Aggiungere un'attività di ripristino di NuGet per ottenere il pacchetto NuGet a cui fa riferimento la soluzione, che include `Azure.DataLake.USQL.SDK`, in modo che MSBuild sia in grado di trovare le destinazioni del linguaggio U-SQL. Impostare **Avanzate** > **Directory di destinazione** su `$(Build.SourcesDirectory)/packages`, se si vuole usare gli argomenti di esempio di MSBuild direttamente nel passaggio 2.

    ![Attività di CI/CD di NuGet per un progetto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Impostare gli argomenti di MSBuild in Visual Studio Build Tools o in un'attività di MSBuild, come illustrato nell'esempio seguente. In alternativa, è possibile definire variabili per questi argomenti nella definizione di compilazione VSTS.

   ![Definire variabili di CI/CD di MSBuild per un progetto di database U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```
 
### <a name="u-sql-database-project-build-output"></a>Output della compilazione del progetto del database U-SQL

L'output della compilazione di un progetto di database U-SQL è un pacchetto di distribuzione di database U-SQL, il cui nome contiene il suffisso `.usqldbpack`. Il pacchetto `.usqldbpack` è un file ZIP contenente tutte le istruzioni DDL in un singolo script U-SQL in una cartella DDL. Include tutti i file **DLL** e i file aggiuntivi per l'assembly in una cartella temp.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testare le funzioni con valori di tabella e le stored procedure

L'aggiunta diretta di test case per le funzioni con valori di tabella e le stored procedure non è al momento supportata. In alternativa, è possibile creare un progetto U-SQL con script U-SQL per chiamare tali funzioni e scrivere i relativi test case. Per configurare i test case per le funzioni con valori di tabella e le stored procedure definite nel progetto di database U-SQL, seguire questa procedura:

1.  Creare un progetto U-SQL a scopo di test e scrivere gli script U-SQL che chiamano le funzioni con valori di tabella e le stored procedure.
2.  Aggiungere un riferimento a database al progetto U-SQL. Per ottenere la definizione di funzione con valori di tabella e di stored procedure, è necessario fare riferimento al progetto di database che contiene l'istruzione DDL. Leggere altre informazioni sui [riferimenti a database](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Aggiungere test case per gli script U-SQL che chiamano le funzioni con valori di tabella e le stored procedure. Leggere le informazioni su come [aggiungere test case per gli script U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Distribuire il database U-SQL tramite Visual Studio Team Services

`PackageDeploymentTool.exe` offre le interfacce di programmazione e della riga di comando che consentono di distribuire pacchetti di distribuzione di database U-SQL, **.usqldbpack**. L'SDK è incluso nel [pacchetto NuGet dell'SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), che si trova in **build/runtime/PackageDeploymentTool.exe**. Usando `PackageDeploymentTool.exe`, è possibile distribuire i database U-SQL sia in Azure Data Lake Analytics che negli account locali.

> [!NOTE]
>
> Il supporto per la riga di comando di PowerShell e il supporto per l'attività di rilascio di Visual Studio Team Services per la distribuzione del database U-SQL sono in via di sviluppo.
>

Per configurare un'attività di distribuzione di database in Visual Studio Team Services, seguire questa procedura:

1. Aggiungere un'attività di script di PowerShell in una pipeline di versione o di compilazione ed eseguire lo script di PowerShell seguente. Questa attività consente di ottenere le dipendenze di Azure SDK per `PackageDeploymentTool.exe` e `PackageDeploymentTool.exe`. È possibile impostare i parametri **-AzureSDK** e **-DBDeploymentTool** per caricare le dipendenze e lo strumento di distribuzione in cartelle specifiche. Passare il percorso **-AzureSDK** a `PackageDeploymentTool.exe` come parametro **-AzureSDKPath** nel passaggio 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
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

2. Aggiungere un'**attività della riga di comando** in una pipeline di compilazione o di versione e compilare lo script chiamando `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` si trova nella cartella **$DBDeploymentTool** definita. Lo script di esempio è il seguente: 

    * Distribuire un database U-SQL in locale:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Usare la modalità di autenticazione interattiva per distribuire un database U-SQL in un account di Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Usare l'autenticazione **secrete** per distribuire un database U-SQL in un account di Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Usare l'autenticazione **certFile** per distribuire un database U-SQL in un account di Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Descrizione dei parametri di PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Parametri comuni

| Parametro | Descrizione | Valore predefinito | Obbligatorio |
|---------|-----------|-------------|--------|
|Package|Percorso del pacchetto di distribuzione del database U-SQL da distribuire.|Null|true|
|Database|Nome del database da distribuire o creare.|master|false|
|LogFile|Percorso del file per la registrazione. L'impostazione predefinita è l'output standard (console).|Null|false|
|LogLevel|Livello di log: dettagliato, normale, avviso o errore.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parametro per la distribuzione locale

|Parametro|Descrizione|Valore predefinito|Obbligatorio|
|---------|-----------|-------------|--------|
|DataRoot|Percorso della cartella radice dei dati locale.|Null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametri per la distribuzione di Azure Data Lake Analytics

|Parametro|Descrizione|Valore predefinito|Obbligatorio|
|---------|-----------|-------------|--------|
|Account|Specifica l'account di Azure Data Lake Analytics in cui eseguire la distribuzione, in base al nome account.|Null|true|
|ResourceGroup|Nome del gruppo di risorse di Azure per l'account di Azure Data Lake Analytics.|Null|true|
|SubscriptionId|ID sottoscrizione di Azure per l'account di Azure Data Lake Analytics.|Null|true|
|Tenant|Il nome del tenant è il nome di dominio di Azure Active Directory (Azure AD). È possibile individuarlo nella pagina di gestione della sottoscrizione nel portale di Azure.|Null|true|
|AzureSDKPath|Percorso di ricerca degli assembly dipendenti in Azure SDK.|Null|true|
|Interattività|Indica se usare o meno la modalità interattiva per l'autenticazione.|false|false|
|ClientId|ID applicazione di Azure AD necessario per l'autenticazione non interattiva.|Null|Necessario per l'autenticazione non interattiva.|
|Secrete|Segreto o password per l'autenticazione non interattiva. Da usare solo in un ambiente sicuro e attendibile.|Null|Obbligatorio per l'autenticazione non interattiva. In alternativa usare SecreteFile.|
|SecreteFile|File in cui viene salvato il segreto o la password per l'autenticazione non interattiva. Assicurarsi che possa essere letto solo dall'utente corrente.|Null|Obbligatorio per l'autenticazione non interattiva. In alternativa usare Secrete.|
|CertFile|File in cui viene salvata la certificazione X.509 per l'autenticazione non interattiva. Per impostazione predefinita viene usata l'autenticazione con segreto client.|Null|false|
| JobPrefix | Prefisso per la distribuzione di database di un processo DDL U-SQL. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>Passaggi successivi

- [Testare il codice Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Eseguire script U-SQL nel computer locale](data-lake-analytics-data-lake-tools-local-run.md).
- [Usare un progetto di database U-SQL per sviluppare un database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).
