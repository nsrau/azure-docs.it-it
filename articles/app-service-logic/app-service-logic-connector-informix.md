<properties
   pageTitle="Uso del connettore Informix nel servizio app di Microsoft Azure | Microsoft Azure"
   description="Informazioni su come usare il connettore Informix con trigger e azioni delle app per la logica"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# Connettore Informix
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Microsoft Connector for Informix è un'app per le API per la connessione di applicazioni tramite il servizio app di Azure alle risorse archiviate in un database IBM Informix. Il connettore include un client Microsoft per la connessione a computer server Informix attraverso una connessione di rete TCP/IP, incluse connessioni ibride di Azure a server Informix locali mediante l'inoltro del bus di servizio di Azure. Il connettore supporta le operazioni di database seguenti:

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


## Creare il connettore Informix
È possibile definire un connettore in un'app per la logica da Azure Marketplace, come illustrato nell'esempio seguente:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Nel pannello **Tutto** digitare **informix** nella casella **Cerca tutti gli elementi**, quindi fare clic su INVIO.
3. Nel riquadro dei risultati della ricerca in tutti gli elementi selezionare **Informix Connector**.
4. Nel pannello di descrizione del connettore Informix selezionare **Crea**.
5. Nel pannello del pacchetto del connettore Informix immettere il nome (ad esempio "InformixConnectorNewOrders"), il piano del servizio app e altre proprietà.
6. Selezionare **Impostazioni pacchetto**, quindi immettere le impostazioni del pacchetto seguenti.

	Nome | Obbligatorio | Descrizione
--- | --- | ---
ConnectionString | Sì | Stringa di connessione del client Informix (ad esempio, "Network Address=servername;Network Port=9089;User ID=username;Password=password;Initial Catalog=nwind;Default Schema=informix").
Tabelle | Sì | Elenco delimitato da virgole di tabelle, visualizzazioni e nomi alias necessari per operazioni OData e per la generazione di documentazione Swagger con esempi (ad esempio "NEWORDERS").
Procedure | Sì | Elenco delimitato da virgole di nomi di procedure e funzioni (ad esempio "SPORDERID").
OnPremise | No | Distribuzione locale mediante l'inoltro del bus di servizio di Azure.
ServiceBusConnectionString | No | Stringa di connessione dell'inoltro del bus di servizio di Azure.
PollToCheckData | No | Istruzione SELECT COUNT da usare con un trigger dell'app per la logica (ad esempio "SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL").
PollToReadData | No | Istruzione SELECT da usare con un trigger dell'app per la logica (ad esempio "SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE").
PollToAlterData | No | Istruzione UPDATE o DELETE da usare con un trigger dell'app per la logica (ad esempio "UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;").

7. Selezionare **OK**, quindi **Crea**.
8. Al termine, l'aspetto di Impostazioni pacchetto dovrebbe essere simile al seguente: ![][1]


## App per la logica con azione del connettore Informix per l'aggiunta di dati ##
È possibile definire un'azione dell'app per la logica per aggiungere dati a una tabella Informix mediante un'operazione OData di tipo API Insert o Post to Entity. Ad esempio, è possibile inserire un nuovo record di ordine cliente elaborando un'istruzione SQL INSERT rispetto a una tabella definita con una colonna Identity e restituendo il valore Identity delle righe interessate all'app per la logica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP] L'operazione "*Post to EntitySet*" della connessione Informix restituisce il valore della colonna Identity e l'operazione "*API Insert*" restituisce le righe interessate.

1. Nella schermata iniziale di Azure selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere il nome (ad esempio "NewOrdersInformix"), il piano del servizio app e le altre proprietà, quindi selezionare **Crea**.
3. Nella schermata iniziale di Azure selezionare l'app per la logica appena creata, fare clic su **Impostazioni** e quindi su **Trigger e azioni**.
4. Nel pannello Trigger e azioni selezionare **Crea da zero** nei modelli dell'app per la logica.
5. Nel pannello delle app per le API selezionare **Ricorrenza**, impostare una frequenza e un intervallo, quindi fare clic sul **segno di spunta**.
6. Nel pannello delle app per le API selezionare **Informix Connector**, quindi espandere l'elenco di operazioni per selezionare **Insert into NEWORDER**.
7. Espandere l'elenco di parametri per immettere i valori seguenti:

	Nome | Valore
