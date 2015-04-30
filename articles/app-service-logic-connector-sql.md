<properties 
   pageTitle="SQL Connector" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft SQL Connector #

I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un flusso. Esistono scenari in cui è necessario lavorare con database SQL in SQL Azure o SQL Server (installato in locale e protetto da un firewall). Inserendo SQL Connector nel flusso è possibile ottenere un'ampia gamma di scenari. Alcuni esempi:  

1.	Esporre una sezione dei dati presenti nel database SQL tramite un front-end utente mobile o Web.
2.	Aggiungere dati alla tabella del database SQL per l'archiviazione, ad esempio record relativi ai dipendenti, ordini di vendita e così via
3.	Estrarre dati da SQL per usarli in un processo aziendale

Per questi scenari è necessario quanto segue: 

1. Creare un'istanza dell'app per le API SQL Connector
2. Stabilire la connettività ibrida per la comunicazione tra l'app per le API e il sistema SQL locale. Questo passaggio è facoltativo. È obbligatorio solo per SQL Server installato in locale, non per SQL Azure.
3. Usare l'app per le API creata in un'app per la logica per ottenere il processo aziendale desiderato

	###Trigger e azioni di base
		
    - Polling dei dati (trigger) 
    - Inserimento nella tabella
    - Aggiornamento della tabella
    - Selezione dalla tabella
    - Eliminazione dalla tabella
    - Chiamata a una stored procedure

## Creare un'istanza dell'app per le API SQL Connector ##

Per usare SQL Connector è necessario creare un'istanza dell'app per le API SQL Connector. A questo scopo, procedere come segue:

1. Aprire Azure Marketplace con l'opzione '+ NUOVO' in basso a sinistra nel portale di Azure.
2. Andare a "Web e dispositivi mobili > App per le API" e cercare "SQL Connector".
3. Inserire i dettagli generici, come nome, piano di servizio app e così via nel primo pannello
4. Specificare le impostazioni pacchetto indicate nella tabella seguente.	

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>Nome</th><th>Obbligatorio</th><th>Valore predefinito</th><th>Descrizione</th></tr>
 <tr><td>Server Name</td><td>Sì</td><td>&nbsp;</td><td>Specificare il nome di SQL Server, ad esempio "SQLserver", "SQLserver/sqlexpress" o "SQLserver.mydomain.com".</td></tr>
 <tr><td>Port</td><td>No</td><td> 1433</td><td>Facoltativo. Il numero di porta in cui viene stabilita la connessione. Se non si specifica un valore, il connettore si connette tramite la porta predefinita.</td></tr>
 <tr><td>User Name</td><td>Sì</td><td>&nbsp;</td><td>Specificare un nome utente valido per la connessione a SQL Server.</td></tr>
 <tr><td>Password</td><td>Sì</td><td>&nbsp;</td><td>Specificare un nome utente valido per la connessione a SQL Server.</td></tr>
 <tr><td>Database Name</td><td>Sì</td><td>&nbsp;</td><td>Specificare un nome di database valido in SQL Server, ad esempio "orders", "dbo/orders" o "myaccount/employees".</td></tr>
 <tr><td>On-Premises</td><td>Sì</td><td>FALSE</td><td>Specificare se SQL Server installato in locale è protetto da firewall o no. Se impostato su TRUE, è necessario installare un agente listener in un server che può accedere a SQL Server. Per installare l'agente, passare alla pagina di riepilogo dell'app per le API e fare clic su  'Hybrid Connection'.</td></tr>
 <tr><td>Service Bus Connection String</td><td>No</td><td>&nbsp;</td><td>Facoltativo. Specificare questo parametro se SQL Server è installato in locale. Deve trattarsi di una stringa di connessione valida allo spazio dei nomi del bus di servizio. Assicurarsi di usare l'edizione  'Standard' del bus di servizio di Azure, non l'edizione  'Basic'.</td></tr>
 <tr><td>Partner Server Name</td><td>No</td><td>&nbsp;</td><td>Facoltativo. Specificare il server partner a cui connettersi quando il server primario è inattivo.</td></tr>
 <tr><td>Tables</td><td>No</td><td>&nbsp;</td><td>Facoltativo. Specificare le tabelle del database che possono essere modificate dal connettore, ad esempio OrdersTable o EmployeeTable.</td></tr>
 <tr><td>Stored Procedures</td><td>No</td><td>&nbsp;</td><td>Facoltativo. Specificare le stored procedure presenti nel database che possono essere chiamate dal connettore, ad esempio IsEmployeeEligible o CalculateOrderDiscount.</td></tr>
 <tr><td>Data Available Query</td><td>No</td><td>&nbsp;</td><td>Facoltativo. Specificare l'istruzione SQL per determinare se sono disponibili dati per il polling di una tabella di database di SQL Server. Esempio: SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>Poll Data Query</td><td>No</td><td>&nbsp;</td><td>Facoltativo. Specificare l'istruzione SQL per eseguire il polling della tabella di database di SQL Server. È possibile specificare un numero qualsiasi di istruzioni SQL, separate da punto e virgola. Esempio: SELECT * from table_name; DELETE from table_name. NOTA: è necessario specificare l'istruzione di polling in modo che non determini un ciclo infinito. Un'operazione di selezione, ad esempio, deve essere seguita da un'operazione di eliminazione, mentre un'operazione di selezione in base a un flag deve essere seguita da un'operazione di aggiornamento del flag stesso.</td></tr>
