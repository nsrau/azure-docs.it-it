---
title: Log di streaming e console
description: Informazioni su console e log in streaming
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7704270d7bba54577f61f3f349dc69dc6ddf08c6


---
# <a name="streaming-logs-and-the-console"></a>Log di streaming e console
## <a name="streaming-logs"></a>Log in streaming
Il **portale di Azure** offre un visualizzatore log di streaming integrato che consente di visualizzare gli eventi di traccia dalle app del **servizio app** in tempo reale.  

L'impostazione di questa funzionalità richiede alcuni semplici passaggi:

* Scrittura delle tracce nel codice
* Abilitazione dei **log di diagnostica** dell'applicazione per l'app
* Visualizzare il flusso dall'interfaccia utente dei **log in streaming** predefiniti nel **portale Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Come scrivere le tracce nel codice
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

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Come abilitare e visualizzare i log in streaming
![][BrowseSitesScreenshot] La diagnostica viene abilitata a livello di singola app. Eseguire l'avvio passando al sito per cui si vuole abilitare questa funzionalità.  

![][DiagnosticsLogs] Dal menu Impostazioni eseguire lo scorrimento verso il basso fino alla sezione di **monitoraggio** e fare clic su **(1) Log di diagnostica**. Quindi **(2) abilitare** **Registrazione applicazioni (file system)** o **Registrazione applicazioni (BLOB)**. L'opzione **Livello** consente di modificare il livello di gravità delle tracce da acquisire. Impostare il livello su **Dettagliato** se si sta solo acquisendo familiarità con la funzionalità, poiché questa impostazione garantirà la registrazione di tutte le istruzioni di traccia.

Fare clic su **SAVE** nella parte superiore del blade per visualizzare i log.

> [!NOTE]
> Più è alto il **livello di gravità**, più risorse si consumano per eseguire il log e più tracce si ottengono. Assicurarsi che **livello di gravità** sia configurato per il livello di dettaglio corretto per un sito a traffico elevato o di produzione. 
> 
> 

![][StreamingLogsScreenshot] Per visualizzare i **log in streaming** dall'interno del portale di Azure fare clic su **(1) Flusso di registrazione** anche nella sezione di **monitoraggio** del menu Impostazioni. Se l'app sta scrivendo attivamente istruzioni di traccia, saranno visibili nella **(2) interfaccia utente dei log in streaming** in tempo quasi reale.

## <a name="console"></a>Console
Il **portale di Azure** fornisce accesso all'app mediante console. È possibile esplorare il file system dell'app ed eseguire script Powershell/cmd. Quando si eseguono i comandi della console, si opera con le stesse autorizzazioni impostate per il codice dell'app in esecuzione. L'accesso alle directory protette o l'esecuzione di script che richiedono autorizzazioni elevate è bloccato.  

![][ConsoleScreenshot] Dal menu Impostazioni scorrere verso il basso fino alla sezione **Strumenti di sviluppo** e fare clic su **(1) Console**; a destra viene aperta l'interfaccia utente della **(2) console**.

Per acquisire familiarità con la **console**, provare ad eseguire i comandi di base come:

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



<!--HONumber=Nov16_HO3-->


