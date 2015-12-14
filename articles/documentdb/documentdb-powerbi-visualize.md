<properties 
	pageTitle="Esercitazione su Power BI per il connettore DocumentDB | Microsoft Azure" 
	description="Utilizzare questa esercitazione su Power BI per importare JSON, creare rapporti dettagliati e visualizzare i dati usando il connettore di DocumentDB e Power BI." 
	keywords="esercitazione Power BI, visualizzare dati, connettore Power BI"
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="hawong"/>

# Esercitazione su Power BI per DocumentDB: Visualizzare dati tramite il connettore Power BI
[PowerBI.com](https://powerbi.microsoft.com/) è un servizio online dove è possibile creare e condividere dashboard e report con dati importanti per gli utenti e l'organizzazione. Power BI Desktop è uno strumento per la creazione di report dedicato che consente di recuperare dati da diverse origini dati, unire e trasformare i dati, creare visualizzazioni e report potenti e pubblicare i report in Power BI. Con la versione più recente di Power BI Desktop è ora possibile connettersi al proprio account DocumentDB tramite il connettore DocumentDB per Power BI.

Questa esercitazione su Power BI illustra in dettaglio i passaggi per connettersi a un account DocumentDB di Power BI Desktop, passare a una raccolta da cui estrarre i dati con lo strumento di navigazione, trasformare i dati JSON in formato tabulare tramite l'Editor di query di Power BI Desktop e creare e pubblicare un rapporto in PowerBI.com.

Dopo aver completato questa esercitazione su Power BI, si potrà rispondere alle domande seguenti:

-	Come è possibile creare report con i dati di DocumentDB tramite Power BI Desktop? 
-	Come è possibile connettersi a un account DocumentDB in Power BI Desktop?
-	Come è possibile recuperare dati da una raccolta di Power BI Desktop?
-	Come è possibile trasformare dati JSON annidati in Power BI Desktop?
-	Come è possibile pubblicare e condividere i report personalizzati in PowerBI.com? 

## Prerequisiti

Prima di seguire le istruzioni di questa esercitazione su Power BI, verificare che siano disponibili gli elementi seguenti:

- [La versione più recente di Power BI Desktop](https://powerbi.microsoft.com/desktop).
- Accesso all'account demo o ai dati nell'account Azure DocumentDB. 
	- L'account demo viene popolato con i dati sui vulcani illustrati in questa esercitazione. Questo account demo non è associato ad alcun contratto di servizio e viene usato esclusivamente a scopo dimostrativo. Microsoft si riserva il diritto di apportare modifiche a questo account demo incluse, a titolo esemplificativo, la chiusura dell'account, la modifica della chiave, la limitazione dell'accesso, la modifica e l'eliminazione dei dati, in qualsiasi momento senza preavviso o motivo. 
		- URL: https://analytics.documents.azure.com
		- Chiave di sola lettura: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
	- In alternativa, per creare un account personalizzato, vedere [Creare un account del database di DocumentDB mediante il portale di Azure](https://azure.microsoft.com/documentation/articles/documentdb-create-account/). Per ottenere quindi dati di esempio sui vulcani simili a quelli usati in questa esercitazione, che però non contengono i blocchi GeoJSON, vedere il [sito NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) e quindi importare i dati con lo [strumento di migrazione dati di DocumentDB](https://azure.microsoft.com/documentation/articles/documentdb-import-data/).


Per condividere i report in PowerBI.com, è necessario avere un account in PowerBI.com. Per altre informazioni su Power BI in versione gratuita o Power BI Pro, visitare il sito [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## Attività iniziali
In questa esercitazione si immagini di essere un geologo che studia i vulcani di tutto il mondo. I dati sui vulcani sono archiviati in un account DocumentDB e i documenti JSON sono simili al seguente.

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

Si vogliono recuperare i dati sui vulcani dall'account DocumentDB e visualizzare dati in un rapporto di Power BI interattivo, come quello riportato di seguito.

![Rapporto volcano di Power BI Desktop per visualizzare dati](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

Ecco come fare una prova. Di seguito sono riportati i requisiti iniziali.


1. Eseguire Power BI Desktop sulla workstation.
2. Dopo l'avvio di Power BI Desktop viene visualizzata una schermata *iniziale*.

	![Schermata iniziale di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)

3. Le opzioni **Recupera dati**, **Origini recenti** e **Apri altri report** sono disponibili direttamente dalla schermata *iniziale*. Fare clic sulla X nell'angolo in alto a destra per chiudere la schermata. Apparirà la visualizzazione **Report** di Power BI Desktop.

	![Visualizzazione Report di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)

4. Selezionare **Home** sulla barra multifunzione, quindi fare clic su **Recupera dati**. Verrà visualizzata la finestra **Recupera dati**.

5. Fare clic su **Azure**, selezionare **Microsoft Azure DocumentDB (Beta)**, quindi fare clic su **Connetti**. Verrà visualizzata la finestra **Connetti di Microsoft Azure DocumentDB**.

	![Recupera dati di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)

6. Specificare l'URL dell'endpoint dell'account DocumentDB da cui recuperare i dati, come illustrato di seguito, quindi fare clic su **OK**. È possibile recuperare l'URL dalla casella URI del pannello **Chiavi** del portale di Azure oppure si possono usare le informazioni dell'account demo fornite in precedenza. Per altre informazioni, vedere [Chiavi](documentdb-manage-account.md#keys).


	*Nota. In questa esercitazione non vengono specificati il nome del database, il nome della raccolta o un'istruzione SQL, perché questi campi sono facoltativi. Si userà invece lo strumento di navigazione per selezionare il database e la raccolta per identificare la provenienza dei dati.*

    ![Finestra Connetti di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)

7. Se ci si connette a questo endpoint per la prima volta, verrà richiesta la chiave dell'account. Immettere la chiave dell'account e fare clic su **Connetti**.
	
	*Nota. È consigliabile usare la chiave di sola lettura durante la creazione di report. In questo modo si impedirà un'inutile esposizione della chiave master a potenziali rischi di sicurezza. La chiave di sola lettura è disponibile nel pannello Chiavi di sola lettura del portale di Azure oppure si possono usare le informazioni dell'account demo fornite in precedenza*.

    ![Chiave account di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)

8. Una volta che l'account è connesso, viene visualizzato lo **Strumento di navigazione**. Lo **Strumento di navigazione** visualizza un elenco dei database che fanno parte dell'account.
9. Fare clic per espandere il database di origine dei dati per il report. Verrà visualizzato un elenco di raccolte incluse nel database.  

10. A questo punto, selezionare una raccolta dalla quale si recupereranno i dati, ad esempio vulcano1.

	*Nota. Il riquadro di anteprima mostra un elenco di elementi **Record**. In Power BI un documento viene rappresentato come tipo di **Record**. In modo analogo, anche un blocco JSON annidato in un documento è un **Record**.*

    ![Strumento di navigazione di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)

11. Fare clic su **Modifica** per avviare l'Editor di query che consente di trasformare i dati.

## Rendere flat e trasformare i documenti JSON
1. Nel riquadro centrale dell'Editor di Query di Power BI verrà visualizzata una colonna **Documento**. ![Editor di Query di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)

2. Fare clic sul pulsante di espansione a destra dell'intestazione della colonna **Documento**. Verrà visualizzato il menu di scelta rapida con un elenco di campi. Selezionare i campi necessari per il report, ad esempio, nome del vulcano, paese, area geografica, località, altezza, tipo, stato e ultima eruzione nota, quindi fare clic su **OK**.
    
	![Espansione di documenti di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)

3. Nel riquadro centrale verrà visualizzata un'anteprima del risultato con i campi selezionati.

	![Risultati in formato flat di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)

4. Nell'esempio la proprietà relativa alla località è un blocco GeoJSON in un documento. Come si può vedere, la località è rappresentata come tipo di **Record** in Power BI Desktop.
5. Fare clic sul pulsante di espansione a destra dell'intestazione della colonna relativa alla località. Verrà visualizzato il menu di scelta rapida con i campi relativi a tipo e coordinate. Selezionare il campo delle coordinate e fare clic su **OK**.

    ![Record sulle località di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)

6. Il riquadro centrale mostra una colonna di coordinate di tipo **Elenco**. Come illustrato all'inizio dell'esercitazione, i dati GeoJSON in questa esercitazione sono di tipo Punto con i valori di latitudine e longitudine registrati nella matrice di coordinate.

	*Nota. L'elemento coordinate [0] rappresenta la longitudine, mentre coordinate [1] rappresenta la latitudine.* ![Elenco di coordinate di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)

7. Per rendere flat la matrice di coordinate, si creerà una **Colonna personalizzato** denominata LatLong. Selezionare **Aggiungi colonna** sulla barra multifunzione e fare clic su **Aggiungi colonna personalizzata**. Verrà visualizzata la finestra **Aggiungi colonna personalizzata**.

8. Specificare un nome per la nuova colonna, ad esempio LatLong.

9. Specificare quindi la formula personalizzata per la nuova colonna. Per questo esempio, verranno concatenati i valori di latitudine e longitudine separati da una virgola, come illustrato di seguito, usando la formula seguente: Text.From([coordinates]{1}) & "," & Text.From([coordinates]{0}). Fare clic su **OK**.
	
    *Nota. Per altre informazioni su Data Analysis Expressions (DAX), incluse le funzioni DAX, vedere [Nozioni di base su DAX in Power BI Designer](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).*

    ![Colonna personalizzata di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Il riquadro centrale mostrerà la nuova colonna LatLong popolata con i valori di latitudine e longitudine separati da una virgola.

	![LatLong personalizzato di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)

11. L'operazione per rendere flat i dati convertendoli in formato tabulare è stata completata. È possibile sfruttare tutte le funzionalità disponibili nell'Editor di Query per definire la forma e trasformare i dati in DocumentDB. Ad esempio, è possibile modificare il tipo di dati per l'altezza in **Numero decimale** modificando il **Tipo di dati** nella scheda **Home** sulla barra multifunzione.

    ![Modifica tipo di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)

12. Fare clic su **Applica e chiudi ** per salvare il modello di dati.
    
    ![Applica e chiudi di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## Compilare i report
È possibile iniziare a creare i rapporti nella visualizzazione Report di Power BI Desktop per visualizzare dati. È possibile creare report trascinando i campi nell'area di disegno **Report**.

![Visualizzazione Report di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)
 
Nella visualizzazione Report dovrebbero essere presenti:

 1. Il riquadro **Campi** dove viene visualizzato un elenco di modelli di dati con i campi da usare per i report.

 2. Il riquadro **Visualizzazioni**. Un report può contenere una o più visualizzazioni. Selezionare i tipi di elemento visivo secondo le proprie esigenze nel riquadro **Visualizzazioni**.

 3. L'area di disegno **Report** che consente di creare gli elementi visivi per il report.

 4. La pagina **Report**. È possibile aggiungere più pagine del report in Power BI Desktop.

Di seguito sono illustrati i passaggi di base per la creazione di un semplice report Vista mappa interattivo.

1. In questo esempio si creerà una vista mappa che mostra la località di ogni vulcano. Nel riquadro **Visualizzazioni** fare clic sul tipo Vista mappa come illustrato nella schermata precedente. Verrà visualizzato il tipo Vista mappa nell'area di disegno **Report**. Nel riquadro**Visualizzazioni** dovrebbe essere presente anche un set di proprietà correlate al tipo Vista mappa.

2. Ora trascinare il campo LatLong dal riquadro **Campi** sulla proprietà **Località** nel riquadro **Visualizzazioni**.
3. Trascinare quindi il campo con il nome vulcano sulla proprietà **Legenda**.  

4. Trascinare quindi il campo relativo all'altezza sulla proprietà **Valori**.

5. Si noterà ora che nella Vista mappa è presente un set di bolle che indica la località in cui si trova ogni vulcano, dove le dimensioni della bolla sono correlate all'altezza del vulcano.

6. È stato così creato un report di base. È possibile continuare a personalizzare il report aggiungendo più visualizzazioni. In questo caso, è stato aggiunto un filtro dei dati al tipo di vulcano per rendere il report interattivo.

    ![Report finale di Power BI Desktop](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## Pubblicare e condividere il report
Per condividere il report, è necessario avere un account in PowerBI.com.

1. In Power BI Desktop fare clic su **Home** sulla barra multifunzione.
2. Fare clic su **Pubblica**. Verrà richiesto di immettere il nome utente e la password per l'account PowerBI.com.
3. Dopo l'autenticazione delle credenziali, il report viene pubblicato nel proprio account PowerBI.com.
4. È quindi possibile condividerlo in PowerBI.com.

## Passaggi successivi
- Per altre informazioni su Power BI, fare clic [qui](https://support.powerbi.com/knowledgebase).
- Per altre informazioni su DocumentDB, vedere [qui](https://azure.microsoft.com/documentation/services/documentdb/).

<!---HONumber=AcomDC_1203_2015-->