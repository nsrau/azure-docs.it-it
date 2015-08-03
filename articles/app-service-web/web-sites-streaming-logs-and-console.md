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
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#Log di streaming e console

### Log in streaming ###

Il [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) fornisce un visualizzatore di log di streaming integrato che consente di visualizzare in tempo reale gli eventi di traccia dalle app Web del [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

L'impostazione di questa funzionalità richiede alcuni semplici passaggi:

- Scrittura delle tracce nel codice
- Abilitazione della diagnostica applicazioni dal portale di anteprima di Azure
- Selezione del riquadro relativo ai log di streaming nel pannello dell'app Web

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

### Abilitare e visualizzare i log di streaming ###

La diagnostica viene abilitata a livello di singola app Web.

1. Nel [portale di anteprima di Azure](https://portal.azure.com) fare clic su **Sfoglia** > **App Web** per ottenere l'elenco di tutte le app Web.  

2. Fare clic sul nome dell'app Web da configurare.

3. Fare quindi clic su **Tutte le impostazioni** > **Log di diagnostica** e impostare **Registrazione applicazioni (file system)** su **ATTIVATO**.

4. Viene visualizzata l'opzione **Level**, che consentirà di modificare il livello di gravità delle tracce da acquisire. Impostare il livello su **Verbose** se si sta solo acquisendo familiarità con la funzionalità, poiché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

5. Fare clic su **Salva** nella parte superiore del pannello per visualizzare i log.

6. Per visualizzare i log di streaming dall'interno del portale, fare clic sul riquadro **Log di streaming** nel pannello dell'app Web. Se l'app sta scrivendo attivamente istruzioni di traccia, queste dovrebbero essere visibili nella finestra risultante in tempo quasi reale.

![][StreamingLogsScreenshot]

## Accedere alla console ##

Il portale di anteprima di Azure consente di accedere all'app Web tramite console. È possibile esplorare il file system dell'app Web ed eseguire script Powershell/cmd. Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice dell'app Web in esecuzione. Non si sarà in grado di accedere a directory protette né di eseguire script che richiedono autorizzazioni elevate.

1. Passare al pannello di un'app Web come descritto nella sezione precedente.

2. Fare clic sul riquadro **Console** per aprire la console.

![][ConsoleScreenshot]

Per acquisire familiarità con la console, provare ad eseguire semplici comandi come i seguenti:

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=July15_HO4-->