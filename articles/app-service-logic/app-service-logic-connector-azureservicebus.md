<properties 
   pageTitle="App per le API Azure Service Bus Connector" 
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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Uso di Azure Service Bus Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Azure Service Bus Connector consente di ricevere e inviare messaggi a una coda, un argomento o una sottoscrizione.

## Creare Azure Service Bus Connector per l'app per la logica ##
Per usare Azure Service Bus Connector, è necessario innanzitutto creare un'istanza dell'app per le API Azure Service Bus Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "Azure Service Bus Connector".
3.	Configurare Azure Service Bus Connector come segue:
 
	![][1] - **Località**: scegliere la località geografica in cui verrà distribuito il connettore - **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore - **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui deve risiedere il connettore - **Piano di hosting Web**: selezionare o creare un piano di hosting Web - **Piano tariffario**: scegliere il piano tariffario per il connettore - **Nome**: assegnare un nome al connettore - **Impostazioni pacchetto** - **Stringa di connessione**: stringa di connessione del bus di servizio di Azure. Esempio: Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=[name];SharedAccessKey=[key] - **Nome entità**: nome della coda o dell'argomento - **Nome sottoscrizione**: nome della sottoscrizione da cui ricevere i messaggi.

4.	Fare clic su Crea. Verrà creato un nuovo Azure Service Bus Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare Azure Service Bus Connector. 

## Uso di Azure Service Bus Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare Azure Service Bus Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Azure Service Bus Connector.
 
	![][2]
2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. 
 
	![][3]
3.	Azure Service Bus Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.
 
	![][4]
4. È possibile trascinare l'app per le API Azure Service Bus Connector nell'editor facendo clic su "Azure Service Bus Connector".
 
5.	È ora possibile usare Azure Service Bus Connector nel flusso. È possibile usare il nuovo messaggio recuperato dal trigger di Azure Service Bus ("Message Available") in altre azioni del flusso.
 
	![][5] ![][6] 
6.	In modo analogo è possibile usare l'azione di Azure Service Bus "Send Message" per l'invio di messaggi

	![][7] ![][8]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-azureservicebus/img1.PNG
[2]: ./media/app-service-logic-connector-azureservicebus/img2.PNG
[3]: ./media/app-service-logic-connector-azureservicebus/img3.png
[4]: ./media/app-service-logic-connector-azureservicebus/img4.PNG
[5]: ./media/app-service-logic-connector-azureservicebus/img5.PNG
[6]: ./media/app-service-logic-connector-azureservicebus/img6.PNG
[7]: ./media/app-service-logic-connector-azureservicebus/img7.PNG
[8]: ./media/app-service-logic-connector-azureservicebus/img8.PNG

<!---HONumber=62-->