<properties
   pageTitle="Uso del connettore Facebook nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Facebook e usarlo in un'app per la logica in Azure App Service"
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


# Uso del connettore Facebook e aggiunta all'app per la logica
Connettersi all'account Facebook per inviare un messaggio o pubblicare una foto. Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Con il connettore Facebook è possibile:

- Usare i trigger per recuperare i nuovi post nel diario dell'utente o i nuovi post nella pagina. Quando un nuovo post viene recuperato, attiva una nuova istanza del flusso e passa i dati ricevuti nella richiesta al flusso per l'elaborazione.
- Usare le azioni che consentono di pubblicare un post, pubblicare una foto e così via. Queste azioni ricevono una risposta e la rendono disponibile per le azioni nel flusso per l'utilizzo.

È possibile aggiungere il connettore Facebook al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Trigger e azioni

Trigger | Azioni
--- | ---
<ul><li>New Post on User Timeline</li><li>New Post on Page</li></ul> | <ul><li>Publish Post</li><li>Publish Photo</li></ul>



## Creare il connettore Facebook per l'app per la logica
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Facebook Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà: ![][1]
4.	Selezionare **Create**.


## Usare il connettore Facebook nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare il connettore Facebook come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Nell'app per la logica, aprire **Triggers and Actions** per aprire la finestra di progettazione delle app per la logica e configurare il flusso: ![][3]
2.	Il connettore Facebook è elencato nella raccolta: ![][4]
3. Selezionare il connettore Facebook per aggiungerlo automaticamente alla finestra di progettazione. Selezionare **Authorize**, immettere le credenziali e selezionare **Allow**: ![][5] ![][6] ![][7] ![][8]
4.	Selezionare un trigger: ![][9]

Ora è possibile usare i post recuperati dal trigger Facebook nelle altre azioni. Nel flusso seguente, quando un nuovo post viene pubblicato nel diario di Facebook dell'utente, lo stesso post viene inviato al diario di Twitter dell'utente: ![][10]

In modo simile, è possibile creare flussi usando le azioni del connettore Facebook. Il flusso seguente recupererà un nuovo messaggio pubblicato nel gruppo Yammer e pubblicherà lo stesso post nella pagina di Facebook gestita dall'utente: ![][11]

> [AZURE.TIP]Per ottenere l'ID della pagina di Facebook o l'ID del gruppo di Yammer, cercare il codice numerico nell'URL.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=Nov15_HO3-->