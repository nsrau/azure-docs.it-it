---
title: Usare l'inserimento con un clic per inserire i dati in Azure Esplora dati
description: Informazioni su come inserire (caricare) i dati in Azure Esplora dati semplicemente usando l'inserimento con un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 0d7c8b2661ee3361b3a485b8cae4eef3a8225120
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975245"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Usare l'inserimento con un clic per inserire i dati in Azure Esplora dati

Questo articolo illustra come usare l'inserimento con un clic per l'inserimento rapido di una nuova tabella in formato JSON o CSV. I dati possono essere inseriti dall'archiviazione o da un file locale in una tabella esistente o in una nuova tabella. Usare la procedura guidata intuitiva con un clic e l'inserimento dei dati entro pochi minuti. Quindi, è possibile modificare la tabella ed eseguire query usando l'interfaccia utente Web di Azure Esplora dati.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account Azure gratuito](https://azure.microsoft.com/free/).
* Accedere all'[applicazione](https://dataexplorer.azure.com/).
* Creare [un cluster e un database di Azure Esplora dati](create-cluster-database-portal.md).
* Accedere all' [interfaccia utente Web](https://dataexplorer.azure.com/) e [aggiungere una connessione al cluster](/azure/data-explorer/web-query-data#add-clusters).
* Creare un'origine dati in archiviazione di Azure.

## <a name="ingest-new-data"></a>Inserire nuovi dati

1. Fare clic con il pulsante destro del mouse sulla riga del *database* o della *tabella* nel menu a sinistra dell'interfaccia utente Web e scegliere Inserisci **nuovi dati (anteprima)** .

    ![Selezionare inserimento con un clic nell'interfaccia utente Web.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Nella finestra Inserisci **nuovi dati (anteprima)** selezionare la scheda **origine** e completare i dettagli del **progetto**:

    * Per **tabella**selezionare un nome di tabella esistente dal menu a discesa oppure selezionare **Crea nuovo per creare** una nuova tabella.
    * Per **tipo**di inserimento selezionare **da archiviazione** o **da file**.
        * Se è stata selezionata l'opzione **da archiviazione**, selezionare **collega ad archiviazione** per aggiungere l'URL. Usare l' [URL SAS BLOB](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) per gli account di archiviazione privati. 
            * Se è stata selezionata l'opzione **da file**, selezionare **Sfoglia** e trascinare il file nella casella.
    * Selezionare **Modifica schema** per visualizzare e modificare la configurazione della colonna della tabella.
 
    ![Dettagli origine inserimento con un clic.](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Se si seleziona inserire **nuovi dati (anteprima)** in una riga di *tabella* , il nome della tabella selezionata verrà visualizzato nei **Dettagli del progetto**.

1. Se è stata selezionata una tabella esistente, viene visualizzata la finestra **mappa colonne** per eseguire il mapping delle colonne di dati di origine alle colonne della tabella di destinazione. 
    * Utilizzare **omette colonna** per rimuovere una colonna di destinazione dalla tabella. 
    * Utilizzare **nuova colonna** per aggiungere una nuova colonna alla tabella. 

    ![Finestra colonne mappa.](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Nella scheda **schema** :

    * Selezionare **tipo di compressione** dal menu a discesa, quindi selezionare non **compresso** o **gzip**.
    * Selezionare **formato dati** dal menu a discesa e quindi selezionare **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **tsve**o **PSV**. 
        * Quando si seleziona il formato **JSON** , è necessario selezionare anche i **livelli JSON**, da 1 a 10. I livelli influiscono sulla rappresentazione dei dati della colonna della tabella. 
        * Se si seleziona un formato diverso da JSON, è necessario selezionare la casella di controllo **Includi nomi di colonna** per ignorare la riga di intestazione del file.
    * Il **nome del mapping** viene impostato automaticamente, ma può essere modificato.
    * Se è stata selezionata una tabella esistente, è possibile selezionare le **colonne della mappa** per aprire la finestra delle **colonne della mappa** .

    ![Schema del formato CSV di inserimento con un clic.](media/ingest-data-one-click/one-click-csv-format.png)

1. Sopra il riquadro **Editor** selezionare il pulsante **v** per aprire l'editor. Nell'editor è possibile visualizzare e copiare le query automatiche generate dagli input. 

1.  Nella tabella: 
    * Fare clic con il pulsante destro del mouse su nuove intestazioni di colonna per **modificare il tipo di dati**, **rinominare**la colonna, **eliminare colonne**, **ordinamento crescente**o **ordinamento decrescente**. Nelle colonne esistenti è disponibile solo l'ordinamento dei dati. 
    * Fare doppio clic sul nome della nuova colonna da modificare.

1. Selezionare **Avvia** inserimento per creare una tabella e un mapping e iniziare l'inserimento dei dati.

    ![Schema del formato JSON di inserimento con un clic.](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Eseguire query sui dati

1. Nella finestra inserimento **dati completata** , tutti e tre i passaggi verranno contrassegnati con segno di spunta verde se l'inserimento dei dati viene completato correttamente.
 
    ![Inserimento dati con un solo clic completato.](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selezionare il pulsante **v** per aprire la query. Copiare nell'interfaccia utente Web per modificare la query.

1. Il menu a destra contiene le opzioni per le **query rapide** e **gli strumenti** . 

    * Le **query rapide** includono collegamenti all'interfaccia utente Web con query di esempio.
    * Gli **strumenti** includono un collegamento per **rilasciare i comandi** nell'interfaccia utente Web, che consentono di risolvere i problemi eseguendo i comandi di `.drop` pertinenti.

    > [!TIP]
    > È possibile che i dati vadano perduti utilizzando `.drop` comandi. Usarle attentamente.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query sui dati in Azure Esplora dati interfaccia utente Web](web-query-data.md)
* [Scrivere query per Esplora dati di Azure usando il linguaggio di query kusto](write-queries.md)
