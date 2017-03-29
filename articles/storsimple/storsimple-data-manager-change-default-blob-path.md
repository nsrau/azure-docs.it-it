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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>Cambiare il percorso BLOB da quello predefinito (anteprima privata)

Questo articolo descrive come impostare una funzione di Azure per rinominare un percorso di file BLOB. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di:
* Una definizione del processo configurata correttamente in una Risorsa dati ibrida all'interno di un gruppo di risorse.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Per creare una funzione di Azure, seguire questa procedura.

#### <a name="to-create-an-azure-function"></a>Per creare una funzione di Azure

1. Accedere al [portale di Azure](http://portal.azure.com/).

2. Fare clic su **+ Nuovo** dall'angolo superiore sinistro. Digitare "app per le funzioni" nella casella di testo **Cerca** e premere **INVIO**.

    ![Passare alla risorsa App per le funzioni](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. Fare clic su **App per le funzioni** nei risultati.

    ![Selezionare la risorsa App per le funzioni](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. Aprire la finestra **App per le funzioni** e fare clic su **Crea**.

    ![Creare una nuova app per le funzioni](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. Nel pannello **Configurazione** immettere tutti i dati di input necessari e fare clic su **Crea**.

    1. Nome app
    2. Sottoscrizione
    3. Gruppo di risorse
    4. Piano di hosting - **Piano a consumo**
    5. Percorso
    6. Account di archiviazione - Usare un account di archiviazione esistente o crearne uno nuovo. Un account di archiviazione viene usato internamente per la funzione.

        ![Immettere i dati per la configurazione della nuova app per le funzioni](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. Dopo aver creato l'app per le funzioni, passare ad **Altri servizi >** in basso a sinistra. Digitare "Servizi app" nella casella di testo **Filtro** e fare clic su **Servizi app**.

    ![Altri servizi >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. Fare clic sul **nome dell'app per le funzioni** nell'elenco dei servizi app.

8. Fare clic su **+ Nuova funzione**. Selezionare **C#** nell'elenco a discesa **Linguaggio**. Selezionare l'opzione **QueueTrigger-CSharp** nell'elenco dei modelli. Immettere tutti gli input.

   1. Nome - Specificare un nome per la funzione.
   2. Nome coda - Immettere il **nome della definizione del processo di trasformazione dati**.
   3. Connessione dell'account di archiviazione - Fare clic sull'opzione **Nuova**. Selezionare l'account corrispondente al processo di trasformazione dati.
      
      Prendere nota di `Connection name`. Questo nome è necessario in seguito nella funzione di Azure.

   4. Fare clic sul pulsante **Crea**.

       ![Creare una nuova funzione C Sharp >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. Nella finestra **Funzione** eseguire il file con estensione _csx_. Copiare e incollare il codice seguente:

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
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
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

   1. Sostituire **STORAGE_CONNECTIONNAME** nella riga 12 con il nome della connessione all'account di archiviazione (vedere il punto 8c).
   2. Fare clic sul pulsante **Salva** in alto a sinistra.

       ![Salvare la funzione >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  Fare clic su **Visualizza file** dall'angolo destro.

    ![Visualizza file](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. Fare clic su **+ Aggiungi**. Digitare **project.json** e premere **INVIO**.
   2. Copiare e incollare il codice seguente nel file **project.json**.

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

   2. Fare clic su **Salva**.

È stata creata una funzione di Azure. Questa funzione viene attivata ogni volta che viene generato un nuovo BLOB tramite il processo di trasformazione dati.

## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).

