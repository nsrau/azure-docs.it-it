---
title: Cambiare il percorso BLOB predefinito | Microsoft Docs
description: Informazioni su come impostare una funzione di Azure per rinominare un percorso di file BLOB
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
ms.openlocfilehash: f73d9dcedee5165af752b9e10fb70de860e8e98b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="change-a-blob-path-from-the-default-path"></a>Cambiare il percorso BLOB predefinito

Quando il servizio StorSimple Data Manager trasforma i dati, per impostazione predefinita posiziona i BLOB trasformati in un contenitore di archiviazione, come specificato durante la creazione del repository di destinazione. Una volta collocati in questa posizione, è possibile spostare i BLOB in un percorso alternativo. Questo articolo illustra come impostare una funzione di Azure per rinominare un percorso di file BLOB predefinito e quindi spostare i BLOB in un altro percorso.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di disporre di una definizione di processo configurata correttamente nel servizio StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Per creare una funzione di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](http://portal.azure.com/).

2. Selezionare **+ Crea una risorsa**. Digitare **App per le funzioni** nella casella **Cerca** e premere **INVIO**. Selezionare e fare clic su **App per le funzioni** nell'elenco di applicazioni visualizzato.

    ![Digitare "App per le funzioni" nella casella Cerca](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Fare clic su **Crea**.

    ![Pulsante "Crea" della finestra App per le funzioni](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Nel pannello di configurazione **App per le funzioni** seguire questa procedura:

    1. Specificare un **nome univoco per l'app**.
    2. Nell'elenco a discesa selezionare **Sottoscrizione**. La sottoscrizione deve corrispondere a quella associata al servizio StorSimple Data Manager.
    3. Selezionare il gruppo di risorse **Crea nuovo**.
    4. Nell'elenco a discesa **Piano di hosting** selezionare **Piano A consumo**.
    5. Specificare un percorso in cui eseguire la funzione. È possibile specificare la stessa area in cui si trovano il servizio StorSimple Data Manager e l'account di archiviazione associato alla definizione del processo.
    6. Selezionare un account di archiviazione esistente o crearne uno nuovo. Un account di archiviazione viene usato internamente per la funzione.

        ![Immettere i dati per la configurazione della nuova app per le funzioni](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Fare clic su **Crea**. Verrà creata l'app per le funzioni.
     
        ![App per le funzioni creata](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selezionare **Funzioni** e fare clic su **+ Nuova funzione**.

    ![Fare clic su + Nuova funzione](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selezionare il linguaggio di programmazione **C#**. Nella matrice di riquadri di modello selezionare **C#** nel riquadro **QueueTrigger-CSharp**.

7. In **Queue trigger** (Trigger della coda):

    1. Immettere un **nome** per la funzione.
    2. Nella casella **Nome coda** digitare il nome della definizione del processo di trasformazione dei dati.
    3. In **Connessione dell'account di archiviazione** fare clic su **nuova**. Nell'elenco di account di archiviazione selezionare l'account associato alla definizione del processo. Prendere nota del nome della connessione (evidenziato). Questo nome è necessario in seguito nella funzione di Azure.

        ![Creare una nuova funzione C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Fare clic su **Crea**. La **funzione** è stata creata.

     
10. Nella finestra Funzione eseguire il file con estensione _csx_.

    ![Creare una nuova funzione C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Eseguire i passaggi seguenti.

    1. Incollare il codice seguente:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Sostituire **STORAGE_CONNECTIONNAME** nella riga 11 con il nome della connessione all'account di archiviazione (vedere il punto 7c).

        ![Copiare il nome della connessione di archiviazione](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Salvare** la funzione.

        ![Salvare la funzione](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Per completare la funzione, aggiungere un altro file seguendo questa procedura:

    1. Fare clic su **Visualizza file**.

       ![Collegamento "Visualizza file"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Fare clic su **+ Aggiungi**.
        
        ![Collegamento "Visualizza file"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Digitare **project.json** e quindi premere **INVIO**. Incollare il codice seguente nel file **project.json**:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Fare clic su **Save**.

        ![Collegamento "Visualizza file"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

È stata creata una funzione di Azure. Questa funzione viene attivata ogni volta che il processo di trasformazione dei dati genera un nuovo BLOB.

## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md)
