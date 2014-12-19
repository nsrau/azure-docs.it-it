<properties urlDisplayName="Store server scripts in source control" pageTitle="Archiviazione di script del server nel controllo del codice sorgente - Servizi mobili di Azure" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />


# Archiviare script del server nel controllo del codice sorgente

Questo argomento illustra come configurare il controllo del codice sorgente per la prima volta in Servizi mobili di Azure per archiviare gli script del server in un repository Git. Gli script e altri file di codice JavaScript possono essere innalzati di livello dall'archivio locale al servizio mobile di produzione. Viene inoltre descritto come definire il codice condiviso che può essere richiesto da più script e come caricare i moduli Node.js. 

In questa esercitazione vengono descritte le operazioni seguenti:

1. [Abilitare il controllo del codice sorgente nel servizio mobile].
2. [Installare Git e creare l'archivio locale].
3. [Distribuire i file di script aggiornati nel servizio mobile].
4. [Usare codice condiviso e moduli Node.js negli script del server].

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].

<h2><a name="enable-source-control"></a>Abilitare il controllo del codice sorgente nel servizio mobile</h2>

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

	![][0]

2. Fare clic sulla scheda **Dashboard**, in **Riepilogo rapido** fare clic su **Impostare il controllo codice sorgente**, e infine su **Sì** per confermare.

	![][1]

	> [WACOM.NOTE]
	> Il controllo del codice sorgente è una funzionalità di anteprima. È consigliabile eseguire un backup regolare dei file di script, anche se sono archiviati in Servizi mobili.

3. Specificare **Nome utente**, **Nuova password**, confermare la password e quindi fare clic sul segno di spunta. 

	![][2]

	Nel servizio mobile verrà creato il repository Git. Prendere nota delle credenziali fornite, in quanto saranno necessarie per accedere a questo repository.

4. Fare clic sulla scheda Configura e osservare i nuovi campi **Controllo codice sorgente**.

	![][3]

	Viene visualizzato l'URL del repository Git. Questo URL sarà necessario per clonare il repository nel computer locale.

Ora che il controllo del codice sorgente è stato abilitato nel servizio mobile, sarà necessario usare Git per clonare l'archivio nel computer locale.

<h2><a name="clone-repo"></a>Installare Git e creare l'archivio locale.</h2>

1. Installare Git nel computer locale. 

	I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git] per indicazioni specifiche del sistema operativo relative a distribuzioni e installazione.

	> [WACOM.NOTE]
	> In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si usa la versione da riga di comando.

2. Aprire una riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

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

7. Aprire la sottocartella .\service\table e osservare che contiene un file TodoItem.json che è una rappresentazione JSON delle autorizzazioni per le operazioni sulla tabella TodoItem. 

	Dopo avere definito gli script del server su questa tabella, saranno presenti uno o più file denominati <code>TodoItem._&lt;operation&gt;_.js</code> che contengono gli script per una determinata operazione sulla tabella. Gli script dell'utilità di pianificazione e dell'API personalizzata sono conservati in cartelle separate con i rispettivi nomi. Per altre informazioni, vedere [Controllo del codice sorgente].

Ora che l'archivio locale è stato creato, è possibile apportare modifiche agli script del server ed effettuare il push delle modifiche nel servizio mobile.

<h2><a name="deploy-scripts"></a>Distribuire i file di script aggiornati nel servizio mobile.</h2>

1. Passare alla sottocartella .\service\table e se non è presente un file todoitem.insert.js crearlo adesso.

2. Aprire il nuovo file todoitem.insert.js in un editor di testo, incollarvi il codice seguente e salvare le modifiche:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	Questo codice consente di scrivere l'elemento inserito nel log. Se il file contiene già del codice, aggiungervi semplicemente del codice JavaScript valido, ad esempio una chiamata a `console.log()` e quindi salvare le modifiche. 

3. Al prompt dei comandi di Git, digitare il comando seguente per iniziare a monitorare il nuovo file di script:

		$ git add .
	

4. Digitare il comando seguente per eseguire il commit delle modifiche:

		$ git commit -m "updated the insert script"

