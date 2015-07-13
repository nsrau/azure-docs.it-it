<properties 
	pageTitle="Come utilizzare io.js con Azure applicazione servizio Web App" 
	description="Imparare a utilizzare un'applicazione web nel servizio di applicazione Azure con io.js." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="felixrieseberg" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="04/23/2015" 
	ms.author="mwasson" />

# Come utilizzare io.js con Azure applicazione servizio Web App

Il popolare fork del nodo [io.js] presenta diverse differenze rispetto al progetto Node.js di Joyent, incluso un modello di governance più aperto, un ciclo di rilascio presumibilmente più veloce e un'adozione più rapida delle funzionalità JavaScript nuove e sperimentali.

Benché su [Siti Web di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) siano preinstallate molte versioni di Node.js, è consentito anche un file binario Node.js fornito dall'utente. In questo articolo vengono descritti due metodi consente l'utilizzo di io.js applicazione servizio Web App: l'utilizzo di uno script di distribuzione estesa, che configura automaticamente Azure per utilizzare la versione più recente di io.js disponibili, nonché il caricamento manuale di un io.js binario.

<a id="deploymentscript"></a>
## Utilizzo di uno script di distribuzione

Durante la distribuzione di un'applicazione Node.js, Siti Web di Azure esegue una serie di piccoli comandi per garantire che l'ambiente sia configurato correttamente. Usando uno script di distribuzione, questo processo può essere personalizzato in modo da includere il download e la configurazione di io.js.

Lo [script di distribuzione io.js] è disponibile su GitHub. Per abilitare io.js nel proprio sito Web, è sufficiente copiare **.deployment**, **deploy.cmd** e **IISNode.yml** nella radice della cartella dell'applicazione e distribuirli in Siti Web di Azure.

Il primo file, **.deployment**, indica a Siti Web di Azure di eseguire **deploy.cmd** durante la distribuzione. Questo script esegue tutti i passaggi abituali per un applicazione Node.js, ma Scarica anche la versione più recente di io.js. Infine, **IISNode.yml** configura Siti Web di Azure in modo da usare il file binario io.js appena scaricato anziché un file binario Node.js preinstallato.

> [AZURE.NOTE]Per aggiornare il file binario io.js usato, eseguire nuovamente la distribuzione dell'applicazione; lo script scaricherà una nuova versione di io.js ogni volta che l'applicazione viene distribuita.

<a id="manualinstallation"></a>
## Utilizzo dell'installazione manuale

L'installazione manuale di una versione personalizzata di io.js prevede solo due passaggi. Innanzitutto, scaricare il file binario **win-x64** direttamente dalla [distribuzione io.js]. Sono necessari due file: **iojs.exe** e **iojs.lib**. Salvare entrambi i file in una cartella all'interno del proprio sto Web, ad esempio in **bin/iojs**.

Per configurare Siti Web di Azure in modo da usare **iojs.exe** anziché una versione del nodo preinstallata, creare un file **IISNode.yml** nella directory principale dell'applicazione e aggiungere la riga seguente.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## Passaggi successivi

In questo articolo si è appreso come usare io.js con Siti Web di Azure, usando sia gli script di distribuzione forniti sia l'installazione manuale.

> [AZURE.NOTE]io.js è in fase di intenso sviluppo e viene aggiornato più di frequente rispetto a Node.js. Una serie di moduli Node.js potrebbe non funzionare con[ io.js; consultare io.js su GitHub] per la risoluzione dei problemi.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

[io.js]: https://iojs.org
[distribuzione io.js]: https://iojs.org/dist/
[ io.js; consultare io.js su GitHub]: https://github.com/iojs/io.js
[script di distribuzione io.js]: https://github.com/felixrieseberg/iojs-azure
 

<!---HONumber=62-->