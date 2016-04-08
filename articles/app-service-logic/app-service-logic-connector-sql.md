<properties
   pageTitle="Uso del connettore SQL nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore SQL e usarlo in un'app per la logica in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="sameerch"/>


# Uso del connettore Microsoft SQL e aggiunta all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione dello schema 2015-08-01-preview di Azure SQL, fare clic su [API SQL Azure](../connectors/connectors-create-api-sqlazure.md).

Connettersi a un'istanza di SQL Server in locale o a un database SQL di Azure per creare e modificare le informazioni o i dati. I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un "flusso di lavoro". Quando si usa il connettore SQL nel flusso di lavoro è possibile ottenere un'ampia gamma di scenari. Ad esempio, è possibile:

- Esporre una sezione dei dati presenti nel database SQL usando un'applicazione Web o per dispositivi mobili.
- Inserire dati in una tabella del database SQL per l'archiviazione. È ad esempio possibile immettere i record dei dipendenti, aggiornare gli ordini di vendita e così via.
- Ottenere dati da SQL per usarli in un processo aziendale. È ad esempio possibile ottenere i record cliente e inserirli in Salesforce.

È possibile aggiungere il connettore SQL al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger. Quando ad esempio si aggiorna un ordine, viene inviato un avviso al venditore oppure quando si aggiunge un nuovo cliente, viene inviato un messaggio di benvenuto.

Il connettore SQL può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per ogni tabella inclusa nelle impostazioni pacchetto (che saranno discusse nel dettaglio più avanti in questo argomento), esiste un set di azioni JSON e uno di azioni XML.

Per il connettore SQL sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Poll Data | <ul><li>Insert Into Table</li><li>Update Table</li><li>Select From Table</li><li>Delete From Table</li><li>Call Stored Procedure</li></ul>

## Creare il connettore SQL

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "SQL Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Server Name | Sì | Immettere il nome dell'istanza di SQL Server, ad esempio *SQLserver/sqlexpress* o *SQLserver.mydomain.com*.
Port | No | Il valore predefinito è 1433.
User Name | Sì | Immettere un nome utente che può accedere all'istanza di SQL Server. Se ci si connette a un'istanza di SQL Server locale, immettere le credenziali di autenticazione SQL.
Password | Sì | Immettere il nome utente e la password.
Database Name | Sì | Immettere il database a cui connettersi, ad esempio *Customers* o *dbo/orders*.
On-Premises | Sì | Il valore predefinito è False. Immettere False per la connessione a un database SQL di Azure. Immettere True per la connessione a un'istanza di SQL Server locale.
Service Bus Connection String | No | Se ci si connette in locale, immettere la stringa di connessione di inoltro del bus di servizio.<br/><br/>[Uso della gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md)<br/>[Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/)
Partner Server Name | No | Se il server primario non è disponibile, è possibile immettere un server partner come server alternativo o di backup.
Tables | No | Elencare le tabelle di database che possono essere aggiornate dal connettore. Immettere ad esempio *OrdersTable* o *EmployeeTable*. Se non viene immessa alcuna tabella, è possibile usare tutte le tabelle. Per usare questo connettore come azione, è necessario usare tabelle e/o stored procedure valide.
Stored procedure | No | Immettere una stored procedure esistente che può essere chiamata dal connettore, ad esempio *sp\_IsEmployeeEligible* o *sp\_CalculateOrderDiscount*. Per usare questo connettore come azione, è necessario usare tabelle e/o stored procedure valide.
Data Available Query | Per il supporto dei trigger | Istruzione SQL per determinare se sono disponibili dati per il polling di una tabella di database di SQL Server. Deve restituire un valore numerico che rappresenta il numero di righe di dati disponibili. Esempio: SELECT COUNT(*) from table\_name.
Poll Data Query | Per il supporto dei trigger | L'istruzione SQL per eseguire il polling della tabella di database di SQL Server. È possibile immettere un numero qualsiasi di istruzioni SQL, separate da punto e virgola. Questa istruzione viene eseguita a livello di transazione e ne viene eseguito il commit solo quando i dati vengono archiviati in modo sicuro nell'app per la logica. Esempio: SELECT * FROM table\_name; DELETE FROM table\_name. <br/><br/>**Nota**<br/>È necessario fornire un'istruzione di polling che evita un ciclo infinito eliminando, spostando o aggiornando i dati selezionati per garantire che non venga eseguito di nuovo il polling degli stessi dati.

5. Al termine, l'aspetto di Impostazioni pacchetto è simile al seguente: ![][1]

6. Selezionare **Create**.


## Usare il connettore come trigger
In questa sezione viene descritta una semplice app per la logica che esegue il polling dei dati da una tabella SQL, aggiunge tali dati a un'altra tabella e quindi li aggiorna.

Per usare il connettore SQL come trigger, immettere i valori per **Data Available Query** e **Poll Data Query**. **Data Available Query** viene eseguita in base alla pianificazione immessa e determina se sono disponibili dati. Poiché questa query restituisce solo un numero scalare, può essere ottimizzata per l'esecuzione frequente.