5. Digitare il comando seguente per caricare le modifiche nell'archivio remoto:

		$ git push origin master
	
	Dovrebbe essere visualizzata una serie di comandi che indica che il commit è stato distribuito nel servizio mobile.

6. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

	![][5]

3. Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

	![][6]

	Si noti che lo script dell'operazione insert visualizzato è lo stesso del codice JavaScript appena caricato nell'archivio.

<h2><a name="use-npm"></a>Usare codice condiviso e moduli Node.js negli script del server</h2>
Servizi mobili offre l'accesso al set completo di moduli Node.js di base che è possibile usare nel codice tramite la funzione **require**. Il servizio mobile può inoltre usare moduli Node.js che non fanno parte del pacchetto Node.js di base ed è perfino possibile definire il codice condiviso personalizzato come moduli Node.js. Per altre informazioni sulla creazione di moduli, vedere la sezione [Moduli][Documentazione di riferimento all'API Node.js: Moduli] nella documentazione di riferimento all'API Node.js.

Si procederà quindi all'aggiunta del modulo Node.js [node-uuid] al servizio mobile mediante il controllo del codice sorgente e il gestore di pacchetti di Node.js (NPM). Questo modulo verrà quindi utilizzato per creare un nuovo valore GUID per la proprietà **uuid** sugli oggetti inseriti. 

1. Se non è già stato fatto, installare Node.js nel computer locale seguendo la procedura illustrata nel <a href="http://nodejs.org/" target="_blank">sito Web Node.js</a>. 

2. Passare alla cartella `.\service` del repository Git locale e quindi dal prompt dei comandi eseguire il comando seguente:

		npm install node-uuid

	Il gestore di pacchetti Node.js crea la directory `node_modules` nel percorso corrente e installa il modulo [node-uuid] nella sottodirectory `\node-uuid`. 

	<div class="dev-callout">
	<strong>Nota</strong>
	<p>Quando <code>node_modules</code> esiste già nella gerarchia di directory, NPM vi creerà la sottodirectory <code>\node-uuid</code> invece di creare una nuova directory <code>node_modules</code> nel repository. In questo caso è sufficiente eliminare la directory <code>node_modules</code> esistente.</p>
	</div>

4. Passare alla sottocartella .\service\table, aprire il file todoitem.insert.js e modificarlo come illustrato di seguito:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Questo codice aggiunge alla tabella una colonna uuid e la popola con identificatori GUID univoci.

5. Come nella sezione precedente, al prompt dei comandi di Git digitare il comando seguente: 

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	Verrà aggiunto il nuovo file, verrà effettuato il commit delle modifiche e verrà eseguito il push del nuovo modulo node-uuid e delle modifiche dello script todoitem.insert.js nel servizio mobile.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso ad archiviare gli script nel controllo del codice sorgente. Per altre informazioni sugli script del server e sulle API personalizzate, vedere: 

+ [Utilizzo degli script del server in Servizi mobili]
	<br/>Informazioni su come usare gli script del server, l'utilità di pianificazione e le API personalizzate.

+ [Definizione di un'API personalizzata che supporta le notifiche periodiche] 
	<br/> Informazioni su come usare le API personalizzate per supportare notifiche periodiche che aggiornano i riquadri animati in un'app di Windows Store.

<!-- Anchors. -->
[Abilitare il controllo del codice sorgente nel servizio mobile]: #enable-source-control
[Installare Git e creare l'archivio locale.]: #clone-repo
[Distribuire i file di script aggiornati nel servizio mobile.]: #deploy-scripts
[Usare codice condiviso e moduli Node.js negli script del server]: #use-npm

<!-- Images. -->
[0]: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
[1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
[2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
[3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Sito Web di Git]: http://git-scm.com
[Controllo del codice sorgente]: http://msdn.microsoft.com/it-it/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Utilizzo degli script del server in Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-server-scripts
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-users-js
[WindowsAzure.com]: http://www.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Definizione di un'API personalizzata che supporta le notifiche periodiche]: /it-it/develop/mobile/tutorials/create-pull-notifications-dotnet
[Documentazione sull'API Node.js: Moduli]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid
