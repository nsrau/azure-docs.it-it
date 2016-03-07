<properties
   pageTitle="Uso di DB2 Connector nel servizio app di Microsoft Azure | Microsoft Azure"
   description="Informazioni su come usare DB2 Connector con trigger e azioni delle app per la logica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="plarsen"/>

# DB2
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Microsoft Connector for DB2 è un'app per le API per la connessione di applicazioni tramite il servizio app di Azure alle risorse archiviate in un database IBM DB2. Il connettore include un client Microsoft per la connessione a computer server DB2 attraverso una connessione di rete TCP/IP, incluse connessioni ibride di Azure a server DB2 locali mediante l'inoltro del bus di servizio di Azure. Il connettore supporta le operazioni di database seguenti:

- Lettura di righe mediante SELECT
- Polling per la lettura di righe mediante SELECT COUNT seguita da SELECT
- Aggiunta di una riga o di più righe (in blocco) mediante INSERT
- Modifica di una riga o di più righe (in blocco) mediante UPDATE
- Rimozione di una riga o di più righe (in blocco) mediante DELETE
- Lettura per la modifica di righe mediante SELECT CURSOR seguita da UPDATE WHERE CURRENT OF CURSOR
- Lettura per la rimozione di righe mediante SELECT CURSOR seguita da UPDATE WHERE CURRENT OF CURSOR
- Esecuzione di una procedura con parametri di input e output, valore restituito e set di risultati mediante CALL
- Comandi personalizzati e operazioni composite mediante SELECT, INSERT, UPDATE, DELETE

## Trigger e azioni
Il connettore supporta i trigger e le azioni seguenti dell'app per la logica:

Trigger | Azioni
--- | ---
<ul><li>Polling di dati</li></ul> | <ul><li>Inserimento in blocco</li><li>Inserimento</li><li>Aggiornamento in blocco</li><li>Aggiornamento</li><li>Chiamata</li><li>Eliminazione in blocco</li><li>Eliminazione</li><li>Selezione</li><li>Aggiornamento condizionale</li><li>Post in EntitySet</li><li>Eliminazione condizionale</li><li>Selezione di una singola entità</li><li>Eliminazione</li><li>Upsert in EntitySet</li><li>Comandi personalizzati</li><li>Operazioni composite</li></ul>


## Creare DB2 Connector
È possibile definire un connettore in un'app per la logica da Azure Marketplace, come illustrato nell'esempio seguente:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Nel pannello **Tutto** digitare **db2** nella casella **Cerca tutti gli elementi**, quindi fare clic su INVIO.
3. Nel riquadro dei risultati della ricerca in tutti gli elementi selezionare **DB2 Connector**.
4. Nel pannello di descrizione del connettore DB2 selezionare **Crea**.
5. Nel pannello del pacchetto del connettore DB2 immettere il nome (ad esempio "Db2ConnectorNewOrders"), il piano del servizio app e altre proprietà.
6. Selezionare **Impostazioni pacchetto**, quindi immettere le impostazioni del pacchetto seguenti:  

	Nome | Obbligatorio | Descrizione
--- | --- | ---
ConnectionString | Sì | Stringa di connessione del client DB2 (ad esempio, "Network Address=servername;Network Port=50000;User ID=username;Password=password;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND").
Tables | Sì | Elenco delimitato da virgole di tabelle, visualizzazioni e nomi alias necessari per operazioni OData e per la generazione di documentazione Swagger con esempi (ad esempio "*NEWORDERS*").
Procedures | Sì | Elenco delimitato da virgole di nomi di procedure e funzioni (ad esempio "SPORDERID").
OnPremise | No | Distribuzione locale mediante l'inoltro del bus di servizio di Azure.
ServiceBusConnectionString | No | Stringa di connessione dell'inoltro del bus di servizio di Azure.
PollToCheckData | No | Istruzione SELECT COUNT da usare con un trigger dell'app per la logica (ad esempio "SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL").
PollToReadData | No | Istruzione SELECT da usare con un trigger dell'app per la logica (ad esempio "SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE").
PollToAlterData | No | Istruzione UPDATE o DELETE da usare con un trigger dell'app per la logica (ad esempio "UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;").

7. Selezionare **OK**, quindi **Crea**.
8. Al termine, l'aspetto di Impostazioni pacchetto dovrebbe essere simile al seguente: ![][1]


