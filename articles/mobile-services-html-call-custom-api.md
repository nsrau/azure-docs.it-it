<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Chiamare un'API personalizzata da un'applicazione HTML

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/it-it/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/it-it/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

Questo argomento descrive come chiamare un'API personalizzata da un'app HTML. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati]. A questo scopo, verranno eseguiti i passaggi seguenti:

1.  [Definizione dell'API personalizzata][Definizione dell'API personalizzata]
2.  [Aggiornamento dell'app per la chiamata all'API personalizzata][Aggiornamento dell'app per la chiamata all'API personalizzata]
3.  [Test dell'app][Test dell'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati].

## <a name="define-custom-api"></a>Definizione dell'API personalizzata

[WACOM.INCLUDE [mobile-services-create-custom-api][mobile-services-create-custom-api]]

## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app </span>Aggiornamento dell'app per la chiamata all'API personalizzata

1.  Nell'editor di testo aprire il file index.html, individuare l'elemento **button** denominato `buttonRefresh`e aggiungere subito dopo il nuovo elemento seguente:

        <button id="buttonCompleteAll">Complete All</button> 

    Verrà aggiunto un nuovo pulsante nella pagina.

2.  In page.js e dopo la funzione **refreshTodoItems** aggiungere il codice seguente:

        var completeAllTodoItems = function () {
            // Asynchronously call the custom API using the POST method.
            client.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                alert(message);
                refreshTodoItems();
            }, function(error) {
                alert(error.message);
            });
        };

        $('#buttonCompleteAll').click(function () {
            completeAllTodoItems();
        });

    Questo metodo gestisce l'evento **Click** per il nuovo pulsante. Nel client viene chiamato il metodo **invokeApi**, che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori.

## <a name="test-app"></a>Test dell'app

1.  Aggiornare il browser.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

3.  Ripetere il passaggio precedente fino ad aggiungere diversi elementi todo all'elenco.

4.  Fare clic sul pulsante **Complete All**. Verrà visualizzata una finestra di dialogo con messaggio indicante il numero di elementi contrassegnati come completati, quindi la query filtrata verrà eseguita di nuovo cancellando tutti gli elementi dall'elenco.

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app HTML, per altre informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla creazione di API personalizzate.

<!-- Anchors. --> 
<!-- URLs. -->

  [Windows Store C\#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /it-it/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-html-get-started
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-html-get-started-data
  [Definizione dell'API personalizzata]: #define-custom-api
  [Aggiornamento dell'app per la chiamata all'API personalizzata]: #update-app
  [Test dell'app]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
