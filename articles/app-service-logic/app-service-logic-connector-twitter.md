<properties
   pageTitle="Uso del connettore Twitter nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Twitter e usarlo in un'app per la logica in Azure App Service"
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
   ms.date="12/17/2015"
   ms.author="sameerch"/>


# Uso del connettore Twitter e aggiunta all'app per la logica
Connettersi al feed di Twitter per inviare e ricevere tweet dalla propria sequenza temporale, dalla sequenza temporale degli amici e dai follower dell'account Twitter. I connettori possono essere usati nelle app per la logica per operazioni di recupero, elaborazione o push di dati nell'ambito di un "flusso di lavoro". Quando si usa il connettore Twitter nel flusso di lavoro, si può ottenere un'ampia gamma di scenari. Ad esempio, è possibile:

- Ottenere nuovi tweet associati a una parola chiave o un testo specificato. Quando viene recuperato un nuovo tweet, questo attiva una nuova istanza del flusso di lavoro e passa i dati al connettore successivo nel flusso di lavoro. Ad esempio, si crea il connettore Twitter e si usa il trigger New Tweet From Search per monitorare #peanutbutterandjelly. Ogni volta che viene rilevato un nuovo tweet per #peanutbutterandjelly, il flusso di lavoro (detto anche app per la logica) viene attivato automaticamente.
- Usando le diverse azioni, ad esempio "Search Tweets", è possibile accettare la risposta e usarla nel flusso di lavoro. Ad esempio, è possibile cercare i tweet in base al nome della società. Quando si trovano, è possibile usare un'app per la logica per scrivere i dati in un database SQL Server. Usare quindi i dati di SQL Server per determinare cosa viene pubblicato su Twitter riguardo alla società. 
- Usare tutti gli operatori nella [Ricerca di Twitter](https://twitter.com/search). Selezionare il collegamento degli **operatori**. Il connettore Twitter supporta tutti gli operatori elencati.


## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio, un nuovo tweet può attivare o avviare un flusso di lavoro o un processo. Un'*azione* è il risultato di un evento. Ad esempio, è possibile cercare un tweet specifico e, quando lo si trova, inviare un messaggio di posta elettronica o aggiornare un database.

connettore Twitter può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML.

Per il connettore Twitter sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
New Tweet From Search | <ul><li>Get User Timeline</li><li>Search Tweets</li><li>Tweet</li><li>Get Mentions Timeline</li><li>Get Home Timeline</li><li>Get Followers</li><li>Get Friends</li><li>Get User Details</li><li>Tweet to User</li><li>Send Direct Message</li></ul>

Il trigger **New Tweet** è stato archiviato. Attualmente è ancora disponibile come operazione avanzata e può essere usato. L'azione **Retweet** è stata rimossa e non è più supportata. Se si usa l'azione Retweet, genera un errore in fase di esecuzione. Di conseguenza, è opportuno rimuovere l'azione Retweet dalle app per la logica.


## Creare il connettore Twitter

> [AZURE.IMPORTANT]Durante la creazione di un connettore Twitter è possibile scegliere di registrare l'app con Twitter e usare le chiavi dell'app con il connettore Twitter. È possibile registrare gratuitamente un'applicazione all'indirizzo [http://apps.twitter.com](http://apps.twitter.com). Durante la registrazione assicurarsi di fornire alcuni URL di callback. Una volta creato il connettore Twitter, è possibile modificare l'URL di callback in un secondo momento. Per creare un connettore saranno necessari una chiave e un segreto di API per Twitter.

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. [Facoltativo] Creare un'applicazione gratuita per Twitter all'indirizzo [http://apps.twitter.com](http://apps.twitter.com).
    * Durante la registrazione dell'app è possibile inserire qualsiasi URL per il sito Web. Specificare un URL di callback (non lasciarlo vuoto). È possibile aggiornarlo in un secondo momento.
2. Nella Schermata iniziale di Azure selezionare **Marketplace**.
3. Cercare "Twitter Connector", selezionarlo e fare clic su **Create**.
4. [Facoltativo] Fare clic su 'Package Settings' e incollare il valore di 'Consumer Key' dall'app Twitter al campo 'clientId'. Incollare il valore di 'Consumer Secret' dall'app Twitter al campo 'clientSecret': ![][10]
5. Immettere altre impostazioni necessarie relative al nome, al servizio app e al gruppo di risorse del connettore.
6.	Fare clic su **Create**.

> [AZURE.NOTE]Per proteggere ulteriormente l’API Twitter con l’URL di reindirizzamento, è possibile usare la [protezione OAUTH](app-service-logic-oauth-security.md).


## Usare il connettore Twitter nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare il connettore Twitter come trigger o azione per l'app per la logica. A tale scopo, seguire questa procedura:

1.	Creare una nuova app per la logica o aprirne una esistente: 
	![][2]
2.	Aprire **Triggers and Actions** per visualizzare la finestra di progettazione delle app per la logica: 
	![][3]
3.	Il connettore Twitter è elencato sul lato destro. Selezionarlo per aggiungerlo automaticamente all'app per la logica: 
	![][4]
4.	Selezionare **Authorize**, immettere le credenziali di Twitter e selezionare **Authorize app**: 
	![][5]


È ora possibile configurare il connettore Twitter per compilare il flusso di lavoro. I tweet recuperati dal trigger Twitter possono essere usati in altre azioni del flusso: 
	![][6]

In modo analogo, si possono usare le azioni Twitter nel flusso. Selezionare un'azione Twitter e configurare gli input per l'azione: 
	![][7] 
	![][8]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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
[9]: ./media/app-service-logic-connector-twitter/settings.PNG
[10]: ./media/app-service-logic-connector-twitter/TwitterAPISettings.png

<!---HONumber=AcomDC_1223_2015-->