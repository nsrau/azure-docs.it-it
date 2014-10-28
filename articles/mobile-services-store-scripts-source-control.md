<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Archiviazione di script del server nel controllo del codice sorgente

In questo argomento viene illustrato come configurare il controllo del codice sorgente per la prima volta in Servizi mobili di Azure per archiviare gli script del server in un archivio Git. Gli script e altri file di codice JavaScript possono essere innalzati di livello dall'archivio locale al servizio mobile di produzione. Viene inoltre descritto come definire il codice condiviso che può essere richiesto da più script e come caricare i moduli Node.js.

In questa esercitazione vengono descritte le operazioni seguenti:

1.  [Abilitazione del controllo del codice sorgente nel servizio mobile][].
2.  [Installazione di Git e creazione dell'archivio locale][].
3.  [Distribuzione dei file di script aggiornati nel servizio mobile][].
4.  [Utilizzo di codice condiviso e di moduli Node.js negli script del server][].

Per completare l'esercitazione, è necessario avere già creato un servizio mobile in base alle procedure descritte nell'esercitazione [Introduzione a Servizi mobili][] o [Introduzione ai dati][].

## <a name="enable-source-control"></a><span class="short-header">Abilitazione del controllo del codice sorgente</span>Abilitare il controllo del codice sorgente nel servizio mobile

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sul servizio mobile.

    ![][]

2.  Fare clic sulla scheda **Dashboard**, in **Quick glance** fare clic su **Set up source control** e infine su **Yes** per confermare.

    ![][1]

    > [WACOM.NOTE]
    > Il controllo del codice sorgente è una funzionalità di anteprima. È consigliabile eseguire un backup regolare dei file di script, anche se sono archiviati in Servizi mobili.

3.  Specificare **User name**, **New password**, confermare la password e quindi fare clic sul segno di spunta.

    ![][2]

    Nel servizio mobile verrà creato l'archivio Git. Prendere nota delle credenziali fornite, in quanto saranno necessarie per accedere a questo archivio.

4.  Fare clic sulla scheda Configure e osservare i nuovi campi **Source control**.

    ![][3]

    Viene visualizzato l'URL dell'archivio Git. Questo URL sarà necessario per clonare l'archivio nel computer locale.

Ora che il controllo del codice sorgente è stato abilitato nel servizio mobile, sarà necessario utilizzare Git per clonare l'archivio nel computer locale.

## <a name="clone-repo"></a><span class="short-header">Clonazione dell'archivio</span>Installare Git e creare l'archivio locale

1.  Installare Git nel computer locale.

    I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git][] per indicazioni specifiche del sistema operativo relative a distribuzioni e installazione.

    > [WACOM.NOTE]
    > In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si utilizza la versione da riga di comando.

2.  Aprire una riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3.  Dalla riga di comando passare alla directory in cui verranno archiviati gli script, Ad esempio `cd SourceControl`.

4.  Usare il comando seguente per creare una copia locale del nuovo archivio Git, sostituendo `<your_git_URL>` con l'URL dell'archivio Git per il servizio mobile:

        git clone <your_git_URL>

5.  Quando richiesto, digitare il nome utente e la password impostati quando è stato abilitato il controllo del codice sorgente nel servizio mobile. Dopo avere eseguito l'autenticazione, verrà visualizzata una serie di risposte simili a questa:

        remote: Counting objects: 8, done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 8 (delta 1), reused 0 (delta 0)
        Unpacking objects: 100% (8/8), done.

6.  Passare alla directory da cui è stato eseguito il comando `git clone` e osservare la struttura di directory seguente:

    ![4][]

    In questo caso, è stata creata una nuova directory con il nome del servizio mobile che rappresenta l'archivio locale per il servizio dati.

7.  Aprire la sottocartella .\\service\\table e osservare che contiene un file TodoItem.json che è una rappresentazione JSON delle autorizzazioni per le operazioni sulla tabella TodoItem.

    Dopo avere definito gli script del server su questa tabella, saranno presenti uno o più file denominati `TodoItem.<operation>.js` che contengono gli script per una determinata operazione sulla tabella. Gli script dell'utilità di pianificazione e dell'API personalizzata sono conservati in cartelle separate con i rispettivi nomi. Per ulteriori informazioni, vedere [Controllo del codice sorgente][].

Ora che l'archivio locale è stato creato, è possibile apportare modifiche agli script del server ed effettuare il push delle modifiche nel servizio mobile.

## <a name="deploy-scripts"></a><span class="short-header">Distribuzione di script</span>Distribuzione di file di script aggiornati nel servizio mobile

1.  Passare alla sottocartella .\\service\\table e se non è presente un file todoitem.insert.js crearlo adesso.