**Poll Data Query** viene eseguita solo quando Data Available Query indica che sono disponibili dati. Questa istruzione viene eseguita all'interno di una transazione e ne viene eseguito il commit solo quando i dati estratti vengono archiviati in modo permanente nel flusso di lavoro. È importante evitare di estrarre sempre gli stessi dati. La natura transazionale di questa esecuzione può essere usata per eliminare o aggiornare i dati per garantire che non vengano raccolti la volta successiva che viene eseguita una query sui dati.

> [AZURE.NOTE] Lo schema restituito da questa istruzione identifica le proprietà disponibili nel connettore. Tutte le colonne devono essere denominate.

#### Esempio di Data Available Query

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### Esempio di Poll Data Query

	SELECT *, GetData() as 'PollTime' FROM [Order]
		WHERE OrderStatus = 'ProcessedForCollection'
		ORDER BY Id DESC;
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
		WHERE Id =
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### Aggiungere il trigger
1. Quando si crea o modifica un'app per la logica, selezionare il connettore SQL creato come trigger. In questo modo vengono elencati i trigger disponibili, ovvero **Poll Data (JSON)** e **Poll Data (XML)**: ![][5]

2. Selezionare il trigger **Poll Data (JSON)**, immettere la frequenza e fare clic su ✓: ![][6]

3. Il trigger viene ora visualizzato come configurato nell'app per la logica. Vengono visualizzati gli output del trigger, che possono essere usati come input in eventuali azioni successive: ![][7]

## Usare il connettore come azione
Usando un semplice scenario di app per la logica che esegue il polling dei dati da una tabella SQL, tali dati vengono aggiunti a un'altra tabella e quindi aggiornati.

Per usare il connettore SQL come azione, immettere il nome delle tabelle e/o delle stored procedure immesse durante la creazione del connettore SQL:

1. Dopo il trigger (o dopo aver scelto di eseguire la logica manualmente), aggiungere il connettore SQL creato dalla raccolta. Selezionare una delle azioni di inserimento, ad esempio *Insert Into TempEmployeeDetails (JSON)*: ![][8]

2. Immettere i valori di input del record da inserire e fare clic su ✓: ![][9]

3. Dalla raccolta, selezionare lo stesso connettore SQL creato. Come azione, selezionare l'azione Update nella stessa tabella, ad esempio *Update EmployeeDetails*: ![][11]

4. Immettere i valori di input per l'azione di aggiornamento e fare clic su ✓: ![][12]

È possibile testare l'app per la logica aggiungendo un nuovo record alla tabella di cui viene effettuato il polling.

## Operazioni consentite e non consentite

Query SQL | Supportato | Non supportato
--- | --- | ---
Clausola Where | <ul><li>Operatori: AND, OR, =, <>, <, <=, >, >= e LIKE</li><li>Più sottocondizioni possono essere combinate con '(' e ')'</li><li>Valori letterali stringa, data/ora (racchiusi tra virgolette singole), numeri (devono contenere solo caratteri numerici)</li><li>Deve essere rigorosamente in un formato di espressione binaria, come ((operando operatore operando) AND/OR (operando operatore operando))**</li></ul> | <ul><li>Operatori: Between, IN</li><li>Tutte le funzioni predefinite, ad esempio ADD(), MAX() NOW(), POWER() e così via</li><li>Operatori matematici, ad esempio *, -, + e così via</li><li>Concatenazioni di stringa con +.</li><li>Tutti i join</li><li>IS NULL e IS NOT Null</li><li>Qualsiasi numero con caratteri non numerici, ad esempio i numeri esadecimali</li></ul>
Campi (nella query Select) | <ul><li>Nomi di colonna validi, separati da virgole. Non sono consentiti prefissi di nomi di tabella (il connettore funziona su una sola tabella alla volta).</li><li>I nomi possono essere preceduti dai caratteri di escape '[' e ']'</li></ul> | <ul><li>Parole chiave come TOP, DISTINCT e così via</li><li>Aliasing, ad esempio Via + Città + CAP COME indirizzo</li><li>Tutte le funzioni predefinite, ad esempio ADD(), MAX() NOW(), POWER() e così via</li><li>Operatori matematici, ad esempio *, -, + e così via</li><li>Concatenazioni di stringa con +</li></ul>

#### Suggerimenti

- Per le query avanzate è consigliabile creare una stored procedure ed eseguirla con l'API Stored procedure di esecuzione.
- Nel caso di query interne, usarle all'interno di stored procedure.
- Per unire più condizioni, è possibile usare gli operatori 'AND' e 'OR'.

## Configurazione ibrida (facoltativo)

> [AZURE.NOTE] Questo passaggio è obbligatorio solo se si usa SQL Server installato in locale e protetto da firewall.

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa un'istanza di SQL Server locale, è richiesta la Gestione connessione ibrida.

> [AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Vedere l'articolo relativo all'[uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png

<!---HONumber=AcomDC_0323_2016--->