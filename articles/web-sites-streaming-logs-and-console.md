<properties pageTitle="Streaming logs and console" description="Streaming logs and console overview" title="Streaming logs and console" authors="adamab" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

## Log in streaming e console

### Log in streaming

Il portale Microsoft Azure Preview fornisce un log in streaming integrato che consente di visualizzare gli eventi di traccia dai siti Web in tempo reale.

L'impostazione di questa funzionalità richiede alcuni semplici passaggi:

-   Scrittura delle tracce nel codice
-   Abilitazione della diagnostica applicazioni dal portale Azure Preview
-   Selezione dell'opzione relativa ai log in streaming nel pannello del sito Web

### Come scrivere le tracce nel codice

La scrittura delle tracce nel codice è facile. In C# è sufficiente scrivere il codice seguente:

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

    console.log("My trace statement").

### Come abilitare e visualizzare i log in streaming

La diagnostica viene abilitata a livello di sito Web individuale. Dall'interno del [portale][portale] fare clic sul pulsante **Browse** nella barra dei menu visualizzata a sinistra, quindi scegliere **Websites** per visualizzare un elenco di tutti i siti Web.

![][0]

Fare clic sul nome del sito Web da configurare. Fare quindi clic sulla parte denominata **DIAGNOSTIC LOGS** e impostare l'interruttore **Application Logging (Filesystem)** sulla posizione **ON**. Viene visualizzata l'opzione **Level**, che consentirà di modificare il livello di gravità delle tracce da acquisire. Impostare il livello su **Verbose** se si sta solo acquisendo familiarità con la funzionalità, poiché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

Fare clic su **SAVE** nella parte superiore del pannello per visualizzare i log.

Per visualizzare i log in streaming dall'interno del portale, fare clic sulla parte **STREAMING LOGS** nel pannello del sito Web. Se il sito sta scrivendo attivamente istruzioni di traccia, queste dovrebbero essere visibili nella finestra risultante in tempo quasi reale.

![][1]

## Console

Il portale di Anteprima di Azure consente di accedere mediante una console all'ambiente dei siti Web. È possibile esplorare il file system del sito Web ed eseguire script Powershell/cmd. Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice del sito Web in esecuzione. Non si sarà in grado di accedere a directory protette né di eseguire script che richiedono autorizzazioni elevate.

Per accedere alla console, passare a un sito Web come descritto nella sezione precedente. Fare clic sulla parte **Console** per aprire la console.

![][2]

Per acquisire familiarità con la console, provare ad eseguire semplici comandi come i seguenti:

    dir

    cd



  [portale]: https://portal.azure.com
  [0]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
  [1]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
  [2]: ./media/web-sites-streaming-logs-and-console/console.png
