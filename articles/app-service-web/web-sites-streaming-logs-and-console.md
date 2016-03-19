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
	ms.date="02/14/2016" 
	ms.author="byvinyal"/>

#Log di streaming e console

### Log in streaming ###

Il portale di Microsoft Azure offre un visualizzatore log di streaming integrato che consente di visualizzare gli eventi di traccia dalle app del servizio app in tempo reale.

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
![][BrowseSitesScreenshot] La diagnostica viene abilitata a livello di singola app Web. Dall'interno del [portale](https://portal.azure.com) andare al sito per il quale si vuole abilitare questa funzionalità.
  
![][DiagnosticsLogs]Quindi fare clic su **(1) Impostazioni** > **(2) Log di diagnostica** e **(3) attivare** **Registrazione applicazioni (file system)** o **Registrazione applicazioni (BLOB)**. L'opzione **Livello** consente di modificare il livello di gravità delle tracce da acquisire. Impostare il livello su **Verbose** se si sta solo acquisendo familiarità con la funzionalità, poiché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

Fare clic su **SAVE** nella parte superiore del blade per visualizzare i log.

**NOTA:**Più è alto il **livello di gravità** più risorse si consumano per effettuare il log e più tracce si ottengono. Assicurarsi che questo sia impostato sul livello appropriato quando si utilizza questa funzionalità per un traffico elevato / sito di produzione.

![][StreamingLogsScreenshot] Per visualizzare i log di streaming all'interno del portale fare clic su **(1) Strumenti** > **(2) Flusso di registrazione**. Se l'app sta scrivendo attivamente istruzioni di traccia, saranno visibili nella finestra risultante **(3)** in tempo quasi reale.

## Console ##
Il portale di Azure fornisce accesso all'ambiente delle app Web mediante console. È possibile esplorare il file system dell'app Web ed eseguire script Powershell/cmd. Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice dell'app Web in esecuzione. Non si sarà in grado di accedere a directory protette né di eseguire script che richiedono autorizzazioni elevate.

![][ConsoleScreenshot] Per accedere alla console, passare a un'app Web come descritto nella sezione precedente. Fare clic su **(1) Strumenti**>**(2) Console ** e la console **(3)** verrà aperta.

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

<!---HONumber=AcomDC_0211_2016-->