<properties urlDisplayName="Debug Websites (Node)" pageTitle="Come eseguire il debug di Siti Web di Azure in Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Learn how to debug an Azure website in Node.js." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="How to debug a Node.js application in Azure Websites" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





#Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure

Azure offre diagnostica integrata per agevolare il debug di applicazioni Node.js ospitate in Siti Web di Azure. In questo articolo verrà illustrato come abilitare la registrazione di stdout e stderr, visualizzare informazioni sugli errori nel browser, nonché come scaricare e visualizzare i file di log.

La diagnostica per le applicazioni Node.js ospitate in Azure viene fornita da [IISNode]. In questo articolo vengono illustrate le impostazioni più comuni per la raccolta delle informazioni di diagnostica; non sono invece incluse informazioni dettagliate sull'uso di IISNode. Per altre informazioni sull'uso di IISNode, vedere il file [Readme di IISNode] su GitHub.

##<a id="enablelogging"></a>Abilitazione della registrazione

Per impostazione predefinita, in Siti Web di Azure vengono acquisite informazioni diagnostiche solo sulle distribuzioni, ad esempio relative a quando si distribuisce un sito Web tramite Git. Tali informazioni sono utili in caso di problemi durante la distribuzione, ad esempio quando non si riesce a installare un modulo cui viene fatto riferimento in **package.json** oppure se si usa uno script di distribuzione personalizzato.

Per abilitare la registrazione di flussi stdout e stderr, è necessario creare un file **IISNode.yml** nella radice dell'applicazione Node.js e aggiungere quanto segue:

	loggingEnabled: true

In tal modo la registrazione di stderr e stdout dall'applicazione Node.js verrà abilitata.

È inoltre possibile usare il file **IISNode.yml** per controllare se in caso di errore al browser vengono restituiti errori descrittivi o dello sviluppatore. Per abilitare gli errori dello sviluppatore, aggiungere la riga seguente al file **IISNode.yml**:

	devErrorsEnabled: true

Dopo aver abilitato questa opzione, IISNode restituirà gli ultimi 64 KB di informazioni inviate a stderr anziché un errore descrittivo, quale "si è verificato un errore interno del server".

<div class="dev-callout">
<strong>Nota</strong>
<p>devErrorsEnabled è utile per diagnosticare problemi che si verificano durante lo sviluppo, tuttavia se viene abilitato in un ambiente di produzione è possibile che gli eventuali errori di sviluppo vengano inviati agli utenti finali.</p>
</div>

Se il file **IISNode.yml** non esisteva già nell'applicazione, sarà necessario riavviare il sito Web dopo aver pubblicato l'applicazione aggiornata. Se si intende solo modificare le impostazioni in un file **IISNode.yml** già pubblicato, il riavvio non è richiesto.

<div class="dev-callout">
<strong>Nota</strong>
<p>Se il sito Web è stato creato con gli strumenti da riga di comando di Azure o i cmdlet di Azure PowerShell, verrà creato automaticamente un file <strong>IISNode.yml</strong> predefinito.</p>
</div>

Per riavviare il sito Web, selezionarlo nel [portale di gestione di Azure], quindi scegliere il pulsante **RIAVVIA**:

![restart button][restart-button]

Se nell'ambiente di sviluppo sono installati gli strumenti da riga di comando di Azure, è possibile usare il comando seguente per riavviare il sito Web:

	azure site restart [sitename]

<div class="dev-callout">
<strong>Nota</strong>
<p>Anche se loggingEnabled e devErrorsEnabled sono le opzioni di configurazione più usate di IISNode.yml per acquisire informazioni diagnostiche, è possibile usare IISNode.yml per configurare numerose opzioni per l'ambiente host. Per un elenco completo delle opzioni di configurazione, vedere il file <a href="https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml">iisnode_schema.xml</a> .</p>
</div>

##<a id="viewlogs"></a>Accesso ai log

È possibile accedere ai log di diagnostica in tre modi, ovvero usando il protocollo FTP (File Transfer Protocol), scaricando un archivio ZIP oppure sotto forma di flusso aggiornato in diretta del log (noto anche come tail). Per il download dell'archivio ZIP dei file di log o per la visualizzazione del flusso in diretta sono necessari gli strumenti da riga di comando di Azure, che è possibile installare tramite il comando seguente:

	npm install azure-cli -g

