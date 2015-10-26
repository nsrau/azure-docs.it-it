<properties 
	pageTitle="Log di streaming e console" 
	description="Informazioni su console e log in streaming" 
	authors="btardif" 
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
	ms.date="08/10/2015" 
	ms.author="byvinyal"/>

#Log di streaming e console

### Log in streaming ###

Il portale di Microsoft Azure fornisce un log di streaming integrato che consente di visualizzare gli eventi di traccia dalle app Web di Servizio app di Azure in tempo reale.

L'impostazione di questa funzionalità richiede alcuni semplici passaggi:

- Scrittura delle tracce nel codice
- Abilitazione della diagnostica applicazioni dal portale di Azure
- Selezione della parte relativa ai log di streaming nel pannello dell'app Web

### Come scrivere le tracce nel codice ###

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

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Come abilitare e visualizzare i log in streaming ###

![][BrowseSitesScreenshot] La diagnostica viene abilitata a livello di singola app Web. Dall'interno del [portale](https://portal.azure.com) fare clic sul pulsante **Browse (1)** nella barra dei menu visualizzata a sinistra, quindi scegliere **App Web (2)** per ottenere un **elenco (3)** di tutti le App web.

Fare clic sul nome dell'app Web da configurare per navigarci.
  
![][DiagnosticsLogs]Poi fare clic su**Impostazioni (1)** > **Log di diagnostica (2)** e attivare ** su ** **Application Logging (Filesystem)(3)** l’opzione **Livello** consente di modificare il livello di gravità delle tracce da acquisire. Impostare il livello su **Verbose** se si sta solo acquisendo familiarità con la funzionalità, poiché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

Fare clic su **SAVE** nella parte superiore del blade per visualizzare i log.

**NOTA:**Più è alto il **livello di gravità** più risorse si consumano per effettuare il log e più tracce si ottengono. Assicurarsi che questo sia impostato sul livello appropriato quando si utilizza questa funzionalità per un traffico elevato / sito di produzione.

![][StreamingLogsScreenshot] Per visualizzare i log in streaming all'interno del portale fare clic su **Tools (1)** > **Log Stream(2)**. Se l'app sta scrivendo attivamente istruzioni di traccia, queste dovrebbero essere visibili nella finestra risultante in tempo quasi reale.

## Console ##

Il portale di Azure fornisce accesso all'ambiente delle app Web mediante console. È possibile esplorare il file system dell'app Web ed eseguire script Powershell/cmd. Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice dell'app Web in esecuzione. Non si sarà in grado di accedere a directory protette né di eseguire script che richiedono autorizzazioni elevate.

![][ConsoleScreenshot] Per accedere alla console, passare a un'app Web come descritto nella sezione precedente. Fare clic sul riquadro **Tools**>**Console** per aprire la console.

Per acquisire familiarità con la console, provare ad eseguire semplici comandi come i seguenti:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=Oct15_HO3-->