<properties
   pageTitle="App per le API Twilio Connector"
   description="Come usare Twilio Connector"
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


# Uso di Twilio Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Twilio Connector consente di inviare e ricevere SMS dall'account Twilio. Consente anche di recuperare numeri di telefono e dati sull'utilizzo.

## Creare Twilio Connector per l'app per la logica ##
Per usare Twilio Connector, è necessario innanzitutto creare un'istanza dell'app per le API Twilio Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Passare a "App per le API" e cercare "Twilio Connector".
3.	Configurare Twilio Connector nel modo seguente:
 
	![][1]
	- **Località**: scegliere la località geografica in cui verrà distribuito il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore
	- **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui deve risiedere il connettore
	- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
	- **Piano tariffario**: scegliere il piano tariffario per il connettore
	- **Nome**: assegnare un nome al connettore Twilio
	- **Impostazioni pacchetto**
		- **SID account**: identificatore univoco dell'account. È possibile recuperare il SID dell'account in <https://www.twilio.com/user/account/settings>
		- **Token autorizzazione** - token di autorizzazione associato all'account. È possibile recuperare il token di autorizzazione per il proprio account da <https://www.twilio.com/user/account/settings>


4.	Fare clic su Crea. Verrà creato un nuovo Twilio Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare Twilio Connector.

## Uso di Twilio Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare Twilio Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Twilio Connector.

	![][2]
2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

	![][3]
3.	Twilio Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.

	![][4]
4. È possibile trascinare l'app per le API Twilio Connector nell'editor facendo clic su "Twilio Connector".

5.	È ora possibile usare Twilio Connector nel flusso. È possibile usare l'azione "Send Message" nel flusso per inviare un messaggio. Configurare le proprietà di input per l'azione "Send Message" nel modo indicato di seguito:
	- **From Phone Number**: immettere un numero di telefono Twilio abilitato per il tipo di messaggio da inviare. Con questo connettore funzioneranno soltanto numeri di telefono o numeri brevi acquistati da Twilio.
	- **To Phone Number**: numero di telefono di destinazione. Il formato accettato è +, seguito dall'indicativo del paese, quindi dal numero di telefono. Ad esempio, +16175551212. Se si omette il segno +, Twilio userà l'indicativo del paese immesso in 'From Phone Number'.
	- **Text**: testo del messaggio da inviare.
 
	![][5]
	![][6]



	<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=August15_HO6-->