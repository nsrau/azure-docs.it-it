---
title: Usare .NET SDK per i processi di Microsoft Azure StorSimple Data Manager | Documentazione Microsoft
description: Informazioni su come usare .NET SDK per avviare i processi di StorSimple Data Manager (anteprima privata)
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 37f795fe59496b0267120537115cf56d44cc5325
ms.openlocfilehash: 60cde851a466a5b4b0752908f11272eedb246b0a

---

# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Usare .Net SDK per avviare la trasformazione dei dati (anteprima privata)

## <a name="overview"></a>Overview

Questo articolo illustra come usare la funzione di trasformazione dei dati all'interno del servizio StorSimple Data Manager per trasformare i dati del dispositivo StorSimple. I dati trasformati vengono quindi usati da altri servizi di Azure nel cloud. L'articolo descrive anche una procedura dettagliata per creare un'applicazione console .NET di esempio allo scopo di avviare un processo di trasformazione dei dati e tenere traccia dei risultati per il completamento.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di:
*   Un sistema con Visual Studio 2012, 2013 o 2015 installato.
*   Azure Powershell installato. [Scaricare Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Impostazioni di configurazione per l'inizializzazione del processo di trasformazione dei dati (le istruzioni per ottenere queste impostazioni sono incluse qui).
*   Una definizione di processo configurata correttamente in una risorsa dati ibridi all'interno di un gruppo di risorse.
*   Tutte le DLL necessarie. Scaricare le DLL dal [repository GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [Script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` dal repository GitHub.

## <a name="step-by-step"></a>Procedura dettagliata

Eseguire la procedura seguente per usare .NET allo scopo di avviare un processo di trasformazione dei dati.

1. Per recuperare i parametri di configurazione, eseguire la procedura seguente:
    1. Scaricare lo script `Get-ConfigurationParams.ps1` dal repository GitHub nel percorso `C:\DataTransformation`.
    1. Eseguire lo script `Get-ConfigurationParams.ps1` dal repository GitHub. Digitare il comando seguente:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        È possibile trasmettere qualsiasi valore per ActiveDirectoryKey e AppName.


2. Questo script restituisce i valori seguenti:      - ID client      - ID tenant      - Chiave di Active Directory (simile a quella immessa in precedenza)      - ID sottoscrizione

3. Usando Visual Studio 2012, 2013 o 2015, creare un'applicazione console .NET in C#.

    1. Avviare **Visual Studio 2012/2013/2015**.
    1. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**.
    2. Espandere **Modelli** e quindi selezionare **Visual C#**.
    3. Selezionare **Applicazione console** dall'elenco dei tipi di progetto a destra.
    4. Immettere **DataTransformationApp** per il **Nome**.
    5. Selezionare **C:\DataTransformation** per il **Percorso**.
    6. Fare clic su **OK** per creare il progetto.

4.  Aggiungere quindi tutte le DLL presenti nella cartella [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) come **Riferimenti** nel progetto creato. Per scaricare i file DLL, eseguire le operazioni seguenti:

    1. In Visual Studio passare a **Visualizza > Esplora soluzioni**.
    1. Fare clic sulla freccia a sinistra del progetto Data Transformation App. Fare clic su **Riferimenti** e quindi fare clic con il pulsante destro del mouse su **Aggiungi riferimento**.
    2. Passare al percorso della cartella dei pacchetti, selezionare tutte le DLL e fare clic su **Aggiungi**, quindi su **OK**.

5. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. Il codice seguente consente di inizializzare l'istanza di processo di trasformazione dei dati. Aggiungerlo nel **metodo Main**. Sostituire i valori dei parametri di configurazione ottenuti in precedenza. Immettere i valori del **nome del gruppo di risorse** e del **nome della risorsa dati ibridi**. Il **nome del gruppo di risorse** ospita la risorsa dati ibridi in cui era stata configurata la definizione del processo.

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

    ```

9. Questo processo carica i file corrispondenti presenti nella directory radice del volume di StorSimple nel contenitore specificato. Quando viene caricato un file, viene rilasciato un messaggio nella coda (nello stesso account di archiviazione del contenitore) con lo stesso nome della definizione del processo. Questo messaggio può essere usato come trigger per avviare un'ulteriore elaborazione del file.

10. Dopo l'attivazione del processo, aggiungere il codice seguente per tenere traccia dei risultati per il completamento.

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


## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).



<!--HONumber=Dec16_HO4-->


