<properties 
   pageTitle="App per le API Twitter Connector" 
   description="Come usare il connettore Twitter" 
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
   ms.date="03/20/2015"
   ms.author="adgoda"/>


# Uso del connettore Twitter nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Il connettore Twitter consente di inviare tweet e ricevere tweet dalla timeline, dagli amici e dai follower dal proprio account Twitter.

- Il trigger del connettore Twitter recupererà i nuovi tweet associati alla parola chiave specificata. Quando un nuovo tweet viene recuperato, attiva una nuova istanza del flusso e passa i dati ricevuti nella richiesta al flusso per l'elaborazione. 
- Le azioni del connettore Twitter consentono di inviare tweet, cercarli, fare retweet, recuperare timeline utente e così via. Queste azioni ricevono una risposta e la rendono disponibile per le azioni nel flusso per l'uso.

## Creazione di un connettore Twitter per l'app per la logica ##
Per usare il connettore Twitter, prima è necessario creare un'istanza dell'app per le API Twitter Connector. Attenersi alla procedura riportata di seguito:

1. Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
1. Andare a "App per le API" e cercare "Twitter Connector".
1. Configurare il connettore Twitter nel modo seguente:

	![][1]
4.	Fare clic su OK per creare.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore Twitter. 
	- L'istanza dell'app per le API Twitter Connector può essere creata anche dall'app per la logica. 
	- Aprire l'editor dell'app per la logica e fare clic sul connettore Twitter disponibile nella raccolta sulla destra
	- Verrà creata un'istanza dell'app per le API Twitter Connector nello stesso gruppo di risorse in cui è stata creata l'app per la logica.


## Uso del connettore Twitter nell'app per la logica ##
Una volta creata l'app per le API, si può usare il connettore Twitter come trigger/azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore Twitter.
 	
	![][2]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. 
 	
	![][3]
3.	Il connettore Twitter apparirà nella sezione "Usati di recente" nella raccolta sulla destra. Selezionarlo.
 
	![][4]
4.	È possibile trascinare l'app per le API Twitter Connector nell'editor facendo clic su "Twitter Connector" sotto "Usati di recente" nella raccolta sulla destra. Fare clic sul pulsante Autorizza. Immettere le credenziali di Twitter. Fare clic su "Autorizza app"
 
	![][5]
6.	A questo punto è possibile usare il connettore Twitter nel flusso. Ora è possibile usare i tweet recuperati dal trigger Twitter in altre azioni nel flusso.
 
	![][6]
7.	Analogamente, è possibile usare le azioni Twitter nel flusso. Selezionare un'azione Twitter e configurare gli input per l'azione corrispondente.

	![][7]
	![][8]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/img6.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/img8.png
 

<!----HONumber=62-->