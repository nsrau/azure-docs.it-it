---
title: Gestire le risorse di Azure Data Lake Store in Azure Storage Explorer
description: Consentire agli utenti di accedere a dati e risorse di Azure Data Lake Store e gestirli in Azure Storage Explorer
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Gestire le risorse di Azure Data Lake Store con Storage Explorer (anteprima)

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) è un servizio per l'archiviazione di grandi quantità di dati non strutturati, come dati binari o di testo. Gli utenti possono accedere ai dati da qualsiasi posizione, tramite HTTP o HTTPS. Azure Data Lake Store in Azure Storage Explorer consente agli utenti di accedere a dati e risorse di Azure Data Lake Store e gestirli insieme ad altre entità di Azure come BLOB e code. Gli utenti possono ora usare lo stesso strumento per gestire le diverse entità di Azure in un'unica posizione.

Un altro vantaggio è rappresentato dal fatto che non è necessario per gli utenti avere un'autorizzazione a livello di sottoscrizione per gestire i dati di Azure Data Lake Store. In Storage Explorer, per collegare il percorso di Azure Data Lake Store al nodo "Local and Attached" (Locale e collegato) è sufficiente che altri utenti abbiano concesso l'autorizzazione.

## <a name="prerequisites"></a>Prerequisiti
Per completare le procedure in questo articolo, sono necessari i prerequisiti seguenti:

