<properties
   pageTitle="Uso del connettore SMTP nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o del connettore SMTP e usarlo in un'app per la logica in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>


# Uso del connettore SMTP e aggiunta all'app per la logica
Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati. L'azione "Send Email" del connettore SMTP consente di inviare e-mail a indirizzi specificati.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte di un flusso di lavoro. È possibile aggiungere il connettore SMTP al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.


## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger. Ad esempio, quando si aggiorna un ordine o si aggiunge un nuovo cliente, viene inviato un messaggio di posta elettronica al nuovo cliente

Il connettore SMTP può essere usato come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Attualmente non sono disponibili trigger per questo connettore.

Per il connettore SMTP sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nessuno | Send email


## Creare il connettore SMTP
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "SMTP Connector".
3. **Creare** il connettore.
4. Immettere il nome, il piano di servizio app e altre proprietà.
5. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
	--- | --- | ---
	User Name | Sì | Immettere un nome utente in grado di connettersi al server SMTP.
	Password | Sì | Immettere il nome utente e la password.
	Server Address | Sì | Immettere il nome o l'indirizzo IP del server SMTP.
	Server Port | Sì | Immettere il numero di porta del server SMTP.
	Enable SSL | No | Immettere *true* per usare SMTP su un canale SSL/TLS sicuro.

6. Selezionare **Create**.

> [AZURE.IMPORTANT]Alcuni server SMTP potrebbero riscontrare problemi relativi al funzionamento di questo connettore (SendGrid e Gmail). Se si desidera inviare posta elettronica da SendGrid, il nostro [archivio GitHub](https://github.com/logicappsio/SendGridAPI) dispone di un'API personalizzata in grado di interagire direttamente con le API SendGrid.

## Usare il connettore SMTP nell'app per la logica
Dopo aver creato il connettore, è possibile usare il connettore SMTP come azione per l'app per la logica. A tale scopo, seguire questa procedura:

1.	Creare una nuova app per la logica: ![][2]
2.	Aprire **Triggers e azioni** per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso di lavoro: ![][3]
3.	Il connettore SMTP viene elencato nella sezione relativa alle app per le API nel gruppo di risorse della raccolta a destra. Selezionarlo: ![][4]
4.	Selezionare il connettore SMTP per aggiungerlo automaticamente alla finestra di progettazione del flusso di lavoro.

È ora possibile configurare il connettore SMTP per usarlo nel flusso di lavoro. Selezionare l'azione **Invia posta elettronica** e configurare le proprietà di input:

	Property | Description
	--- | ---
	To | Enter the email address of recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Enter the email address of the carbon copy recipient(s). Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Enter the subject of the email.
	Body | Enter body of the email.
	Is HTML | When this property is set to true, the contents of the body are sent as HTML.
	Bcc | Enter the email address of recipient(s) for blind carbon copy. Separate multiple email addresses using a semicolon (;). For example, enter: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Enter the Importance of the email. The options are Normal, Low, and High.
	Attachments | Attachments to be sent along with the email. It contains the following fields: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

![][5] ![][6]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare le app per la logica di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova le app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=Nov15_HO3-->