<properties
   pageTitle="App per le API Yammer Connector"
   description="Come usare Yammer Connector"
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


# Uso di Yammer Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Yammer Connector consente di connettersi a Yammer, di eseguire l'azione Post Message e di attivare un trigger per recuperare un nuovo messaggio.

## Creare Yammer Connector per l'app per la logica ##
Per usare Yammer Connector, è necessario innanzitutto creare un'istanza dell'app per le API Yammer Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "Yammer Connector".
3.	Configurare Yammer Connector nel modo seguente:

 ![][1]

	- **Location** - choose the geographic location where you would like the connector to be deployed
	- **Subscription** - choose a subscription you want this connector to be created in
	- **Resource group** - select or create a resource group where the connector should reside
	- **App Service plan** - select or create a web hosting plan
	- **Pricing tier** - choose a pricing tier for the connector
	- **Name** - give a name for your Yammer Connector

4.	Fare clic su Crea. Verrà creato un nuovo Yammer Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare Yammer Connector.

## Uso di Yammer Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare Yammer Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Yammer Connector.

![][2]

2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

![][3]

3.	Yammer Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.

![][4]

4. È possibile trascinare l'app per le API Yammer Connector nell'editor facendo clic su “Yammer Connector”. Fare clic sul pulsante Autorizza. Fornire le credenziali di Yammer. Fare clic su "Consenti".

![][5]

![][6]

![][7]

È ora possibile usare Yammer Connector nel flusso.

## Usare il connettore Yammer come trigger

1.	 È possibile usare il nuovo messaggio recuperato dal trigger di Yammer ("New Message") in altre azioni del flusso. Configurare le proprietà di input per il trigger Yammer nel modo indicato di seguito:

	- **Group ID**: ID del gruppo da cui deve essere recuperato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà recuperato dal feed seguente. L'ID del gruppo può essere recuperato dall'URL del gruppo in Yammer. Esempio: l'ID del gruppo nell'URL seguente è "5453203" https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203

	![][8]

	![][9]

## Usare il connettore Yammer per postare un messaggio

6.	È anche possibile usare il connettore Yammer come azione nelle app per la logica. In primo luogo, specificare un trigger per l'app per la logica oppure selezionare 'Esegui la logica manualmente', come illustrato sotto. Aggiungere il connettore Yammer, concedere le autorizzazioni appropriate e scegliere l'azione "Post Message". Configurare le proprietà di input per l'azione "Post Message" nel modo indicato di seguito:

	- **Message Text**: contenuto di testo del messaggio da inviare.
	- **Group ID**: specificare l'ID del gruppo a cui deve essere inviato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà inviato a tutti i feed dell'organizzazione. L'ID del gruppo può essere recuperato dall'URL del gruppo in Yammer. Esempio: l'ID del gruppo nell'URL seguente è "5453203" https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Tag Users**: matrice di nomi utente a cui deve essere associato un tag nel messaggio.

	![][10]

	![][11]

	<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=July15_HO4-->