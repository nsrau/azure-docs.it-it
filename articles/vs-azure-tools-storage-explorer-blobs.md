---
title: Gestire le risorse Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer (anteprima) | Microsoft Docs
description: Gestire contenitori BLOB e BLOB di Azure con Storage Explorer (anteprima)
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: tarcher

---
# Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer (anteprima)
## Overview
[Archivio BLOB di Azure](storage/storage-dotnet-how-to-use-blobs.md) è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. L'archiviazione BLOB può essere usata per esporre dati pubblicamente a livello mondiale o archiviare privatamente i dati delle applicazioni. In questo articolo si apprenderà come usare Storage Explorer (anteprima) per l'utilizzo di contenitori BLOB e BLOB.

## Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario eseguire queste operazioni:

* [Scaricare e installare Storage Explorer (anteprima)](http://www.storageexplorer.com)
* [Connettersi a un account o a un servizio di archiviazione di Azure](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## Creare un contenitore BLOB
Tutti i BLOB devono risiedere in un contenitore BLOB, che è semplicemente un raggruppamento logico di BLOB. Un account può contenere un numero illimitato di contenitori e ogni contenitore può archiviare un numero illimitato di BLOB.

I passaggi seguenti illustrano come creare un contenitore BLOB all'interno di Storage Explorer (anteprima).

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione all'interno del quale si vuole creare il contenitore BLOB.
3. Fare clic con il pulsante destro del mouse su **Contenitori BLOB** e scegliere **Crea contenitore BLOB** dal menu di scelta rapida.
   
   ![Menu di scelta rapida Crea contenitore BLOB][0]
4. Sotto la cartella **contenitori BLOB** verrà visualizzata una casella di testo. Immettere il nome per il contenitore BLOB. Per un elenco di regole e restrizioni relative alla denominazione dei contenitori BLOB, vedere le [regole di denominazione dei contenitori](storage/storage-dotnet-how-to-use-blobs.md#create-a-container) nella sezione Creare un contenitore.
   
   ![Casella di testo Crea contenitore BLOB][1]
5. Premere **INVIO** al termine della creazione del contenitore BLOB o **ESC** per annullare. Dopo aver creato il contenitore BLOB, verrà visualizzato sotto la cartella **contenitori BLOB** per l'account di archiviazione selezionato.
   
   ![Contenitore BLOB creato][2]

## Visualizzare il contenuto di un contenitore BLOB
I contenitori BLOB includono BLOB e cartelle, che possono anche contenere BLOB.

I passaggi seguenti illustrano come visualizzare il contenuto di un contenitore BLOB all'interno di Storage Explorer (anteprima):

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB da visualizzare.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Fare clic con il pulsante destro del mouse sul contenitore BLOB che si vuole visualizzare e scegliere **Open Blob Container Editor** (Apri editor contenitori BLOB) dal menu di scelta rapida. È anche possibile fare doppio clic sul contenitore BLOB che si vuole visualizzare.
   
   ![Menu di scelta rapida Open Blob Container Editor (Apri editor contenitori BLOB)][19]
5. Nel riquadro principale verrà visualizzato il contenuto del contenitore BLOB.
   
   ![Editor contenitori BLOB][3]

## Eliminare un contenitore BLOB
I contenitori di BLOB possono essere creati facilmente ed eliminati in base alle esigenze. Per informazioni su come eliminare singoli BLOB, vedere la sezione [Gestione dei BLOB in un contenitore BLOB](..md#managing-blobs-in-a-blob-container).

I passaggi seguenti illustrano come eliminare un contenitore BLOB all'interno di Storage Explorer (anteprima):

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB da visualizzare.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Fare clic con il pulsante destro del mouse sul contenitore BLOB che si vuole eliminare e scegliere **Elimina** dal menu di scelta rapida. È anche possibile premere **CANC** per eliminare il contenitore BLOB attualmente selezionato.
   
   ![Menu di scelta rapida Elimina per il contenitore BLOB][4]
5. Fare clic su **Sì** nella finestra di dialogo di conferma.
   
   ![Menu di scelta rapida dell'eliminazione per il contenitore BLOB][5]

## Copiare un contenitore BLOB
Storage Explorer (anteprima) consente di copiare un contenitore BLOB negli Appunti e quindi incollarlo in un altro account di archiviazione. Per informazioni su come copiare singoli BLOB, vedere la sezione [Gestione dei BLOB in un contenitore BLOB](..md#managing-blobs-in-a-blob-container).

I passaggi seguenti illustrano come copiare un contenitore BLOB da un account di archiviazione a un altro.

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB da copiare.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Fare clic con il pulsante destro del mouse sul contenitore BLOB che si vuole copiare e scegliere **Copy Blob Container** (Copia contenitore BLOB) dal menu di scelta rapida.
   
   ![Menu di scelta Copy Blob Container (Copia contenitore BLOB)][6]
5. Fare clic con il pulsante destro del mouse sull'account di archiviazione di "destinazione" in cui si vuole incollare il contenitore BLOB e - scegliere **Paste Blob Container** (Incolla contenitore BLOB) dal menu di scelta rapida.
   
   ![Menu di scelta rapida Paste Blob Container (Incolla contenitore BLOB)][7]

## Ottenere la firma di accesso condiviso per un contenitore BLOB
Una [firma di accesso condiviso (SAS)](storage/storage-dotnet-shared-access-signature-part-1.md) fornisce l'accesso delegato alle risorse nell'account di archiviazione. Questo significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account.

I passaggi seguenti illustrano come creare una firma di accesso condiviso per un contenitore BLOB:

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB per cui si vuole ottenere una firma di accesso condiviso.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Fare clic con il pulsante destro del mouse sul contenitore BLOB desiderato e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso) dal menu di scelta rapida.
   
   ![Menu di scelta rapida Get Shared Access Signature (Ottieni firma di accesso condiviso)][8]
5. Nella finestra di dialogo **Firma di accesso condiviso** specificare i criteri, le date di inizio e di scadenza, il fuso orario e i livelli di accesso da impostare per la risorsa.
   
   ![Opzioni di Get Shared Access Signature (Ottieni firma di accesso condiviso)][9]
6. Una volta specificate le opzioni per la firma di accesso condiviso, scegliere **Crea**.
7. Verrà visualizzata una seconda finestra di dialogo **Firma di accesso condiviso** con l'elenco del contenitore BLOB insieme all'URL e alle stringhe di query che è possibile usare per accedere alla risorsa di archiviazione. Selezionare **Copia** accanto all'URL che si vuole copiare negli Appunti.
   
   ![Copiare gli URL della firma di accesso condiviso][10]
8. Al termine, scegliere **Chiudi**.

## Gestire i criteri di accesso per un contenitore BLOB
I passaggi seguenti illustrano come gestire, ovvero aggiungere e rimuovere, criteri di accesso per un contenitore BLOB:

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB di cui si vogliono gestire i criteri di accesso.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Selezionare il contenitore BLOB desiderato e scegliere **Manage Access Policies** (Gestisci criteri di accesso) dal menu di scelta rapida.
   
   ![Menu di scelta rapida Manage Access Policies (Gestisci criteri di accesso)][11]
5. Nella finestra di dialogo **Criteri di accesso** saranno elencati tutti i criteri di accesso già creati per il contenitore BLOB selezionato.
   
   ![Opzioni di Criteri di accesso][12]
6. Seguire questi passaggi a seconda dell'attività di gestione dei criteri di accesso:
   
   * **Aggiungere nuovi criteri di accesso**: selezionare **Aggiungi**. Una volta generati, nella finestra di dialogo **Criteri di accesso** verranno visualizzati i criteri di accesso appena aggiunti, con le impostazioni predefinite.
   * **Modificare i criteri di accesso**: apportare le modifiche desiderata e scegliere **Salva**.
   * **Rimuovere criteri di accesso** : selezionare **Rimuovi** accanto ai criteri di accesso da rimuovere.

## Impostare il livello di accesso pubblico per un contenitore BLOB
Per impostazione predefinita, ogni contenitore BLOB è impostato su "No public access" (Nessun accesso pubblico).

La procedura seguente illustra come specificare un livello di accesso pubblico per un contenitore BLOB.

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB di cui si vogliono gestire i criteri di accesso.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Selezionare il contenitore BLOB desiderato e scegliere **Set Public Access Level** (Imposta livello di accesso pubblico) dal menu di scelta rapida.
   
   ![Menu di scelta rapida Set Public Access Level (Imposta livello di accesso pubblico)][13]
5. Nella finestra di dialogo **Set Container Public Access Level** (Imposta livello di accesso pubblico del contenitore) specificare il livello di accesso desiderato.
   
   ![Opzioni di Set Public Access Level (Imposta livello di accesso pubblico)][14]
6. Selezionare **Applica**.

## Gestione dei BLOB in un contenitore BLOB
Dopo aver creato un contenitore BLOB, è possibile caricare un BLOB in tale contenitore BLOB, scaricare un BLOB nel computer locale, aprire un BLOB nel computer locale e molto altro ancora.

I passaggi seguenti illustrano come gestire i BLOB e le cartelle all'interno di un contenitore BLOB.

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere l'account di archiviazione che include il contenitore BLOB da gestire.
3. Espandere **contenitori BLOB** dell'account di archiviazione.
4. Fare doppio clic sul contenitore BLOB che si vuole visualizzare.
5. Nel riquadro principale verrà visualizzato il contenuto del contenitore BLOB.
   
   ![Visualizzazione del contenitore BLOB][3]
6. Nel riquadro principale verrà visualizzato il contenuto del contenitore BLOB.
7. Seguire questi passaggi in base all'attività da eseguire:
   
   * **Caricare file in un contenitore BLOB**
     
     1. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Carica i file** dal menu a discesa.
        
        ![Menu Carica i file][15]
     2. Nella finestra di dialogo **Carica i file** scegliere il pulsante con i puntini di sospensione (**…**) a destra della casella **File** per selezionare i file da caricare.
        
        ![Opzioni di Carica i file][16]
     3. Specificare un valore per tipo **Tipo BLOB**. L'articolo [Introduzione all'archivio BLOB di Azure con .NET](storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) illustra le differenze tra i vari tipi di BLOB.
     4. È possibile specificare una cartella di destinazione in cui verranno caricati i file selezionati. Se la cartella di destinazione non esiste, verrà creata.
     5. Selezionare **Carica**.
   * **Caricare una cartella in un contenitore BLOB**
     
     1. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Upload Folder** (Carica cartella) dal menu a discesa.
        
        ![Menu di Upload Folder (Carica cartella)][17]
     2. Nella finestra di dialogo **Upload Folder** (Carica cartella) scegliere il pulsante con i puntini di sospensione (**…**) a destra della casella **Cartella** per selezionare la cartella di cui si vuole caricare il contenuto.
        
        ![Opzioni di Upload Folder (Carica cartella)][18]
     3. Specificare un valore per tipo **Tipo BLOB**. L'articolo [Introduzione all'archivio BLOB di Azure con .NET](storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) illustra le differenze tra i vari tipi di BLOB.
     4. È possibile specificare una cartella di destinazione in cui verrà caricato il contenuto della cartella selezionata. Se la cartella di destinazione non esiste, verrà creata.
     5. Selezionare **Carica**.
   * **Scaricare un BLOB nel computer locale**
     
     1. Selezionare il blob che si vuole scaricare.
     2. Sulla barra degli strumenti del riquadro principale selezionare **Scarica**.
     3. Nella finestra di dialogo **Specify where to save the downloaded blob** (Specificare dove salvare il BLOB scaricato) specificare il percorso in cui salvare il BLOB scaricato e il nome da assegnare.
     4. Selezionare **Salva**.
   * **Aprire un BLOB nel computer locale**
     
     1. Selezionare il BLOB che si vuole aprire.
     2. Sulla barra degli strumenti del riquadro principale selezionare **Apri**.
     3. Il BLOB verrà scaricato e aperto usando l'applicazione associata al tipo di file sottostante del BLOB.
   * **Copiare un BLOB negli Appunti**
     
     1. Selezionare il BLOB che si vuole copiare.
     2. Sulla barra degli strumenti del riquadro principale selezionare **Copia**.
     3. Nel riquadro a sinistra passare a un altro contenitore BLOB e fare doppio clic su esso per visualizzarlo nel riquadro principale.
     4. Sulla barra degli strumenti del riquadro principale selezionare **Incolla** per creare una copia del BLOB.
   * **Eliminare un BLOB**
     
     1. Selezionare il BLOB che si vuole eliminare.
     2. Sulla barra degli strumenti del riquadro principale selezionare **Elimina**.
     3. Fare clic su **Sì** nella finestra di dialogo di conferma.

## Passaggi successivi
* Vedere le note sulla versione e i video in [Microsoft Azure Storage Explorer](http://www.storageexplorer.com) (anteprima).
* Informazioni su come creare applicazioni con BLOB, tabelle, code e file di Azure nella [Documentazione su Archiviazione](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png

<!---HONumber=AcomDC_0817_2016-->