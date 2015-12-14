<properties
   pageTitle="Uso del connettore Yammer nelle app per la logica | Microsoft Azure App Service"
   description="Come creare e configurare l'app per le API o il connettore Yammer e usarlo in un'app per la logica in Azure App Service"
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
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Uso del connettore Yammer nell'app per la logica #
Connettersi a Yammer ed eseguire un'azione di pubblicazione dei messaggi e un trigger per recuperare nuovi messaggi.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

## Creare il connettore Yammer per l'app per la logica ##
Per usare il connettore Yammer, è innanzitutto necessario creare un'istanza dell'app per le API del connettore Yammer. Attenersi alla procedura riportata di seguito:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Yammer Connector", selezionarlo e fare clic su **Crea**.
3. Configurare Yammer Connector nel modo seguente: ![][1]

	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **App Service plan**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Name**: assegnare un nome al connettore Yammer

4. Fare clic su Create. Viene creato un nuovo connettore Yammer.
5. Dopo aver creato l'istanza dell'app per le API, è possibile creare un'app per la logica per usare il connettore Yammer.

## Usare il connettore Yammer nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore Yammer come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica: ![][2]

2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso: ![][3]

3.	Il connettore Yammer viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra: ![][4]

4. È possibile trascinare l'app per le API del connettore Yammer nell'editor facendo clic su "Yammer Connector". Fare clic sul pulsante Authorize. Fornire le credenziali di Yammer. Fare clic su “Allow”: ![][5] ![][6] ![][7]

È ora possibile usare il connettore Yammer nel flusso.

## Usare il connettore Yammer come trigger

È possibile usare il nuovo messaggio recuperato dal trigger di Yammer ("New Message") in altre azioni del flusso. Configurare le proprietà di input per il trigger Yammer nel modo indicato di seguito:

- **Group ID**: ID del gruppo da cui deve essere recuperato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà recuperato dal feed seguente. L'ID del gruppo può essere recuperato dall'URL del gruppo in Yammer.
		
	Esempio: l'ID del gruppo nell'URL seguente è "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## Usare il connettore Yammer per postare un messaggio

È anche possibile usare il connettore Yammer come azione nelle app per la logica. In primo luogo, specificare un trigger per l'app per la logica oppure selezionare 'Run this logic manually', come illustrato di seguito. Aggiungere il connettore Yammer, concedere le autorizzazioni appropriate e scegliere l'azione "Post Message". Configurare le proprietà di input per l'azione "Post Message" nel modo indicato di seguito:

- **Message Text**: contenuto di testo del messaggio da inviare.
- **Group ID**: specificare l'ID del gruppo a cui deve essere inviato il nuovo messaggio. Se l'ID del gruppo non viene specificato, il messaggio verrà inviato a tutti i feed dell'organizzazione. L'ID del gruppo può essere recuperato dall'URL del gruppo in Yammer.  

	Esempio: l'ID del gruppo nell'URL seguente è "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**Tag Users**: matrice di nomi utente a cui deve essere associato un tag nel messaggio: ![][10] ![][11]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

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

<!---HONumber=AcomDC_1203_2015-->