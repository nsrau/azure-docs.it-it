<properties
	pageTitle="Archiviare il codice del progetto di back-end JavaScript nel controllo del codice sorgente | Servizi mobili di Azure"
	description="Informazioni su come archiviare i moduli e i file di script del server in un repository Git locale nel computer."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/15/2015" 
	ms.author="ggailey777"/>

# Archiviare il codice del progetto nel controllo del codice sorgente dei Servizi mobili

> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-store-code-source-control.md)
- [Javascript backend](mobile-services-store-scripts-source-control.md)

Questo argomento descrive come usare il controllo del codice sorgente offerto da Servizi mobili di Azure per archiviare gli script del server. Gli script e altri file di codice JavaScript possono essere innalzati di livello dal repository Git al servizio mobile di produzione. Descrive inoltre come definire codice condiviso che può essere richiesto da più script e come usare il file package.json per aggiungere moduli Node.js al servizio mobile.

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente].

##<a name="enable-source-control"></a>Abilitare il controllo del codice sorgente nel servizio mobile

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installare Git e creare il repository locale

1. Installare Git nel computer locale.

	I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git] per indicazioni specifiche del sistema operativo relative a distribuzioni e installazione.

	> [AZURE.NOTE]
	> In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si usa la versione da riga di comando.

2. Aprire una riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3. Dalla riga di comando passare alla directory in cui verranno archiviati gli script, ad esempio `cd SourceControl`.

4. Usare il seguente comando per creare una copia locale del nuovo repository Git, sostituendo `<your_git_URL>` con l'URL del repository Git per il servizio mobile:

		git clone <your_git_URL>

5. Quando richiesto, digitare il nome utente e la password impostati quando è stato abilitato il controllo del codice sorgente nel servizio mobile. Dopo avere eseguito l'autenticazione, verrà visualizzata una serie di risposte simili a questa:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Passare alla directory da cui è stato eseguito il comando `git clone` e osservare la struttura di directory seguente:

	![4][4]

	In questo caso, è stata creata una nuova directory con il nome del servizio mobile che rappresenta il repository locale per il servizio dati.

7. Aprire la sottocartella .\\service\\table e osservare che contiene un file TodoItem.json che è una rappresentazione JSON delle autorizzazioni per le operazioni sulla tabella TodoItem.

	Dopo avere definito gli script del server su questa tabella, saranno presenti uno o più file denominati <code>TodoItem._&lt;operation&gt;_.js</code> che contengono gli script per una determinata operazione sulla tabella. Gli script dell'utilità di pianificazione e dell'API personalizzata sono conservati in cartelle separate con i rispettivi nomi. Per altre informazioni, vedere [Controllo del codice sorgente].

Ora che il repository locale è stato creato, è possibile apportare modifiche agli script del server ed effettuare il push delle modifiche nel servizio mobile.

##<a name="deploy-scripts"></a>Distribuire i file di script aggiornati nel servizio mobile

1. Passare alla sottocartella .\\service\\table e se non è presente un file todoitem.insert.js crearlo adesso.

2. Aprire il nuovo file todoitem.insert.js in un'editor di testo, incollarvi il codice seguente e salvare le modifiche:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Questo codice consente di scrivere l'elemento inserito nel log. Se il file contiene già codice, aggiungervi semplicemente un codice JavaScript valido, ad esempio una chiamata a `console.log()` e quindi salvare le modifiche.

3. Al prompt dei comandi di Git, digitare il comando seguente per iniziare a monitorare il nuovo file di script:

		$ git add .


4. Digitare il comando seguente per eseguire il commit delle modifiche:

		$ git commit -m "updated the insert script"

5. Digitare il comando seguente per caricare le modifiche nel repository remoto:

		$ git push origin master

	Dovrebbe essere visualizzata una serie di comandi che indica che il commit è stato distribuito nel servizio mobile.

6. Nel portale di gestione, fare clic sulla scheda **Data**, quindi sulla tabella **TodoItem**, selezionare **Script** e infine selezionare l'operazione **Inserisci**. 
7. Si noti che lo script dell'operazione insert visualizzato è lo stesso del codice JavaScript appena caricato nell'archivio.

##<a name="use-npm"></a>Usare codice condiviso e moduli Node.js negli script del server

Servizi mobili offre l'accesso al set completo di moduli Node.js di base che è possibile utilizzare nel codice tramite la funzione **require**. Il servizio mobile può inoltre usare moduli Node.js che non fanno parte del pacchetto Node.js di base ed è perfino possibile definire il codice condiviso personalizzato come moduli Node.js. Per ulteriori informazioni sulla creazione di moduli, vedere la sezione relativa ai [moduli] nella documentazione di riferimento all'API Node.js.

Il metodo consigliato per aggiungere moduli Node.js al servizio mobile consiste nell'aggiunta di riferimenti al file package.json del servizio. Si aggiungerà quindi il modulo Node.js [node-uuid] al servizio mobile aggiornando il file package.json. Quando viene eseguito il push dell'aggiornamento ad Azure, il servizio mobile viene riavviato e il modulo viene installato. Questo modulo verrà quindi utilizzato per creare un nuovo valore GUID per la proprietà **uuid** sugli oggetti inseriti.

2. Passare alla cartella `.\service` del repository Git locale e aprire il file package.json in un editor di testo, quindi aggiungere il campo seguente all'oggetto **dependencies**:

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]Questo aggiornamento del file package.json provocherà un riavvio del servizio mobile dopo il push del commit.

4. Passare alla sottocartella .\\service\\table, aprire il file todoitem.insert.js e modificarlo come illustrato di seguito:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
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
	<br/>Ulteriori informazioni su come utilizzare gli script del server, l'utilità di pianificazione dei processi e le API personalizzate.

+ [Chiamare un'API personalizzata dal client] 
	<br/>Illustra come creare API personalizzate che possono essere chiamate dal client.

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[Controllo del codice sorgente]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introduzione a Servizi mobili]: mobile-services-ios-get-started.md
[Aggiungere Servizi mobili a un'app esistente]: mobile-services-ios-get-started-data.md
[Utilizzo degli script del server in Servizi mobili]: mobile-services-how-to-use-server-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Chiamare un'API personalizzata dal client]: mobile-services-ios-call-custom-api.md
[moduli]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!---HONumber=Sept15_HO3-->