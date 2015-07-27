<properties pageTitle="Uso di moduli Node.js" description="Informazioni su come usare i moduli Node.js con Siti Web e Servizi cloud di Azure." services="" documentationCenter="nodejs" authors="MikeWasson" manager="wpickett" editor="mollybos"/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="02/19/2015" ms.author="mwasson"/>





# Utilizzo di moduli Node.js con le applicazioni Azure

In questo documento vengono fornite le linee guida per l'utilizzo dei moduli Node.js con le applicazioni ospitate in Azure. Queste indicazioni consentono di garantire che l'applicazione utilizzi una versione specifica di moduli, nonché di utilizzare i moduli nativi con Azure.

Se si ha familiarità con l'uso dei moduli Node.js e dei file **package.json** e **npm-shrinkwrap.json** di seguito è riportato un breve riepilogo degli argomenti illustrati in questo articolo:

* In Siti Web di Azure vengono riconosciuti i file **package.json** e **npm-shrinkwrap.json** ed è possibile installare moduli in base alle voci incluse in tali file.
* In Servizi cloud di Azure è previsto che tutti i moduli vengano installati nell'ambiente di sviluppo e che la directory **node_modules** sia inclusa come parte del pacchetto di distribuzione.

> [AZURE.NOTE]Macchine virtuali di Azure non è incluso in questo articolo, in quanto l'esperienza di distribuzione in una macchina virtuale dipende dal sistema operativo ospitato dalla macchina virtuale.

> [AZURE.NOTE]È possibile abilitare il supporto per l'installazione dei moduli tramite i file **package.json** o **npm-shrinkwrap.json** in Azure, ma questa operazione richiede la personalizzazione degli script predefiniti utilizzati dai progetti servizio cloud. Per un esempio di come eseguire questa operazione, vedere il post di blog relativo alla configurazione dell'[attività di avvio di Azure per l'esecuzione dell'installazione di npm per evitare la distribuzione dei moduli Node](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure)

##Moduli Node.js

I moduli sono pacchetti JavaScript caricabili che forniscono funzionalità specifiche per l'applicazione. Un modulo è in genere installato tramite lo strumento da riga di comando **npm**, tuttavia, alcuni di essi, come ad esempio il modulo http, sono forniti come parte del pacchetto Node.js di base.

Quando i moduli sono installati, vengono archiviati nella directory **node_modules** nella radice della struttura di directory dell'applicazione. Ogni modulo all'interno della directory **node_modules** mantiene la relativa directory **node_modules** che contiene i moduli da cui dipende e questo si ripete per tutti i moduli fino alla fine della catena delle dipendenze. Questo consente a ogni modulo installato di avere requisiti di versione specifici per i moduli da cui dipende, anche se ciò può determinare una struttura di directory molto grande.

Durante la distribuzione della directory **node_modules** come parte dell'applicazione, le dimensioni della distribuzione risulteranno maggiori rispetto a quando si utilizza un file **package.json** o **npm-shrinkwrap.json**, ma questa soluzione garantisce che la versione dei moduli utilizzata in produzione sia la stessa di quella utilizzata in fase di sviluppo.

###Moduli nativi

Sebbene la maggior parte dei moduli sia semplicemente costituita da file JavaScript in testo normale, alcuni di essi sono immagini binarie specifiche della piattaforma. Questi moduli vengono compilati in fase di installazione, in genere tramite Python e node-gyp. Poiché Servizi cloud di Azure si basa sulla distribuzione della cartella **node_modules** come parte dell'applicazione, qualsiasi modulo nativo incluso come parte dei moduli installati può funzionare in un servizio cloud purché sia stato installato e compilato in un sistema di sviluppo Windows.

Siti Web di Azure non supporta tutti i moduli nativi e potrebbe non riuscire a compilare quelli con prerequisiti molto specifici. Mentre alcuni moduli più diffusi, come MongoDB, hanno dipendenze native facoltative e funzionano anche senza di esse, due soluzioni alternative hanno fornito risultati positivi con quasi tutti i moduli nativi disponibili attualmente:

