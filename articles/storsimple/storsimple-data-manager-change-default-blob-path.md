---
title: Cambiare il percorso BLOB da quello predefinito | Microsoft Docs
description: Informazioni su come impostare una funzione di Azure per rinominare un percorso di file BLOB (anteprima privata)
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
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Cambiare il percorso BLOB da quello predefinito (anteprima privata)

L'articolo descrive come impostare una funzione di Azure per rinominare un percorso di file BLOB predefinito. 

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una definizione del processo configurata correttamente in una risorsa dati ibrida all'interno di un gruppo di risorse.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Per creare una funzione di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](http://portal.azure.com/).

2. Nella parte superiore del riquadro sinistro fare clic su **Nuovo**. 

3. Digitare **App per le funzioni** nella casella **Cerca** e quindi premere INVIO.

    ![Digitare "App per le funzioni" nella casella Cerca](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. Fare clic su **App per le funzioni** nell'elenco **Risultati**.

    ![Selezionare la risorsa app per le funzioni nell'elenco dei risultati](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    Verrà visualizzata la finestra **App per le funzioni**.

5. Fare clic su **Crea**.

    ![Pulsante "Crea" della finestra App per le funzioni](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. Nel pannello di configurazione **App per le funzioni** seguire questa procedura:

    a. Nella casella **Nome app** digitare il nome dell'app.
    
    b. Nella casella **Sottoscrizione** digitare il nome della sottoscrizione.

    c. In **Gruppo di risorse** fare clic su **Crea nuovo** e quindi digitare il nome del gruppo di risorse.

    d. Nella casella **Piano di hosting** digitare **Piano a consumo**.

    e. Nella casella **Percorso** digitare il percorso.

    f. In **Account di archiviazione** selezionare un account di archiviazione esistente o crearne uno nuovo. Un account di archiviazione viene usato internamente per la funzione.

    ![Immettere i dati per la configurazione della nuova app per le funzioni](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Fare clic su **Crea**.  
    Verrà creata l'app per le funzioni.

8. Nel riquadro sinistro fare clic su **Altri servizi** e quindi seguire questa procedura:
    
    a. Nella casella **Filtro** digitare **Servizi app**.
    
    b. Fare clic su **Servizi app**. 

    ![Collegamento "Altri servizi" nel riquadro sinistro](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. Nell'elenco dei servizi app fare clic sul nome dell'app per le funzioni.  
    Verrà visualizzata la pagina dell'app per le funzioni.

10. Nel riquadro sinistro fare clic su **Nuova funzione** e quindi seguire questa procedura: 

    a. Nell'elenco **Linguaggio** selezionare **C#**.
    
    b. Nella matrice di riquadri di modello selezionare **QueueTrigger-CSharp**.

    c. Nella casella **Assegnare un nome alla funzione** digitare un nome per la funzione.

    d. Nella casella **Nome coda** digitare in nome della definizione del processo di trasformazione dei dati.

    e. In **Connessione dell'account di archiviazione** fare clic su **Nuova** e quindi selezionare l'account che corrisponde al processo di trasformazione dei dati.  
        Prendere nota del nome della connessione. Questo nome è necessario in seguito nella funzione di Azure.

       ![Creare una nuova funzione C#](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Fare clic su **Crea**.  
    Verrà visualizzata la finestra **Funzione**.

11. Nella finestra **Funzione** eseguire il file con estensione _csx_ e quindi seguire questa procedura:

    a. Incollare il codice seguente:

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

    b. Sostituire **STORAGE_CONNECTIONNAME** nella riga 11 con il nome della connessione all'account di archiviazione (vedere il punto 8c).

    c. Nella parte superiore sinistra fare clic su **Salva**.

    ![Salvare la funzione](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Per completare la funzione, aggiungere un altro file seguendo questa procedura:

    a. Fare clic su **Visualizza file**.

       ![Collegamento "Visualizza file"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Fare clic su **Aggiungi**.
    
    c. Digitare **project.json** e quindi premere INVIO.
    
    d. Incollare il codice seguente nel file **project.json**:

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

    e. Fare clic su **Salva**.

È stata creata una funzione di Azure. Questa funzione viene attivata ogni volta che il processo di trasformazione dei dati genera un nuovo BLOB.

## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md)
