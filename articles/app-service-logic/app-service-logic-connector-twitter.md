<properties
   pageTitle="Uso di Twitter Connector nel servizio app di Microsoft Azure"
   description="Come usare l'app per le API Twitter Connector"
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
   ms.date="07/14/2015"
   ms.author="sameerch"/>


# Twitter Connector

Connettersi al feed di Twitter per inviare e ricevere tweet dalla propria sequenza temporale, dalla sequenza temporale degli amici e dai follower dell'account Twitter. I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un "flusso di lavoro". Quando si usa Twitter Connector nel flusso di lavoro, si può ottenere un'ampia gamma di scenari. Ad esempio, è possibile:

- Ottenere nuovi tweet associati a una parola chiave o un testo specificato. Quando viene recuperato un nuovo tweet, questo attiva una nuova istanza del flusso di lavoro e passa i dati al connettore successivo nel flusso di lavoro. Ad esempio, si crea Twitter Connector e si usa il trigger New Tweet From Search per monitorare #peanutbutterandjelly. Ogni volta che viene rilevato un nuovo tweet per #peanutbutterandjelly, il flusso di lavoro (detto anche app per la logica) viene attivato automaticamente.
- Usando le diverse azioni, ad esempio "Search Tweets", è possibile accettare la risposta e usarla nel flusso di lavoro. Ad esempio, è possibile cercare i tweet in base al nome della società. Quando si trovano, è possibile usare un'app per la logica per scrivere i dati in un database SQL Server. Usare quindi i dati di SQL Server per determinare cosa viene pubblicato su Twitter riguardo alla società. 


## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio, un nuovo tweet può attivare o avviare un flusso di lavoro o un processo. Un'*azione* è il risultato di un evento. Ad esempio, è possibile cercare un tweet specifico e, quando lo si trova, inviare un messaggio di posta elettronica o aggiornare un database.

Twitter Connector può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML.

Per Twitter Connector sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
New Tweet From Search | <ul><li>Get User Timeline</li><li>Search Tweets</li><li>Tweet</li><li>Get Mentions Timeline</li><li>Get Home Timeline</li><li>Get Followers</li><li>Get Friends</li><li>Get User Details</li><li>Tweet to User</li><li>Send Direct Message</li></ul>

> [AZURE.IMPORTANT]Il trigger **New Tweet** è stato archiviato. Attualmente è ancora disponibile come operazione avanzata e può essere usato. L'azione **Retweet** viene rimossa è non è più supportata. Se si usa l'azione Retweet, genera un errore in fase di esecuzione. Di conseguenza, è opportuno rimuovere l'azione Retweet dalle app per la logica.


## Creare Twitter Connector
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Twitter Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà:

	![][1]
4.	Fare clic su **Crea**.


## Uso del connettore Twitter nell'app per la logica
Dopo aver creato l'app per le API, si può usare Twitter Connector come trigger o azione per le app per la logica. A tale scopo, effettuare l'operazione seguente:

1.	Creare una nuova app per la logica o aprirne una esistente:

	![][2]
2.	Aprire **Trigger e azioni** per visualizzare la finestra di progettazione delle app per la logica:

	![][3]
3.	Twitter Connector è elencato sul lato destro. Selezionarlo per aggiungerlo automaticamente all'app per la logica.

	![][4]
4.	Selezionare **Authorize**, immettere le credenziali di Twitter e selezionare **Authorize app**:

	![][5]


È ora possibile configurare Twitter Connector per compilare il flusso di lavoro. I tweet recuperati dal trigger Twitter possono essere usati in altre azioni del flusso:

![][6]

In modo analogo, si possono usare le azioni Twitter nel flusso. Selezionare un'azione Twitter e configurare gli input per l'azione:

	![][7]
	![][8]
## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png

<!----HONumber=July15_HO4-->