* Eseguire **npm install** in un computer Windows con installati tutti i prerequisiti del modulo nativo. Distribuire quindi la cartella **node_modules** creata come parte dell'applicazione in Siti Web di Azure.
* È possibile configurare Siti Web di Azure per l'esecuzione di script bash o della shell durante la distribuzione, offrendo la possibilità di eseguire comandi personalizzati per configurare la modalità di esecuzione di **npm install**. Per un video che illustra come eseguire questa operazione, vedere l'articolo relativo agli [script di distribuzione di un sito Web personalizzato con Kudu].

###Usare un file package.json

Il file **package.json** consente di specificare le dipendenze di livello superiore necessarie all'applicazione in modo che la piattaforma di hosting possa installare le dipendenze anziché richiedere che venga inclusa la cartella **node_packages** come parte della distribuzione. Dopo che l'applicazione è stata distribuita, viene utilizzato il comando **npm install** per analizzare il file **package.json** e installare le dipendenze elencate.

In fase di sviluppo è possibile utilizzare i parametri **--save**, **--save-dev** o **--save-optional** durante l'installazione dei moduli per aggiungere automaticamente una voce per il modulo al file **package.json**. Per ulteriori informazioni, vedere [npm-install](https://npmjs.org/doc/install.html).

Un potenziale problema che può essere riscontrato con il file **package.json** è che specifica solo la versione delle dipendenze di livello superiore. Ogni modulo installato può specificare o meno la versione dei moduli da cui dipende, pertanto è possibile che venga specificata una catena delle dipendenze diversa da quella utilizzata in fase di sviluppo.

> [AZURE.NOTE]Durante la distribuzione in un sito Web di Azure, se il file <b>package.json</b> fa riferimento a un modulo nativo, verrà visualizzato un errore simile al seguente durante la pubblicazione dell'applicazione tramite Git:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###Usare un file npm-shrinkwrap.json

Il file **npm-shrinkwrap.json** costituisce un tentativo per risolvere i limiti relativi al controllo delle versioni dei moduli del file **package.json**. Mentre il file **package.json** include solo le versioni per i moduli di livello superiore, il file **npm-shrinkwrap.json** contiene i requisiti della versione per l'intera catena delle dipendenze dei moduli.

Quando l'applicazione è pronta per la produzione, è possibile bloccare i requisiti della versione e creare un file **npm-shrinkwrap.json** tramite il comando **npm shrinkwrap**. In questo modo verranno utilizzate le versioni attualmente installate nella cartella **node_modules** e queste verranno registrate nel file **npm-shrinkwrap.json**. Dopo che l'applicazione è stata distribuita nell'ambiente host, viene utilizzato il comando **npm install** per analizzare il file **npm-shrinkwrap.json** e installare le dipendenze elencate. Per ulteriori informazioni, vedere [npm-install](https://npmjs.org/doc/install.html).

> [AZURE.NOTE]Durante la distribuzione in un sito Web di Azure, se il file<b> npm-shrinkwrap.json</b> fa riferimento a un modulo nativo, verrà visualizzato un errore simile al seguente durante la pubblicazione dell'applicazione tramite Git:

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##Passaggi successivi

Una volta imparato a usare i moduli Node.js con Azure, per altre informazioni vedere gli articoli che illustrano come [specificare la versione di Node.js], [compilare e distribuire un sito Web Node.js] e [usare l'interfaccia della riga di comando di Azure per Mac e Linux].

[specificare la versione di Node.js]: nodejs-specify-node-version-azure-apps.md
[usare l'interfaccia della riga di comando di Azure per Mac e Linux]: xplat-cli.md
[compilare e distribuire un sito Web Node.js]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Publishing with Git]: web-sites-publish-source-control.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[script di distribuzione di un sito Web personalizzato con Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/

<!---HONumber=July15_HO3-->