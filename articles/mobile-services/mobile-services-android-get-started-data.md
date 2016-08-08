<properties
	pageTitle="Introduzione ai dati su Android (back-end JavaScript) | Microsoft Azure"
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Android.(back-end JavaScript)"
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Aggiungere Servizi mobili a un'app esistente (back-end JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Come usare la libreria client Android per le app per dispositivi mobili](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).

## Riepilogo

Questo argomento illustra come usare Servizi mobili di Azure per aggiungere dati persistenti a un'app per Android. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e si integrerà l'app con il servizio mobile, in modo che archivi e aggiorni i dati in Servizi mobili di Azure invece che localmente, quindi si userà il portale di Azure classico per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

Questa esercitazione illustra più dettagliatamente in che modo Servizi mobili di Azure può archiviare e recuperare dati da un'app per Android. Vengono pertanto riproposte molte delle procedure già completate nell'esercitazione basata sulla guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili](mobile-services-android-get-started.md).

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357).


- [Azure Mobile Services Android SDK].
- [Ambiente di sviluppo integrato di Android Studio](https://developer.android.com/sdk/index.html), che include Android SDK, e Android 4.2 o versione successiva. Il progetto GetStartedWithData scaricato richiede Android 4.2 o versione successiva invece con Mobile Services SDK è richiesto solo Android 2.2 o versione successiva.

## Codice di esempio

Per visualizzare il codice sorgente completato, passare [qui](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio).

## Scaricare il progetto GetStartedWithData

###Scaricare il codice di esempio

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Esaminare ed eseguire il codice di esempio

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Creare un nuovo servizio mobile nel portale di Azure classico

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Aggiungere una nuova tabella al servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Testare l'app nel nuovo servizio mobile

Ora che l'app è stata aggiornata per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili usando l'emulatore di Android o un telefono Android.

1. Nel menu **Run** scegliere **Run app** per avviare il progetto.

	L'app, compilata tramite Android SDK, che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

5. Come in precedenza, digitare un testo significativo e quindi fare clic su **Add**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul servizio mobile in questione.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]

   	Si noti che la tabella **TodoItem** ora contiene dati, con alcuni valori generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Android è terminata.

## Risoluzione dei problemi

### Verificare la versione di Android SDK

[AZURE.INCLUDE [Verificare l'SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per Android di usare dati in Servizi mobili. Provare a eseguire le esercitazioni per Android seguenti:

* [Introduzione all'autenticazione](mobile-services-android-get-started-users.md) <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push](mobile-services-javascript-backend-android-get-started-push.md) <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->

[portale di Azure classico]: https://manage.windowsazure.com/
[Azure Mobile Services Android SDK]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0727_2016-->