--- | --- 
CUSTID | 10042
SHIPID | 10000
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Selezionare il **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**.
9. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][3]
10. Nell'elenco **Tutte le esecuzioni** in **Operazioni** selezionare il primo elemento dell'elenco, ovvero l'esecuzione più recente.
11. Nel pannello relativo alle **esecuzioni delle app per la logica** selezionare l'elemento **ACTION** di tipo **informixconnectorneworders**.
12. Nel pannello relativo alle **azioni delle app per la logica** selezionare **INPUTS LINK**. Il connettore Informix usa gli input per elaborare un'istruzione INSERT con parametri.
13. Nel pannello relativo alle **azioni delle app per la logica** selezionare **OUTPUTS LINK**. Gli input dovrebbero avere un aspetto simile al seguente: ![][4]

#### Informazioni importanti

- Il connettore tronca i nomi di tabella Informix quando forma i nomi delle azioni delle app per la logica. Ad esempio, l'operazione **Insert into NEWORDERS** viene troncata in **Insert into NEWORDER**.
- Dopo il salvataggio di **Trigger e azioni** delle app per la logica, l'operazione viene elaborata dall'app per la logica. È possibile che per l'elaborazione dell'operazione da parte dell'app per la logica sia necessario attendere alcuni secondi, ad esempio 3-5 secondi. È facoltativamente possibile fare clic su **Esegui ora** per elaborare l'operazione.
- Il connettore Informix definisce membri EntitySet con attributi, indicando anche se il membro corrisponde a una colonna Informix con colonne predefinite o generate, ad esempio la colonna Identity. L'app per la logica visualizza un asterisco rosso accanto al nome di ID membro EntitySet per indicare le colonne Informix che richiedono valori. È consigliabile non immettere alcun valore per il membro ORDID, che corrisponde alla colonna Identity Informix. È possibile immettere valori per i membri facoltativi (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), che corrispondono a colonne Informix con valori predefiniti.
- Il connettore Informix restituisce all'app per la logica la risposta a Post to EntitySet che include i valori per le colonne Identity, derivati da DRDA SQLDARD (SQL Data Area Reply Data) nell'istruzione SQL INSERT preparata. Il server Informix non restituisce i valori inseriti per le colonne con i valori predefiniti.


## App per la logica con azione del connettore Informix per l'aggiunta di dati in blocco ##
È possibile definire un'azione dell'app per la logica per aggiungere dati a una tabella Informix mediante un'operazione di tipo API Bulk Insert. Ad esempio, è possibile inserire due nuovi record di ordine cliente elaborando un'istruzione SQL INSERT con una matrice di valori di riga rispetto a una tabella definita con una colonna Identity e restituendo le righe interessate all'app per la logica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. Nella schermata iniziale di Azure selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere il nome (ad esempio "NewOrdersBulkInformix"), il piano del servizio app e le altre proprietà, quindi selezionare **Crea**.
3. Nella schermata iniziale di Azure selezionare l'app per la logica appena creata, fare clic su **Impostazioni** e quindi su **Trigger e azioni**.
4. Nel pannello Trigger e azioni selezionare **Crea da zero** nei modelli dell'app per la logica.
5. Nel pannello delle app per le API selezionare **Ricorrenza**, impostare una frequenza e un intervallo, quindi fare clic sul **segno di spunta**.
6. Nel pannello delle app per le API selezionare **Informix Connector**, quindi espandere l'elenco di operazioni per selezionare **Bulk Insert into NEW**.
7. Immettere il valore **rows** come matrice. Ad esempio, copiare e incollare il codice seguente:

	```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
	```
        
8. Selezionare il **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][6]

9. Nell'elenco **Tutte le esecuzioni** in **Operazioni** fare clic sul primo elemento dell'elenco, ovvero l'esecuzione più recente.
10. Nel pannello relativo alle **esecuzioni delle app per la logica** fare clic sull'elemento **ACTION**.
11. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **INPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: [][7]
12. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **OUTPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: ![][8]

#### Informazioni importanti

