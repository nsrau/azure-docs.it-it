<properties urlDisplayName="Store JavaScript project code in source control" pageTitle="Archiviare il codice del progetto nel controllo del codice sorgente - Servizi mobili di Azure" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Store project code in source control" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

<div class="dev-center-tutorial-subselector">
	<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>

# Archiviare il codice del progetto nel controllo del codice sorgente

Questo argomento descrive come usare il controllo del codice sorgente offerto da Servizi mobili di Azure per archiviare gli script del server. Gli script e altri file di codice JavaScript possono essere innalzati di livello dal repository Git al servizio mobile di produzione. Descrive inoltre come definire codice condiviso che può essere richiesto da più script e come usare il file package.json per aggiungere moduli Node.js al servizio mobile. 

In questa esercitazione vengono descritte le operazioni seguenti:

1. [Abilitare il controllo del codice sorgente nel servizio mobile].
2. [Installare Git e creare il repository locale].
3. [Distribuire file di script aggiornati nel servizio mobile].
4. [Usare codice condiviso e moduli Node.js negli script del server].

Per completare questa esercitazione, è necessario avere già creato un servizio mobile in base alle procedure contenute nell'[esercitazione introduttiva su Servizi mobili] o su quella per l'[aggiunta di Servizi mobili all'app esistente].

##<a name="enable-source-control"></a>Abilitare il controllo del codice sorgente nel servizio mobile

[WACOM.INCLUDE [mobile-services-enable-source-control](../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installare Git e creare il repository locale

1. Installare Git nel computer locale. 

	I passaggi necessari per installare Git variano a seconda del sistema operativo. Per informazioni aggiuntive specifiche del sistema operativo sulle distribuzioni e l'installazione, vedere la pagina relativa all'[installazione di Git].

	> [WACOM.NOTE]
	> In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si usa la versione da riga di comando.

2. Aprire una riga di comando, come **GitBash** (Windows) o **Bash** (Unix Shell). Nei sistemi operativi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3. Dalla riga di comando passare alla directory in cui verranno archiviati gli script, ad esempio `cd SourceControl`.

4. Usare il comando seguente per creare una copia locale del nuovo repository Git, sostituendo `<your_git_URL>` con l'URL del repository Git per il servizio mobile:

		git clone <your_git_URL>

5. Quando richiesto, digitare il nome utente e la password impostati quando è stato abilitato il controllo del codice sorgente nel servizio mobile. Dopo avere eseguito l'autenticazione, verrà visualizzata una serie di risposte simili a questa:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Passare alla directory da cui è stato eseguito il comando `git clone` e osservare la struttura di directory seguente:

	![4][4]

	In questo caso, è stata creata una nuova directory con il nome del servizio mobile che rappresenta l'archivio locale per il servizio dati. 

7. Aprire la sottocartella .\service\table e osservare che contiene un file TodoItem.json, che è una rappresentazione JSON delle autorizzazioni per le operazioni sulla tabella TodoItem. 

	Dopo avere definito gli script del server su questa tabella, saranno presenti uno o più file denominati <code>TodoItem._&lt;operazione&gt;_.js</code> che contengono gli script per una determinata operazione sulla tabella. Gli script dell'utilità di pianificazione e dell'API personalizzata vengono mantenuti in cartelle separate con i rispettivi nomi. Per altre informazioni, vedere [Controllo codice sorgente].

Ora che l'archivio locale è stato creato, è possibile apportare modifiche agli script del server ed effettuare il push delle modifiche nel servizio mobile.

##<a name="deploy-scripts"></a>Distribuire file di script aggiornati nel servizio mobile.

1. Passare alla sottocartella .\service\table e se non è presente un file todoitem.insert.js, crearlo a questo punto.

2. Aprire il nuovo file todoitem.insert.js in un'editor di testo, incollarvi il codice seguente e salvare le modifiche:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	Questo codice consente di scrivere l'elemento inserito nel log. Se il file contiene già codice, aggiungere semplicemente codice JavaScript valido, ad esempio una chiamata a `console.log()`, quindi salvare le modifiche. 

3. Al prompt dei comandi di Git, digitare il comando seguente per iniziare a monitorare il nuovo file di script:

		$ git add .
	

4. Digitare il comando seguente per eseguire il commit delle modifiche:

		$ git commit -m "updated the insert script"

5. Digitare il comando seguente per caricare le modifiche nell'archivio remoto:

		$ git push origin master
	
	Dovrebbe essere visualizzata una serie di comandi che indica che il commit è stato distribuito nel servizio mobile.

6. Di nuovo dal portale di gestione, fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

	![][5]

3. Fare clic su **Script**, quindi selezionare l'operazione **Inserisci**.

	![][6]

	Si noti che lo script dell'operazione insert visualizzato è lo stesso del codice JavaScript appena caricato nell'archivio.

##<a name="use-npm"></a>Usare codice condiviso e moduli Node.js negli script del server

Servizi mobili offre l'accesso al set completo dei moduli Node.js di base, che possono essere usati nel codice tramite la funzione **require**. Il servizio mobile può inoltre usare moduli Node.js che non fanno parte del pacchetto Node.js di base ed è perfino possibile definire il codice condiviso personalizzato come moduli Node.js. Per altre informazioni sulla creazione di moduli, vedere la sezione relativa ai [moduli][Node.js API Documentation: Modules] nella documentazione di riferimento dell'API di Node.js.

Il metodo consigliato per aggiungere moduli Node.js al servizio mobile consiste nell'aggiunta di riferimenti al file package.json del servizio. Si aggiungerà quindi il modulo Node.js [node-uuid] al servizio mobile aggiornando il file package.json. Quando viene eseguito il push dell'aggiornamento ad Azure, il servizio mobile viene riavviato e il modulo viene installato. Questo modulo viene quindi usato per generare un nuovo valore GUID per la proprietà **uuid** negli elementi inseriti. 

2. Passare alla cartella `.\service` del repository Git locale e aprire il file package.json in un editor di testo.

3. Individuare il codice  

		npm install node-uuid

	Il gestore di pacchetti Node.js crea la directory `node_modules` nel percorso corrente e installa il modulo [node-uuid] nella sottodirectory `\node-uuid`. 

	<div class="dev-callout">
	<strong>Nota</strong>
	<p>Se <code>node_modules</code> è già presente nella gerarchia di directory, il gestore di pacchetti Node.js crea qui la sottodirectory <code>\node-uuid</code> invece di creare una nuova directory <code>node_modules</code> nel repository. In questo caso, eliminare la directory <code>node_modules</code> esistente.</p>
	</div>

4. Passare alla sottocartella .\service\table, aprire il file todoitem.insert.js e modificarlo come illustrato di seguito:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Questo codice aggiunge alla tabella una colonna uuid e la completa con identificatori GUID univoci.

5. Come nella sezione precedente, al prompt dei comandi di Git digitare il comando seguente: 

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	Verrà aggiunto il nuovo file, verrà effettuato il commit delle modifiche e verrà eseguito il push del nuovo modulo node-uuid e delle modifiche dello script todoitem.insert.js nel servizio mobile.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso ad archiviare gli script nel controllo del codice sorgente. Per ulteriori informazioni sugli script del server e sulle API personalizzate, vedere: 

+ [Uso degli script del server in Servizi mobili]
	<br/>Informazioni su come usare script del server, l'utilità di pianificazione e API personalizzate.

+ [Chiamata di un'API personalizzata dal client] 
	<br/> Informazioni su come creare API personalizzate che possono essere chiamate dal client.

<!-- Anchors. -->
[Abilitare il controllo del codice sorgente nel servizio mobile]: #enable-source-control
[Installare Git e creare il repository locale]: #clone-repo
[Distribuire file di script aggiornati nel servizio mobile]: #deploy-scripts
[Usare codice condiviso e moduli Node.js negli script del server]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Sito Web Git]: http://git-scm.com
[Controllo codice sorgente]: http://msdn.microsoft.com/it-it/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-ios-get-started/
[Aggiungere Servizi mobili a un'app esistente]: /it-it/documentation/articles/mobile-services-ios-get-started-data/
[Uso degli script del server in Servizi mobili]: /it-it/documentation/articles/mobile-services-how-to-use-server-scripts/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Chiamata di un'API personalizzata dal client]: /it-it/documentation/articles/mobile-services-ios-call-custom-api/
[Documentazione sull'API Node.js: moduli]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!--HONumber=35.1-->
