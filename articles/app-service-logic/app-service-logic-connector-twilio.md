<properties
   pageTitle="Uso del connettore Twilio nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Twilio e usarlo in un'app per la logica in Azure App Service"
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
   ms.date="02/11/2016"
   ms.author="sameerch"/>


# Uso del connettore Twilio e aggiunta all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione schema 2015-08-01-preview, fare clic su [API Twilio](../connectors/create-api-twilio.md).

Connettersi all'account Twilio per inviare e ricevere messaggi SMS. È inoltre possibile recuperare numeri di telefono e dati di utilizzo. Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore Twilio al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Creare un connettore Twilio per l'app per la logica ##
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Twilio Connector", selezionarlo e fare clic su **Crea**.
3. Configurare il connettore Twilio come indicato di seguito: ![][1]  
	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Name**: assegnare un nome al connettore Twilio
	- **Impostazioni pacchetto**
		- **Account SID**: identificatore univoco dell'account. È possibile recuperare l'identificatore univoco dell'account da <https://www.twilio.com/user/account/settings>
		- **Auth Token**: token di autorizzazione associato all'account. È possibile recuperare il token di autorizzazione per il proprio account da <https://www.twilio.com/user/account/settings>


4.	Fare clic su Create. Viene creato un nuovo connettore Twilio.
5.	Dopo aver creato l'istanza dell'app per le API, è possibile creare un'app per la logica per usare il connettore Twilio.

## Usare il connettore Twilio nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore Twilio come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore: ![][2]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso: ![][3]
3.	Il connettore Twilio viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra: ![][4]
4. È possibile trascinare l'app per le API del connettore Twilio nell'editor facendo clic su "Twilio Connector".

5.	È ora possibile usare il connettore Twilio nel flusso. È possibile usare l'azione "Send Message" nel flusso per inviare un messaggio. Configurare le proprietà di input per l'azione "Send Message" nel modo indicato di seguito:
	- **From Phone Number**: immettere un numero di telefono Twilio abilitato per il tipo di messaggio da inviare. Con questo connettore funzioneranno soltanto numeri di telefono o numeri brevi acquistati da Twilio.
	- **To Phone Number**: numero di telefono di destinazione. Il formato accettato è +, seguito dall'indicativo del paese, quindi dal numero di telefono. Ad esempio, +16175551212. Se si omette il segno +, Twilio userà l'indicativo del paese immesso in 'From Phone Number'.
	- **Text**: testo del messaggio da inviare.

	![][5] ![][6]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=AcomDC_0224_2016-->