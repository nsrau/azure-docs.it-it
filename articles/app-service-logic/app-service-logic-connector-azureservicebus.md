<properties
   pageTitle="Uso del connettore Azure Service Bus nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore Azure Service Bus e usarlo in un'app per la logica in Azure App Service"
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
	ms.author="andalmia"/>


# Uso del connettore Azure Service Bus e aggiunta all'app per la logica 
Connettersi al bus di servizio di Azure per inviare messaggi alle code e agli argomenti e ricevere messaggi dalle code e dalle sottoscrizioni. È possibile usare i connettori nelle app per la logica come parte di un "flusso di lavoro".

## Trigger e azioni
I trigger sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'azione è il risultato del trigger. Ad esempio, quando un ordine o un nuovo messaggio viene inserito in una coda, viene inviato un avviso o un messaggio.

Il connettore Azure Service Bus può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML.

Per il connettore Azure Service Bus sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Message Available | Send message

## Creare il connettore Azure Service Bus
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Azure Service Bus Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà:  
	![][1]

4. Immettere le impostazioni pacchetto seguenti:

	Nome | Descrizione
--- | ---
Connection String | Stringa di connessione del bus di servizio di Azure. Immettere ad esempio: *Endpoint=sb://[spaziodeinomi].servicebus.windows.net;SharedAccessKeyName=[nome];SharedAccessKey=[chiave]*.
Entity Name | Immettere il nome della coda o dell'argomento.
Subscription Name | Immettere il nome della sottoscrizione da cui ricevere messaggi.

5. Fare clic su **Create**.

## Usare il connettore Service Bus nell'app per la logica
Dopo aver creato il connettore, è possibile usare il connettore Azure Service Bus come trigger o azione per l'app per la logica. A tale scopo, seguire questa procedura:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore Azure Service Bus:  
	![][2]

2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso di lavoro:  
	![][3]

3. Il connettore Azure Service Bus viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra:  
	![][4]

4. È possibile trascinare il connettore Azure Service Bus nell'editor facendo clic su "Azure Service Bus Connector".

5.	È ora possibile usare il connettore Azure Service Bus nel flusso di lavoro. È possibile usare il messaggio recuperato dal trigger del bus di servizio di Azure ("Message Available") in altre azioni del flusso:  
	![][5]

	![][6]

È anche possibile usare l'azione "Send Message" del bus di servizio di Azure:  
	![][7]

![][8]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=August15_HO9-->