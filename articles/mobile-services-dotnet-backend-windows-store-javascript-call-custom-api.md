<properties 
	pageTitle="Chiamata di un'API personalizzata da un client di Windows Store - Servizi mobili" 
	description="Informazioni su come definire un'API personalizzata e chiamarla da un'app per Windows Store che usa Servizi mobili di Microsoft Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	Writer="glenga" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Chiamare un'API personalizzata dal client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app di Windows Store. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'uso di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata mediante l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per chiamare l'API personalizzata]
3. [Testare l'app] 

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare [Introduzione a servizi mobili] o [Introduzione ai dati]. Per completare questa esercitazione, è necessario usare Visual Studio 2012 Express per Windows 8.

## <a name="define-custom-api"></a>Definire l'API personalizzata

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>Aggiornare l'app per chiamare l'API personalizzata

[AZURE.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]


## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app di Windows Store, per altre informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Definire un'API personalizzata che supporta le notifiche periodiche]
	<br/>Informazioni su come usare un'API personalizzata per supportare le notifiche periodiche in un'app di Windows Store. Se le notifiche periodiche sono abilitate, Windows accederà periodicamente all'endpoint dell'API personalizzata e userà il file XML restituito, in un formato specifico del riquadro, per aggiornare il riquadro dell'app nel menu Start.

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
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Definire un'API personalizzata che supporta le notifiche periodiche]: /it-it/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
[Archiviare script del server nel controllo del codice sorgente]: /it-it/documentation/articles/mobile-services-store-scripts-source-control



<!--HONumber=42-->
