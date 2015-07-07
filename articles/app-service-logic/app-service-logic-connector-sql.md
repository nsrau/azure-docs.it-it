<properties 
   pageTitle="Uso del connettore SQL in Microsoft Azure App Service" 
   description="Come usare SQL Connector" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/17/2015"
   ms.author="sutalasi"/>


# Microsoft SQL Connector

Connettersi a un'istanza di SQL Server in locale o a un database SQL di Azure per creare e modificare le informazioni o i dati. I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un "flusso di lavoro". Quando si usa il connettore SQL nel flusso di lavoro è possibile ottenere un'ampia gamma di scenari. Ad esempio, è possibile:

- Esporre una sezione dei dati presenti nel database SQL usando un'applicazione Web o per dispositivi mobili. 
- Aggiungere dati alla tabella del database SQL per l'archiviazione. Ad esempio, è possibile immettere i record dei dipendenti, aggiornare gli ordini di vendita e così via.
- Ottenere dati da SQL per usarli in un processo aziendale. Ad esempio, è possibile ottenere i record cliente e inserirli in SalesForce. 

## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio, quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger; ad esempio, quando si aggiorna un ordine, inviare un avviso al venditore oppure, quando si aggiunge un nuovo cliente, inviargli/le un messaggio di benvenuto.

Il connettore SQL può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per ogni tabella inclusa nelle impostazioni pacchetto (che saranno discusse nel dettaglio più avanti in questo argomento), esiste un set di azioni JSON e uno di azioni XML.

Il connettore SQL ha a disposizione i seguenti trigger e azioni:

Trigger | Azioni
--- | ---
Poll Data | <ul><li>Inserimento nella tabella</li><li>Aggiornamento della tabella</li><li>Selezione dalla tabella</li><li>Eliminazione dalla tabella</li><li>Chiamata della stored procedure</li>

