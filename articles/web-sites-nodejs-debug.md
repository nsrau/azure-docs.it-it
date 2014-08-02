<properties linkid="dev-nodejs-how-to-debug-website" urlDisplayName="Debug Websites (Node)" pageTitle="How to Debug Azure Websites in Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Learn how to debug an Azure website in Node.js." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="How to debug a Node.js application in Azure Web Sites" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure
======================================================================

Azure offre diagnostica integrata per agevolare il debug di applicazioni Node.js ospitate in Siti Web di Azure. In questo articolo verrà illustrato come abilitare la registrazione di stdout e stderr, visualizzare informazioni sugli errori nel browser, nonché come scaricare e visualizzare i file di log.

La diagnostica per le applicazioni Node.js ospitate in Azure viene fornita da [IISNode](https://github.com/tjanczuk/iisnode). In questo articolo vengono illustrate le impostazioni più comuni per la raccolta delle informazioni di diagnostica; non sono invece incluse informazioni dettagliate sull'utilizzo di IISNode. Per ulteriori informazioni sull'utilizzo di IISNode, vedere il [file Readme di IISNode](https://github.com/tjanczuk/iisnode#readme) su GitHub.

Abilitazione della registrazione
--------------------------------

Per impostazione predefinita, in Siti Web di Azure vengono acquisite informazioni diagnostiche solo sulle distribuzioni, ad esempio relative a quando si distribuisce un sito Web tramite Git. Tali informazioni sono utili in caso di problemi durante la distribuzione, ad esempio quando non si riesce a installare un modulo cui viene fatto riferimento in **package.json** oppure se si utilizza uno script di distribuzione personalizzato.

Per abilitare la registrazione di flussi stdout e stderr, è necessario creare un file **IISNode.yml** nella radice dell'applicazione Node.js e aggiungere quanto segue:

    loggingEnabled: true

In tal modo la registrazione di stderr e stdout dall'applicazione Node.js verrà abilitata.

È inoltre possibile utilizzare il file **IISNode.yml** per controllare se in caso di errore al browser vengono restituiti errori descrittivi o dello sviluppatore. Per abilitare gli errori dello sviluppatore, aggiungere la riga seguente al file **IISNode.yml**:

    devErrorsEnabled: true

Dopo aver abilitato questa opzione, IISNode restituirà gli ultimi 64 KB di informazioni inviate a stderr anziché un errore descrittivo, quale "si è verificato un errore interno del server".

**Nota**

devErrorsEnabled è utile per diagnosticare problemi che si verificano durante lo sviluppo, tuttavia se viene abilitato in un ambiente di produzione è possibile che gli eventuali errori di sviluppo vengano inviati agli utenti finali.

Se il **IISNode.yml** non esisteva già nell'applicazione, sarà necessario riavviare il sito Web dopo aver pubblicato l'applicazione aggiornata. Se si intende solo modificare le impostazioni in un file **IISNode.yml** già pubblicato, il riavvio non è richiesto.

**Nota**

Se il sito Web è stato creato con gli strumenti da riga di comando di Azure o i cmdlet di Azure PowerShell, verrà creato automaticamente un file **IISNode.yml** predefinito.

Per riavviare il sito Web, selezionarlo nel [portale di gestione di Azure](https://manage.windowsazure.com/), quindi scegliere il pulsante **RESTART**:

![Pulsante Restart](./media/web-sites-nodejs-debug/restartbutton.png)

Se nell'ambiente di sviluppo sono installati gli strumenti da riga di comando di Azure, è possibile utilizzare il comando seguente per riavviare il sito Web:

    azure site restart [nome sito]

**Nota**

Anche se loggingEnabled e devErrorsEnabled sono le opzioni di configurazione più utilizzate di IISNode.yml per acquisire informazioni diagnostiche, è possibile utilizzare IISNode.yml per configurare numerose opzioni per l'ambiente host. Per un elenco completo delle opzioni di configurazione, vedere il file [iisnode\_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml).

Accesso ai log
--------------

È possibile accedere ai log di diagnostica in tre modi, ovvero utilizzando il protocollo FTP (File Transfer Protocol), scaricando un archivio ZIP oppure sotto forma di flusso aggiornato in diretta del log (noto anche come tail). Per il download dell'archivio ZIP dei file di log o per la visualizzazione del flusso in diretta sono necessari gli strumenti da riga di comando di Azure, che è possibile installare tramite il comando seguente:

    npm install azure-cli -g

Dopo l'installazione, è possibile accedere agli strumenti tramite il comando 'azure'. È prima necessario configurare gli strumenti da riga di comando per l'uso della sottoscrizione di Azure. Per informazioni su come eseguire questa attività, vedere la sezione **Come scaricare e importare impostazioni di pubblicazione** dell'articolo [Come utilizzare gli strumenti da riga di comando](https://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/).

### FTP

Per accedere alle informazioni diagnostiche tramite FTP, visitare il [portale di Azure], selezionare il sito Web e scegliere **DASHBOARD**. Nella sezione **quick links** fare clic sui collegamenti **FTP DIAGNOSTIC LOGS** e **FTPS DIAGNOSTIC LOGS** per accedere ai log tramite il protocollo FTP.

**Nota**

Se in precedenza non sono stati configurati nome utente e password per FTP o per la distribuzione, è possibile effettuare questa operazione nella pagina di gestione **Quick Start** selezionando **Set up deployment credentials**.

L'URL FTP restituito nel dashboard si riferisce alla directory **LogFiles**, che conterrà le seguenti sottodirectory:

-   [Metodo di distribuzione]: se si utilizza un metodo di distribuzione, ad esempio Git, verrà creata una directory con lo stesso nome, che conterrà le informazioni correlate alle distribuzioni.

-   nodejs: contiene le informazioni di stdout e stderr acquisite da tutte le istanze dell'applicazione, quando loggingEnabled è impostato su true.

### Archivio ZIP

Per scaricare un archivio ZIP dei log di diagnostica, utilizzare il seguente comando degli strumenti da riga di comando di Azure:

    azure site log download [nomesito]

Nella directory corrente verrà scaricato un file **diagnostics.zip**. Questo archivio contiene la seguente struttura di directory:

-   deployments: log delle informazioni sulle distribuzioni dell'applicazione

-   LogFiles

    -   [Metodo di distribuzione]: se si utilizza un metodo di distribuzione, ad esempio Git, verrà creata una directory con lo stesso nome, che conterrà le informazioni correlate alle distribuzioni.

    -   nodejs: contiene le informazioni di stdout e stderr acquisite da tutte le istanze dell'applicazione, quando loggingEnabled è impostato su true.

### Flusso in diretta (tail)

Per visualizzare un flusso in diretta delle informazioni dei log di diagnostica, utilizzare il seguente comando degli strumenti da riga di comando di Azure:

    azure site log tail [nomesito]

Verrà restituito un flusso di eventi log che vengono aggiornati non appena si verificano nel server. Questo flusso restituirà le informazioni relative alla distribuzione, oltre alle informazioni di stdout e stderr, quando loggingEnabled è impostato su true.

Passaggi successivi
-------------------

In questo articolo è stato illustrato come abilitare e accedere alle informazioni di diagnostica in Azure. Queste informazioni sono utili per comprendere problemi che si verificano nell'applicazione, tuttavia è possibile che indichino un problema relativo a un modulo in uso oppure che segnalino che la versione di Node.js utilizzata in Siti Web di Azure è diversa da quella dell'ambiente di distribuzione.

Per informazioni sull'utilizzo di moduli in Azure, vedere [Utilizzo di moduli Node.js con applicazioni Azure](https://www.windowsazure.com/en-us/develop/nodejs/common-tasks/working-with-node-modules/).

Per informazioni sulla specifica di una versione di Node.js per l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure](https://www.windowsazure.com/en-us/develop/nodejs/common-tasks/specifying-a-node-version/).

