<properties 
	pageTitle="Chiamare un'API personalizzata da un client HTML - Servizi mobili" 
	description="Informazioni su come definire un'API personalizzata e chiamarla da un'app HTML che usa Servizi mobili di Microsoft Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	Writer="jparrel" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Chiamare un'API personalizzata da un'applicazione HTML

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Questo argomento descrive come chiamare un'API personalizzata da un'app HTML. Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'uso di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

L'API personalizzata creata in questo argomento consente di inviare una singola richiesta POST che imposta il flag completato su `true` per tutti gli elementi todo nella tabella. Senza l'API personalizzata, il client dovrebbe inviare singole richieste per aggiornare il flag per ogni elemento Todo nella tabella.

Questa funzionalità verrà aggiunta all'app creata mediante l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per chiamare l'API personalizzata]
3. [Testare l'app] 

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare [Introduzione a servizi mobili] o [Introduzione ai dati].

## <a name="define-custom-api"></a>Definire l'API personalizzata

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>Aggiornare l'app per chiamare l'API personalizzata</h2>

1. Nell'editor di testo aprire il file index.html, individuare l'elemento **button** denominato `buttonRefresh` e aggiungere subito dopo il nuovo elemento seguente: 

		<button id="buttonCompleteAll">Complete All</button> 

	Verrà aggiunto un nuovo pulsante nella pagina. 

2. In page.js e dopo la funzione **refreshTodoItems** aggiungere il codice seguente:

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

## <a name="test-app"></a>Testare l'app

1. Aggiornare il browser.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

3. Ripetere il passaggio precedente fino ad aggiungere diversi elementi todo all'elenco.

4. Fare clic sul pulsante **Complete All**. Verrà visualizzata una finestra di dialogo con messaggio indicante il numero di elementi contrassegnati come completati, quindi la query filtrata verrà eseguita di nuovo cancellando tutti gli elementi dall'elenco.

## Passaggi successivi

Dopo avere creato un'API personalizzata e averla chiamata dall'app HTML, per altre informazioni, vedere anche gli argomenti relativi a Servizi mobili seguenti:

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla creazione di API personalizzate.

<!-- Anchors. -->
[Definire l'API personalizzata]: #define-custom-api
[Aggiornare l'app per chiamare l'API personalizzata]: #update-app
[Testare l'app]: #test-app
[Passaggi successivi]: #next-steps

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-html-get-started
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-html-get-started-data


<!--HONumber=42-->
