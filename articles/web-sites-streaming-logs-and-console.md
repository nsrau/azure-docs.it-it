<properties pageTitle="Log di streaming e console" description="Streaming logs and console overview" title="Streaming logs and console" authors="adamab" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

#Log di streaming e console

### Log di streaming ###

Il portale di anteprima di Microsoft Azure fornisce un log di streaming integrato che consente di visualizzare gli eventi di traccia dai siti Web in tempo reale.  

L'impostazione di questa funzionalità richiede alcuni semplici passaggi:

- Scrittura delle tracce nel codice
- Abilitazione della diagnostica applicazioni dal portale Azure Preview
- Selezione della parte relativa ai log di streaming nel pannello del sito Web

### Come scrivere le tracce nel codice ###

La scrittura delle tracce nel codice è facile.  In C# è sufficiente scrivere il codice seguente:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La classe Trace risiede nello spazio dei nomi System.Diagnostics.

In una app node.js è possibile scrivere il codice seguente per ottenere lo stesso risultato:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Come abilitare e visualizzare i log di streaming ###

La diagnostica viene abilitata a livello di sito Web individuale.  Dal [portale](https://portal.azure.com) fare clic sul pulsante **Sfoglia** sulla barra dei menu a sinistra, quindi scegliere **Siti Web** per visualizzare un elenco di tutti i siti Web.  

![][BrowseSitesScreenshot]

Fare clic sul nome del sito Web da configurare.  Fare quindi clic sulla parte denominata **LOG DI DIAGNOSTICA** e impostare l'interruttore **Registrazione applicazioni (file system)** sulla posizione **ON**.  Viene visualizzata l'opzione **Livello**, che consentirà di modificare il livello di gravità delle tracce da acquisire.  Impostare il livello su **Dettagliato** se si sta solo acquisendo familiarità con la funzionalità, poiché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

Fare clic su **SALVA** nella parte superiore del pannello per visualizzare i log.

Per visualizzare i log di streaming dall'interno del portale, fare clic sulla parte **LOG DI STREAMING** nel pannello del sito Web.  Se il sito sta scrivendo attivamente istruzioni di traccia, queste dovrebbero essere visibili nella finestra risultante in tempo quasi reale.

![][StreamingLogsScreenshot]

## Console ##

Il portale di Anteprima di Azure consente di accedere mediante una console all'ambiente dei siti Web. È possibile esplorare il file system del sito Web ed eseguire script Powershell/cmd.  Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice del sito Web in esecuzione. Non si sarà in grado di accedere a directory protette né di eseguire script che richiedono autorizzazioni elevate.  

Per accedere alla console, passare a un sito Web come descritto nella sezione precedente.  Fare clic sulla parte **Console** per aprire la console.

![][ConsoleScreenshot]

Per acquisire familiarità con la console, provare ad eseguire semplici comandi come i seguenti:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