## App per la logica con azione del connettore DB2 per l'aggiunta di dati ##
È possibile definire un'azione dell'app per la logica per aggiungere dati a una tabella DB2 mediante un'operazione OData di tipo API Insert o Post to Entity. Ad esempio, è possibile inserire un nuovo record di ordine cliente elaborando un'istruzione SQL INSERT rispetto a una tabella definita con una colonna Identity e restituendo il valore Identity delle righe interessate all'app per la logica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP] L'operazione "*Post to EntitySet*" della connessione DB2 restituisce il valore della colonna Identity e l'operazione "*API Insert*" restituisce le righe interessate.

1. Nella schermata iniziale di Azure selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere il nome (ad esempio "NewOrdersDb2"), il piano del servizio app e le altre proprietà, quindi selezionare **Crea**.
3. Nella schermata iniziale di Azure selezionare l'app per la logica appena creata, fare clic su **Impostazioni** e quindi su **Trigger e azioni**.
4. Nel pannello Trigger e azioni selezionare **Crea da zero** nei modelli dell'app per la logica.
5. Nel pannello delle app per le API selezionare **Ricorrenza**, impostare una frequenza e un intervallo, quindi fare clic sul **segno di spunta**.
6. Nel pannello delle app per le API selezionare **DB2 Connector**, quindi espandere l'elenco di operazioni per selezionare **Insert into NEWORDER**.
7. Espandere l'elenco di parametri per immettere i valori seguenti:  

	Nome | Valore
--- | --- 
CUSTID | 10042
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Selezionare il **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**.
9. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][3]

10. Nell'elenco **Tutte le esecuzioni** in **Operazioni** selezionare il primo elemento dell'elenco, ovvero l'esecuzione più recente.
11. Nel pannello relativo alle **esecuzioni delle app per la logica** selezionare l'elemento **ACTION** di tipo **db2connectorneworders**.
12. Nel pannello relativo alle **azioni delle app per la logica** selezionare **INPUTS LINK**. DB2 Connector usa gli input per elaborare un'istruzione INSERT con parametri.
13. Nel pannello relativo alle **azioni delle app per la logica** selezionare **OUTPUTS LINK**. Gli input dovrebbero avere un aspetto simile al seguente: ![][4]

#### Informazioni importanti

- Il connettore tronca i nomi di tabella DB2 quando forma i nomi delle azioni delle app per la logica. Ad esempio, l'operazione **Insert into NEWORDERS** viene troncata in **Insert into NEWORDER**.
- Dopo il salvataggio di **Trigger e azioni** delle app per la logica, l'operazione viene elaborata dall'app per la logica. È possibile che per l'elaborazione dell'operazione da parte dell'app per la logica sia necessario attendere alcuni secondi, ad esempio 3-5 secondi. È facoltativamente possibile fare clic su **Esegui ora** per elaborare l'operazione.
- DB2 Connector definisce membri EntitySet con attributi, indicando anche se il membro corrisponde a una colonna DB2 con colonne predefinite o generate, ad esempio la colonna Identity. L'app per la logica visualizza un asterisco rosso accanto al nome di ID membro EntitySet per indicare le colonne DB2 che richiedono valori. È consigliabile non immettere alcun valore per il membro ORDID, che corrisponde alla colonna Identity DB2. È possibile immettere valori per i membri facoltativi (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), che corrispondono a colonne DB2 con valori predefiniti. 
- DB2 Connector restituisce all'app per la logica la risposta a Post to EntitySet che include i valori per le colonne Identity, derivati da DRDA SQLDARD (SQL Data Area Reply Data) nell'istruzione SQL INSERT preparata. Il server DB2 non restituisce i valori inseriti per le colonne con i valori predefiniti.  


## App per la logica con azione del connettore DB2 per l'aggiunta di dati in blocco ##
È possibile definire un'azione dell'app per la logica per aggiungere dati a una tabella DB2 mediante un'operazione di tipo API Bulk Insert. Ad esempio, è possibile inserire due nuovi record di ordine cliente elaborando un'istruzione SQL INSERT con una matrice di valori di riga rispetto a una tabella definita con una colonna Identity e restituendo le righe interessate all'app per la logica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. Nella schermata iniziale di Azure selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere il nome (ad esempio "NewOrdersBulkDb2"), il piano del servizio app e le altre proprietà, quindi selezionare **Crea**.
3. Nella schermata iniziale di Azure selezionare l'app per la logica appena creata, fare clic su **Impostazioni** e quindi su **Trigger e azioni**.
4. Nel pannello Trigger e azioni selezionare **Crea da zero** nei modelli dell'app per la logica.
5. Nel pannello delle app per le API selezionare **Ricorrenza**, impostare una frequenza e un intervallo, quindi fare clic sul **segno di spunta**.
6. Nel pannello delle app per le API selezionare **DB2 Connector**, quindi espandere l'elenco di operazioni per selezionare **Bulk Insert into NEW**.
7. Immettere il valore **rows** come matrice. Ad esempio, copiare e incollare il codice seguente:

	```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
	```