Dopo l'installazione, è possibile accedere agli strumenti tramite il comando 'azure'. È prima necessario configurare gli strumenti da riga di comando per l'uso della sottoscrizione di Azure. Per informazioni su come eseguire questa attività, vedere la sezione **Come scaricare e importare impostazioni di pubblicazione** dell'articolo [Come usare gli strumenti da riga di comando].

###FTP

Per accedere alle informazioni diagnostiche tramite FTP, visitare il [portale di Azure**, selezionare il sito Web e scegliere **DASHBOARD**. Nella sezione relativa ai **collegamenti rapidi** fare clic sui collegamenti **REGISTRI DI DIAGNOSTICA FTP** e **REGISTRI DI DIAGNOSTICA FTPS** per accedere ai log tramite il protocollo FTP.

<div class="dev-callout">
<strong>Nota</strong>
<p>Se in precedenza non sono stati configurati nome utente e password per FTP o per la distribuzione, è possibile effettuare questa operazione nella pagina di gestione <strong>Avvio rapido</strong> selezionando <strong>Imposta credenziali di distribuzione</strong>.</p>
</div>

L'URL FTP restituito nel dashboard si riferisce alla directory **LogFiles**, che conterrà le seguenti sottodirectory:

* [Metodo di distribuzione]: se si usa un metodo di distribuzione, ad esempio Git, verrà creata una directory con lo stesso nome, che conterrà le informazioni correlate alle distribuzioni.

* nodejs: contiene le informazioni di stdout e stderr acquisite da tutte le istanze dell'applicazione, quando loggingEnabled è impostato su true.

###Archivio ZIP

Per scaricare un archivio ZIP dei log di diagnostica, usare il seguente comando degli strumenti da riga di comando di Azure:

	azure site log download [sitename]

Nella directory corrente verrà scaricato un file **diagnostics.zip**. Questo archivio contiene la seguente struttura di directory:

* deployments: log delle informazioni sulle distribuzioni dell'applicazione

* LogFiles

	* [Metodo di distribuzione]: se si usa un metodo di distribuzione, ad esempio Git, verrà creata una directory con lo stesso nome, che conterrà le informazioni correlate alle distribuzioni.

	* nodejs: contiene le informazioni di stdout e stderr acquisite da tutte le istanze dell'applicazione, quando loggingEnabled è impostato su true.

###Flusso in diretta (tail)

Per visualizzare un flusso in diretta delle informazioni dei log di diagnostica, usare il seguente comando degli strumenti da riga di comando di Azure:

	azure site log tail [sitename]

Verrà restituito un flusso di eventi log che vengono aggiornati non appena si verificano nel server. Questo flusso restituirà le informazioni relative alla distribuzione, oltre alle informazioni di stdout e stderr, quando loggingEnabled è impostato su true.

##<a id="nextsteps"></a>Passaggi successivi

In questo articolo è stato illustrato come abilitare e accedere alle informazioni di diagnostica in Azure. Queste informazioni sono utili per comprendere problemi che si verificano nell'applicazione, tuttavia è possibile che indichino un problema relativo a un modulo in uso oppure che segnalino che la versione di Node.js usata in Siti Web di Azure è diversa da quella dell'ambiente di distribuzione.

Per informazioni sull'uso di moduli in Azure, vedere [Uso di moduli Node.js con applicazioni Azure].

Per informazioni sulla specifica di una versione di Node.js per l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure].

[IISNode]: https://github.com/tjanczuk/iisnode
[File Readme di IISNode]: https://github.com/tjanczuk/iisnode#readme
[Come usare l'interfaccia della riga di comando multipiattaforma di Azure]: /it-it/documentation/articles/xplat-cli/
[Utilizzo di moduli Node.js con le applicazioni Azure]: /it-it/documentation/articles/nodejs-use-node-modules-azure-apps/
[Specifica di una versione di Node.js in un'applicazione Azure]: /it-it/documentation/articles/nodejs-specify-node-version-azure-apps/
[Portale di gestione di Azure]: https://manage.windowsazure.com/

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
