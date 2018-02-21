---
title: Esercitazione su Power BI per il connettore Azure Cosmos DB | Microsoft Docs
description: Usare questa esercitazione su Power BI per importare JSON, creare report dettagliati e visualizzare i dati con Azure Cosmos DB e il connettore per Power BI.
keywords: esercitazione Power BI, visualizzare dati, connettore Power BI
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: mimig
ms.openlocfilehash: 6414cdc942c43f6eb13ca8f050d6503bdd3e0b42
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Esercitazione su Power BI per Azure Cosmos DB: visualizzare dati tramite il connettore per Power BI
[PowerBI.com](https://powerbi.microsoft.com/) è un servizio online dove è possibile creare e condividere dashboard e report con dati importanti per gli utenti e l'organizzazione.  Power BI Desktop è uno strumento per la creazione di report dedicato che consente di recuperare dati da diverse origini dati, unire e trasformare i dati, creare visualizzazioni e report potenti e pubblicare i report in Power BI.  Con la versione più recente di Power BI Desktop è ora possibile connettersi al proprio account Cosmos DB tramite il connettore Cosmos DB per Power BI.   

Questa esercitazione su Power BI illustra in dettaglio i passaggi per connettersi a un account Cosmos DB in Power BI Desktop, passare a una raccolta da cui estrarre i dati con lo strumento di navigazione, trasformare i dati JSON in formato tabulare tramite l'Editor di query di Power BI Desktop e creare e pubblicare un report in PowerBI.com.

Dopo aver completato questa esercitazione su Power BI, si potrà rispondere alle domande seguenti:  

* Come è possibile creare report con i dati di Cosmos DB tramite Power BI Desktop?
* Come è possibile connettersi a un account Cosmos DB in Power BI Desktop?
* Come è possibile recuperare dati da una raccolta di Power BI Desktop?
* Come è possibile trasformare dati JSON annidati in Power BI Desktop?
* Come è possibile pubblicare e condividere i report personalizzati in PowerBI.com?

> [!NOTE]
> Il connettore Power BI per Azure Cosmos DB si connette a Power BI Desktop per estrarre e trasformare i dati. I report creati in Power BI Desktop possono essere quindi pubblicati in PowerBI.com. Non è possibile estrarre e trasformare direttamente in PowerBI.com dati di Azure Cosmos DB. 

> [!NOTE]
> Per connettere Azure Cosmos DB a Power BI usando l'API di MongoDB, è necessario usare il [driver ODBC per MongoDB di Simba](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>prerequisiti
Prima di seguire le istruzioni di questa esercitazione su Power BI, verificare di avere accesso alle risorse seguenti:

* [La versione più recente di Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Accesso all'account demo o ai dati nell'account Cosmos DB.
  * L'account demo viene popolato con i dati sui vulcani illustrati in questa esercitazione. Questo account demo non è associato ad alcun contratto di servizio e viene usato esclusivamente a scopo dimostrativo.  Microsoft si riserva il diritto di apportare modifiche a questo account demo incluse, a titolo esemplificativo, la chiusura dell'account, la modifica della chiave, la limitazione dell'accesso, la modifica e l'eliminazione dei dati, in qualsiasi momento senza preavviso o motivo.
    * URL: https://analytics.documents.azure.com
    * Chiave di sola lettura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * In alternativa, per creare un account personalizzato, vedere [Creare un account database Cosmos DB usando il portale di Azure](https://azure.microsoft.com/documentation/articles/create-account/). Per ottenere dati di esempio sui vulcani simili a quelli usati in questa esercitazione, che però non contengono i blocchi GeoJSON, vedere il [sito NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) e quindi importare i dati con lo [strumento di migrazione dati di Azure Cosmos DB](import-data.md).

Per condividere i report in PowerBI.com, è necessario avere un account in PowerBI.com.  Per altre informazioni su Power BI in versione gratuita o Power BI Pro, visitare il sito [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Attività iniziali
In questa esercitazione si immagini di essere un geologo che studia i vulcani di tutto il mondo.  I dati sui vulcani sono archiviati in un account Cosmos DB e i documenti JSON hanno un aspetto simile al documento di esempio seguente.

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

Si vogliono recuperare i dati sui vulcani dall'account Cosmos DB e visualizzare dati in un report di Power BI interattivo, come il report seguente.

![Dopo aver completato questa esercitazione Power BI con il connettore Power BI, sarà possibile visualizzare i dati con il report volcano Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Ecco come fare una prova. Di seguito sono riportati i requisiti iniziali.

1. Eseguire Power BI Desktop sulla workstation.
2. Dopo l'avvio di Power BI Desktop viene visualizzata una schermata *iniziale* .
   
    ![Schermata iniziale di Power BI Desktop - Connettore Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Le opzioni **Recupera dati**, **Origini recenti** e **Apri altri report** sono disponibili direttamente dalla schermata *iniziale*.  Fare clic sulla X nell'angolo in alto a destra per chiudere la schermata. Apparirà la visualizzazione **Report** di Power BI Desktop.
   
    ![Visualizzazione report di Power BI Desktop - Connettore Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Selezionare **Home** sulla barra multifunzione, quindi fare clic su **Recupera dati**.  Verrà visualizzata la finestra **Recupera dati** .
5. Fare clic su **Azure**, selezionare **Microsoft Azure DocumentDB (Beta)**, quindi fare clic su **Connetti**. 

    ![Recupero di dati in Power BI Desktop - Connettore Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Nella pagina **Anteprima connettore** fare clic su **Continua**. Viene visualizzata la finestra **Connetti di Microsoft Azure DocumentDB**.
7. Specificare l'URL dell'endpoint dell'account Cosmos DB da cui recuperare i dati, come illustrato di seguito, quindi fare clic su **OK**. Per usare il proprio account, è possibile recuperare l'URL dalla casella URI del pannello **[Chiavi](manage-account.md#keys)** del portale di Azure. Per usare l'account demo, immettere `https://analytics.documents.azure.com` come URL. 
   
    Lasciare vuoti i campi Nome database, Nome raccolta e iItruzione SQL poiché si tratta di campi facoltativi.  Si userà invece lo strumento di navigazione per selezionare il database e la raccolta per identificare la provenienza dei dati.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: finestra di connessione desktop](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Se ci si connette a questo endpoint per la prima volta, viene richiesta la chiave dell'account. Per il proprio account, recuperare la chiave dalla casella **Chiave primaria** nel pannello **[Chiavi di sola lettura](manage-account.md#keys)** del portale di Azure. Per l'account demo, la chiave è `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Immettere la chiave appropriata e quindi fare clic su **Connetti**.
   
    È consigliabile usare la chiave di sola lettura durante la creazione di report.  In questo modo si impedirà un'inutile esposizione della chiave master a potenziali rischi di sicurezza. La chiave di sola lettura è disponibile nel pannello [Chiavi](manage-account.md#keys) del portale di Azure oppure si possono usare le informazioni dell'account demo fornite in precedenza.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: chiave dell'account](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Se viene visualizzato l'errore "Il database specificato non è stato trovato", vedere i passaggi della soluzione alternativa in questo [problema di Power BI](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Una volta che l'account è connesso, viene visualizzato lo **Strumento di navigazione** .  Lo **Strumento di navigazione** visualizza un elenco dei database che fanno parte dell'account.
10. Fare clic per espandere il database di origine dei dati per il report e se si usa l'account demo, selezionare **volcanodb**.   
11. A questo punto, selezionare una raccolta dalla quale si recupereranno i dati. Se si usa l'account demo, selezionare **volcano1**.
    
    Il riquadro di anteprima mostra un elenco di elementi **Record** .  In Power BI un documento viene rappresentato come tipo di **Record** . In modo analogo, anche un blocco JSON annidato in un documento è un **Record**.
    
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: finestra di spostamento](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Fare clic su **Modifica** per avviare l'Editor di query in una nuova finestra per trasformare i dati.

## <a name="flattening-and-transforming-json-documents"></a>Rendere flat e trasformare i documenti JSON
1. Passare alla finestra Editor di Query di Power BI, dove viene visualizzata la colonna **Documento** nel riquadro centrale.
   ![Editor di Query di Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Fare clic sul pulsante di espansione a destra dell'intestazione della colonna **Documento** .  Verrà visualizzato il menu di scelta rapida con un elenco di campi.  Selezionare i campi necessari per il report, ad esempio, nome del vulcano, paese, area geografica, località, altezza, tipo, stato e ultima eruzione nota, quindi fare clic su **OK**.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: espandere i documenti](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Nel riquadro centrale verrà visualizzata un'anteprima del risultato con i campi selezionati.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: appiattire i risultati](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. Nell'esempio la proprietà relativa alla località è un blocco GeoJSON in un documento.  Come si può vedere, la località è rappresentata come tipo di **Record** in Power BI Desktop.  
5. Fare clic sul pulsante di espansione a destra dell'intestazione della colonna relativa alla località.  Verrà visualizzato il menu di scelta rapida con i campi relativi a tipo e coordinate.  Selezionare il campo delle coordinate e fare clic su **OK**.
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: record della posizione](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Il riquadro centrale mostra una colonna di coordinate di tipo **Elenco** .  Come illustrato all'inizio dell'esercitazione, i dati GeoJSON in questa esercitazione sono di tipo Punto con i valori di latitudine e longitudine registrati nella matrice di coordinate.
   
    L'elemento coordinate [0] rappresenta la longitudine, mentre coordinate [1] rappresenta la latitudine.
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: elenco di coordinate](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Per rendere flat la matrice di coordinate, si creerà una **Colonna personalizzato** denominata LatLong.  Selezionare **Aggiungi colonna** sulla barra multifunzione e fare clic su **Aggiungi colonna personalizzata**.  Verrà visualizzata la finestra **Aggiungi colonna personalizzata** .
8. Specificare un nome per la nuova colonna, ad esempio LatLong.
9. Specificare quindi la formula personalizzata per la nuova colonna.  Per questo esempio, verranno concatenati i valori di latitudine e longitudine separati da una virgola, come illustrato di seguito, usando la formula seguente: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Fare clic su **OK**.
   
    Per altre informazioni su Data Analysis Expressions (DAX), incluse le funzioni DAX, vedere [Nozioni di base su DAX in Power BI Designer](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: Aggiungi colonna personalizzata](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. Il riquadro centrale mostrerà la nuova colonna LatLong popolata con i valori di latitudine e longitudine separati da una virgola.
    
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: colonna LatLong personalizzata](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Se viene restituito un errore nella nuova colonna, assicurarsi che i passaggi applicati in Impostazioni query corrispondano alla figura seguente:
    
    ![I passaggi applicati devono essere Origine, Navigazione, Documento espanso, Documento posizione espanso, Aggiunto personalizzato](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Se i passaggi sono diversi, eliminare i passaggi aggiuntivi e tentare di aggiungere nuovamente la colonna personalizzata. 
11. L'operazione per rendere flat i dati convertendoli in formato tabulare è stata completata.  È possibile sfruttare tutte le funzionalità disponibili nell'Editor di query per definire la forma e trasformare i dati in Cosmos DB.  Se si usa il campione, modificare il tipo di dati per il parametro Altezza su **Numero intero**, modificando il parametro **Tipo di dati** nella barra multifunzione **Home**.
    
    ![Esercitazione su Power BI per il connettore Azure Cosmos DB per Power BI: Cambia tipo di colonna](./media/powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Fare clic su **Applica e chiudi** per salvare il modello di dati.
    
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
   
    ![Schermata del report finale di Power BI Desktop al termine dell'esercitazione su Power BI per Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Pubblicare e condividere il report
Per condividere il report, è necessario avere un account in PowerBI.com.

1. In Power BI Desktop fare clic su **Home** sulla barra multifunzione.
2. Fare clic su **Pubblica**.  Verrà richiesto di immettere il nome utente e la password per l'account PowerBI.com.
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

## <a name="refresh-data-in-powerbicom"></a>Aggiornare i dati in PowerBI.com
Esistono due modi per aggiornare i dati: procedura ad hoc e pianificata.

Per un aggiornamento ad hoc, è sufficiente fare clic su (...) accanto al **set di dati**, ad esempio PowerBITutorial. A questo punto viene visualizzato un elenco di azioni, tra cui **Aggiorna adesso**. Fare clic su **Aggiorna adesso** per aggiornare i dati.

![Schermata di Aggiorna adesso in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

Per un aggiornamento pianificato, attenersi alla seguente procedura.

1. Fare clic su **Pianifica aggiornamento** nell'elenco delle azioni. 

    ![Screenshot di Aggiorna pianificazione in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. Nella pagina **Impostazioni** espandere **Credenziali origine dati**. 
3. Fare clic su **Modifica credenziali**. 
   
    Si apre la finestra popup Configura. 
4. Immettere la chiave per connettersi all'account Cosmos DB per il set di dati e quindi fare clic su **Accedi**. 
5. Espandere **Pianifica aggiornamento** e impostare la pianificazione desiderata per aggiornare il set di dati. 
6. Fare clic su **Applica** per completare la configurazione dell'aggiornamento pianificato.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su Power BI, vedere [Introduzione a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Per altre informazioni su Cosmos DB, vedere la [pagina iniziale della documentazione di Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

