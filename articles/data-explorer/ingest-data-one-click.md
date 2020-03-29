---
title: Usare l'inserimento con un clic per inserire dati in Azure Data ExplorerUse one-click ingestion to ingest data into Azure Data Explorer
description: Informazioni su come inserire (caricare) i dati in Azure Data Explorer semplicemente usando l'inserimento con un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444544"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Usare l'inserimento con un clic per inserire dati in Azure Data ExplorerUse one-click ingestion to ingest data into Azure Data Explorer

Questo articolo illustra come usare l'inserimento con un clic per l'inserimento rapido di una nuova tabella in formato JSON o CSV. I dati possono essere inseriti dall'archiviazione o da un file locale in una tabella esistente o in una nuova tabella. Usa l'intuitiva procedura guidata con un clic e i tuoi dati vengono reinseriti in pochi minuti. È quindi possibile modificare la tabella ed eseguire query usando l'interfaccia utente Web di Azure Data Explorer.Then, you can edit the table and run queries by using the Azure Data Explorer Web UI.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Accedere [all'applicazione](https://dataexplorer.azure.com/).
* Creare [un database e un cluster di Azure Data Explorer](create-cluster-database-portal.md).
* Accedere [all'interfaccia utente Web](https://dataexplorer.azure.com/) e aggiungere una connessione al [cluster.](/azure/data-explorer/web-query-data#add-clusters)

## <a name="ingest-new-data"></a>Inserimento di nuovi dati

1. Fare clic con il pulsante destro del mouse sulla riga del *database* o della *tabella* nel menu a sinistra dell'interfaccia utente Web e selezionare **Inserisci nuovi dati (anteprima)**.

    ![Selezionare l'inserimento con un clic nell'interfaccia utente Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Nella finestra **Inserisci nuovi dati (anteprima),** selezionare la scheda **Origine** e completare i dettagli del **progetto:**

    * Per **Tabella**, selezionare un nome di tabella esistente dal menu a discesa o selezionare **Crea nuovo** per creare una nuova tabella.
    * Per **Tipo di inserimento**, selezionare **dall'archivio** o **dal file**.
      * Se si seleziona **dall'archivio**, selezionare **Collega all'archiviazione** per aggiungere l'URL. Usare [l'URL di accesso](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) condiviso BLOB per gli account di archiviazione privati. 
      * Se si seleziona **da file**, selezionare **Sfoglia** e trascinare il file nella casella.
    * Selezionare **Modifica schema** per visualizzare e modificare la configurazione della colonna della tabella.
 
    ![Dettagli origine di inserimento con un clic](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Se si seleziona **Inserisci nuovi dati (Anteprima)** su una riga di *tabella,* il nome della tabella selezionata verrà visualizzato in **Dettagli progetto.**

1. Se è stata selezionata una tabella esistente, viene visualizzata la finestra **Mappa colonne** per mappare le colonne dei dati di origine alle colonne della tabella di destinazione. 
    * Utilizzare **Omit column** per rimuovere una colonna di destinazione dalla tabella.
    * Utilizzare **Nuova colonna** per aggiungere una nuova colonna alla tabella.

    ![Finestra Colonne mappa](media/ingest-data-one-click/one-click-map-columns-window.png)

1. Nella scheda **Schema:**

    * Dal menu a discesa, selezionare **Tipo di compressione,** quindi selezionare **Non compresso** o **G-ip**.
    * Selezionare **Formato dati** dal menu a discesa, quindi selezionare **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**o **PSV**. 
        * Quando si seleziona il formato **JSON,** è necessario selezionare anche i **livelli JSON,** da 1 a 10. I livelli influiscono sulla rappresentazione dei dati delle colonne della tabella. 
        * Se si seleziona un formato diverso da JSON, è necessario selezionare la casella di controllo **Includi nomi** di colonna per ignorare la riga di intestazione del file.
    * **Il nome del mapping** viene impostato automaticamente, ma può essere modificato.
    * Se è stata selezionata una tabella esistente, è possibile selezionare **Mappa colonne** per aprire la finestra **Mappa colonne.**

    ![Schema del formato CSV di inserimento con un clic](media/ingest-data-one-click/one-click-csv-format.png)

1. Sopra il riquadro **Editor,** selezionare il pulsante **v** per aprire l'editor. Nell'editor è possibile visualizzare e copiare le query automatiche generate dagli input. 

1. Nella tabella: 
    * Fare clic con il pulsante destro del mouse su nuove intestazioni di colonna in **Modifica tipo di dati**, Rinomina **colonna**, **Elimina colonna**, Ordina in ordine **crescente**o **Ordinamento decrescente.** Nelle colonne esistenti è disponibile solo l'ordinamento dei dati. 
    * Fare doppio clic sul nuovo nome della colonna da modificare.

1. Selezionare **Avvia inserimento** per creare una tabella e il mapping e iniziare l'inserimento dei dati.

    ![Schema del formato JSON di inserimento con un clic](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Eseguire query sui dati

1. Nella finestra **Inserimento dati completata,** tutti e tre i passaggi verranno contrassegnati con segni di spunta verdi se l'inserimento dei dati viene completato correttamente.
 
    ![Inserimento dati con un clic completato](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selezionare il pulsante **v** per aprire la query. Copiare nell'interfaccia utente Web per modificare la query.

1. Il menu a destra contiene le **opzioni Query rapide** e **Strumenti.** 

    * **Le query rapide** includono collegamenti all'interfaccia utente Web con query di esempio.
    * **Strumenti** include un collegamento **ai comandi di rilascio** nell'interfaccia `.drop` utente Web, che consentono di risolvere i problemi eseguendo i comandi pertinenti.

    > [!TIP]
    > È possibile che si `.drop` verifichi una perdita di dati quando si utilizzano i comandi. Usali con attenzione.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query sui dati nell'interfaccia utente Web di Esplora dati di Azure](web-query-data.md)
* [Scrivere query per Azure Data Explorer usando Kusto Query Language](write-queries.md)
