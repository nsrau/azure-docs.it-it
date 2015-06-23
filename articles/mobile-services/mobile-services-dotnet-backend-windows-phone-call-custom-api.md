<properties 
	pageTitle="Chiamare un'API personalizzata da un'app di Windows Phone - Servizi mobili" 
	description="Informazioni su come definire un'API personalizzata e chiamarla da un'app per Windows Phone che usa Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="glenga"/>

# Chiamare un'API personalizzata dal client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app per Windows Phone. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il contrassegno di completamento su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Aggiungere Servizi mobili a un'app esistente](mobile-services-dotnet-backend-windows-phone-get-started-data.md). A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per la chiamata all'API personalizzata]
3. [Testare l'app] 

Questa esercitazione è basata sull'esempio GetStartedWithData, una semplice app TodoList. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

## <a name="define-custom-api"></a>Definire l'API personalizzata

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](mobile-services-windows-phone-call-custom-api.md)]


## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app per Windows Phone, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Riferimento per gli script del server di Servizi mobili] <br/>Ulteriori informazioni sulla creazione di API personalizzate.

* [Archiviazione degli script del server nel controllo del codice sorgente] <br/> Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. -->
[Definire l'API personalizzata]: #define-custom-api
[Aggiornare l'app per la chiamata all'API personalizzata]: #update-app
[Testare l'app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Archiviazione degli script del server nel controllo del codice sorgente]: mobile-services-store-scripts-source-control.md

<!--HONumber=54--> 