8. Selezionare il **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][6]

9. Nell'elenco **Tutte le esecuzioni** in **Operazioni** fare clic sul primo elemento dell'elenco, ovvero l'esecuzione più recente.
10. Nel pannello relativo alle **esecuzioni delle app per la logica** fare clic sull'elemento **ACTION**.
11. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **INPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: [][7]
12. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **OUTPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: ![][8]

#### Informazioni importanti

- Il connettore tronca i nomi di tabella DB2 quando forma i nomi delle azioni delle app per la logica. Ad esempio, l'operazione **Bulk Insert into NEWORDERS** viene troncata in **Bulk Insert into NEW**.
- Omettendo le colonne Identity (ad esempio ORDID), le colonne che ammettono valori Null (ad esempio SHIPDATE) e le colonne con valori predefiniti (ad esempio ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), il database DB2 genera i valori.
- Specificando "today" e "tomorrow", DB2 Connector genera le funzioni "CURRENT DATE" e "CURRENT DATE + 1 DAY" (ad esempio REQDATE). 


## App per la logica con trigger di DB2 Connector per la lettura, la modifica o l'eliminazione di dati ##
È possibile definire un trigger dell'app per la logica per eseguire il polling e leggere i dati da una tabella DB2 mediante un'operazione composita di tipo API Poll Data. Ad esempio, è possibile leggere uno o più nuovi record di ordine cliente, restituendo i record all'app per la logica. Le impostazioni del pacchetto/dell'app di connessione DB2 dovrebbero avere un aspetto simile al seguente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <no value specified>


