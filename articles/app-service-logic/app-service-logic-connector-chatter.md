<properties
   pageTitle="Uso del connettore Chatter nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Chatter e usarlo in un'app per la logica in Azure App Service"
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


# Uso del connettore Chatter e aggiunta all'app per la logica 
Connettersi a Chatter e inviare un messaggio o cercare un feed. Ad esempio, è possibile cercare un feed Chatter e quando si individua un oggetto specifico, è possibile pubblicare tale messaggio Chatter a un gruppo di vendite.

È possibile aggiungere il connettore Chatter al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Trigger e azioni

Un trigger avvia una nuova istanza in base a un evento specifico, come ad esempio l'arrivo di un messaggio Chatter. Un'azione è il risultato, come ad esempio dopo la ricezione di un nuovo messaggio Chatter, quindi inviare il messaggio a un altro gruppo Chatter o un altro sito di social networking, ad esempio Facebook o Twitter.

Il connettore Chatter può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per il connettore Chatter sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
New Message | <ul><li>Post Message</li><li>Search</li></ul>


## Creare il connettore Chatter per l'app per la logica
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Chatter Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà: 
	![][1]  
	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Web hosting plan**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Name**: assegnare un nome al connettore Chatter

4. Selezionare **Create**.


## Usare il connettore Chatter nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare il connettore Chatter come trigger o azione per l'app per la logica. A tale scopo, seguire questa procedura:

1. Nell'app per la logica, aprire **Triggers and Actions** per aprire la finestra di progettazione delle app per la logica e configurare il flusso.

2. Il connettore Chatter è elencato nella raccolta: 
	![][4]
3. Selezionare il connettore Chatter per aggiungerlo automaticamente alla finestra di progettazione. Selezionare **Authorize**, immettere le credenziali e selezionare **Allow**: 
	![][5] 
	![][6] 
	![][7]

È ora possibile usare il connettore Chatter nel flusso. È possibile usare il nuovo messaggio recuperato dal trigger di Chatter ("New Message") in altre azioni del flusso. Configurare le proprietà di input per il trigger Chatter nel modo indicato di seguito:

**Group ID** - Inserire l'ID del gruppo da cui deve essere recuperato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il nuovo messaggio verrà recuperato dal Feed dell'Utente: 
	![][8] 
	![][9]


Allo stesso modo, è possibile usare l'azione Chatter "Post Message" nel flusso per inviare un messaggio. Configurare le proprietà di input per l'azione "Messaggio Post" nel modo indicato di seguito:  
	- **Message Text** - contenuto di testo del messaggio da inviare
	- **Group ID** -specificare l'ID del gruppo in cui deve essere inviato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà inviato al feed dell'utente.
	- 	**File Name**- nome del file da allegare a questo messaggio
	- 	**Content Data** - Dati di contenuto dell'allegato
	- 	**Content Type** - tipo di contenuto dell'allegato
	- 	**Content Transfer Encoding** - Codifica transfer del contenuto dell'allegato ("none"|"base64")
	- 	**Mentions** - L’array dei nomi dell'utente deve essere contrassegnato in questo messaggio
	- 	**Hashtags**- L’array di hashtags deve essere registrato nel messaggio  

![][10]
![][11]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=Oct15_HO1-->