*   Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial).
*   Un account Azure Data Lake Store. Per istruzioni su come crearne uno, vedere [Introduzione ad Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="installation"></a>Installazione

Installare la versione più recente di Azure Storage Explorer disponibile qui: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Attualmente è supportata una versione per Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

1. Dopo l'installazione di **Azure Storage Explorer**, fare clic sull'icona **plug-in** a sinistra, come illustrato nell'immagine seguente.
       
   ![Icona plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Selezionare **Add an Azure Account** (Aggiungi un account Azure), quindi fare clic su **Accedi**.

   ![Connettersi a una sottoscrizione di Azure](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Nella finestra di dialogo **Azure Sign In** (Accesso ad Azure), fare clic su **Accedi** e immettere le credenziali di Azure.

    ![Accesso](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Selezionare la sottoscrizione dall'elenco e quindi fare clic su **Applica**.

    ![Applica](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Il riquadro di Explorer si aggiorna e consente di visualizzare gli account nella sottoscrizione selezionata.

    ![Elenco degli account](./media/data-lake-store-in-storage-explorer/account-list.png)

    La connessione ad **Azure Data Lake Store** nella sottoscrizione di Azure è stata stabilita.

## <a name="connect-to-data-lake-store"></a>Connessione ad Archivio Data Lake
Se si vuole accedere a risorse che non sono incluse nella propria sottoscrizione ma per le quali altri utenti concedono di ottenere l'URI, è possibile connettersi a Data Lake Store usando l'URI dopo aver effettuato l'accesso. Vedere la procedura seguente.
1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere **Local and Attached** (Locale e collegato).
3. Fare clic con il pulsante destro del mouse su **Data Lake Store** e scegliere **Connect to Data Lake Store** (Connetti a Data Lake Store) dal menu di scelta rapida.

      ![Menu di scelta rapida per la connessione a Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Immettere l'URI. Lo strumento passerà quindi alla posizione dell'URL appena immesso.

      ![Finestra di dialogo contestuale per la connessione a Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Risultato della connessione a Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Visualizzare il contenuto di un account Azure Data Lake Store
Le risorse di un account Azure Data Lake Store includono cartelle e risorse.

La procedura seguente illustra come visualizzare il contenuto di un account Azure Data Lake Store in Storage Explorer (anteprima):

1. Aprire Storage Explorer (anteprima).
2. Nel riquadro sinistro espandere la sottoscrizione contenente l'account Azure Data Lake Store che si vuole visualizzare.
3. Espandere **Data Lake Store**.
4. Fare clic con il pulsante destro del mouse sul nodo dell'account Azure Data Lake Store da visualizzare e scegliere **Apri** dal menu di scelta rapida. È anche possibile aprire l'account Azure Data Lake Store facendo doppio clic su di esso. 
5. Nel riquadro principale verrà visualizzato il contenuto dell'account Azure Data Lake Store.

     ![Riquadro principale](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Gestione delle risorse di Azure Data Lake Store

È possibile gestire le risorse di Azure Data Lake Store eseguendo queste operazioni:
*   Spostare le risorse di Azure Data Lake Store tra più account Azure Data Lake.  
*   Usare la stringa di connessione per connettersi e gestire direttamente Azure Data Lake Store. 
*   Visualizzare le risorse di Azure Data Lake Store condivise da altri utenti tramite un elenco di controllo di accesso in Local and Attached (Locale e collegato).
*   Eseguire operazioni CRUD su file e cartelle, con supporto di cartelle ricorsive e selezione multipla di file. 
*   Trascinare e aggiungere una cartella ad Accesso rapido e posizioni recenti, rispecchiando l'esperienza di Esplora file su desktop. 
*   Copiare e aprire un collegamento ipertestuale di Azure Data Lake con Storage Explorer, con un clic. 
*   Visualizzare il log attività nel riquadro in basso a destra per visualizzare lo stato delle attività.
*   Visualizzare le statistiche delle cartelle e le proprietà dei file.

## <a name="manage-resources-in-azure-storage-explorer"></a>Gestire le risorse in Azure Storage Explorer
Dopo aver creato un account Azure Data Lake Store, è possibile caricare cartelle e file, eseguire download e aprire le risorse sul computer locale, nonché effettuare aggiunte ad Accesso rapido, creare una nuova cartella, copiare URL e selezionare tutto. È possibile, inoltre, copiare, incollare, rinominare, eliminare, ottenere le statistiche delle cartelle e aggiornare.

Di seguito viene illustrato come gestire le risorse in un account Azure Data Lake Store. Seguire questi passaggi in base all'attività da eseguire:
   * **Caricare file**

     1. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Carica file** dal menu a discesa.

        ![Menu Carica i file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. Nella finestra di dialogo **Select files to Upload** (Seleziona file da caricare) selezionare i file che si vogliono caricare.

        ![Finestra di dialogo per il caricamento di cartelle](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. Selezionare **Apri** per iniziare il caricamento.
   * **Caricare una cartella**

     1. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Upload Folder** (Carica cartella) dal menu a discesa.

        ![Menu di Upload Folder (Carica cartella)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. Nella finestra di dialogo **Select folder to Upload** (Seleziona cartella da caricare) selezionare una cartella che si vuole caricare.

        ![Finestra di dialogo per il caricamento di cartelle](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. Selezionare **Select Folder** (Seleziona cartella) per iniziare il caricamento di cartelle.

        ![Finestra di dialogo per il caricamento di cartelle](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > È possibile implementare il caricamento trascinando direttamente le cartelle e i file del computer locale. 
       
   * **Scaricare cartelle o file sul computer locale**

     1. Selezionare le cartelle o i file da scaricare.
     2. Sulla barra degli strumenti del riquadro principale selezionare **Scarica**.
     3. Nella finestra di dialogo **Select a folder to save the downloaded files into** (Selezionare una cartella in cui salvare i file scaricati) specificare la posizione in cui si vogliono scaricare le cartelle o i file e il nome che si vuole assegnare a tale posizione.
     4. Selezionare **Salva**.
   * **Aprire una cartella o un file dal computer locale**

     1. Selezionare la cartella o il file da aprire.
     2. Selezionare **Apri** sulla barra degli strumenti del riquadro principale oppure fare clic con il pulsante destro del mouse sulla cartella o sul file selezionato e scegliere **Apri** dal menu di scelta rapida.
     3. Il file verrà scaricato e aperto con l'applicazione associata al relativo tipo di file sottostante. La cartella verrà invece aperta nel riquadro principale.

        ![Aprire il file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **Copiare cartelle o file negli Appunti**

     1. Selezionare le cartelle o i file da copiare.
     2. Selezionare **Copia** sulla barra degli strumenti del riquadro principale oppure fare clic con il pulsante destro del mouse sulle cartelle o sui file selezionati e scegliere **Copia** dal menu di scelta rapida.
     3. Nel riquadro sinistro passare a un altro account Azure Data Lake Store e fare doppio clic su di esso per visualizzarlo nel riquadro principale.
     4. Sulla barra degli strumenti del riquadro principale selezionare **Incolla** per creare una copia. In alternativa, scegliere **Incolla** dal menu di scelta rapida sulla destinazione.

        ![Copiare e incollare cartelle o file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + Le operazioni di copia/incolla in un diverso tipo di archiviazione **non sono** supportate. È possibile copiare cartelle o file di Azure Data Lake Store e incollarli in un altro account Azure Data Lake Store, ma **non** copiare cartelle o file di Azure Data Lake Store e incollarli in un archivio BLOB o viceversa. 
          > + Le operazioni di copia/incolla vengono eseguite scaricando le cartelle o i file in locale e quindi caricandoli nella destinazione. Lo strumento **non** esegue l'azione nel back-end. Un'operazione di copia/incolla di file di grandi dimensioni è lenta. È in corso l'ottimizzazione per attività di copia/spostamento file ad alte prestazioni.
   * **Eliminare cartelle o file**

     1. Selezionare le cartelle o i file da eliminare.
     2. Selezionare **Elimina** sulla barra degli strumenti del riquadro principale oppure fare clic con il pulsante destro del mouse sulle cartelle o sui file selezionati e scegliere **Elimina** dal menu di scelta rapida.
     3. Selezionare **Sì** nella finestra di dialogo di conferma.

        ![Copiare e incollare cartelle o file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **Aggiungere ad Accesso rapido**

     1. Selezionare la cartella da aggiungere.
     2. Sulla barra degli strumenti del riquadro principale selezionare **Aggiungi ad Accesso rapido**.
     3. Nel riquadro sinistro è possibile osservare che la cartella selezionata è stata aggiunta al nodo **Accesso rapido**.

        ![Aggiungere ad Accesso rapido](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. Dopo aver creato l'accesso rapido, è possibile accedere facilmente alle risorse.
   * **Collegamenti diretti**
     1. Se si ha un URL, è possibile immettere l'URL come percorso in forma di indirizzo in **Esplora file** o nel browser.
     2. Verrà avviato automaticamente **Storage Explorer.exe** per passare alla posizione dell'URL appena immesso.

        ![Collegamento diretto in Esplora file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Passaggi successivi
* Vedere le note sulla versione e i video in [Microsoft Azure Storage Explorer](http://www.storageexplorer.com)(anteprima).
* Informazioni su come [gestire Azure Cosmos DB in Azure Storage Explorer](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)
* Altre informazioni su Storage Explorer in [Guida introduttiva a Storage Explorer (anteprima)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* Introduzione ad Azure Data Lake Store (ADLS) in [Panoramica di Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* Video su [come usare Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)
