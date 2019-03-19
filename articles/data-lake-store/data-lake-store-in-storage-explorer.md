---
title: Gestire le risorse di Azure Data Lake Storage Gen1 in Azure Storage Explorer
description: Informazioni su come accedere e gestire dati e risorse di Azure Data Lake Storage Gen1 in Azure Storage Explorer
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: data-lake-store
ms.custom: Azure Data Lake Store
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: bf2af67bb9498d4814f63fa66fdd50171ce0b4a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534345"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Gestire le risorse di Azure Data Lake Storage Gen1 con Storage Explorer

[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) è un servizio per l'archiviazione di grandi quantità di dati non strutturati, come dati binari o di testo. È possibile accedere ai dati da qualsiasi posizione, tramite HTTP o HTTPS. Data Lake Storage Gen1 in Azure Storage Explorer consente di accedere e gestire dati e risorse di Data Lake Storage Gen1 insieme ad altre entità di Azure come BLOB e code. È ora possibile usare lo stesso strumento per gestire le diverse entità di Azure in un'unica posizione.

Un altro vantaggio è rappresentato dal fatto che non è necessario avere un'autorizzazione a livello di sottoscrizione per gestire i dati di Data Lake Storage Gen1. In Storage Explorer per collegare il percorso di Data Lake Storage Gen1 al nodo **Local and Attached** (Locale e collegato) è sufficiente che un altro utente abbia concesso l'autorizzazione.

## <a name="prerequisites"></a>Prerequisiti
Per completare le procedure in questo articolo, sono necessari i prerequisiti seguenti:

*   Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial).
*   Un account Data Lake Storage Gen1. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Installare Storage Explorer

