---
title: Domande frequenti sulle prestazioni delle applicazioni in App Web di Azure | Microsoft Docs
description: "È possibile ottenere risposte alle domande frequenti sulla disponibilità, sulle prestazioni e sui problemi delle applicazioni nella funzionalità App Web del servizio app di Azure."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 36869eb523706787257160e5859f50b24f7bf08b
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Domande frequenti sulle prestazioni delle applicazioni in App Web di Azure

Questo articolo offre risposte alle domande frequenti sui problemi di prestazioni delle applicazioni per la [funzionalità App Web del servizio app di Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Perché l'app è lenta?

Più fattori possono contribuire a rallentare le prestazioni delle app. Per la procedura di risoluzione dei problemi dettagliata, vedere [Risolvere i problemi di rallentamento delle prestazioni delle app Web](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Come si risolvono i problemi di uno scenario con utilizzo elevato di CPU?

In alcuni scenari di utilizzo elevato di CPU, l'app può richiedere realmente più risorse di calcolo. In tal caso, prendere in considerazione il passaggio a un livello di servizio superiore per fornire tutte le risorse necessarie all'applicazione. In altri casi, un utilizzo elevato di CPU può essere causato da un ciclo non valido o da una procedura di codifica. La procedura che consente di ottenere informazioni su cosa provochi un maggiore utilizzo di CPU prevede due parti. Creare prima un dump dei processi e analizzarlo. Per altre informazioni, vedere [Acquisire e analizzare un file di dump per l'utilizzo elevato di CPU per le app Web](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Come si risolvono i problemi di uno scenario con utilizzo elevato di memoria?

In alcuni scenari di utilizzo elevato di memoria, l'app può richiedere realmente più risorse di calcolo. In tal caso, prendere in considerazione il passaggio a un livello di servizio superiore per fornire tutte le risorse necessarie all'applicazione. In altri casi, un bug nel codice può causare una perdita di memoria. Anche una procedura di codifica può provocare un maggiore utilizzo di memoria. La procedura che consente di ottenere informazioni su cosa provochi un utilizzo elevato di memoria prevede due parti. Creare prima un dump dei processi e analizzarlo. Crash Diagnoser della raccolta di estensioni sito di Azure può eseguire in modo efficiente entrambi questi passaggi. Per altre informazioni, vedere [Acquisire e analizzare un file di dump per l'utilizzo elevato intermittente di memoria per le app Web](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Come si automatizzano le app Web del servizio app usando PowerShell?

È possibile usare i cmdlet di PowerShell per gestire le app Web del servizio app. Il post di blog [Automatizzare le app Web ospitate nel servizio app di Azure usando PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/) descrive come usare i cmdlet di PowerShell basati su Azure Resource Manager per automatizzare attività comuni. Il post di blog offre anche codice di esempio per diverse attività di gestione delle app Web. Per le descrizioni e la sintassi di tutti i cmdlet delle app Web del servizio app, vedere [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Come si possono visualizzare i registri eventi dell'app Web?

Per visualizzare i registri eventi dell'app Web:

1. Accedere al [sito Web Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Nel menu selezionare **Debug Console (Console di debug)**  > **CMD**.
3. Selezionare la cartella **LogFiles**.
4. Per visualizzare i registri eventi, selezionare l'icona della matita accanto a **eventlog.xml**.
5. Per scaricare i log, eseguire il cmdlet `Save-AzureWebSiteLog -Name webappname` di PowerShell.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Come si acquisisce un dump della memoria in modalità utente per l'app Web?

Per acquisire un dump della memoria in modalità utente per l'app Web:

1. Accedere al [sito Web Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selezionare il menu **Process Explorer** (Esplora processi).
3. Fare clic con il pulsante destro del mouse sul processo **w3wp.exe** o sul proprio processo Web.
4. Selezionare **Download Memory Dump (Scarica dump di memoria)** > **Full Dump (Dump completo)**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Come si visualizzano le informazioni a livello di processo per l'app Web?

Sono disponibili due opzioni per la visualizzazione di informazioni a livello di processo per l'app Web:

*   Nel portale di Azure:
    1. Aprire **Process Explorer** (Esplora processi) per l'app Web.
    2. Per visualizzare i dettagli, selezionare il processo **w3wp.exe**.
*   Nella console Kudu:
    1. Accedere al [sito Web Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Selezionare il menu **Process Explorer** (Esplora processi).
    3. Per il processo **w3wp.exe** selezionare **Properties** (Proprietà).

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Quando si passa all'app, viene visualizzato il messaggio "Errore 403. L'app Web è arrestata". Come posso risolvere il problema?

Questo errore può essere causato da tre condizioni:

* È stato raggiunto un limite di fatturazione per l'app Web e il sito è stato disabilitato.
* L'app Web è stata arrestata nel portale.
* L'app Web ha raggiunto un limite di quota per le risorse che può essere applicabile a un piano di servizio Gratuito o Condiviso.

Per visualizzare la causa dell'errore e risolvere il problema, seguire i passaggi in [App Web: "Errore 403. L'app Web è arrestata"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Dove si trovano altre informazioni su quote e limiti per i vari piani del servizio app?

Per informazioni su quote e limiti, vedere [Limiti relativi al servizio app](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Come si riduce il tempo di risposta per la prima richiesta dopo il tempo di inattività?

Per impostazione predefinita, le app Web vengono scaricate se restano inattive per un determinato periodo di tempo. Ciò consente al sistema di conservare le risorse. Lo svantaggio è che la risposta alla prima richiesta dopo aver scaricato l'app Web richiede più tempo per consentire all'app Web di caricarsi e iniziare a gestire le risposte. Nei piani di servizio Basic e Standard, è possibile attivare l'impostazione **Sempre online** per mantenere l'app sempre caricata. In questo modo si evitano tempi di caricamento lunghi dopo l'inattività dell'app. Per modificare l'impostazione **Sempre online**:

1. Nel portale di Azure passare all'app Web.
2. Selezionare **Impostazioni dell'applicazione**.
3. Per **Sempre online** selezionare **Sì**.

## <a name="how-do-i-turned-on-failed-request-tracing"></a>Come si abilita la traccia delle richieste non riuscite?

Per abilitare la traccia delle richieste non riuscite:

1. Nel portale di Azure passare all'app Web.
3. Selezionare **Tutte le impostazioni** > **Log di diagnostica**.
4. Per **Traccia delle richieste non riuscite** selezionare **Sì**.
5. Selezionare **Salva**.
6. Nel pannello dell'app Web selezionare **Strumenti**.
7. Selezionare **Visual Studio Online**.
8. Selezionare **Sì** per l'**impostazione** se non è già selezionato.
9. Selezionare **Vai**.
10. Selezionare **Web.config**.
11. In system.webServer aggiungere questa configurazione per l'acquisizione di un URL specifico:

    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Per risolvere i problemi di prestazioni lente, aggiungere questa configurazione se la richiesta di acquisizione richiede più di 30 secondi:
    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Per scaricare le tracce di richieste non riuscite, nel [portale](https://portal.azure.com) passare al sito Web.
15. Select **Strumenti** > **Kudu** > **Vai**.
18. Nel menu selezionare **Debug Console (Console di debug)** > **CMD**.
19. Selezionare la cartella **LogFiles** e quindi la cartella con un nome che inizia con **W3SVC**.
20. Per visualizzare il file XML, selezionare l'icona della matita.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Viene visualizzato il messaggio "Worker Process requested recycle due to 'Percent Memory' limit" (Il processo di lavoro è stato riciclato a causa di un limite 'Percentuale di memoria'). Come si risolve questo problema?

La quantità massima di memoria per un processo a 32 bit è 2 GB, anche in un sistema operativo a 64 bit. Per impostazione predefinita, il processo di lavoro è impostato su 32 bit nel servizio app, per compatibilità con le applicazioni Web legacy.

Valutare se passare a processi a 64 bit per poter sfruttare la memoria aggiuntiva disponibile nel ruolo di lavoro Web. Questa operazione provoca il riavvio dell'app Web, quindi pianificarla di conseguenza.

Si noti anche che un ambiente a 64 bit richiede il piano di servizio Basic o Standard. I piani Gratuito e Condiviso vengono eseguiti sempre in un ambiente a 32 bit.

Per altre informazioni, vedere [Configurare le app Web nel servizio app](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-240-seconds"></a>Perché la richiesta raggiunge il timeout dopo 240 secondi?

Azure Load Balancer ha un'impostazione predefinita di quattro minuti per il timeout di inattività. Si tratta in genere di un limite di tempo di risposta ragionevole per una richiesta Web. Se l'app Web richiede l'elaborazione in background, è consigliabile usare Processi Web di Azure. L'app Web di Azure può chiamare Processi Web e ricevere una notifica al termine dell'elaborazione in background. È possibile scegliere tra più metodi per l'uso di Processi Web, tra cui le code e i trigger.

Processi Web è progettato per l'elaborazione in background. In un processo Web non vengono applicati limiti all'elaborazione in background. Per altre informazioni su Processi Web, vedere [Eseguire attività in background con Processi Web](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>A volte le applicazioni ASP.NET Core ospitate nel servizio app si bloccano. Come si risolve il problema?

Un problema noto con una [versione di Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) precedente potrebbe provocare il blocco intermittente di un'app ASP.NET Core 1.0 ospitata nel servizio app. Potrebbe essere visualizzato il messaggio: "L'applicazione CGI specificata ha rilevato un errore e il server ha interrotto il processo".

Il problema è stato risolto in Kestrel versione 1.0.2. Questa versione è inclusa nell'aggiornamento di ASP.NET Core 1.0.3. Per risolvere questo problema, aggiornare le dipendenze app per l'uso di Kestrel 1.0.2. In alternativa è possibile usare una delle due soluzioni descritte nel post di blog [Problemi di prestazioni lente di ASP.NET Core 1.0 nelle app Web del servizio app](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Non è possibile trovare i file di log nella struttura di file dell'app Web. Come si possono trovare?

Se si usa la funzionalità di cache locale del servizio app, la struttura delle cartelle LogFiles e Data per l'istanza di servizio App sono interessate. Quando si usa la cache locale, vengono create sottocartelle nelle cartelle LogFiles e Data dell'archivio. Le sottocartelle usano il modello di denominazione "identificatore univoco" + timestamp. Ogni sottocartella corrisponde a un'istanza di VM in cui l'app Web è o era in esecuzione.

Per determinare se si sta usando la cache locale, verificare la scheda **Impostazioni dell'applicazione** del servizio app. Se si sta usando la cache locale, l'impostazione `WEBSITE_LOCAL_CACHE_OPTION` dell'app ha il valore `Always`.

Se non si usa la cache locale e si verifica questo problema, inviare una richiesta di supporto.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Viene visualizzato il messaggio: "Si è verificato un tentativo di accesso a un socket in modalità non consentite dalle relative autorizzazioni di accesso". Come posso risolvere il problema?

Questo errore si verifica in genere quando si esauriscono le connessioni TCP in uscita nell'istanza della VM. Nel servizio app, i limiti vengono applicati per il numero massimo di connessioni in uscita che possono essere eseguite per ogni istanza della VM. Per altre informazioni, vedere [Cross-VM numerical limits](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) (Limiti numerici tra VM).

Questo errore può verificarsi anche se si prova ad accedere a un indirizzo locale dall'applicazione. Per altre informazioni, vedere [Local address requests](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests) (Richieste di indirizzi locali).

Per altre informazioni sulle connessioni in uscita nell'app Web, vedere il post di blog sulle [connessioni in uscita a siti Web di Azure](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Come si usa Visual Studio per eseguire il debug remoto dell'app Web del servizio app?

Per una procedura dettagliata che illustra come eseguire il debug dell'app Web usando Visual Studio, vedere [Remote debug your App Service web app](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/) (Eseguire il debug remoto dell'app Web del servizio app).