- Il connettore tronca i nomi di tabella Informix quando forma i nomi delle azioni delle app per la logica. Ad esempio, l'operazione **Bulk Insert into NEWORDERS** viene troncata in **Bulk Insert into NEW**.
- È possibile che il database Informix applichi la distinzione tra maiuscole e minuscole ai nomi di tabelle e colonne. Ad esempio, è possibile sia necessario specificare i nomi di colonna della matrice dell'operazione Bulk Insert in minuscolo ("custid") invece che in maiuscolo ("CUSTID").
- Omettendo le colonne Identity (ad esempio ORDID), le colonne che ammettono valori Null (ad esempio SHIPDATE) e le colonne con valori predefiniti (ad esempio ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), il database Informix genera i valori.
- Specificando "today" e "tomorrow", il connettore Informix genera le funzioni "CURRENT DATE" e "CURRENT DATE + 1 DAY" (ad esempio REQDATE).


## App per la logica con trigger del connettore Informix per la lettura, la modifica o l'eliminazione di dati ##
È possibile definire un trigger dell'app per la logica per eseguire il polling e leggere i dati da una tabella Informix mediante un'operazione composita di tipo API Poll Data. Ad esempio, è possibile leggere uno o più nuovi record di ordine cliente, restituendo i record all'app per la logica. Le impostazioni del pacchetto/dell'app di connessione Informix dovrebbero avere un aspetto simile al seguente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <nessun valore specificato>


