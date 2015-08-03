<properties
   pageTitle="Uso di Azure Service Bus Connector nel servizio app di Azure"
   description="Come usare Azure Service Bus Connector"
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
   ms.date="06/29/2015"
   ms.author="andalmia"/>


# Azure Service Bus Connector

Azure Service Bus Connector consente di inviare messaggi dalle entità del bus di servizio, ad esempio code e argomenti, e di ricevere messaggi dalle entità del bus di servizio, ad esempio code e sottoscrizioni.

## Trigger e azioni
I trigger sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'azione è il risultato del trigger. Ad esempio, quando un ordine o un nuovo messaggio viene inserito in una coda, viene inviato un avviso o un messaggio.

Azure Service Bus Connector può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML.

Per Azure Service Bus Connector sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Message Available | Send message

## Creare Azure Service Bus Connector
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "Azure Service Bus Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà: <br/> ![][1]

4. Immettere le impostazioni pacchetto seguenti:

	Nome | Descrizione
--- | ---
Connection String | Stringa di connessione del bus di servizio di Azure. Immettere ad esempio: *Endpoint=sb://[spaziodeinomi].servicebus.windows.net;SharedAccessKeyName=[nome];SharedAccessKey=[chiave]*.
Entity Name | Immettere il nome della coda o dell'argomento.
Subscription Name | Immettere il nome della sottoscrizione da cui ricevere messaggi.

5. Fare clic su **Crea**.

Dopo aver creato il connettore, è possibile aggiungerlo a un'app per la logica nello stesso gruppo di risorse.

## Uso di Service Bus Connector nell'app per la logica
Dopo aver creato il connettore, è possibile usare Azure Service Bus Connector come trigger o azione per l'app per la logica. A tale scopo, effettuare l'operazione seguente:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Azure Service Bus Connector: <br/> ![][2]

2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso di lavoro: <br/> ![][3]

3. Azure Service Bus Connector verrà visualizzato nella sezione relativa alle app per le API nel gruppo di risorse della raccolta sul lato destro: <br/> ![][4]

4. È possibile trascinare Azure Service Bus Connector nell'editor facendo clic su "Azure Service Bus Connector".

5.	È ora possibile usare Azure Service Bus Connector nel flusso di lavoro. È possibile usare il messaggio recuperato dal trigger di Azure Service Bus ("Message Available") in altre azioni del flusso: <br/> ![][5] <br/> ![][6]

È anche possibile usare l'azione "Send Message" di Azure Service Bus: <br/> ![][7] <br/> ![][8]

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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

<!---HONumber=July15_HO4-->