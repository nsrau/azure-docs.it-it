<properties pageTitle="Introduzione a servizi mobili di Azure per Appcelerator Titanium" metaKeywords="" description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per Appcelerator. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Questa esercitazione illustra come sfruttare Servizi mobili di Mobile nelle applicazioni compilate con Appcelerator.

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app _To do list_ che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato viene usato JavaScript per la logica di business sul lato server.

Per completare questa esercitazione, è necessario disporre di [Appcelerator Titanium].

Se si compila per iOS saranno necessari anche Xcode 5.1 e iOS 7.1 SDK o versioni successive. 

Se si compila per Android saranno necessari anche Android SDK 4.3 o versione successiva.

## <a name="create-new-service"> </a>Creare un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creare una nuova app Appcelerator

1. Nel portale di Servizi mobili selezionare la scheda **Dati**.

2. Fare clic su **Aggiungi una tabella** e creare una nuova tabella denominata **TodoItem**.

3. Scaricare una nuova [applicazione Todolist] Appcelerator e decomprimere il progetto.

4. Se necessario, scaricare e installare [Appcelerator Titanium Studio][Appcelerator Titanium] e l'SDK v3.2.1 o versione successiva. Per eseguire il progetto sono necessari anche Xcode (v 5.0 +) e/o Android SDK (v 4.3 o successiva).

5. Nel portale di Servizi mobili, in **Dashboard** selezionare **Gestisci chiavi** e copiare il valore **Chiave applicazione**.

5. Nel file index.js dell'app sostituire `<---App Name---->` e `<------------APP KEY------------>` con i valori del servizio mobile.

## Eseguire la nuova app Appcelerator ##

La fase finale di questa esercitazione prevede la compilazione e l'esecuzione della nuova app.

1. Aprire Titanium Studio e scegliere **File -> Import** per importare il progetto scaricato in precedenza.

    ![][0]

2.	Nella schermata successiva selezionare **Existing Projects into Workspace** e fare clic su **Next**.

    ![][1]

3.	Nella schermata di selezione del progetto usare l'opzione Browse e individuare il progetto Titanium Azure scaricato dal portale di gestione di Azure.

    ![][2]

4.	Verrà infine visualizzato il progetto selezionato nella sezione dei progetti. Facoltativamente è possibile selezionare l'opzione &quot;Copy projects into workspace&quot;, che consente di copiare il progetto scaricato nell'area di lavoro. Infine, fare clic su Finish per aprire il progetto in Titanium Studio.

    ![][3]

5.	Selezionare la piattaforma (iOS/Android) su cui eseguire il progetto.

    ![][4]

6.	Fare clic su Run per compilare il progetto e avviare l'app nell'emulatore iPhone o nell'emulatore Android.

7.	Selezionare l'opzione desiderata in base alle impostazioni del portale di gestione di Azure.

    ![][5]

8.	Nella schermata successiva fare clic sull'icona con il segno PIÙ (+) e digitare un testo completo e comprensibile, ad esempio &quot;Completare l'esercitazione&quot;, quindi fare clic su Save.<br />

    ![][6]

    ![][7]

Verrà inviata una richiesta POST al nuovo servizio mobile ospitato in Microsoft Azure. I dati della richiesta vengono inseriti nella tabella TodoItem. Gli elementi archiviati nella tabella vengono restituiti dal servizio mobile e i dati vengono visualizzati nell'elenco.

![][8]


>[WACOM.NOTE] È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file TodoService.m.

4. Tornare al portale di gestione e fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItems**.

   	![][9]

   	In questo modo sarà possibile visualizzare i dati inseriti nella tabella dall'app.

   	![][10]


## <a name="next-steps"> </a>Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app.



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Applicazione Todolist]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[Introduzione all'autenticazione]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push

<!--HONumber=35.1-->
