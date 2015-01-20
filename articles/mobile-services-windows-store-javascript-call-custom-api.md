<properties urlDisplayName="Call a custom API from the client" pageTitle="Chiamata di un'API personalizzata da un client JS di Windows Store - Servizi mobili" metaKeywords="" description="Informazioni su come definire un'API personalizzata e quindi chiamarla da un'app di Windows Store che usa Servizi mobili di Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Chiamata di un'API personalizzata dal client

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

In questo argomento viene descritto come chiamare un'API personalizzata da un'app di Windows Store. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag di completamento su `true` per tutti gli elementi Todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata mediante l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definizione dell'API personalizzata]
2. [Aggiornamento dell'app per chiamare l'API personalizzata]
3. [Test dell'app] 

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare [Introduzione a servizi mobili] o [Introduzione ai dati]. Per completare questa esercitazione, è necessario utilizzare Visual Studio 2012 Express per Windows 8.

## <a name="define-custom-api"></a>Definizione dell'API personalizzata

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app di Windows Store, per ulteriori informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Definizione di un'API personalizzata che supporta le notifiche periodiche]
	<br/>Informazioni su come usare un'API personalizzata per supportare le notifiche periodiche in un'app di Windows Store. Se le notifiche periodiche sono abilitate, Windows accederà periodicamente all'endpoint dell'API personalizzata e utilizzerà il file XML restituito, in un formato specifico del riquadro, per aggiornare il riquadro dell'app nel menu Start.

* [Riferimento per gli script del server di Servizi mobili]
  <br/>Informazioni sulla creazione di API personalizzate.

* [Archiviazione di script del server nel controllo del codice sorgente]
  <br/> Informazioni su come usare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo più facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. -->
[Definizione dell'API personalizzata]: #define-custom-api
[Aggiornamento dell'app per chiamare l'API personalizzata]: #update-app
[Test dell'app]: #test-app
[Passaggi successivi]: #next-steps

<!-- URLs. -->
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Definizione di un'API personalizzata che supporta le notifiche periodiche]: /it-it/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
[Archiviazione di script del server nel controllo del codice sorgente]: /it-it/documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=35.2-->
