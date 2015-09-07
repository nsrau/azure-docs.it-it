<properties
   pageTitle="Uso del connettore POP3 nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore POP3 e usarlo in un'app per la logica in Azure App Service"
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
	ms.author="sameerch"/>


# Uso del connettore POP3 e aggiunta all'app per la logica
Connettersi a un server POP3 per recuperare i messaggi di posta elettronica con allegati.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore POP3 al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.


## Creare il connettore POP3 per l'app per la logica ##
Per usare il connettore POP3, è innanzitutto necessario creare un'istanza dell'app per le API del connettore POP3. È possibile eseguire questa operazione direttamente dall'area di progettazione dell'app per la logica oppure dall'esterno. Per creare un'istanza all'esterno dell'area di progettazione, seguire questa procedura:

1.	Aprire Azure Marketplace dalla home page del portale di Azure.
2.	In "Tutto" cercare "POP3 Connector".
3.	Configurare il connettore POP3 nel modo seguente:

	![][1]
	- **Località**: scegliere la località geografica in cui verrà distribuito il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore
	- **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui risiederà il connettore
	- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
	- **Piano tariffario**: scegliere il piano tariffario per il connettore
	- **Nome**: assegnare un nome al connettore POP3
	- **Impostazioni pacchetto**
		- **Nome utente**: specificare il nome utente per la connessione al server POP3
		- **Password**: specificare la password per la connessione al server POP3
		- **Indirizzo server**: specificare il nome del server o l'indirizzo IP del server POP3
		- **Porta del server**: specificare il numero di porta del server POP3
		- **Abilita SSL**: specificare true per usare il protocollo POP3 tramite un canale SSL/TLS sicuro
4.	Fare clic su Crea. Verrà creato un nuovo POP3 Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare POP3 Connector.

## Usare il connettore POP3 nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore POP3 come trigger per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore POP3.

	![][2]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

	![][3]
3.	Il connettore POP3 viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra. Selezionarlo.

	![][4]
4.	È possibile trascinare l'app per le API del connettore POP3 nell'editor facendo clic su "POP3 Connector".

5.	È ora possibile usare il connettore POP3 nel flusso. Selezionare il trigger "Get Email" e configurare la frequenza e l'intervallo. È possibile usare il messaggio e-mail recuperato dal trigger di POP3 in altre azioni del flusso.
		 

	![][5]
	![][6]
## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=August15_HO9-->