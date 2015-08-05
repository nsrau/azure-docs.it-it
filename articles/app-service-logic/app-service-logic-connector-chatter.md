<properties
   pageTitle="App per le API Chatter Connector"
   description="Come usare Chatter Connector"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


# Uso di Chatter Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Chatter Connector consente di connettersi a Chatter e di eseguire diverse azioni, ad esempio Post Message e Search, e di attivare un trigger per recuperare nuovi messaggi.

## Creare Chatter Connector per l'app per la logica ##
Per usare Chatter Connector, è necessario innanzitutto creare un'istanza dell'app per le API Chatter Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "Chatter Connector".
3.	Configurare Chatter Connector nel modo seguente:

	![][1]
	- **Località**: scegliere la località geografica in cui verrà distribuito il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore
	- **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui deve risiedere il connettore
	- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
	- **Piano tariffario**: scegliere il piano tariffario per il connettore
	- **Nome**: assegnare un nome al connettore.

4.	Fare clic su Crea. Verrà creato un nuovo Chatter Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare Chatter Connector.

## Uso di Chatter Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare Chatter Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Chatter Connector.

	![][2]
2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

3.	Chatter Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.

	![][4]
4. È possibile trascinare l'app per le API Chatter Connector nell'editor facendo clic su "Chatter Connector" Fare clic sul pulsante Autorizza. Fornire le credenziali. Fare clic su "Consenti".

	![][5]
	![][6]
	![][7]
5.	È ora possibile usare Chatter Connector nel flusso. È possibile usare il nuovo messaggio recuperato dal trigger di Chatter ("New Message") in altre azioni del flusso. Configurare le proprietà di input per il trigger Chatter nel modo indicato di seguito:
	- **Group ID**: specificare l'ID del gruppo da cui deve essere recuperato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il nuovo messaggio verrà recuperato dal feed dell'utente.

  ![][8]
  ![][9]

6. Allo stesso modo, è possibile usare l'azione Chatter "Post Message" nel flusso per inviare un messaggio. Configurare le proprietà di input per l'azione "Post Message" nel modo indicato di seguito:
	- **Message Text**: contenuto di testo del messaggio da inviare.
	- **Group ID**: specificare l'ID del gruppo a cui deve essere inviato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà inviato al feed dell'utente.
	- 	**File Name**: nome del file da allegare al messaggio.
	- 	**Content Data**: dati di contenuto dell'allegato.
	- 	**Content Type**: tipo di contenuto dell'allegato.
	- 	**Content Transfer Encoding**: codifica di trasferimento del contenuto dell'allegato ("none" o "base64").
	- 	**Mentions**: matrice di nomi utente a cui deve essere associato un tag nel messaggio.
	- 	**Hashtags**: matrice di hashtag da inviare insieme al messaggio.

	![][10]
	![][11]

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

<!----HONumber=July15_HO4-->