2.  Aprire il nuovo file todoitem.insert.js in un'editor di testo, incollarvi il codice seguente e salvare le modifiche:

        function insert(item, user, request) {
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Questo codice consente di scrivere l'elemento inserito nel log. Se il file contiene già codice, aggiungervi semplicemente un codice JavaScript valido, ad esempio una chiamata a `console.log()`, quindi salvare le modifiche.

3.  Al prompt dei comandi di Git, digitare il comando seguente per iniziare a monitorare il nuovo file di script:

        $ git add .

4.  Digitare il comando seguente per eseguire il commit delle modifiche:

        $ git commit -m "updated the insert script"

5.  Digitare il comando seguente per caricare le modifiche nell'archivio remoto:

        $ git push origin master

    Dovrebbe essere visualizzata una serie di comandi che indica che il commit è stato distribuito nel servizio mobile.

6.  Di nuovo nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][5]

7.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

    ![][6]

    Si noti che lo script dell'operazione insert visualizzato è lo stesso del codice JavaScript appena caricato nell'archivio.

## <a name="use-npm"></a><span class="short-header">Codice condiviso e moduli</span>Utilizzo di codice condiviso e di moduli Node.js negli script del server

Servizi mobili offre l'accesso al set completo di moduli Node.js di base che è possibile utilizzare nel codice tramite la funzione **require**. Il servizio mobile può inoltre utilizzare moduli Node.js che non fanno parte del pacchetto Node.js di base ed è perfino possibile definire il codice condiviso personalizzato come moduli Node.js. Per ulteriori informazioni sulla creazione di moduli, vedere la sezione relativa ai [moduli][] nella documentazione di riferimento all'API Node.js.

Si procederà quindi all'aggiunta del modulo Node.js [node-uuid][] al servizio mobile utilizzando il controllo del codice sorgente e il gestore di pacchetti di Node.js (NPM). Questo modulo verrà quindi utilizzato per creare un nuovo valore GUID per la proprietà **uuid** sugli oggetti inseriti.

1.  Se non è già stato fatto, installare Node.js nel computer locale seguendo la procedura illustrata nel [sito Web Node.js][].

2.  Passare alla cartella `.\service` dell'archivio Git locale e quindi dal prompt dei comandi eseguire il comando seguente:

        npm install node-uuid

    Il gestore di pacchetti Node.js crea la directory `node_modules` nel percorso corrente e installa il modulo [node-uuid][] nella sottodirectory `\node-uuid`.

    <div class="dev-callout">
<strong>Nota</strong>
<p>Quando <code data-inline="1">node_modules</code> esiste gi&agrave; nella gerarchia di directory, NPM vi creer&agrave; la sottodirectory <code data-inline="1">\node-uuid</code> invece di creare una nuova directory <code data-inline="1">node_modules</code> nell'archivio. In questo caso &egrave; sufficiente eliminare la directory <code data-inline="1">node_modules</code> esistente.</p>
</div>

3.  Passare alla sottocartella .\\service\\table, aprire il file todoitem.insert.js e modificarlo come illustrato di seguito:

        function insert(item, user, request) {
            var uuid = require('node-uuid');
            item.uuid = uuid.v1();
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Questo codice aggiunge alla tabella una colonna uuid e la popola con identificatori GUID univoci.

4.  Come nella sezione precedente, al prompt dei comandi di Git digitare il comando seguente:

        $ git add .
        $ git commit -m "added node-uuid module"
        $ git push origin master

    Verrà aggiunto il nuovo file, verrà effettuato il commit delle modifiche e verrà eseguito il push del nuovo modulo node-uuid e delle modifiche dello script todoitem.insert.js nel servizio mobile.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso ad archiviare gli script nel controllo del codice sorgente. Per ulteriori informazioni sugli script del server e sulle API personalizzate, vedere:

-   [Utilizzo degli script del server in Servizi mobili][]
    Ulteriori informazioni su come usare gli script del server, l'utilità di pianificazione dei processi e le API personalizzate.

-   [Definizione di un'API personalizzata che supporta le notifiche pull][]
    Ulteriori informazioni su come usare le API personalizzate per supportare le notifiche periodiche che aggiornano i riquadri animati in un'app di Windows Store.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Abilitazione del controllo del codice sorgente nel servizio mobile]: #enable-source-control
  [Installazione di Git e creazione dell'archivio locale]: #clone-repo
  [Distribuzione dei file di script aggiornati nel servizio mobile]: #deploy-scripts
  [Utilizzo di codice condiviso e di moduli Node.js negli script del server]: #use-npm
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
  [1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
  [2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
  [3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
  [Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
  [Controllo del codice sorgente]: http://msdn.microsoft.com/it-it/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
  [5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png
  [moduli]: http://nodejs.org/api/modules.html
  [node-uuid]: https://npmjs.org/package/node-uuid
  [sito Web Node.js]: http://nodejs.org/
  [Utilizzo degli script del server in Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-server-scripts
  [Definizione di un'API personalizzata che supporta le notifiche pull]: /it-it/develop/mobile/tutorials/create-pull-notifications-dotnet