È anche possibile definire un trigger dell'app per la logica per eseguire il polling, leggere e modificare i dati in una tabella DB2 mediante un'operazione composita di tipo API Poll Data. Ad esempio, è possibile leggere uno o più nuovi record di ordine cliente e aggiornare i valori di riga, restituendo i record selezionati (prima dell'aggiornamento) all'app per la logica. Le impostazioni del pacchetto/dell'app di connessione DB2 dovrebbero avere un aspetto simile al seguente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


È anche possibile definire un trigger dell'app per la logica per eseguire il polling, leggere e rimuovere dati da una tabella DB2 mediante un'operazione composita di tipo API Poll Data. Ad esempio, è possibile leggere uno o più nuovi record di ordine cliente ed eliminare le righe, restituendo i record selezionati (prima dell'eliminazione) all'app per la logica. Le impostazioni del pacchetto/dell'app di connessione DB2 dovrebbero avere un aspetto simile al seguente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

In questo esempio l'app per la logica eseguirà il polling, leggerà, aggiornerà e quindi leggerà di nuovo i dati nella tabella DB2.

1. Nella schermata iniziale di Azure selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere il nome (ad esempio "ShipOrdersDb2"), il piano del servizio app e le altre proprietà, quindi selezionare **Crea**.
3. Nella schermata iniziale di Azure selezionare l'app per la logica appena creata, fare clic su **Impostazioni** e quindi su **Trigger e azioni**.
4. Nel pannello Trigger e azioni selezionare **Crea da zero** nei modelli dell'app per la logica.
5. Nel pannello delle app per le API selezionare **DB2 Connector**, impostare una frequenza e un intervallo, quindi fare clic sul **segno di spunta**.
6. Nel pannello delle app per le API selezionare **DB2 Connector**, quindi espandere l'elenco di operazioni per selezionare **Select from NEWORDERS**.
7. Selezionare il **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][10]  
8. Fare clic per chiudere il pannello **Trigger e azioni**, quindi fare clic per chiudere il pannello **Impostazioni**.
9. Nell'elenco **Tutte le esecuzioni** in **Operazioni** fare clic sul primo elemento dell'elenco, ovvero l'esecuzione più recente.
10. Nel pannello relativo alle **esecuzioni delle app per la logica** fare clic sull'elemento **ACTION**.
11. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **OUTPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: ![][11]


## App per la logica con azione del connettore DB2 per la rimozione di dati ##
È possibile definire un'azione dell'app per la logica per rimuovere dati da una tabella DB2 mediante un'operazione OData di tipo API Delete o Post to Entity. Ad esempio, è possibile inserire un nuovo record di ordine cliente elaborando un'istruzione SQL INSERT rispetto a una tabella definita con una colonna Identity e restituendo il valore Identity delle righe interessate all'app per la logica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Creare un'app per la logica usando DB2 Connector per rimuovere i dati ##
È possibile creare una nuova app per la logica in Azure Marketplace, quindi usare DB2 Connector come azione per rimuovere gli ordini cliente. Ad esempio, è possibile usare l'operazione condizionale Delete di DB2 Connector per elaborare un'istruzione SQL DELETE (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. Nel menu dell'hub della schermata **iniziale** di Azure fare clic su **+** (segno più), quindi su **Web e dispositivi mobili** e infine su **App per la logica**. 
2. Nel pannello **Crea app per la logica** digitare un **Nome**, ad esempio **RemoveOrdersDb2**.
3. Selezionare o definire i valori per le altre impostazioni, ad esempio il piano di servizio o il gruppo di risorse.
4. Le impostazioni dovrebbero avere un aspetto simile al seguente. Fare clic su **Crea**: ![][12]  
5. Nel pannello **Impostazioni** fare clic su **Trigger e azioni**.
6. Nell'elenco **Modelli di app per la logica** del pannello **Trigger e azioni** fare clic su **Crea da zero**.
7. Nel riquadro **App per le API** del pannello **Trigger e azioni** fare clic su **Ricorrenza** nel gruppo di risorse.
8. Nell'area di progettazione dell'app per la logica fare clic sull'elemento **Ricorrenza** impostare un valore per **Frequenza** e **Intervallo**, ad esempio **Giorni** e **1**, quindi fare clic sul **segno di spunta** per salvare le impostazioni dell'elemento di ricorrenza.
9. Nel riquadro **App per le API** del pannello **Trigger e azioni** fare clic su **DB2 Connector** nel gruppo di risorse.
10. Nell'area di progettazione dell'app per la logica fare clic sull'attività di **DB2 Connector**, fare clic sui puntini di sospensione (**...**) per espandere l'elenco di operazioni e quindi fare clic su **Conditional delete from N**.
11. Nell'attività di DB2 Connector digitare **ORDID ge 10000** per un'**espressione che identifica un sottoinsieme di voci**.
12. Fare clic sul **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][13]  
13. Fare clic per chiudere il pannello **Trigger e azioni**, quindi fare clic per chiudere il pannello **Impostazioni**.
14. Nell'elenco **Tutte le esecuzioni** in **Operazioni** fare clic sul primo elemento dell'elenco, ovvero l'esecuzione più recente.
15. Nel pannello relativo alle **esecuzioni delle app per la logica** fare clic sull'elemento **ACTION**.
16. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **OUTPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: ![][14]

**Nota:** la finestra di progettazione dell'app per la logica tronca i nomi delle tabelle. Ad esempio, l'operazione **Conditional delete from NEWORDERS** viene troncata in **Conditional delete from N**.


> [AZURE.TIP] Usare le istruzioni SQL seguenti per creare la tabella e le stored procedure di esempio.

È possibile creare la tabella NEWORDERS di esempio usando le istruzioni SQL DDL di DB2 seguenti:
 
 	CREATE TABLE ORDERS (  
 		ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
 		CUSTID INT NOT NULL ,  
 		EMPID INT NOT NULL DEFAULT 10000 ,  
 		ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
 		REQDATE DATE DEFAULT CURRENT DATE ,  
 		SHIPDATE DATE ,  
 		SHIPID INT NOT NULL DEFAULT 10000,  
 		FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
 		SHIPNAME CHAR (40) NOT NULL ,  
 		SHIPADDR CHAR (60) NOT NULL ,  
 		SHIPCITY CHAR (20) NOT NULL ,  
 		SHIPREG CHAR (15) NOT NULL ,  
 		SHIPZIP CHAR (10) NOT NULL ,  
 		SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
 		PRIMARY KEY(ORDID)  
 		)  
 
 	CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



È possibile creare la stored procedure SPOERID di esempio usando l'istruzione DDL di DB2 seguente:
 
 	CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
 		DYNAMIC RESULT SETS 1  
 	P1: BEGIN  
 		DECLARE CURSOR1 CURSOR WITH RETURN FOR  
 			SELECT * FROM NWIND.NEWORDERS  
 				WHERE ORDID = ORDERID;  
 		OPEN CURSOR1;  
 	END P1  
 	') 


## Configurazione ibrida (facoltativo)

> [AZURE.NOTE] Questo passaggio è obbligatorio solo se si usa DB2 Connector installato in locale e protetto da firewall.

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa un server IBM DB2 locale per Windows, è richiesta la Gestione connessione ibrida.

Vedere l'articolo relativo all'[uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!---HONumber=AcomDC_0224_2016-->