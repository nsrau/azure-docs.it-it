<properties 
	pageTitle="Chiamata di un'API personalizzata da un client di Windows Phone - Servizi mobili" 
	description="Informazioni su come definire un'API personalizzata e chiamarla da un'app per Windows Phone che usa Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/06/2014" 
	ms.author="glenga"/>

# Chiamare un'API personalizzata dal client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app di Windows Phone. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'uso di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Aggiungere Servizi mobili a un'app esistente](/it-it/documentation/articles/mobile-services-windows-phone-get-started-data/) . A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per chiamare l'API personalizzata]
3. [Testare l'app] 

Questa esercitazione è basata sull'esempio GetStartedWithData, una semplice app TodoList. Prima di iniziare questa esercitazione, è necessario completare [Aggiungere Servizi mobili a un'app esistente](/it-it/documentation/articles/mobile-services-windows-phone-get-started-data/).

## <a name="define-custom-api"></a>Definire l'API personalizzata

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app per Windows Phone, per altre informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

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
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-push/

[Archiviare script del server nel controllo del codice sorgente]: /it-it/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
