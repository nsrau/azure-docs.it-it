---
title: Uso di Storage Explorer (anteprima) con Archiviazione file di Azure | Microsoft Docs
description: Informazioni su come usare Storage Explorer (anteprima) per l'utilizzo con file e condivisioni file.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 964691758254531cb92a5b1cbe055ef61d25dba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-storage-explorer-preview-with-azure-file-storage"></a>Uso di Storage Explorer (anteprima) con Archiviazione file di Azure

Archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo Server Message Block (SMB)standard. Sono supportati sia SMB 2.1 che SMB 3.0. Con Archiviazione file di Azure si può eseguire la migrazione ad Azure delle applicazioni legacy basate su condivisioni file velocemente e senza costose riscritture. È possibile usare Archiviazione file per esporre pubblicamente dati a livello mondiale o per archiviare privatamente dati applicazione. Questo articolo illustra come usare Storage Explorer (anteprima) per l'utilizzo con file e condivisioni file.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario eseguire queste operazioni:

- [Scaricare e installare Storage Explorer (anteprima)](http://www.storageexplorer.com/)

- [Connettersi a un account o a un servizio di archiviazione di Azure](https://docs.microsoft.com//azure/vs-azure-tools-storage-manage-with-storage-explorer#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Creare una condivisione file

Tutti i file devono risiedere in una condivisione file, ovvero un semplice raggruppamento logico di file. Un account può contenere un numero illimitato di condivisioni file e ogni condivisione può archiviare un numero illimitato di file.

La procedura seguente illustra come creare una condivisione file all'interno di Storage Explorer (anteprima).

1. Aprire Storage Explorer (anteprima).

2. Nel riquadro sinistro espandere l'account di archiviazione all'interno del quale si vuole creare la condivisione file.

3. Fare clic con il pulsante destro del mouse su **Condivisioni file** e scegliere **Crea condivisione file** dal menu di scelta rapida.

    ![Crea condivisione file](media/vs-azure-tools-storage-explorer-files/image1.png)

4. Sotto la cartella **Condivisioni file** verrà visualizzata una casella di testo. Immettere il nome per la condivisione file. Per un elenco di regole e restrizioni relative alla denominazione delle condivisioni file, vedere la sezione relativa alle [regole di denominazione delle condivisioni](https://docs.microsoft.com//azure/storage/storage-dotnet-how-to-use-blobs#create-a-container).

    ![Denominare la condivisione](media/vs-azure-tools-storage-explorer-files/image2.png)

5. Premere **INVIO** al termine della creazione della condivisione file o **ESC** per annullare. La condivisione file appena creata viene visualizzata sotto la cartella **Condivisioni file** per l'account di archiviazione selezionato.

    ![La nuova condivisione](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>Visualizzare il contenuto di una condivisione file

Le condivisioni file contengono file e cartelle, che a loro volta possono contenere file.

La procedura seguente illustra come visualizzare il contenuto di una condivisione file all'interno di Storage Explorer (anteprima).

1. Aprire Storage Explorer (anteprima).

2. Nel riquadro sinistro espandere l'account di archiviazione che contiene la condivisione file da visualizzare.

3. Espandere le **Condivisioni file** dell'account di archiviazione.

4. Fare clic con il pulsante destro del mouse sulla condivisione file da visualizzare e scegliere **Apri** dal menu di scelta rapida. È anche possibile fare doppio clic sulla condivisione file che si vuole visualizzare.

    ![Apri condivisione](media/vs-azure-tools-storage-explorer-files/image4.png)

5. Nel riquadro principale verrà visualizzato il contenuto della condivisione file.
    
    ![Contenuto della condivisione file](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Eliminare una condivisione file

È possibile creare ed eliminare facilmente le condivisioni file in base alle esigenze. Per informazioni su come eliminare singoli file, vedere la sezione relativa alla [gestione dei file in una condivisione file](https://docs.microsoft.com//azure/vs-azure-tools-storage-explorer-blobs#managing-blobs-in-a-blob-container).

La procedura seguente illustra come eliminare una condivisione file all'interno di Storage Explorer (anteprima).

1. Aprire Storage Explorer (anteprima).

2. Nel riquadro sinistro espandere l'account di archiviazione che contiene la condivisione file da visualizzare.

3. Espandere le **Condivisioni file** dell'account di archiviazione.

4. Fare clic con il pulsante destro del mouse sulla condivisione file da eliminare e scegliere **Elimina** dal menu di scelta rapida. È anche possibile premere **CANC** per eliminare la condivisione file attualmente selezionata.

    ![Eliminazione](media/vs-azure-tools-storage-explorer-files/image6.png)

5. Fare clic su **Sì** nella finestra di dialogo di conferma.
    
    ![Finestra di dialogo di conferma](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Copiare una condivisione file

Storage Explorer (anteprima) consente di copiare una condivisione file negli Appunti e quindi incollarla in un altro account di archiviazione. Per informazioni su come copiare singoli file, vedere la sezione relativa alla [gestione dei file in una condivisione file](https://docs.microsoft.com//azure/vs-azure-tools-storage-explorer-blobs#managing-blobs-in-a-blob-container).

La procedura seguenti illustra come copiare una condivisione file da un account di archiviazione a un altro.

1. Aprire Storage Explorer (anteprima).

2. Nel riquadro sinistro espandere l'account di archiviazione che contiene la condivisione file da copiare.

3. Espandere le **Condivisioni file** dell'account di archiviazione.

4. Fare clic con il pulsante destro del mouse sulla condivisione file da copiare e scegliere **Copy File Share** (Copia condivisione file) dal menu di scelta rapida.

    ![Copia condivisione file](media/vs-azure-tools-storage-explorer-files/image8.png)

5. Fare clic con il pulsante destro del mouse sull'account di archiviazione di destinazione in cui si vuole incollare la condivisione file e scegliere **Paste File Share** (Incolla condivisione file) dal menu di scelta rapida.

    ![Incolla condivisione file](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>Ottenere la firma di accesso condiviso per una condivisione file

Una [firma di accesso condiviso (SAS)](https://docs.microsoft.com//azure/storage/storage-dotnet-shared-access-signature-part-1) fornisce accesso delegato alle risorse nell'account di archiviazione. Questo significa che è possibile concedere a un client autorizzazioni limitate per BLOB, code o tabelle per un periodo di tempo specificato e con un set di autorizzazioni specificato senza dover condividere le chiavi di accesso dell'account.

La procedura seguente illustra come creare una firma di accesso condiviso per una condivisione file.

1. Aprire Storage Explorer (anteprima).

2. Nel riquadro sinistro espandere l'account di archiviazione che contiene la condivisione file per cui si vuole ottenere una firma di accesso condiviso.

3. Espandere le **Condivisioni file** dell'account di archiviazione.

4. Fare clic con il pulsante destro del mouse sulla condivisione file e scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso) dal menu di scelta rapida.

    ![Ottenere la firma di accesso condiviso](media/vs-azure-tools-storage-explorer-files/image10.png)

5. Nella finestra di dialogo **Firma di accesso condiviso** specificare i criteri, le date di inizio e di scadenza, il fuso orario e i livelli di accesso da impostare per la risorsa.

    ![Finestra di dialogo Firma di accesso condiviso](media/vs-azure-tools-storage-explorer-files/image11.png)

6. Una volta specificate le opzioni per la firma di accesso condiviso, scegliere **Crea**.

7. Verrà visualizzata una seconda finestra di dialogo **Firma di accesso condiviso** con l'elenco della condivisione file insieme all'URL e alle stringhe di query che è possibile usare per accedere alla risorsa di archiviazione. Selezionare **Copia** accanto all'URL che si vuole copiare negli Appunti.
    
    ![Seconda finestra di dialogo Firma di accesso condiviso](media/vs-azure-tools-storage-explorer-files/image12.png)

8. Al termine, scegliere **Chiudi**.

## <a name="manage-access-policies-for-a-file-share"></a>Gestire i criteri di accesso per una condivisione file

La procedura seguente illustra come gestire, ovvero aggiungere e rimuovere, criteri di accesso per una condivisione file. I criteri di accesso vengono usati per la creazione di URL di firma di accesso condiviso che permettono di accedere alla risorsa di Archiviazione file in un periodo di tempo specificato.

1. Aprire Storage Explorer (anteprima).

2. Nel riquadro sinistro espandere l'account di archiviazione che contiene la condivisione file di cui si vogliono gestire i criteri di accesso.

3. Espandere le **Condivisioni file** dell'account di archiviazione.

4. Selezionare la condivisione file e scegliere **Manage Access Policies** (Gestisci criteri di accesso) dal menu di scelta rapida.

    ![Menu di scelta rapida Manage Access Policies (Gestisci criteri di accesso)](media/vs-azure-tools-storage-explorer-files/image13.png)

5. Nella finestra di dialogo **Criteri di accesso** saranno elencati tutti i criteri di accesso già creati per la condivisione file selezionata.
    
    ![Criteri di accesso](media/vs-azure-tools-storage-explorer-files/image14.png)

6. Seguire questi passaggi a seconda dell'attività di gestione dei criteri di accesso:
    
    - **Aggiungere nuovi criteri di accesso**: selezionare **Aggiungi**. Una volta generati, nella finestra di dialogo **Criteri di accesso** verranno visualizzati i criteri di accesso appena aggiunti, con le impostazioni predefinite.

    - **Modificare i criteri di accesso**: apportare le modifiche desiderate e scegliere **Salva**.

    - **Rimuovere criteri di accesso**: selezionare **Rimuovi** accanto ai criteri di accesso da rimuovere.

7. Creare un nuovo URL di firma di accesso condiviso usando i criteri di accesso creati in precedenza:
    
    ![Ottenere una firma di accesso condiviso](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![Nome e proprietà della firma di accesso condiviso](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Gestione dei file in una condivisione file

Dopo aver creato una condivisione file, è possibile caricare un file in tale condivisione file, scaricare un file nel computer locale, aprire un file nel computer locale e molto altro ancora.

La procedura seguente illustra come gestire i file e le cartelle all'interno di una condivisione file.

1.  Aprire Storage Explorer (anteprima).

2.  Nel riquadro sinistro espandere l'account di archiviazione che contiene la condivisione file da gestire.

3.  Espandere le **Condivisioni file** dell'account di archiviazione.

4.  Fare doppio clic sulla condivisione file che si vuole visualizzare.

5.  Nel riquadro principale verrà visualizzato il contenuto della condivisione file.

    ![Contenuto della condivisione file](media/vs-azure-tools-storage-explorer-files/image17.png)

6.  Nel riquadro principale verrà visualizzato il contenuto della condivisione file.

7.  Seguire questi passaggi in base all'attività da eseguire:

    - **Caricare file in una condivisione file**

        a.  Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Carica i file** dal menu a discesa.

        ![Caricare file](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. Nella finestra di dialogo **Carica i file** scegliere il pulsante con i puntini di sospensione (**…**) a destra della casella **File** per selezionare i file da caricare.

        ![Aggiunta di file](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Selezionare **Carica**.

    - **Caricare una cartella in una condivisione file**
        
        a. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Upload Folder** (Carica cartella) dal menu a discesa.

        ![Menu di Upload Folder (Carica cartella)](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. Nella finestra di dialogo **Upload Folder** (Carica cartella) scegliere il pulsante con i puntini di sospensione (**…**) a destra della casella di testo **Cartella** per selezionare la cartella di cui si vuole caricare il contenuto.

        c. È possibile specificare una cartella di destinazione in cui verrà caricato il contenuto della cartella selezionata. Se la cartella di destinazione non esiste, verrà creata.

        d. Selezionare **Carica**.

    - **Scaricare un file nel computer locale**
        
        a. Selezionare il file da scaricare.
        
        b. Sulla barra degli strumenti del riquadro principale selezionare **Scarica**.
        
        c. Nella finestra di dialogo **Specify where to save the downloaded file** (Specificare dove salvare il file scaricato) specificare il percorso in cui salvare il file scaricato e il nome da assegnare.

        d. Selezionare **Salva**.

    - **Aprire un file nel computer locale**
        
        a.  Selezionare il file da aprire.
        
        b.  Sulla barra degli strumenti del riquadro principale selezionare **Apri**.
        
        c.  Il file verrà scaricato e aperto usando l'applicazione associata al tipo di file sottostante del file.

    - **Copiare un file negli Appunti**

        a. Selezionare il file da copiare.

        b. Sulla barra degli strumenti del riquadro principale selezionare **Copia**.

        c. Nel riquadro sinistro passare a un'altra condivisione file e fare doppio clic su di essa per visualizzarla nel riquadro principale.

        d. Sulla barra degli strumenti del riquadro principale selezionare **Incolla** per creare una copia del file.

    - **Eliminare un file**

        a. Selezionare il file da eliminare.

        b. Sulla barra degli strumenti del riquadro principale selezionare **Elimina**.

        c. Fare clic su **Sì** nella finestra di dialogo di conferma.

## <a name="next-steps"></a>Passaggi successivi

- Vedere le note sulla versione e i video in [Microsoft Azure Storage Explorer](http://www.storageexplorer.com/)(anteprima).

- Informazioni su come creare applicazioni con BLOB, tabelle, code e file di Azure nella [Documentazione su Archiviazione](https://azure.microsoft.com/documentation/services/storage/).
