---
title: Usare l'inserimento con un clic per inserire i dati in Azure Esplora dati
description: Informazioni su come inserire (caricare) i dati in Azure Esplora dati semplicemente usando l'inserimento con un clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520049"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Usare l'inserimento con un clic per inserire i dati in Azure Esplora dati

Questo articolo illustra come usare l'inserimento con un clic per l'inserimento rapido di una nuova tabella nei formati JSON o CSV dall'archiviazione in Azure Esplora dati. Una volta inseriti i dati, è possibile modificare la tabella ed eseguire query usando l'interfaccia utente Web.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Accedere all'[applicazione](https://dataexplorer.azure.com/).
* Creare [un cluster e un database di Azure Esplora dati](create-cluster-database-portal.md)
* Origine dei dati in archiviazione di Azure.

## <a name="ingest-new-data"></a>Inserire nuovi dati

1. Fare clic con il pulsante destro del mouse sul *nome del database* e scegliere Inserisci **nuovi dati (anteprima)**

    ![selezione dell'inserimento di un clic nell'interfaccia utente Web](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. Nella finestra inserimento **dati (anteprima)** , nella scheda **origine** , completare i **Dettagli del progetto**:

    * Immettere il **nome**della nuova tabella. 
    * Selezionare il **tipo** > **di inserimento dalla risorsa di archiviazione**.
    * Immettere il **collegamento all'archiviazione** Aggiungi URL alla risorsa di archiviazione. Usare l'URL SAS BLOB per gli account di archiviazione privati. 
    * Selezionare **Modifica schema**
 
    ![Dettagli origine inserimento un clic](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. Nella scheda **schema** selezionare **formato dati** dall'elenco a discesa > **JSON** o **CSV**. 
   
   Se selezionare **CSV**:
    * Selezionare la casella di controllo **Ignora titolo** per ignorare la riga di intestazione del file CSV.    
    * Il **nome del mapping** viene impostato automaticamente, ma può essere modificato.

    ![schema in formato CSV di inserimento con un clic. png](media/ingest-data-one-click/one-click-csv-format.png)

   Se si seleziona **JSON**:
    * Selezionare i **livelli JSON**: 1-10 dall'elenco a discesa. I livelli nel file JSON vengono visualizzati nella tabella in basso a destra. 
    * Il **nome del mapping** viene impostato automaticamente, ma può essere modificato.

    ![schema del formato JSON di inserimento con un clic](media/ingest-data-one-click/one-click-json-format.png)  

1. Nell' **Editor**selezionare **V** a destra per aprire l'editor. Nell'editor è possibile visualizzare e copiare le query automatiche generate dagli input. 

1.  Nella tabella in basso a destra: 
    * Selezionare **V** a destra della colonna per **rinominare la colonna**, **eliminare colonne**, **ordinamento crescente**o **ordinamento decrescente**
    * Fare doppio clic sul nome della colonna da modificare.
    * Per modificare il tipo di dati, selezionare l'icona a sinistra del nome della colonna. 

1. Selezionare **Avvia** inserimento per creare la tabella, creare il mapping e inserire i dati.
 
## <a name="query-data"></a>Eseguire query sui dati

1. Nella finestra inserimento **dati completata** , tutti e tre i passaggi verranno contrassegnati con segni di spunta verdi, se l'inserimento dei dati è stato completato correttamente. 
 
    ![inserimento dati con un solo clic completato](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Selezionare **V** per aprire la query. Copiare nell'interfaccia utente Web per modificare la query.

1. Il menu a destra contiene **query** e **strumenti**rapidi. 

    * Le **query rapide** includono collegamenti all'interfaccia utente Web con query di esempio.
    * **Gli strumenti** includono il collegamento all'interfaccia utente Web con i **comandi drop** che consentono di risolvere i problemi `.drop` eseguendo il comando pertinente.

    > [!TIP]
    > I dati possono andare persi `.drop` usando i comandi. Usarle attentamente.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query sui dati in Azure Esplora dati interfaccia utente Web](web-query-data.md)
* [Scrivere query per Esplora dati di Azure usando il linguaggio di query kusto](write-queries.md)
