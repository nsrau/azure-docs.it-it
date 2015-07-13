<properties
   pageTitle="App per le API POP3 Connector"
   description="Come usare POP3 Connector"
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


# Uso di POP3 Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

POP3 Connector consente di connettersi a un server POP3 e fornisce un trigger per il recupero di e-mail con allegati.

## Creare POP3 Connector per l'app per la logica ##
Per usare POP3 Connector, è necessario innanzitutto creare un'istanza dell'app per le API POP3 Connector. È possibile eseguire questa operazione direttamente dall'area di progettazione dell'app per la logica oppure dall'esterno. Per creare un'istanza all'esterno dell'area di progettazione, seguire questa procedura:

1.	Aprire Azure Marketplace dalla home page del portale di Azure.
2.	In "Tutto" cercare "POP3 Connector".
3.	Configurare POP3 Connector nel modo seguente:

	![][1] - **Località**: scegliere la località geografica in cui verrà distribuito il connettore - **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore - **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui risiederà il connettore - **Piano di hosting Web**: selezionare o creare un piano di hosting Web - **Piano tariffario**: scegliere il piano tariffario per il connettore - **Nome**: assegnare un nome al connettore POP3 - **Impostazioni pacchetto** - **Nome utente**: specificare il nome utente per la connessione al server POP3 - **Password**: specificare la password per la connessione al server POP3 - **Indirizzo server**: specificare il nome del server o l'indirizzo IP del server POP3 - **Porta del server**: specificare il numero di porta del server POP3 - **Abilita SSL**: specificare true per usare il protocollo POP3 tramite un canale SSL/TLS sicuro
4.	Fare clic su Crea. Verrà creato un nuovo POP3 Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare POP3 Connector.

## Uso di POP3 Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare POP3 Connector come trigger per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di POP3 Connector.

	![][2]
2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

	![][3]
3.	POP3 Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra. Selezionarlo.

	![][4]
4.	È possibile trascinare l'app per le API POP3 Connector nell'editor facendo clic su "POP3 Connector".

5.	È ora possibile usare POP3 Connector nel flusso. Selezionare il trigger "Get Email" e configurare la frequenza e l'intervallo. È possibile usare il messaggio e-mail recuperato dal trigger di POP3 in altre azioni del flusso.
		 
	
	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=62-->