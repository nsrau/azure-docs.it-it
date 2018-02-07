---
title: Usare .NET SDK per i processi di Microsoft Azure StorSimple Data Manager | Documentazione Microsoft
description: Informazioni su come usare .NET SDK per avviare i processi di StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d15a5cbda2f0c2a363b40e94c38fed6631aa81b5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Usare .Net SDK per avviare la trasformazione dei dati

## <a name="overview"></a>Panoramica

Questo articolo illustra come usare la funzione di trasformazione dei dati all'interno del servizio StorSimple Data Manager per trasformare i dati del dispositivo StorSimple. I dati trasformati vengono quindi usati da altri servizi di Azure nel cloud.

È possibile avviare un processo di trasformazione dei dati in due modi:

 - Usare .NET SDK
 - Usare un runbook di Automazione di Azure
 
 L'articolo descrive una procedura dettagliata per creare un'applicazione console .NET di esempio allo scopo di avviare un processo di trasformazione dei dati e tenere traccia dei risultati per il completamento. Per altre informazioni su come avviare la trasformazione dei dati tramite automazione, passare a [Usare Automazione di Azure per attivare processi d trasformazione dei dati](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare, assicurarsi di disporre di:
*   Un computer che esegue:

    - Visual Studio 2012, 2013, 2015 o 2017.

    - Azure PowerShell. [Scaricare Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Una definizione di processo configurata correttamente nel servizio StorSimple Data Manager all'interno di un gruppo di risorse.
*   Tutte le DLL necessarie. Scaricare le DLL dal [repository GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)Script dal repository GitHub.

## <a name="step-by-step-procedure"></a>Procedura dettagliata

Eseguire la procedura seguente per usare .NET allo scopo di avviare un processo di trasformazione dei dati.

1. Per recuperare i parametri di configurazione, eseguire la procedura seguente:
    1. Scaricare lo script `Get-ConfigurationParams.ps1` dal repository GitHub nel percorso `C:\DataTransformation`.
    1. Eseguire lo script `Get-ConfigurationParams.ps1` dal repository GitHub. Digitare il comando seguente:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        È possibile trasmettere qualsiasi valore per ActiveDirectoryKey e AppName.

2. Questo script restituisce i valori seguenti:
    * ID client
    * ID tenant
    * Chiave di Active Directory (uguale a quella immessa in precedenza)
    * ID sottoscrizione

        ![Output dello script dei parametri di configurazione](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Usando Visual Studio 2012, 2013 o 2015, creare un'applicazione console .NET in C#.

    1. Avviare **Visual Studio 2012/2013/2015**.
    1. Fare clic su **File > Nuovo > Progetto**.

        ![Creare un progetto 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selezionare **Modelli installati > Modelli > Visual C# >Applicazione console**.
    3. Immettere **DataTransformationApp** per il **Nome**.
    4. Selezionare **C:\DataTransformation** per il **Percorso**.
    6. Fare clic su **OK** per creare il progetto.

        ![Creare un progetto 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4.  Aggiungere quindi tutte le DLL presenti nella cartella [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) come **Riferimenti** nel progetto creato. Per aggiungere i file DLL, eseguire le operazioni seguenti:

    1. In Visual Studio passare a **Visualizza > Esplora soluzioni**.
    2. Fare clic sulla freccia a sinistra del progetto Data Transformation App. Fare clic su **Riferimenti** e quindi fare clic con il pulsante destro del mouse su **Aggiungi riferimento**.
    
        ![Aggiungere DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

    3. Passare al percorso della cartella dei pacchetti, selezionare tutte le DLL e fare clic su **Aggiungi**, quindi su **OK**.

        ![Aggiungere DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Il codice seguente consente di inizializzare l'istanza di processo di trasformazione dei dati. Aggiungerlo nel **metodo Main**. Sostituire i valori dei parametri di configurazione ottenuti in precedenza. Immettere i valori del **nome del gruppo di risorse** e del **nome della risorsa**. Il **nome del gruppo di risorse** è associato alla gestione dati di StorSimple in cui è stata configurata la definizione del processo. Il **nome della risorsa** è il nome del servizio gestione dati di StorSimple.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Specificare i parametri con cui deve essere eseguita la definizione del processo

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    OPPURE

    Se si desidera modificare i parametri della definizione del processo in fase di esecuzione, aggiungere il codice seguente:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Dopo l'inizializzazione, aggiungere il codice seguente per attivare un processo di trasformazione dati sulla definizione del processo. Immettere il **nome della definizione processo**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Una volta incollato il codice, compilare la soluzione. Di seguito è riportata una schermata del frammento di codice per inizializzare l'istanza di processo di trasformazione dei dati.

   ![Frammento di codice per inizializzare il processo di trasformazione dei dati](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Questo processo trasforma i dati che soddisfano la directory radice e i filtri all'interno del volume StorSimple e li inserisce nel contenitore o nella condivisione file specificati. Quando un file viene trasformato, viene rilasciato un messaggio nella coda di archiviazione (nello stesso account di archiviazione del contenitore/della condivisione file) con lo stesso nome della definizione del processo. Questo messaggio può essere usato come trigger per avviare un'ulteriore elaborazione del file.

10. Dopo l'attivazione del processo, è possibile usare il codice seguente per tenere traccia dei risultati per il completamento. Non è obbligatorio aggiungere il codice per l'esecuzione del processo.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
 Di seguito è riportata una schermata dell'intero codice di esempio usato per attivare il processo tramite .NET.

 ![Frammento di codice completo per attivare un processo .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).
