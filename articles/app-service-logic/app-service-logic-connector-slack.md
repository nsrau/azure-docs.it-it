<properties 
	pageTitle="Uso del connettore Slack nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore Slack e usarlo in un'app per la logica in Azure App Service"
	authors="rajeshramabathiran" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="rajram"/>

# Uso del connettore Slack e aggiunta all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione dello schema 2015-08-01-preview, fare clic su [API Slack](../connectors/create-api-slack.md).

Consente di connettersi ai canali Slack e di inviare messaggi al team. I connettori possono essere usati nelle app per la logica come parte di un "flusso di lavoro" per eseguire diverse attività. Quando si usa il connettore Slack nel flusso di lavoro, è possibile ottenere un'ampia gamma di scenari con altri connettori. Ad esempio, è possibile usare il [connettore Facebook](app-service-logic-connector-facebook.md) nel flusso di lavoro per inviare un messaggio al canale Slack.

## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger. Quando ad esempio si aggiorna un ordine, viene inviato un avviso al venditore oppure quando si aggiunge un nuovo cliente, viene inviato un messaggio di benvenuto.

Il connettore Slack può essere usato come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Attualmente non sono disponibili trigger per il connettore Slack.

Per il connettore Slack sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nessuno | Post Message

## Creare il connettore Slack
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "Slack Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà: ![][1] 

4. Fare clic su **Crea**.

## Uso del connettore come azione nell'app per la logica

> [AZURE.IMPORTANT] I connettori e le app per la logica devono essere sempre creati nello stesso gruppo di risorse.

Dopo aver creato il connettore Slack, è possibile aggiungerlo come azione all'app per la logica:

1.	Nell'app per la logica aprire **Triggers and Actions**. [Creare una nuova app per la logica](app-service-logic-create-a-logic-app.md)

2.	Il connettore Slack è elencato nella raccolta a destra: ![][2]

3.	Selezionare il connettore Slack creato in precedenza per aggiungerlo automaticamente all'app per la logica.
4.	Selezionare **Authorize**. Accedere all'account Slack. Verso la fine, viene chiesto di concedere al connettore l'autorizzazione per accedere all'account Slack. Selezionare **Authorizify**: ![][3] ![][4] ![][5] ![][6]  
	
5.	È ora possibile usare il connettore Slack nel flusso. È disponibile l'azione Post Message: ![][7]


Di seguito viene descritto l'uso dell'azione "Post Message". È possibile usare questa azione per pubblicare un messaggio in qualsiasi canale Slack: ![][8]

Configurare le proprietà di input per l'azione "Post Message":

Proprietà | Descrizione
--- | ---
Text | Immettere il testo del messaggio da inviare.
Channel Name | Immettere il canale Slack in cui viene inviato il messaggio Se non si specifica il canale, il messaggio viene inviato a #general.
Advanced Properties | **Bot User name**: nome del bot da usare per questo messaggio. Se non si specifica questo valore, il messaggio viene inviato come "Bot".<p><p>**Icon URL**: URL dell'immagine da usare come icona per il messaggio.<p><p>**Icon Emoji**: emoji da usare come icona per il messaggio. Questa proprietà esegue l'override della proprietà Icon URL.


Per il connettore Slack sono disponibili API REST, quindi è possibile usarlo all'esterno di un'app per la logica. Aprire il connettore Slack e selezionare **API definition**: ![][9]


## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!---HONumber=AcomDC_0224_2016-->