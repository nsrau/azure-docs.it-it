<properties linkid="develop-nodejs-common-tasks-working-with-node-modules" urlDisplayName="Working with Node.js Modules" pageTitle="Working with Node.js Modules" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title="Using Node.js Modules with Azure applications" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Utilizzo di moduli Node.js con le applicazioni Azure
====================================================

In questo documento vengono fornite le linee guida per l'utilizzo dei moduli Node.js con le applicazioni ospitate in Azure. Queste indicazioni consentono di garantire che l'applicazione utilizzi una versione specifica di moduli, nonché di utilizzare i moduli nativi con Azure.

Se si ha familiarità con l'utilizzo dei moduli Node.js e dei file **package.json** e **npm-shrinkwrap.json** di seguito è riportato un breve riepilogo degli argomenti illustrati in questo articolo:

-   In Siti Web di Azure vengono riconosciuti i file **package.json** e **npm-shrinkwrap.json** ed è possibile installare moduli in base alle voci incluse in tali file.
-   In Servizi cloud di Azure è previsto che tutti i moduli vengano installati nell'ambiente di sviluppo e che la directory **node\_modules** sia inclusa come parte del pacchetto di distribuzione.

**Nota**

Macchine virtuali di Azure non è incluso in questo articolo, in quanto l'esperienza di distribuzione in una macchina virtuale dipende dal sistema operativo ospitato dalla macchina virtuale.

**Nota**

È possibile abilitare il supporto per l'installazione dei moduli tramite i file **package.json** o **npm-shrinkwrap.json** in Azure, ma questa operazione richiede la personalizzazione degli script predefiniti utilizzati dai progetti servizio cloud. Per un esempio di come eseguire questa operazione, vedere il post di blog relativo alla configurazione dell'[attività di avvio di Azure per l'esecuzione dell'installazione di npm per evitare la distribuzione dei moduli Node](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure)

Moduli Node.js
--------------

I moduli sono pacchetti JavaScript caricabili che forniscono funzionalità specifiche per l'applicazione. Un modulo è in genere installato tramite lo strumento da riga di comando **npm**, tuttavia, alcuni di essi, come ad esempio il modulo http, sono forniti come parte del pacchetto Node.js di base.

Quando i moduli sono installati, vengono archiviati nella directory **node\_modules** nella radice della struttura di directory dell'applicazione. Ogni modulo all'interno della directory **node\_modules** mantiene la relativa directory **node\_modules** che contiene i moduli da cui dipende e questo si ripete per tutti i moduli fino alla fine della catena delle dipendenze. Questo consente a ogni modulo installato di avere requisiti di versione specifici per i moduli da cui dipende, anche se ciò può determinare una struttura di directory molto grande.

Durante la distribuzione della directory **node\_modules** come parte dell'applicazione, le dimensioni della distribuzione risulteranno maggiori rispetto a quando si utilizza un file **package.json** o **npm-shrinkwrap.json**, ma questa soluzione garantisce che la versione dei moduli utilizzata in produzione sia la stessa di quella utilizzata in fase di sviluppo.

### Moduli nativi

Sebbene la maggior parte dei moduli sia semplicemente costituita da file JavaScript in testo normale, alcuni di essi sono immagini binarie specifiche della piattaforma. Questi moduli vengono compilati in fase di installazione, in genere tramite Python e node-gyp. Uno dei limiti specifici di Siti Web di Azure è che sebbene riconosca in modo nativo come installare i moduli specificati in un file **package.json** o **npm-shrinkwrap.json**, non fornisce Python o node-gyp e pertanto non è in grado di compilare moduli nativi.

Poiché Servizi cloud di Azure si basa sulla distribuzione della cartella **node\_modules** come parte dell'applicazione, qualsiasi modulo nativo incluso come parte dei moduli installati può funzionare in un servizio cloud purché sia stato installato e compilato in un sistema di sviluppo Windows.

I moduli nativi non sono supportati in Siti Web di Azure. Alcuni moduli, ad esempio JSDOM e MongoDB, presentano dipendenze native facoltative e pertanto funzionano solo con le applicazioni ospitate in Siti Web di Azure.

### Utilizzare un file package.json

Il file **package.json** consente di specificare le dipendenze di livello superiore necessarie all'applicazione in modo che la piattaforma di hosting possa installare le dipendenze anziché richiedere che venga inclusa la cartella **node\_packages** come parte della distribuzione. Dopo che l'applicazione è stata distribuita, viene utilizzato il comando **npm install** per analizzare il file **package.json** e installare le dipendenze elencate.

In fase di sviluppo è possibile utilizzare i parametri **--save**, **--save-dev** o **--save-optional** durante l'installazione dei moduli per aggiungere automaticamente una voce per il modulo al file **package.json**. Per ulteriori informazioni, vedere [npm-install](https://npmjs.org/doc/install.html).

Un potenziale problema che può essere riscontrato con il file **package.json** è che specifica solo la versione delle dipendenze di livello superiore. Ogni modulo installato può specificare o meno la versione dei moduli da cui dipende, pertanto è possibile che venga specificata una catena delle dipendenze diversa da quella utilizzata in fase di sviluppo.

> [WACOM.NOTE] Durante la distribuzione in un sito Web di Azure, se il file **package.json** fa riferimento a un modulo nativo verrà visualizzato un errore simile al seguente durante la pubblicazione dell'applicazione tramite Git:

>        npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>        npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1    

### Utilizzare un file npm-shrinkwrap.json

Il file **npm-shrinkwrap.json** costituisce un tentativo per risolvere i limiti relativi al controllo delle versioni dei moduli del file **package.json**. Mentre il file **package.json** include solo le versioni per i moduli di livello superiore, il file **npm-shrinkwrap.json** contiene i requisiti della versione per l'intera catena delle dipendenze dei moduli.

Quando l'applicazione è pronta per la produzione, è possibile bloccare i requisiti della versione e creare un file **npm-shrinkwrap.json** tramite il comando **npm shrinkwrap**. In questo modo verranno utilizzate le versioni attualmente installate nella cartella **node\_modules** e queste verranno registrate nel file **npm-shrinkwrap.json**. Dopo che l'applicazione è stata distribuita nell'ambiente host, viene utilizzato il comando **npm install** per analizzare il file **npm-shrinkwrap.json** e installare le dipendenze elencate. Per ulteriori informazioni, vedere [npm-install](https://npmjs.org/doc/install.html).

> [WACOM.NOTE] Durante la distribuzione in un sito Web di Azure, se il file **npm-shrinkwrap.json** fa riferimento a un modulo nativo verrà visualizzato un errore simile al seguente durante la pubblicazione dell'applicazione tramite Git:

>        npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>        npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1

Passaggi successivi
-------------------

Dopo avere appreso a utilizzare i moduli Node.js con Azure, per ulteriori informazioni vedere gli articoli che illustrano come [specificare la versione di Node.js](/en-us/develop/nodejs/common-tasks/specifying-a-node-version/), [compilare e distribuire un sito Web Node.js](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/) e [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