## Creare il connettore SQL

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "Connettore SQL".
3. Immettere il Nome, il Piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Server Name | Sì | Immettere il nome dell'istanza di SQL Server, ad esempio *SQLserver/sqlexpress* o *SQLserver.mydomain.com*.
Port | No | Il valore predefinito è 1433.
User Name | Sì | Immettere un nome utente che può accedere all'istanza di SQL Server. Se ci si connette a un'istanza di Server SQL locale, immettere dominio\nome utente. 
Password | Sì | Immettere il nome utente e la password.
Database Name | Sì | Immettere il database a cui connettersi, ad esempio *Customers* o *dbo/orders*.
On-Premises | Sì | Il valore predefinito è False. Immettere False per la connessione a un database SQL di Azure. Immettere True per la connessione a un'istanza di Server SQL locale. 
Service Bus Connection String | No | Se ci si connette in locale, immettere la stringa di connessione di inoltro del bus di servizio.<br/><br/>[Uso della gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md)<br/>[Prezzi del bus di servizio](http://azure.microsoft.com/pricing/details/service-bus/)
Partner Server Name | No | Se il server primario non è disponibile, è possibile immettere un server partner come server alternativo o di backup. 
Tables | No | Elencare le tabelle di database che possono essere aggiornate dal connettore. Immettere ad esempio *OrdersTable* o *EmployeeTable*. Se non viene immessa alcuna tabella, è possibile usare tutte le tabelle. Per usare questo connettore come azione, è necessario usare tabelle e/o stored procedure valide. 
Stored procedure | No | Immettere una stored procedure esistente che può essere chiamata dal connettore, ad esempio *sp_IsEmployeeEligible* o *sp_CalculateOrderDiscount*. Per usare questo connettore come azione, è necessario usare tabelle e/o stored procedure valide. 
Data Available Query | Per il supporto dei trigger | Istruzione SQL per determinare se sono disponibili dati per il polling di una tabella di database di SQL Server. Deve restituire un valore numerico che rappresenta il numero di righe di dati disponibili. Esempio: SELECT COUNT(*) from table_name. 
Poll Data Query | Per il supporto dei trigger | Istruzione SQL per eseguire il polling della tabella di database di SQL Server. È possibile immettere un numero qualsiasi di istruzioni SQL, separate da punto e virgola. Questa istruzione viene eseguita a livello di transazione e ne viene eseguito il commit solo quando i dati vengono archiviati in modo sicuro nell'app per la logica. Esempio: SELECT * FROM table_name; DELETE FROM table_name. <br/><br/>**Nota**<br/>È necessario fornire un'istruzione di polling che evita un ciclo infinito eliminando, spostando o aggiornando i dati selezionati per garantire che non venga eseguito di nuovo il polling degli stessi dati. 

5. Al termine, l'aspetto di Impostazioni pacchetto dovrebbe essere simile al seguente: <br/> ![][1]

## Usare il connettore come trigger
In questa sezione viene descritta una semplice app per la logica che esegue il polling dei dati da una tabella SQL, aggiunge tali dati a un'altra tabella e quindi li aggiorna.

Per usare il connettore SQL come trigger, immettere i valori per **Data Available Query** e **Poll Data Query**. **Data Available Query** viene eseguito in base alla pianificazione immessa e determina se sono disponibili dati. Poiché questa query restituisce solo un numero scalare, può essere ottimizzata per l'esecuzione frequente.

**Poll Data Query** viene eseguito solo quando Data Available Query indica che sono disponibili dati. Questa istruzione viene eseguita all'interno di una transazione e ne viene eseguito il commit solo quando i dati estratti vengono archiviati in modo permanente nel flusso di lavoro. È importante evitare di estrarre sempre gli stessi dati. La natura transazionale di questa esecuzione può essere usata per eliminare o aggiornare i dati per garantire che non vengano raccolti la volta successiva che viene eseguita una query sui dati.

> [AZURE.NOTE]Lo schema restituito da questa istruzione identifica le proprietà disponibili nel connettore. Tutte le colonne devono essere denominate.

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
1. Quando si crea o modifica un'app per la logica, selezionare il connettore SQL creato come trigger. In questo modo verranno elencati i trigger disponibili, ovvero **Poll Data (JSON)** e **Poll Data (XML)**: <br/> ![][5] 

2. Selezionare il trigger **Poll Data (JSON)**, immettere la frequenza e fare clic su ✓: <br/> ![][6]

3. Il trigger viene ora visualizzato come configurato nell'app per la logica. Vengono visualizzati gli output del trigger, che possono essere usati come input in eventuali azioni successive: <br/> ![][7]

## Usare il connettore come azione
Usando un semplice scenario di app per la logica che esegue il polling dei dati da una tabella SQL, aggiunge tali dati a un'altra tabella e quindi li aggiorna.

Per usare il connettore SQL come azione, immettere il nome delle tabelle e/o delle stored procedure immesse durante la creazione del connettore del connettore SQL:

1. Dopo il trigger (o dopo aver scelto 'Esegui la logica manualmente'), aggiungere il connettore SQL creato dalla raccolta. Selezionare una delle azioni di inserimento, ossia *Insert Into TempEmployeeDetails (JSON)*: <br/> ![][8] 

2. Immettere i valori di input del record da inserire e fare clic su ✓: <br/> ![][9]

3. Dalla raccolta, selezionare lo stesso connettore SQL creato. Come azione, selezionare l'azione Update nella stessa tabella, ad esempio *Update EmployeeDetails*: <br/> ![][11]

4. Immettere i valori di input per l'azione di aggiornamento e fare clic su ✓: <br/> ![][12]

È possibile testare l'app per la logica aggiungendo un nuovo record alla tabella di cui viene effettuato il polling.

## Configurazione ibrida (facoltativo)

> [AZURE.NOTE]Questo passaggio è obbligatorio solo se si usa SQL Server installato in locale e protetto da firewall.

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa un'istanza di SQL Server locale, è richiesta la Gestione connessione ibrida.

Vedere [Uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## Più vantaggi con il connettore
Dopo aver creato il connettore è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e il connettore](../app-service-api/app-service-api-manage-in-portal.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png


 

<!---HONumber=62-->