</table>


 ![][1]  

## Configurazione ibrida (facoltativo) ##

Nota: questo passaggio è obbligatorio solo se si utilizza SQL Server installato in locale e protetto da firewall.

Individuare l'applicazione API creata scegliendo Sfoglia -> App per le API -> <Nome dell'app per le API creata>. Verrà visualizzato il comportamento seguente. La configurazione è incompleta, in quanto la connessione ibrida non è stata ancora stabilita.

![][2] 

Per stabilire la connettività ibrida eseguire le operazioni seguenti:

1. Copiare la stringa di connessione primaria
2. Fare clic sul collegamento  'Download and Configure'
3. Seguire il processo di installazione che viene avviato e, quando viene richiesto, fornire la stringa di connessione primaria
4. Al termine del processo di installazione verrà visualizzata una finestra di dialogo simile alla seguente

![][3] 

A questo punto, quando si passa nuovamente all'app per le API creata, lo stato della connessione ibrida visualizzato sarà Connected. 

![][4] 

Nota: per passare alla stringa di connessione secondaria è sufficiente ripetere la configurazione della connessione ibrida e inserire la stringa di connessione secondaria al posto della stringa di connessione primaria.  

## Utilizzo in un'app per la logica ##

SQL Connector può essere usato come trigger o azione in un'app per la logica. Il trigger e tutte le azioni supportano i formati dati JSON e XML. Per ogni tabella fornita come parte delle impostazioni pacchetto sarà disponibile un set di azioni JSON e uno di azioni XML. Se si usa un trigger o un'azione XML, è possibile usare l'app per le API Transform per convertire i dati in un altro formato dati XML. 

In questa sezione viene descritta una semplice app per la logica che esegue il polling dei dati da una tabella SQL, aggiunge tali dati a un'altra tabella e quindi li aggiorna.



-  Quando si crea o modifica un'app per la logica, scegliere l'app per le API SQL Connector creata come trigger. In questo modo verranno elencati i trigger disponibili, ovvero Poll Data (JSON) e Poll Data (XML).

 ![][5] 


- Selezionare il trigger Poll Data (JSON), specificare la frequenza e fare clic su ✓.

![][6] 



- Il trigger verrà ora visualizzato come configurato nell'app per la logica. Verranno visualizzati gli output del trigger, che possono essere usati come input in azioni successive. 

![][7] 


- Selezionare lo stesso connettore SQL dalla raccolta come azione. Selezionare una delle azioni di inserimento, ovvero Insert Into TempEmployeeDetails (JSON).

![][8] 



- Fornire gli input del record da inserire e fare clic su ✓. 

![][9] 



- Selezionare lo stesso connettore SQL dalla raccolta come azione. Selezionare l'azione di aggiornamento nella stessa tabella, ad esempio Update EmployeeDetails.

![][11] 



- Fornire gli input per l'azione di aggiornamento e fare clic su ✓. 

![][12] 

È possibile testare l'app per la logica aggiungendo un nuovo record alla tabella di cui viene effettuato il polling.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg



<!--HONumber=52-->