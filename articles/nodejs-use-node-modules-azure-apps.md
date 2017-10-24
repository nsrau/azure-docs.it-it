---
title: Uso di moduli Node.js
description: Informazioni su come usare i moduli Node.js con Servizio app di Azure e Servizi cloud.
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 76679ea0ff2c1e88d1923488717a245351437165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizzo di moduli Node.js con le applicazioni Azure
In questo documento vengono fornite le linee guida per l'utilizzo dei moduli Node.js con le applicazioni ospitate in Azure. Queste indicazioni consentono di garantire che l'applicazione utilizzi una versione specifica di moduli, nonché di utilizzare i moduli nativi con Azure.

Se si ha familiarità con l'uso dei moduli Node.js e dei file **package.json** e **npm-shrinkwrap.json**, le informazioni seguenti offrono un breve riepilogo degli argomenti illustrati in questo articolo:

* Nel Servizio app di Azure vengono riconosciuti i file **package.json** e **npm-shrinkwrap.json** ed è possibile installare moduli in base alle voci incluse in tali file.

* In Servizi cloud di Azure è previsto che tutti i moduli vengano installati nell'ambiente di sviluppo e che la directory **node\_modules** sia inclusa come parte del pacchetto di distribuzione. È possibile abilitare il supporto per l'installazione dei moduli tramite i file **package.json** o **npm-shrinkwrap.json** in Servizi cloud, ma questa configurazione richiede la personalizzazione degli script predefiniti usati dai progetti del servizio cloud. Per un esempio di come configurare questo ambiente, vedere [Azure Startup task to run npm install to avoid deploying node modules](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) (Attività di avvio di Azure per l'esecuzione dell'installazione di npm per evitare la distribuzione dei moduli Node)

> [!NOTE]
> Macchine virtuali di Azure non è incluso in questo articolo, perché l'esperienza di distribuzione in una macchina virtuale dipende dal sistema operativo ospitato dalla macchina virtuale.
> 
> 

## <a name="nodejs-modules"></a>Moduli Node.js
I moduli sono pacchetti JavaScript caricabili che forniscono funzionalità specifiche per l'applicazione. I moduli vengono in genere installati usando lo strumento da riga di comando **npm**, tuttavia, alcuni moduli, ad esempio il modulo HTTP, sono forniti nel pacchetto Node.js di base.

Quando i moduli sono installati, vengono archiviati nella directory **node\_modules** nella radice della struttura di directory dell'applicazione. Ogni modulo nella directory **node\_modules** mantiene la propria directory **node\_modules** che contiene i moduli da cui dipende e questo comportamento si ripete per tutti i moduli fino alla fine della catena delle dipendenze. Questo ambiente consente a ogni modulo installato di avere requisiti di versione specifici per i moduli da cui dipende, anche se ciò può determinare una struttura di directory molto grande.

La distribuzione della directory **node\_modules** come parte dell'applicazione aumenta le dimensioni della distribuzione rispetto a quando si usa un file **package.json** o **npm-shrinkwrap.json**, ma questa soluzione garantisce che le versioni dei moduli usate in fase di produzione siano le stesse dei moduli usati in fase di sviluppo.

### <a name="native-modules"></a>Moduli nativi
Sebbene la maggior parte dei moduli sia semplicemente costituita da file JavaScript in testo normale, alcuni di essi sono immagini binarie specifiche della piattaforma. Questi moduli vengono compilati in fase di installazione, in genere tramite Python e node-gyp. Poiché Servizi cloud di Azure si basa sulla distribuzione della cartella **node\_modules** come parte dell'applicazione, qualsiasi modulo nativo incluso come parte dei moduli installati può funzionare in un servizio cloud purché sia stato installato e compilato in un sistema di sviluppo Windows.

Servizio app di Azure non supporta tutti i moduli nativi e potrebbe non riuscire a compilare i moduli con prerequisiti specifici. Mentre alcuni moduli più diffusi, come MongoDB, hanno dipendenze native facoltative e funzionano anche senza di esse, due soluzioni alternative hanno fornito risultati positivi con quasi tutti i moduli nativi disponibili attualmente:

