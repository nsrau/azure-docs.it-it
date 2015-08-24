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
   ms.date="08/09/2015"
   ms.author="sameerch"/>


# Uso di Chatter Connector nell'app per la logica

Il Connettore Chatter consente di collegarsi a Chatter ed eseguire attività comuni, ad esempio inviare un messaggio. Ad esempio, è possibile cercare un feed Chatter e quando si individua un oggetto specifico, è possibile pubblicare tale messaggio Chatter a un gruppo di vendite.

È possibile aggiungere il connettore Chatter a dati di flusso di lavoro e a processo di business come parte del flusso di lavoro.

## Trigger e azioni

Un trigger avvia una nuova istanza in base a un evento specifico, come ad esempio l'arrivo di un messaggio Chatter. Un'azione è il risultato, come ad esempio dopo la ricezione di un nuovo messaggio Chatter, quindi inviare il messaggio a un altro gruppo Chatter o un altro sito di social networking, ad esempio Facebook o Twitter.

Il Connettore Chatter può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per il Conettore Chatter sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nuovo messaggio | <ul><li>Pubblica messaggi</li><li>Ricerca</li></ul>


## Creare il Connettore Chatter per l'app per la logica
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "Chatter Connector".
3.	Configurare Chatter Connector nel modo seguente:

	![][1]
	- **Località**: scegliere la località geografica in cui verrà distribuito il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore
	- **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui deve risiedere il connettore
	- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
	- **Piano tariffario**: scegliere un piano tariffario per il connettore
	- **Nome** - assegnare un nome a Connettore Chatter

4. Selezionare **Crea**.


## Uso di Chatter Connector nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare Connettore Chatter come trigger o azione per l'app per la logica. A tale scopo, effettuare l'operazione seguente:

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

<!---HONumber=August15_HO7-->