<properties 
	pageTitle="Chiamare un'API personalizzata da un'app per iOS | Servizi mobili" 
	description="Informazioni su come definire un'API personalizzata e chiamarla da un'app per iOS che usa Servizi mobili di Microsoft Azure." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	Writer="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>


# Chiamare un'API personalizzata dal client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app per iOS. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'uso di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata mediante l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per chiamare l'API personalizzata]
3. [Testare l'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. Per completare questa esercitazione, è necessario disporre di [Mobile Services SDK per iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533), di [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) e di iOS 5.0 o versioni successive.

## <a name="define-custom-api"></a>Definire l'API personalizzata

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app per iOS, per altre informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla creazione di API personalizzate.

* [Archiviare script del server nel controllo del codice sorgente]
  <br/> Informazioni su come usare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo più facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. -->
[Definire l'API personalizzata]: #define-custom-api
[Aggiornare l'app per chiamare l'API personalizzata]: #update-app
[Testare l'app]: #test-app
[Passaggi successivi]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Notifiche Push Windows e Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Archiviare script del server nel controllo del codice sorgente]: /it-it/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