È anche possibile definire un trigger dell'app per la logica per eseguire il polling, leggere e modificare i dati in una tabella Informix mediante un'operazione composita di tipo API Poll Data. Ad esempio, è possibile leggere uno o più nuovi record di ordine cliente e aggiornare i valori di riga, restituendo i record selezionati (prima dell'aggiornamento) all'app per la logica. Le impostazioni del pacchetto/dell'app di connessione Informix dovrebbero avere un aspetto simile al seguente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


È anche possibile definire un trigger dell'app per la logica per eseguire il polling, leggere e rimuovere dati da una tabella Informix mediante un'operazione composita di tipo API Poll Data. Ad esempio, è possibile leggere uno o più nuovi record di ordine cliente ed eliminare le righe, restituendo i record selezionati (prima dell'eliminazione) all'app per la logica. Le impostazioni del pacchetto/dell'app di connessione Informix dovrebbero avere un aspetto simile al seguente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

In questo esempio l'app per la logica eseguirà il polling, leggerà, aggiornerà e quindi leggerà di nuovo i dati nella tabella Informix.

1. Nella schermata iniziale di Azure selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**.
2. Immettere il nome (ad esempio "ShipOrdersInformix"), il piano del servizio app e le altre proprietà, quindi selezionare **Crea**.
3. Nella schermata iniziale di Azure selezionare l'app per la logica appena creata, fare clic su **Impostazioni** e quindi su **Trigger e azioni**.
4. Nel pannello Trigger e azioni selezionare **Crea da zero** nei modelli di app per la logica.
5. Nel pannello delle app per le API selezionare **Informix Connector** (Connettore Informix), impostare una frequenza e un intervallo, quindi fare clic sul **segno di spunta**.
6. Nel pannello delle app per le API selezionare **Informix Connector** (Connettore Informix), quindi espandere l'elenco di operazioni per selezionare **Select from NEWORDERS**.
7. Selezionare il **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][10]
8. Fare clic per chiudere il pannello **Trigger e azioni**, quindi fare clic per chiudere il pannello **Impostazioni**.
9. Nell'elenco **Tutte le esecuzioni** in **Operazioni** fare clic sul primo elemento dell'elenco, ovvero l'esecuzione più recente.
10. Nel pannello relativo alle **esecuzioni delle app per la logica** fare clic sull'elemento **ACTION**.
11. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **OUTPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: ![][11]


## App per la logica con azione del connettore Informix per la rimozione di dati ##
È possibile definire un'azione dell'app per la logica per rimuovere dati da una tabella Informix mediante un'operazione OData di tipo API Delete o Post to Entity. Ad esempio, è possibile inserire un nuovo record di ordine cliente elaborando un'istruzione SQL INSERT rispetto a una tabella definita con una colonna Identity e restituendo il valore Identity delle righe interessate all'app per la logica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Creare un'app per la logica usando il connettore Informix per rimuovere i dati ##
È possibile creare una nuova app per la logica in Azure Marketplace, quindi usare il connettore Informix come azione per rimuovere gli ordini cliente. Ad esempio, è possibile usare l'operazione condizionale Delete del connettore Informix per elaborare un'istruzione SQL DELETE (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. Nel menu dell'hub della schermata **iniziale** di Azure fare clic su **+** (segno più), quindi su **Web e dispositivi mobili** e infine su **App per la logica**.
2. Nel pannello **Crea app per la logica** digitare un **Nome**, ad esempio **RemoveOrdersInformix**.
3. Selezionare o definire i valori per le altre impostazioni, ad esempio il piano di servizio o il gruppo di risorse.
4. Le impostazioni dovrebbero avere un aspetto simile al seguente. Fare clic su **Crea**: ![][12]
5. Nel pannello **Impostazioni** fare clic su **Trigger e azioni**.
6. Nell'elenco **Modelli di app per la logica** del pannello **Trigger e azioni** fare clic su **Crea da zero**.
7. Nel riquadro **App per le API** del pannello **Trigger e azioni** fare clic su **Ricorrenza** nel gruppo di risorse.
8. Nell'area di progettazione dell'app per la logica fare clic sull'elemento **Ricorrenza** impostare un valore per **Frequenza** e **Intervallo**, ad esempio **Giorni** e **1**, quindi fare clic sul **segno di spunta** per salvare le impostazioni dell'elemento di ricorrenza.
9. Nel riquadro **App per le API** del pannello **Trigger e azioni** fare clic su **Informix Connector** (Connettore Informix) nel gruppo di risorse.
10. Nell'area di progettazione dell'app per la logica fare clic sull'attività di **Informix Connector** (Connettore Informix), fare clic sui puntini di sospensione (**...**) per espandere l'elenco di operazioni e quindi fare clic su **Conditional delete from N** (Eliminazione condizionale da N).
11. Nell'attività del connettore Informix digitare **ordid ge 10000** per un'**espressione che identifica un sottoinsieme di voci**.
12. Fare clic sul **segno di spunta** per salvare le impostazioni delle azioni, quindi fare clic su **Salva**. Le impostazioni dovrebbero avere un aspetto simile al seguente: ![][13]
13. Fare clic per chiudere il pannello **Trigger e azioni**, quindi fare clic per chiudere il pannello **Impostazioni**.
14. Nell'elenco **Tutte le esecuzioni** in **Operazioni** fare clic sul primo elemento dell'elenco, ovvero l'esecuzione più recente.
15. Nel pannello relativo alle **esecuzioni delle app per la logica** fare clic sull'elemento **ACTION**.
16. Nel pannello relativo alle **azioni delle app per la logica** fare clic su **OUTPUTS LINK**. Gli output dovrebbero avere un aspetto simile al seguente: ![][14]

**Nota:** la finestra di progettazione dell'app per la logica tronca i nomi delle tabelle. Ad esempio, l'operazione **Conditional delete from NEWORDERS** viene troncata in **Conditional delete from N**.


> [AZURE.TIP] Usare le istruzioni SQL seguenti per creare la tabella e le stored procedure di esempio.

È possibile creare la tabella NEWORDERS di esempio usando le istruzioni SQL DDL di Informix seguenti:
 
    create table neworders (  
 		ordid serial(10000) unique ,  
 		custid int not null ,  
 		empid int not null default 10000 ,  
 		orddate date not null default today ,  
 		reqdate date default today ,  
 		shipdate date ,  
 		shipid int not null default 10000 ,  
 		freight decimal (9,2) not null default 0.00 ,  
 		shipname char (40) not null ,  
 		shipaddr char (60) not null ,  
 		shipcity char (20) not null ,  
 		shipreg char (15) not null ,  
 		shipzip char (10) not null ,  
 		shipctry char (15) not null default ''USA'' 
 		)


È possibile creare la stored procedure SPORDERID di esempio usando l'istruzione DDL di Informix seguente:
 
    create procedure sporderid ( ord_id int)  
 		returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
 		define xordid, xcustid, xempid, xshipid int;  
 		define xorddate, xreqdate, xshipdate date;  
 		define xfreight decimal (9,2);  
 		define xshipname char (40);  
 		define xshipaddr char (60);  
 		define xshipcity char (20);  
 		define xshipreg, xshipctry char (15);  
 		define xshipzip char (10);  
 		select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
 			into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
 			from neworders where ordid = ord_id;  
 		return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## Configurazione ibrida (facoltativo)

> [AZURE.NOTE] Questo passaggio è obbligatorio solo se si usa DB2 Connector installato in locale e protetto da firewall.

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa un server IBM DB2 locale per Windows, è richiesta la Gestione connessione ibrida.

Vedere l'articolo relativo all'[uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png

<!---HONumber=AcomDC_0803_2016-->