* Eseguire **npm install** in un computer Windows con installati tutti i prerequisiti del modulo nativo. Distribuire quindi la cartella **node\_modules** creata come parte dell'applicazione in Servizio app di Azure.

  * Prima di eseguire la compilazione, verificare che l'installazione locale di Node.js disponga di un'architettura corrispondente e che la versione sia molto simile a quella usata in Azure (i valori correnti possono essere verificati in fase di esecuzione tramite le proprietà **process.arch** e **process.version**).

* È possibile configurare Servizio app di Azure per l'esecuzione di script bash o della shell durante la distribuzione, offrendo la possibilità di eseguire comandi personalizzati e di configurare con precisione la modalità di esecuzione di **npm install** . Per un video che illustra come configurare tale ambiente, vedere [Custom Website Deployment Scripts with Kudu] (Script di distribuzione di un sito Web personalizzato con Kudu).

### <a name="using-a-packagejson-file"></a>Utilizzare un file package.json

Il file **package.json** consente di specificare le dipendenze di livello superiore necessarie all'applicazione in modo che la piattaforma di hosting possa installare le dipendenze invece di richiedere l'aggiunta della cartella **node\_modules** come parte della distribuzione. Dopo che l'applicazione è stata distribuita, viene usato il comando **npm install** per analizzare il file **package.json** e installare le dipendenze elencate.

In fase di sviluppo è possibile usare i parametri **--save**, **--save-dev** o **--save-optional** durante l'installazione dei moduli per aggiungere automaticamente una voce per il modulo al file **package.json**. Per ulteriori informazioni, vedere [npm-install](https://docs.npmjs.com/cli/install).

Un potenziale problema che può essere riscontrato con il file **package.json** è che specifica solo la versione delle dipendenze di livello superiore. Ogni modulo installato può specificare o meno la versione dei moduli da cui dipende, pertanto è possibile che venga specificata una catena delle dipendenze diversa da quella utilizzata in fase di sviluppo.

> [!NOTE]
> Durante la distribuzione di Servizio app di Azure, se il file <b>package.json</b> fa riferimento a un modulo nativo, potrebbe essere visualizzato un errore simile all'esempio seguente durante la pubblicazione dell'applicazione tramite Git:
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Utilizzare un file npm-shrinkwrap.json
Il file **npm-shrinkwrap.json** costituisce un tentativo per risolvere i limiti relativi al controllo delle versioni dei moduli del file **package.json**. Mentre il file **package.json** include solo le versioni per i moduli di livello superiore, il file **npm-shrinkwrap.json** contiene i requisiti della versione per l'intera catena delle dipendenze dei moduli.

Quando l'applicazione è pronta per la produzione, è possibile bloccare i requisiti della versione e creare un file **npm-shrinkwrap.json** usando il comando **npm shrinkwrap**. Questo comando userà le versioni attualmente installate nella cartella **node\_modules**, che verranno registrate nel file **npm-shrinkwrap.json**. Dopo che l'applicazione è stata distribuita nell'ambiente host, viene usato il comando **npm install** per analizzare il file **npm-shrinkwrap.json** e installare le dipendenze elencate. Per ulteriori informazioni, vedere [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Durante la distribuzione di Servizio app di Azure, se il file <b>npm-shrinkwrap.json</b> fa riferimento a un modulo nativo, potrebbe essere visualizzato un errore simile all'esempio seguente durante la pubblicazione dell'applicazione tramite Git:
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Dopo avere imparato a usare i moduli Node.js con Azure, per altre informazioni vedere gli articoli che illustrano come [specificare una versione di Node.js], [compilare e distribuire un'App Web Node.js](app-service/app-service-web-get-started-nodejs.md) e [usare l'interfaccia della riga di comando di Azure per Mac e Linux].

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Node.js](/nodejs/azure/).

[specificare una versione di Node.js]: nodejs-specify-node-version-azure-apps.md
[usare l'interfaccia della riga di comando di Azure per Mac e Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
