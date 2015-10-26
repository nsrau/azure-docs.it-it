<properties
   pageTitle="Uso del connettore Oracle Database nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Oracle Database e usarlo in un'app per la logica in Azure App Service"
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
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# Uso del connettore Oracle Database e aggiunta all'app per la logica
Connettersi a un server di database Oracle in locale per creare e modificare le informazioni o i dati. I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un "flusso di lavoro". Quando si usa il connettore Oracle Database nel flusso di lavoro, è possibile ottenere un'ampia gamma di scenari. Ad esempio, è possibile:

- Esporre una sezione dei dati presenti nel database Oracle usando un'applicazione Web o per dispositivi mobili.
- Inserire dati nella tabella del database Oracle per l'archiviazione. È ad esempio possibile immettere i record dei dipendenti, aggiornare gli ordini di vendita e così via.
- Ottenere dati da Oracle per usarli in un processo aziendale. È ad esempio possibile ottenere i record cliente e inserirli in Salesforce.


## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger. Quando ad esempio si aggiorna un ordine, viene inviato un avviso al venditore oppure quando si aggiunge un nuovo cliente, viene inviato un messaggio di benvenuto.

Il connettore Oracle Database può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per ogni tabella inclusa nelle impostazioni pacchetto (che saranno discusse nel dettaglio più avanti in questo argomento), esiste un set di azioni JSON e uno di azioni XML. Se si usa un trigger o un'azione XML, è possibile usare l'[app per le API Transform](app-service-logic-transform-xml-documents.md) per convertire i dati in un altro formato dati XML.

Per il connettore Oracle Database sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Poll Data | <ul><li>Insert Into Table</li><li>Update Table</li><li>Select From Table</li><li>Delete From Table</li><li>Call Stored Procedure</li>


## Creare un connettore Oracle Database

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "Oracle Database Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Data Source | Sì | Un nome di origine dati (servizio di rete) specificato nel file tnsnames.ora sul computer in cui è installato il client Oracle. Per informazioni sui nomi delle origini dati e sul file tnsnames.ora, vedere l'articolo relativo alla [configurazione del client Oracle](http://msdn.microsoft.com/library/dd787872.aspx).
User Name | Sì | Immettere un nome utente valido per la connessione al server Oracle.
Password | Sì | Immettere il nome utente e la password.
Service Bus Connection String | Sì | Se ci si connette in locale, immettere la stringa di connessione di inoltro del bus di servizio.<br/><br/>[Uso della gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md)<br/>[Prezzi del bus di servizio](http://azure.microsoft.com/pricing/details/service-bus/)
Tables | No | Immettere le tabelle del database che possono essere modificate dal connettore, ad esempio *OrdersTable,EmployeeTable*.
Stored procedure | No | Immettere le stored procedure presenti nel database che possono essere chiamate dal connettore, ad esempio *IsEmployeeEligible, CalculateOrderDiscount*.
Functions | No | Immettere le funzioni presenti nel database che possono essere chiamate dal connettore, ad esempio *IsEmployeeEligible, CalculateOrderDiscount*.
Package Entities | No | Immettere i pacchetti presenti nel database che possono essere chiamati dal connettore, ad esempio *PackageOrderProcessing.CompleteOrder o PackageOrderProcessing.GenerateBill*.
Data Available Statement | No | Immettere l'istruzione per determinare se sono disponibili dati per il polling, ad esempio *SELECT * from table\_name*.
Poll Type | No | Immettere il tipo di polling. I valori consentiti sono "Select", "Procedure", "Function" e "Package".
Poll Statement | No | Immettere l'istruzione per il polling del database del server Oracle, ad esempio *SELECT * from table\_name*.
Post Poll Statement | No | Immettere l'istruzione da eseguire dopo il polling, ad esempio *DELETE * from table\_name*.

5. Al termine, l'aspetto di Impostazioni pacchetto è simile al seguente: <br/> ![][1]


## Usare il connettore come trigger
In questa sezione viene descritta una semplice app per la logica che esegue il polling dei dati da una tabella Oracle, aggiunge tali dati a un'altra tabella e quindi li aggiorna.

### Aggiungere il trigger
1. Quando si crea o modifica un'app per la logica, selezionare il connettore Oracle creato come trigger. In questo modo verranno elencati i trigger disponibili, ovvero **Poll Data (JSON)** e **Poll Data (XML)**: <br/> ![][5]

2. Selezionare il trigger **Poll Data (JSON)**, immettere la frequenza e fare clic su ✓: <br/> ![][6]

3. Il trigger viene ora visualizzato come configurato nell'app per la logica. Vengono visualizzati gli output del trigger, che possono essere usati come input in eventuali azioni successive:<br/> ![][7]

## Usare il connettore come azione
Usando una semplice app per la logica che esegue il polling dei dati da una tabella Oracle, aggiunge tali dati a un'altra tabella e quindi li aggiorna.

Per usare il connettore Oracle come azione, immettere il nome delle tabelle e/o le stored procedure immesse durante la creazione del connettore Oracle:

1. Selezionare lo stesso connettore Oracle dalla raccolta come azione. Selezionare una delle azioni di inserimento, ad esempio *Insert Into TempEmployeeDetails (JSON)*: <br/> ![][8]

2. Immettere i valori di input del record da inserire e fare clic su ✓: <br/> ![][9]

3. Dalla raccolta, selezionare lo stesso connettore Oracle creato. Come azione, selezionare l'azione Update nella stessa tabella, ad esempio *Update TempEmployeeDetails*: <br/> ![][11]

4. Immettere i valori di input per l'azione di aggiornamento e fare clic su ✓: <br/> ![][12]

È possibile testare l'app per la logica aggiungendo un nuovo record alla tabella di cui viene effettuato il polling.

## Configurazione ibrida

> [AZURE.NOTE]Questo passaggio è obbligatorio solo se si usa Oracle installato in locale e protetto da firewall.

Il servizio app usa Gestione connessione ibrida per connettersi in modo sicuro al sistema locale. Se il connettore usa Oracle in locale, è richiesta la Gestione connessione ibrida.

Vedere l'articolo relativo all'[uso di Gestione connessione ibrida](app-service-logic-hybrid-connection-manager.md).

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png

<!---HONumber=Oct15_HO3-->