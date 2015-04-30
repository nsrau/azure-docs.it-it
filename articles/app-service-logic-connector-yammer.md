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
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Uso di Yammer Connector nell'app per la logica #

Le app per la logica possono eseguire un'attivazione in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. 

Yammer Connector consente di connettersi a Yammer, di eseguire l'azione Post Message e di attivare un trigger per recuperare un nuovo messaggio.

## Creare Yammer Connector per l'app per la logica ##
Per usare Yammer Connector, è necessario innanzitutto creare un'istanza dell'app per le API Yammer Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "Yammer Connector".
3.	Configurare Yammer Connector nel modo seguente:
 
	![][1]
	- **Location** - Scegliere la località geografica in cui si vuole distribuire il connettore
	- **Subscription** - Scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group** - Selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Web hosting plan** - Selezionare o creare un piano di hosting Web
	- **Pricing tier** - Scegliere un piano tariffario per il connettore
	- **Name** - Assegnare un nome per Yammer Connector 

4.	Fare clic su Create. Verrà creato un nuovo Yammer Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare Yammer Connector. 

## Usare Yammer Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare Yammer Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Yammer Connector.
 
	![][2]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. 
 
	![][3]
3.	Yammer Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.
 
	![][4]
4. È possibile trascinare l'app per le API Yammer Connector nell'editor facendo clic su "Yammer Connector". Fare clic sul pulsante Authorize. Fornire le credenziali di Yammer. Fare clic su "Allow".
 
	![][5]
	![][6]
	![][7]
5.	È ora possibile usare Yammer Connector nel flusso. È possibile usare il nuovo messaggio recuperato dal trigger di Yammer ("New Message") in altre azioni del flusso. Configurare le proprietà di input per il trigger Yammer nel modo indicato di seguito:

	- **Group ID**: ID del gruppo da cui deve essere recuperato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà recuperato dal feed seguente. L'ID del gruppo può essere recuperato dall'URL del gruppo in Yammer.
		Esempio: l'ID del gruppo nell'URL seguente è "5453203"
		https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
 
	![][8]
	![][9]

	

6.	Allo stesso modo, è possibile usare l'azione Yammer "Post Message" nel flusso per inviare un messaggio. Configurare le proprietà di input per l'azione "Post Message" nel modo indicato di seguito:
	- **Message Text** - Contenuto di testo del messaggio da inviare
	- **Group ID** - Specificare l'ID del gruppo a cui deve essere inviato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà inviato a tutti i feed dell'organizzazione. L'ID del gruppo può essere recuperato dall'URL del gruppo in Yammer. 
	Esempio: l'ID del gruppo nell'URL seguente è "5453203"
	https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Tag Users** - Matrice di nomi utente a cui deve essere associato un tag nel messaggio. 
	- 	**Attachments** - Allegati da inviare insieme al messaggio. Un allegato contiene i seguenti campi:
		- File Name
		- Content
		- Content transfer Encoding
		- Content Type

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

<!--HONumber=52-->