<properties
	pageTitle="Come chiamare un'API personalizzata da un client iOS"
	description="Informazioni su come definire un'API personalizzata e quindi chiamarla da un'app per iOS che usa Servizi mobili di Azure."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="krisragh"/>

# Come chiamare un'API personalizzata da un client iOS (back-end JavaScript)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app per iOS. Un'API personalizzata consente di definire endpoint personalizzati con la funzionalità del server, ma non consente di eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura del database. Con un'API personalizzata, è possibile avere maggiore controllo sulla messaggistica, incluse le intestazioni HTTP e il formato del corpo.

## <a name="define-custom-api"></a>Definire un'API personalizzata

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## Passaggi successivi

In questo argomento è stato illustrato come usare il metodo **invokeApi** per chiamare un'API personalizzata abbastanza semplice dall'app per iOS. Per altre informazioni sull'uso del metodo **invokeApi**, vedere il post sull'[API personalizzata in Servizi mobili di Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Riferimento per gli script del server di Servizi mobili] <br/>Ulteriori informazioni sulla creazione di API personalizzate.

* [Archiviazione degli script del server nel controllo del codice sorgente] <br/> Ulteriori informazioni su come utilizzare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[Archiviazione degli script del server nel controllo del codice sorgente]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->