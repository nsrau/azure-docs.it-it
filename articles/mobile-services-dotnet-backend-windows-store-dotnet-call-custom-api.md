	<properties pageTitle="Chiamare un'API personalizzata da un client di Windows Store - Servizi mobili" metaKeywords="" description="Informazioni su come definire un'API personalizzata e chiamarla da un'app per Windows Store che usa Servizi mobili di Microsoft Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Chiamare un'API personalizzata dal client

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento illustra come chiamare un'API personalizzata da un'app di Windows Store. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'uso di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per la chiamata all'API personalizzata]
3. [Testare l'app] 

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili] o [Introduzione ai dati]. 

## <a name="define-custom-api"></a>Definire l'API personalizzata

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>Aggiornare l'app per la chiamata all'API personalizzata

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]


## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app di Windows Store, per altre informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Definizione di un'API personalizzata che supporta le notifiche periodiche]
	<br/>Informazioni su come usare un'API personalizzata che supporta le notifiche periodiche in un'app di Windows Store. Se le notifiche periodiche sono abilitate, Windows accederà periodicamente all'endpoint dell'API personalizzata e userà il file XML restituito, in un formato specifico del riquadro, per aggiornare il riquadro dell'app nel menu Start.

* [Riferimento per gli script del server di Servizi mobili]
  <br/>Altre informazioni sulla creazione di API personalizzate.

* [Archiviare script del server nel controllo del codice sorgente]
  <br/> Altre informazioni su come usare la funzionalità di controllo del codice sorgente per sviluppare e pubblicare in modo facile e sicuro il codice di script dell'API personalizzata.

<!-- Anchors. -->
[Definire l'API personalizzata]: #define-custom-api
[Aggiornare l'app per la chiamata all'API personalizzata]: #update-app
[Testare l'app]: #test-app
[Passaggi successivi]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Definizione di un'API personalizzata che supporta le notifiche periodiche]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications
[Archiviare script del server nel controllo del codice sorgente]: /it-it/documentation/articles/mobile-services-store-scripts-source-control
