<properties
   pageTitle="App per le API Facebook Connector"
   description="Come usare il connettore Facebook"
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
   ms.author="andalmia"/>


# Uso del connettore Facebook nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Il connettore Facebook consente di recuperare i nuovi post nella sequenza temporale utente e i nuovi post nella pagina, di pubblicare un post, di pubblicare una foto e così via dall'account Facebook.

- Il trigger del connettore Facebook recupererà i nuovi post nella sequenza temporale utente o i nuovi post nella pagina. Quando un nuovo tweet viene recuperato, attiva una nuova istanza del flusso e passa i dati ricevuti nella richiesta al flusso per l'elaborazione.
- Le azioni del connettore Facebook consentono di pubblicare un post, pubblicare una foto e così via. Queste azioni ricevono una risposta e la rendono disponibile per le azioni nel flusso per l'utilizzo.

## Creazione di un connettore Facebook per l'app per la logica ##
Per usare il connettore Facebook, prima è necessario creare un'istanza dell'app per le API Facebook Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "Facebook Connector".
3.	Configurare il connettore Facebook nel modo seguente:

	![][1]
4.	Fare clic su OK per creare.
5.	Dopo avere creato l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore Facebook.
	- L'istanza dell'app per le API Facebook Connector può essere creata dall'app per la logica.
	- Aprire l'editor dell'app per la logica e fare clic sul connettore Facebook disponibile nella raccolta sulla destra
	- Verrà creata un'istanza dell'app per le API Facebook Connector nello stesso gruppo di risorse in cui è stata creata l'app per la logica.


## Uso del connettore Facebook nell'app per la logica ##
Una volta creata l'app per le API, è possibile usare il connettore Facebook come trigger/azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore Facebook.

	![][2]
2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

	![][3]
3.	Il connettore Facebook apparirà nella sezione "Usati di recente" nella raccolta sulla destra. Selezionarlo.

	![][4]
4.	È possibile trascinare l'app per le API Facebook Connector nell'editor facendo clic su "Facebook Connector" sotto "Usati di recente" nella raccolta sulla destra. Fare clic sul pulsante Autorizza. Immettere le credenziali di Facebook.

	![][5]
5.	Consentire le app per la logica del servizio app di Azure

	![][6] ![][7] ![][8]
6.	Selezionare un trigger.

	![][9]
7.	Ora è possibile usare i post recuperati dal trigger Facebook nelle altre azioni. Nel flusso seguente, quando un post viene pubblicato nella sequenza temporale di Facebook dell'utente, lo stesso post viene inviato alla sequenza temporale di Twitter dell'utente.

	![][10]
8.	In modo simile, è possibile creare flussi usando le azioni del connettore Facebook. Il flusso seguente recupererà un nuovo messaggio pubblicato nel gruppo Yammer e pubblicherà lo stesso post nella pagina Facebook gestita dall'utente

	![][11]

**SUGGERIMENTO**: per ottenere l'ID della pagina di Facebook o l'ID del gruppo di Yammer, cercare il codice numerico nell'URL.

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=August15_HO6-->