<properties 
	pageTitle="Slack Connector"
	description="Introduzione a Slack Connector"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# Uso del connettore Slack nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Il connettore Slack consente di postare messaggi nei canali Slack.

## Creazione di un connettore Slack per l'app per la logica ##
Per usare il connettore Slack, è prima necessario creare un'istanza dell'app per le API connettore Slack. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace usando l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Passare a "Web e dispositivi mobili > Azure Marketplace" e cercare "Slack Connector".
3.	Configurare il connettore Slack nel modo seguente:
 
	![][1] 
	- **Nome** - assegnare un nome al connettore Slack
	- **Piano di servizio app** - selezionare o creare un piano di servizio app
	- **Piano tariffario** - scegliere un piano tariffario per il connettore
	- **Gruppo di risorse** - selezionare o creare il gruppo di risorse in cui deve risiedere il connettore
	- **Sottoscrizione** - scegliere la sottoscrizione in cui deve essere creato il connettore
	- **Località** - scegliere la località geografica in cui si vuole distribuire il connettore

4. Fare clic su Crea. Verrà creato un nuovo connettore Slack
5. Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore Slack.

## Uso del connettore Slack nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore Slack come azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse in cui risiede il connettore Slack. Seguire le istruzioni per [creare una nuova app per la logica].  	
	
2.	Aprire "Trigger e azioni" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.
	
3.	Il connettore Slack verrà visualizzato nella sezione "App per le API in questo gruppo di risorse" della raccolta a destra.
 
	![][2]
4.	È possibile trascinare l'app per le API connettore Slack nell'editor facendo clic su "Slack Connector". Fare clic sul pulsante Autorizza. Fornire le credenziali di Microsoft (se non è stata eseguita la connessione automatica). Seguire la procedura per accedere all'account Slack. Verso la fine, verrà richiesto di concedere al connettore l'autorizzazione per accedere all'account Slack. Fare clic su "Authorizify".
 
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	È ora possibile usare il connettore Slack nel flusso. Attualmente nel connettore Slack non sono disponibili trigger. Le azioni disponibili sono: Pubblica messaggio
 
	![][7]

6.	Esaminiamo l'esperienza di pubblicazione del messaggio. È possibile usare questa azione per pubblicare un messaggio in qualsiasi canale Slack.
 
	![][8]

	Configurare le proprietà di input per l'azione "Post Message" nel modo indicato di seguito:

 - **Testo**: specificare il testo del messaggio da pubblicare
 - **Nome canale**: specificare il canale Slack in cui deve essere caricato il messaggio. Se non viene specificato, il messaggio verrà pubblicato nel canale #general

 	**Proprietà avanzate**
 	- **Nome utente bot**: nome del bot da usare per questo messaggio. Il messaggio verrà pubblicato come "Bot" se non si specifica questo valore.
 	-  **URL icona**: URL relativo all'immagine da usare come icona per il messaggio
 	- **Emoji icona**: emoji da usare come icona per il messaggio. Esegue l'override dell'URL icona
 

7. Per usare il connettore all'esterno di un'app per la logica, è possibile sfruttare le API REST esposte dal connettore. È possibile visualizzare le definizioni delle API selezionando Sfoglia->App per le API->Slack Connector. È ora possibile fare clic su Definizione API Definition nella sezione di riepilogo per visualizzare tutte le API esposte dal connettore.

	![][9]

9. Per informazioni dettagliate sulle API, vedere l'articolo relativo alla [definizione delle API di Slack].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!-- Links -->
[creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md
[definizione delle API di Slack]: https://msdn.microsoft.com/it-it/library/dn708020.aspx
<!--HONumber=52-->
