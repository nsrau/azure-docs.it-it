<properties urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Definire un'API personalizzata che supporta le notifiche periodiche - Servizi mobili di Azure" metaKeywords="" description="Informazioni su come definire un'API personalizzata che supporta le notifiche periodiche nelle app di Windows Store che usano Servizi mobili di Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />
# Definizione di un'API personalizzata che supporta le notifiche periodiche

<div class="dev-center-tutorial-selector"> 
	<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
</div>

In questo argomento viene illustrato come usare un'API personalizzata che supporta le notifiche periodiche in un'app di Windows Store. Se le notifiche periodiche sono abilitate, Windows accederà periodicamente all'endpoint dell'API personalizzata e userà il file XML restituito, in un formato specifico del riquadro, per aggiornare il riquadro dell'app nel menu Start. Per altre informazioni, vedere [Notifiche periodiche]. 

Questa funzionalità verrà aggiunta all'app creata durante l'esercitazione [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente]. A questo scopo, verranno eseguiti i passaggi seguenti:

1. [Definire l'API personalizzata]
2. [Aggiornare l'app per l'abilitazione delle notifiche periodiche]
3. [Testare l'app] 

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente].  

## <a name="define-custom-api"></a>Definire l'API personalizzata

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![][0]

2. Fare clic sulla scheda **API** e quindi su **Crea API personalizzata**.

   	![][1]

   	Verrà visualizzata la finestra di dialogo **Crea nuova API personalizzata**.

3. Impostare **Autorizzazione GET** su **Tutti**, digitare _tiles_ in **Nome API**, quindi fare clic sul segno di spunta.

   	![][2]

	Verrà creata la nuova API con accesso GET pubblico.

4. Fare clic sulla nuova voce tiles nella tabella dell'API.

	![][3]

5. Fare clic sulla scheda **Script** e sostituire il codice esistente con quello riportato di seguito:

		exports.get = function(request, response) {
		    var wns = require('wns');
		    var todoItems = request.service.tables.getTable('TodoItem');
		    todoItems.where({
		        complete: false
		    }).read({
		        success: sendResponse
		    });
		
		    function sendResponse(results) {
		        var tileText = {
		            text1: "My todo list"
		        };
		        var i = 0;
		        console.log(results)
		        results.forEach(function(item) {
		            tileText["text" + (i + 2)] = item.text;
		            i++;
		        });
		        var xml = wns.createTileSquareText01(tileText);
		        response.set('content-type', 'application/xml');
		        response.send(200, xml);
		    }
		};

	This code returns the top 3 uncompleted items from the TodoItem table, then loads them into a JSON object passed to the **wns**.**createTileSquareText01** function. This function returns the following tile template XML:

		<tile>
			<visual>
				<binding template="TileSquareText01">
					<text id="1">My todo list</text>
					<text id="2">Task 1</text>
					<text id="3">Task 2</text>
					<text id="4">Task 3</text>
				</binding>
			</visual>
		</tile>

	Viene usata la funzione **exports.get** perché il client invierà una richiesta GET per accedere al modello di riquadro.

   	<div class="dev-callout"><b>Nota</b>
   		<p>Lo script di questa API personalizzata usa il <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">modulo wns</a> di Node.js, al quale viene fatto riferimento mediante la funzione <strong>require</strong>. Questo modulo è diverso rispetto all'<a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">oggetto wns</a> restituito dall'<a href="http://msdn.microsoft.com/it-it/library/windowsazure/jj554217.aspx">oggetto push</a>, che viene usato per l'invio di notifiche push da script del server.</p>
   	</div>

Si procederà quindi alla modifica dell'app di guida introduttiva per avviare notifiche periodiche che aggiornano il riquadro animato richiedendo la nuova API personalizzata.

<h2><a name="update-app"></a>Aggiornamento dell'app per l'abilitazione delle notifiche periodiche</h2>

1. In Visual Studio premere F5 per eseguire l'app di guida introduttiva creata nell'esercitazione precedente.

2. Verificare che sia visualizzata almeno una voce. Se non sono presenti voci, digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

3. In Visual Studio espandere la cartella `\js` in Esplora soluzioni, aprire il progetto default.js e quindi aggiungere le righe di codice seguenti dopo il codice che definisce la variabile **client**:

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

	Questo codice consente di attivare le notifiche periodiche per richiedere i dati del modello di riquadro dalla nuova API personalizzata **tiles**. Selezionare il valore di **PeriodicUpdateRecurrance** più adatto alla frequenza di aggiornamento dei dati desiderata.

## <a name="test-app"></a>Testare l'app

1. In Visual Studio premere F5 per eseguire di nuovo l'app.

	Verranno abilitate le notifiche periodiche.

2. Passare alla schermata Start, individuare il riquadro animato per l'app e osservare che i dati dell'elemento sono ora visualizzati nel riquadro.

 	![][4]

## Passaggi successivi

Dopo avere creato una notifica periodica, per ulteriori informazioni vedere anche gli articoli relativi a Servizi mobili seguenti:

* [Introduzione alle notifiche push]
	<br/>Le notifiche periodiche sono gestite da Windows e vengono inviate solo in base a una pianificazione predefinita. Le notifiche push possono essere inviate dal servizio mobile su richiesta e possono essere di tipo avviso popup, riquadro e notifiche non elaborate.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla creazione di API personalizzate.

<!-- Anchors. -->
[Definire l'API personalizzata]: #define-custom-api
[Aggiornare l'app per l'abilitazione delle notifiche periodiche]: #update-app
[Testare l'app]: #test-app
[Passaggi successivi]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Notifiche push di Windows e Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Dashboard App personali]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[Aggiungere Servizi mobili a un'app esistente]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Notifiche periodiche]: http://msdn.microsoft.com/it-it/library/windows/apps/jj150587.aspx


<!--HONumber=35.1-->