Installare la versione più recente di Azure Storage Explorer dalla [pagina Web del prodotto](https://azure.microsoft.com/features/storage-explorer/). L'installazione supporta versioni per Windows, Linux e Mac.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

1. In Storage Explorer selezionare l'icona del plug-in a sinistra.
       
   ![Icona del plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Selezionare **Add an Azure Account** (Aggiungi un account Azure) e quindi **Accedi**.

   ![Finestra di dialogo per la connessione ad Archiviazione di Azure](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Nella finestra di dialogo **Accesso all'account** immettere le credenziali di Azure.

    ![Finestra di dialogo per l'accesso ad Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Selezionare la sottoscrizione nell'elenco e quindi **Applica**.

    ![Informazioni relative alla sottoscrizione e pulsante "Applica"](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Il riquadro **EXPLORER** viene aggiornato e mostra gli account nella sottoscrizione selezionata.

    ![Elenco degli account](./media/data-lake-store-in-storage-explorer/account-list.png)

Data Lake Storage Gen1 è stato connesso alla sottoscrizione di Azure.

## <a name="connect-to-data-lake-storage-gen1"></a>Connettersi a Data Lake Storage Gen1
È possibile accedere a risorse non incluse nella propria sottoscrizione se un altro utente concede l'URI per le risorse. È quindi possibile connettersi a Data Lake Storage Gen1 usando l'URI dopo aver effettuato l'accesso.
1. Aprire Esplora archivi.
2. Nel riquadro sinistro espandere **Local and Attached** (Locale e collegato).
3. Fare clic con il pulsante destro del mouse su **Data Lake Store** e quindi scegliere **Connect to Data Lake Store** (Connetti a Data Lake Store).

      !["Connect to Data Lake Store" (Connetti a Data Lake Store) nel menu di scelta rapida](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Immettere l'URI. Lo strumento passerà alla posizione dell'URL appena immesso.

      ![Finestra di dialogo "Connect to Data Lake Store" (Connetti a Data Lake Store) con casella di testo per l'immissione dell'URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Risultato della connessione a Data Lake Storage Gen1](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Visualizzare i contenuti di un account Data Lake Storage Gen1
Le risorse di un account Data Lake Storage Gen1 includono cartelle e file.

La procedura seguente illustra come visualizzare i contenuti di un account Data Lake Storage Gen1 in Storage Explorer:

1. Aprire Esplora archivi.
2. Nel riquadro sinistro espandere la sottoscrizione contenente l'account Data Lake Storage Gen1 che si vuole visualizzare.
3. Espandere **Data Lake Store**.
4. Fare clic con il pulsante destro del mouse sul nodo dell'account Data Lake Storage Gen1 da visualizzare e quindi scegliere **Apri**. È anche possibile aprire l'account Data Lake Storage Gen1 facendo doppio clic su di esso. 
   
   Nel riquadro principale sono visualizzati i contenuti dell'account Data Lake Storage Gen1.

   ![Riquadro principale con un elenco di cartelle](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Gestire le risorse in Data Lake Storage Gen1

È possibile gestire le risorse di Data Lake Storage Gen1 eseguendo queste operazioni:
*   Esplorare le risorse di Data Lake Storage Gen1 in più account Data Lake Storage Gen1.  
*   Usare una stringa di connessione per connettersi e gestire direttamente Data Lake Storage Gen1. 
*   Visualizzare le risorse di Data Lake Storage Gen1 condivise da altri utenti tramite un elenco di controllo di accesso in **Local and Attached** (Locale e collegato).
*   Eseguire operazioni CRUD su file e cartelle, con supporto di cartelle ricorsive e selezione multipla di file. 
*   Trascinare e aggiungere una cartella per accedere rapidamente alle posizioni recenti. Questa operazione rispecchia l'esperienza di Esplora file su desktop. 
*   Copiare e aprire un collegamento ipertestuale di Data Lake Storage Gen1 in Storage Explorer con un clic. 
*   Visualizzare il log attività nel riquadro in basso a destra per visualizzare lo stato delle attività.
*   Visualizzare le statistiche delle cartelle e le proprietà dei file.

## <a name="manage-resources-in-azure-storage-explorer"></a>Gestire le risorse in Azure Storage Explorer
Dopo aver creato un account Data Lake Storage Gen1, è possibile:

* Caricare e scaricare cartelle e file e aprire le risorse sul computer locale.
* Effettuare aggiunte ad **Accesso rapido**, creare una nuova cartella, copiare un URL e selezionare tutto.
* Copiare, incollare, rinominare, eliminare, ottenere le statistiche delle cartelle e aggiornare.

Di seguito viene illustrato come gestire le risorse in un account Data Lake Storage Gen1. Seguire la procedura per l'attività desiderata.

### <a name="upload-files"></a>Caricare file

1. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Carica file** nel menu a discesa.

   ![Voce di menu "Carica file"](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. Nella finestra di dialogo **Select files to Upload** (Seleziona file da caricare) selezionare i file che si vogliono caricare.

   ![Finestra di dialogo per il caricamento di file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Selezionare **Apri** per iniziare il caricamento.

### <a name="upload-a-folder"></a>Caricare una cartella

1. Sulla barra degli strumenti del riquadro principale selezionare **Carica** e quindi **Upload Folder** (Carica cartella) nel menu a discesa.

   ![Voce di menu "Upload Folder" (Carica cartella)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. Nella finestra di dialogo **Select folder to upload** (Seleziona cartella da caricare) selezionare una cartella che si vuole caricare. Fare quindi clic su **Select Folder** (Seleziona cartella).

   ![Finestra di dialogo per il caricamento di cartelle](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   Il caricamento si avvia.

   ![Finestra di dialogo con caricamento in corso](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE]
> È possibile avviare il caricamento trascinando direttamente le cartelle e i file di un computer locale. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Scaricare cartelle o file sul computer locale

1. Selezionare le cartelle o i file da scaricare.
2. Sulla barra degli strumenti del riquadro principale selezionare **Scarica**.
3. Nella finestra di dialogo **Select a folder to save the downloaded files into** (Selezionare una cartella in cui salvare i file scaricati) specificare la posizione e il nome.
4. Selezionare **Salva**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Aprire una cartella o un file dal computer locale

1. Selezionare la cartella o il file da aprire.
2. Sulla barra degli strumenti del riquadro principale selezionare **Apri**. In alternativa, fare clic con il pulsante destro del mouse sulla cartella o sul file selezionato e quindi scegliere **Apri** dal menu di scelta rapida.

Il file verrà scaricato e aperto tramite l'applicazione associata al tipo di file sottostante. La cartella verrà invece aperta nel riquadro principale.

![File aperto](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Copiare cartelle o file negli Appunti

1. Selezionare le cartelle o i file da copiare.
2. Sulla barra degli strumenti del riquadro principale selezionare **Copia**. In alternativa, fare clic con il pulsante destro del mouse sulle cartelle o sui file selezionati e quindi scegliere **Copia** dal menu di scelta rapida.
3. Nel riquadro sinistro passare a un altro account Data Lake Storage Gen1 e fare doppio clic su di esso per visualizzarlo nel riquadro principale.
4. Sulla barra degli strumenti del riquadro principale selezionare **Incolla** per creare una copia. In alternativa, scegliere **Incolla** dal menu di scelta rapida della destinazione.

![Selezioni per copiare una cartella](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> Le operazioni di copia/incolla tra diversi tipi di archiviazione non sono supportate. È possibile copiare cartelle o file di Data Lake Storage Gen1 e incollarli in un altro account Data Lake Storage Gen1, ma *non* copiare cartelle o file di Data Lake Storage Gen1 e incollarli in un archivio BLOB di Azure o viceversa.
> 
> Le operazioni di copia/incolla vengono eseguite scaricando le cartelle o i file sul computer locale e quindi caricandoli nella destinazione. Lo strumento *non* esegue l'azione nel back-end. L'operazione di copia/incolla di file di grandi dimensioni è lenta. È in corso l'ottimizzazione per attività di copia/spostamento file ad alte prestazioni.

### <a name="delete-folders-or-files"></a>Eliminare cartelle o file

1. Selezionare le cartelle o i file da eliminare.
2. Sulla barra degli strumenti del riquadro principale selezionare **Elimina**. In alternativa, fare clic con il pulsante destro del mouse sulle cartelle o sui file selezionati e quindi scegliere **Elimina** dal menu di scelta rapida.
3. Selezionare **Sì** nella finestra di dialogo di conferma.

![Selezioni per eliminare una cartella](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Aggiungere ad Accesso rapido

1. Selezionare la cartella da aggiungere.
2. Sulla barra degli strumenti del riquadro principale selezionare **Aggiungi ad Accesso rapido**.

   La cartella selezionata viene aggiunta al nodo **Accesso rapido** nel riquadro sinistro.

   ![Selezioni per aggiungere una cartella ad "Accesso rapido"](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Dopo aver aggiunto una cartella al nodo **Accesso rapido**, è possibile accedere facilmente alle risorse.

### <a name="use-deep-links"></a>Usare collegamenti diretti
Se si ha un URL, è possibile immettere l'URL come percorso in forma di indirizzo in Esplora file o in un browser. Verrà quindi eseguito automaticamente Storage Explorer.exe per passare alla posizione dell'URL.

![Collegamento diretto in Esplora file](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Passaggi successivi
* [Note sulla versione e video più recenti per Storage Explorer](https://www.storageexplorer.com).
* Informazioni su come [gestire Azure Cosmos DB in Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [Guida introduttiva a Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Iniziare a usare Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* [Video di YouTube su come usare Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
