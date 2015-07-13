<properties 
	writer="ricksal" 
	pageTitle="Chiamare un'API personalizzata da un client Android | Mobile Dev Center" 
	description="Informazioni su come definire un'API personalizzata e chiamarla da un'app per Android che usa Servizi mobili di Microsoft Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="ricksal"/>

# Chiamare un'API personalizzata dal client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app per Android. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag *completed* su `true` per tutti gli elementi todo nella tabella del servizio mobile. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].

## <a name="define-custom-api"></a>Definire l'API personalizzata

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app per Android, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Riferimento per gli script del server di Servizi mobili] <br/>Ulteriori informazioni sulla creazione di API personalizzate.

* [Archiviazione degli script del server nel controllo del codice sorgente] <br/> Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-android-get-started.md
[Introduzione ai dati]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md

[Archiviazione degli script del server nel controllo del codice sorgente]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO1-->