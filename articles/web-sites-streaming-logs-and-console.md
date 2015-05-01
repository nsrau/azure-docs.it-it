<properties 
	pageTitle="Log di streaming e console" 
	description="Informazioni su console e log in streaming" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

# Log di streaming e console

### Log di streaming ###

Il portale di Microsoft Azure fornisce un log di streaming integrato che consente di visualizzare gli eventi di traccia dalle app Web di Servizio app di Azure in tempo reale.  

L'impostazione di questa funzionalità richiede alcuni semplici passaggi:

- Scrittura delle tracce nel codice
- Abilitazione della diagnostica applicazioni dal portale di Azure
- Selezione della parte relativa ai log di streaming nel pannello dell'app Web

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

La diagnostica viene abilitata a livello di singola app Web.  Dall'interno del [portale](https://portal.azure.com) fare clic sul pulsante **Sfoglia** nella barra dei menu visualizzata a sinistra, quindi fare clic su **App Web** per visualizzare un elenco di tutte le app Web.  

![][BrowseSitesScreenshot]

Fare clic sul nome dell'app Web da configurare.  Fare quindi clic sulla parte denominata **LOG DI DIAGNOSTICA** e impostare **Registrazione applicazioni (file system)** su **ATTIVATO**.  Viene visualizzata l'opzione **Livello**, che consentirà di modificare il livello di gravità delle tracce da acquisire.  Impostare il livello su **Dettagliato** se si sta solo acquisendo familiarità con la funzionalità perché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

Fare clic su **SALVA** nella parte superiore del pannello per visualizzare i log.

Per visualizzare i log di streaming dall'interno del portale, fare clic sulla parte denominata **LOG DI STREAMING** nel pannello dell'app Web.  Se l'app sta scrivendo attivamente istruzioni di traccia, queste dovrebbero essere visibili nella finestra risultante in tempo quasi reale.

![][StreamingLogsScreenshot]

## Console ##

Il portale di Azure fornisce accesso all'ambiente delle app Web mediante console. È possibile esplorare il file system dell'app Web ed eseguire script Powershell/cmd.  Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice dell'app Web in esecuzione. Non si sarà in grado di accedere a directory protette né di eseguire script che richiedono autorizzazioni elevate.  

Per accedere alla console, passare a un'app Web come descritto nella sezione precedente.  Fare clic sulla parte denominata **Console** per aprire la console.

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


<!--HONumber=52-->