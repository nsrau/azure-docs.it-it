---
title: Esercitazione su Power BI per il connettore Azure Cosmos DB
description: Usare questa esercitazione su Power BI per importare JSON, creare report dettagliati e visualizzare i dati con Azure Cosmos DB e il connettore per Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 76531de279dfe6e9b73b3895f0ef63c4c88b63cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65979001"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizzare i dati di Azure Cosmos DB con il connettore Power BI

[Power BI](https://powerbi.microsoft.com/) è un servizio online in cui è possibile creare e condividere dashboard e report. Power BI Desktop è uno strumento di creazione di report che consente di recuperare dati da diverse origini dati. Azure Cosmos DB è una delle origini dati che è possibile usare con Power BI Desktop. È possibile connettere Power BI Desktop all'account Azure Cosmos DB con il connettore Azure Cosmos DB per Power BI.  Dopo aver importato i dati di Azure Cosmos DB in Power BI, è possibile trasformarli, creare report e pubblicare i report in Power BI.   

Questo articolo descrive i passaggi necessari per connettere l'account Azure Cosmos DB a Power BI Desktop. Dopo aver eseguito la connessione, passare a una raccolta, estrarre i dati, trasformare i dati JSON in un formato tabulare e pubblicare un report in Power BI.

> [!NOTE]
> Il connettore Power BI per Azure Cosmos DB si connette a Power BI Desktop. I report creati in Power BI Desktop possono essere pubblicati in PowerBI.com. Non è possibile estrarre direttamente da PowerBI.com dati di Azure Cosmos DB. 

> [!NOTE]
> La connessione ad Azure Cosmos DB con il connettore Power BI è attualmente supportata solo per gli account dell'API SQL di Azure Cosmos DB e dell'API Gremlin.

## <a name="prerequisites"></a>Prerequisiti
Prima di seguire le istruzioni di questa esercitazione su Power BI, verificare di avere accesso alle risorse seguenti:

* [Scaricare la versione più recente di Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Scaricare i [dati di esempio sui vulcani](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) da GitHub.

* [Creare un account di database di Azure Cosmos DB](https://azure.microsoft.com/documentation/articles/create-account/) e importare i dati sui vulcani usando l'[Utilità di migrazione dati di Azure Cosmos DB](import-data.md). Quando si importano i dati, tenere conto delle impostazioni seguenti per l'origine e le destinazioni nell'Utilità di migrazione dati:

   * **Parametri relativi all'origine** 

       * **Importa da:** File JSON

   * **Parametri relativi alla destinazione** 

      * **Stringa di connessione:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Chiave di partizione:** /Country 

      * **Velocità effettiva di raccolta:** 1000 

Per condividere i report in PowerBI.com, è necessario avere un account in PowerBI.com.  Per altre informazioni su Power BI e su Power BI Pro, visitare il sito [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Attività iniziali
In questa esercitazione si immagini di essere un geologo che studia i vulcani di tutto il mondo. I dati sui vulcani sono archiviati in un account Cosmos DB e i documenti JSON hanno il formato seguente:

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

I dati sui vulcani verranno recuperati dall'account di Azure Cosmos DB e i dati verranno visualizzati in un report di Power BI interattivo.

1. Eseguire Power BI Desktop.

2. Le opzioni **Recupera dati**, **Origini recenti** e **Apri altri report** sono disponibili direttamente dalla schermata iniziale. Fare clic sulla X nell'angolo in alto a destra per chiudere la schermata. Apparirà la visualizzazione **Report** di Power BI Desktop.
   
   ![Visualizzazione report di Power BI Desktop - Connettore Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Selezionare **Home** sulla barra multifunzione, quindi fare clic su **Recupera dati**.  Verrà visualizzata la finestra **Recupera dati** .

4. Fare clic su **Azure**, selezionare **Azure Cosmos DB (Beta)** e quindi fare clic su **Connetti**. 

    ![Recupero di dati in Power BI Desktop - Connettore Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Nella pagina **Anteprima connettore** fare clic su **Continua**. Viene visualizzata la finestra **Azure Cosmos DB**.

6. Specificare l'URL dell'endpoint dell'account Azure Cosmos DB da cui recuperare i dati, come illustrato di seguito, e quindi fare clic su **OK**. Per usare il proprio account, è possibile recuperare l'URL dalla casella URI nel pannello **Chiavi** del portale di Azure. Facoltativamente è possibile specificare il nome del database, il nome della raccolta oppure selezionare il database e la raccolta per identificare la provenienza dei dati.
   
7. Se ci si connette a questo endpoint per la prima volta, viene richiesta la chiave dell'account. Per il proprio account, recuperare la chiave dalla casella **Chiave primaria** nel pannello **Chiavi di sola lettura** del portale di Azure. Immettere la chiave appropriata e quindi fare clic su **Connetti**.
   
   È consigliabile usare la chiave di sola lettura durante la creazione di report. In questo modo si impedisce un'inutile esposizione della chiave master a potenziali rischi di sicurezza. La chiave di sola lettura è disponibile nel pannello **Chiavi** del portale di Azure. 
    
8. Una volta che l'account è connesso, viene visualizzato il riquadro **Strumento di navigazione**. Lo **Strumento di navigazione** mostra un elenco dei database che fanno parte dell'account.

9. Fare clic per espandere il database di origine dei dati per il report e selezionare **volcanodb** (il nome del database potrebbe essere diverso).   

10. A questo punto, selezionare una raccolta che contiene i dati da recuperare, selezionare **volcano1** (il nome della raccolta potrebbe essere diverso).
    
    Il riquadro di anteprima mostra un elenco di elementi **Record** .  In Power BI un documento viene rappresentato come tipo di **Record** . In modo analogo, anche un blocco JSON annidato in un documento è un **Record**.
    
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: finestra di spostamento](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Fare clic su **Modifica** per avviare l'Editor di query in una nuova finestra per trasformare i dati.

## <a name="flattening-and-transforming-json-documents"></a>Rendere flat e trasformare i documenti JSON
1. Passare alla finestra Editor di Query di Power BI, dove viene visualizzata la colonna **Documento** nel riquadro centrale.
   ![Editor di Query di Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Fare clic sul pulsante di espansione a destra dell'intestazione della colonna **Documento** .  Verrà visualizzato il menu di scelta rapida con un elenco di campi.  Selezionare i campi necessari per il report, ad esempio, nome del vulcano, paese, area geografica, località, altezza, tipo, stato e ultima eruzione nota. Deselezionare la casella di controllo **Usa il nome della colonna originale come prefisso** e quindi fare clic su **OK**.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: espandere i documenti](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Nel riquadro centrale viene visualizzata un'anteprima del risultato con i campi selezionati.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: appiattire i risultati](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Nell'esempio la proprietà relativa alla località è un blocco GeoJSON in un documento.  Come si può vedere, la località è rappresentata come tipo di **Record** in Power BI Desktop.  
5. Fare clic sul pulsante di espansione a destra dell'intestazione della colonna Document.Location.  Viene visualizzato il menu di scelta rapida con i campi relativi a tipo e coordinate.  A questo punto selezionare il campo delle coordinate, assicurarsi che la casella di controllo **Usa il nome della colonna originale come prefisso** non sia selezionata e fare clic su **OK**.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: record della posizione](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Il riquadro centrale mostra una colonna di coordinate di tipo **Elenco** .  Come illustrato all'inizio dell'esercitazione, i dati GeoJSON in questa esercitazione sono di tipo Punto con i valori di latitudine e longitudine registrati nella matrice di coordinate.
   
    L'elemento coordinate [0] rappresenta la longitudine, mentre coordinate [1] rappresenta la latitudine.
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: elenco di coordinate](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Per rendere flat la matrice di coordinate, creare una **Colonna personalizzata** denominata LatLong.  Selezionare **Aggiungi colonna** sulla barra multifunzione e fare clic su **Colonna personalizzata**.  Verrà visualizzata la finestra **Colonna personalizzata**.
8. Specificare un nome per la nuova colonna, ad esempio LatLong.
9. Specificare quindi la formula personalizzata per la nuova colonna.  Per questo esempio, verranno concatenati i valori di latitudine e longitudine separati da una virgola, come illustrato di seguito, usando la formula seguente: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Fare clic su **OK**.
   
    Per altre informazioni su Data Analysis Expressions (DAX) incluse le funzioni DAX, visitare [nozioni di DAX in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: Aggiungi colonna personalizzata](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. A questo punto, nel riquadro centrale vengono visualizzate le nuove colonne LatLong popolate con i valori.
    
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: colonna LatLong personalizzata](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se viene restituito un errore nella nuova colonna, assicurarsi che i passaggi applicati in Impostazioni query corrispondano alla figura seguente:
    
    ![I passaggi applicati devono essere Origine, Navigazione, Documento espanso, Documento posizione espanso, Aggiunto personalizzato](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se i passaggi sono diversi, eliminare i passaggi aggiuntivi e tentare di aggiungere nuovamente la colonna personalizzata. 

11. Fare clic su **Applica e chiudi** per salvare il modello di dati.
    
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: Chiudi e applica](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Compilare i report
È possibile iniziare a creare i rapporti nella visualizzazione Report di Power BI Desktop per visualizzare dati.  È possibile creare report trascinando i campi nell'area di disegno **Report** .

![Visualizzazione report di Power BI Desktop - Connettore Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

Nella visualizzazione Report dovrebbero essere presenti:

1. Il riquadro **Campi** dove viene visualizzato un elenco di modelli di dati con i campi da usare per i report.
2. Il riquadro **Visualizzazioni** . Un report può contenere una o più visualizzazioni.  Selezionare i tipi di elemento visivo secondo le proprie esigenze nel riquadro **Visualizzazioni** .
3. L'area di disegno **Report** che consente di creare gli elementi visivi per il report.
4. La pagina **Report** . È possibile aggiungere più pagine del report in Power BI Desktop.

Di seguito sono illustrati i passaggi di base per la creazione di un semplice report Vista mappa interattivo.

1. In questo esempio si creerà una vista mappa che mostra la località di ogni vulcano.  Nel riquadro **Visualizzazioni** fare clic sul tipo Vista mappa come illustrato nella schermata precedente.  Verrà visualizzato il tipo Vista mappa nell'area di disegno **Report** .  Nel riquadro **Visualizzazioni** dovrebbe essere presente anche un set di proprietà correlate al tipo Vista mappa.
2. Ora trascinare il campo LatLong dal riquadro **Campi** sulla proprietà **Località** nel riquadro **Visualizzazioni**.
3. Trascinare quindi il campo con il nome vulcano sulla proprietà **Legenda** .  
4. Trascinare quindi il campo relativo all'altezza sulla proprietà **Dimensioni** .  
5. Si noterà ora che nella Vista mappa è presente un set di bolle che indica la località in cui si trova ogni vulcano, dove le dimensioni della bolla sono correlate all'altezza del vulcano.
6. È stato così creato un report di base.  È possibile continuare a personalizzare il report aggiungendo più visualizzazioni.  In questo caso, è stato aggiunto un filtro dei dati al tipo di vulcano per rendere il report interattivo.  
   
7. Dal menu File scegliere **Salva** e salvare il file come PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Pubblicare e condividere il report
Per condividere il report, è necessario avere un account in PowerBI.com.

1. In Power BI Desktop fare clic su **Home** sulla barra multifunzione.
2. Fare clic su **Pubblica**.  Viene richiesto di immettere il nome utente e la password per l'account PowerBI.com.
3. Dopo l'autenticazione delle credenziali, il report viene pubblicato nella destinazione selezionata.
4. Fare clic su **Apri 'PowerBITutorial.pbix' in Power BI** per visualizzare e condividere il report in PowerBI.com.
   
    ![Pubblicazione su Power BI riuscita Aprire Esercitazione in Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Creare un dashboard in Power BI
Ora che si dispone di un report, è possibile condividerlo in PowerBI.com

Quando si pubblica il report da Power BI Desktop a PowerBI.com, nel tenant di PowerBI.com. vengono generati un **report** e un **set di dati**. Ad esempio, dopo aver pubblicato un report denominato **PowerBITutorial** su PowerBI.com, PowerBITutorial verrà visualizzato in entrambe le sezioni **Report** e **Set di dati** in PowerBI.com.

   ![Schermata del report e del set di dati creati in PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Per creare un dashboard condivisibile, fare clic sul pulsante **Aggiungi pagina dinamica** nel report di PowerBI.com.

   ![Schermata del report e del set di dati creati in PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Dopodiché, seguire le istruzioni fornite in [Aggiungere un riquadro da un report](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) per creare un nuovo dashboard. 

È inoltre possibile eseguire modifiche ad hoc al report prima di creare un dashboard. Tuttavia, si consiglia l'uso di Power BI Desktop per apportare le modifiche e ripubblicare il report su PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su Power BI, vedere [Introduzione a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Per altre informazioni su Azure Cosmos DB, vedere la [pagina iniziale della documentazione di Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

