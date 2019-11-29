---
title: Abilitare la registrazione diagnostica per le app - Servizio app di Azure
description: Informazioni su come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione e su come accedere alle informazioni registrate da Azure.
services: app-service
author: cephalin
manager: gwallace
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/17/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bf25967133901aca911c0ad3a69d43c75bb816a0
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558560"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Abilitare la registrazione diagnostica per le app nel Servizio app di Azure
## <a name="overview"></a>Panoramica
Azure offre diagnostica integrata per facilitare il debug di un'[app del servizio app](overview.md). In questo articolo viene descritto come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione. Viene anche descritto come accedere alle informazioni registrate da Azure.

L'articolo illustra anche l'uso del [portale di Azure](https://portal.azure.com) e dell'interfaccia della riga di comando di Azure per elaborare i log di diagnostica. Per informazioni sull'elaborazione dei log di diagnostica in Visual Studio vedere [Risoluzione dei problemi di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Oltre alle istruzioni per la registrazione in questo articolo, sono disponibili nuove funzionalità di registrazione integrate con il monitoraggio di Azure. Ulteriori informazioni su questa funzionalità sono disponibili nella sezione [Invia log a monitoraggio di Azure (anteprima)](#send-logs-to-azure-monitor-preview) . 
>
>

|Type|Piattaforma|Località|Description|
|-|-|-|-|
| Registrazione di applicazioni | Windows, Linux | File system del servizio app e/o BLOB di archiviazione di Azure | Registra i messaggi generati dal codice dell'applicazione. I messaggi possono essere generati dal framework Web scelto oppure dal codice dell'applicazione usando direttamente il modello di registrazione standard della lingua. A ogni messaggio viene assegnata una delle seguenti categorie: **Critical**, **Error**, **warning**, **info**, **debug**e **Trace**. È possibile selezionare il livello di dettaglio desiderato per la registrazione impostando il livello di gravità quando si Abilita la registrazione dell'applicazione.|
| Registrazione del server Web| Windows | file system del servizio app o BLOB di archiviazione di Azure| Dati della richiesta HTTP non elaborati nel [formato di file di log esteso W3C](/windows/desktop/Http/w3c-logging). Ogni messaggio di log include dati quali il metodo HTTP, l'URI della risorsa, l'indirizzo IP del client, la porta client, l'agente utente, il codice di risposta e così via. |
| Registrazione dettagliata degli errori | Windows | file system del servizio app | Copie delle pagine di errore *htm* che verrebbero inviate al browser client. Per motivi di sicurezza, le pagine di errore dettagliate non devono essere inviate ai client in produzione, ma il servizio app può salvare la pagina di errore ogni volta che si verifica un errore dell'applicazione con codice HTTP 400 o versione successiva. La pagina può contenere informazioni che consentono di determinare il motivo per cui il server restituisce il codice di errore. |
| Traccia delle richieste non riuscite | Windows | file system del servizio app | Informazioni dettagliate sulla traccia delle richieste non riuscite, inclusa una traccia dei componenti IIS usati per elaborare la richiesta e il tempo impiegato in ogni componente. È utile per migliorare le prestazioni del sito o isolare uno specifico errore HTTP. Viene generata una cartella per ogni richiesta non riuscita, che contiene il file di log XML e il foglio di stile XSL per visualizzare il file di log con. |
| Registrazione della distribuzione | Windows, Linux | file system del servizio app | Registra quando si pubblica il contenuto in un'app. La registrazione della distribuzione viene eseguita automaticamente e non sono disponibili impostazioni configurabili per la registrazione della distribuzione. Consente di determinare il motivo per cui una distribuzione non è riuscita. Se ad esempio si usa uno [script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script), è possibile usare la registrazione della distribuzione per determinare il motivo per cui lo script non riesce. |

> [!NOTE]
> Il servizio app offre uno strumento di diagnostica interattivo dedicato che consente di risolvere i problemi dell'applicazione. Per ulteriori informazioni, vedere [Panoramica di diagnostica del servizio app Azure](overview-diagnostics.md).
>
> Inoltre, è possibile usare altri servizi di Azure per migliorare le funzionalità di registrazione e monitoraggio dell'app, ad esempio [monitoraggio di Azure](../azure-monitor/app/azure-web-apps.md).
>

## <a name="enable-application-logging-windows"></a>Abilitare la registrazione delle applicazioni (Windows)

Per abilitare la registrazione delle applicazioni per le app di Windows nell' [portale di Azure](https://portal.azure.com), passare all'app e selezionare **log del servizio app**.

Selezionare **on** per **registrazione applicazioni (file System)** o **registrazione applicazione (BLOB)** o entrambi. 

L'opzione **filesystem** è a scopo di debug temporaneo e si disattiva in 12 ore. L'opzione **BLOB** è per la registrazione a lungo termine e necessita di un contenitore di archiviazione BLOB in cui scrivere i log.  L'opzione **BLOB** include anche informazioni aggiuntive nei messaggi di log, ad esempio l'ID dell'istanza della macchina virtuale di origine del messaggio del log (`InstanceId`), l'ID del thread (`Tid`) e un timestamp più granulare ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> Attualmente solo i log applicazioni .NET possono essere scritti nell'archiviazione BLOB. Java, PHP, node. js, i log applicazioni Python possono essere archiviati solo nel servizio app file system (senza modifiche al codice per scrivere i log in una risorsa di archiviazione esterna).
>
> Inoltre, se si [rigenerano le chiavi di accesso dell'account di archiviazione](../storage/common/storage-create-storage-account.md), è necessario reimpostare la rispettiva configurazione di registrazione in modo da usare le chiavi di accesso aggiornate. A tale scopo, effettuare l'operazione seguente:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

Consente di selezionare il **livello**o il livello dei dettagli da registrare. La tabella seguente illustra le categorie di log incluse in ogni livello:

| Level | Categorie incluse |
|-|-|
|**Disabilitato** | Nessuno |
|**Error (Errore) (Error (Errore)e)** | Errore, Errore critico |
|**Warning** | Avviso, Errore, Errore critico|
|**Informazioni** | Informazioni, Avviso, Errore, Errore critico|
|**Dettagliato** | Analisi, Debug, Informazioni, Avviso, Errore, Errore critico (tutte le categorie) |

Al termine, selezionare **Salva**.

## <a name="enable-application-logging-linuxcontainer"></a>Abilitare la registrazione delle applicazioni (Linux/contenitore)

Per abilitare la registrazione delle applicazioni Linux o delle app contenitore personalizzate nella [portale di Azure](https://portal.azure.com), passare all'app e selezionare **log del servizio app**.

In **registrazione applicazioni**selezionare **file System**.

In **quota (MB)** specificare la quota del disco per i log dell'applicazione. In **periodo di conservazione (giorni)** impostare il numero di giorni per cui conservare i log.

Al termine, selezionare **Salva**.

## <a name="enable-web-server-logging"></a>Abilitazione della registrazione del server Web

Per abilitare la registrazione del server Web per le app di Windows nel [portale di Azure](https://portal.azure.com), passare all'app e selezionare **log del servizio app**.

Per la **registrazione del server Web**, selezionare **archiviazione** per archiviare i log nell'archivio BLOB o il **file System** per archiviare i log nel servizio app file System. 

In **periodo di conservazione (giorni)** impostare il numero di giorni per cui conservare i log.

> [!NOTE]
> Se si [rigenerano le chiavi di accesso dell'account di archiviazione](../storage/common/storage-create-storage-account.md), è necessario reimpostare la configurazione di registrazione corrispondente per l'uso delle chiavi aggiornate. A tale scopo, effettuare l'operazione seguente:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

Al termine, selezionare **Salva**.

## <a name="log-detailed-errors"></a>Registra errori dettagliati

Per salvare la pagina di errore o la traccia delle richieste non riuscite per le app di Windows nell' [portale di Azure](https://portal.azure.com), passare all'app e selezionare **log del servizio app**.

In **log degli errori dettagliati** o **traccia delle richieste non riuscite**Selezionare **on**, quindi selezionare **Salva**.

Entrambi i tipi di log vengono archiviati nel servizio app file system. Vengono conservati fino a 50 errori (file/cartelle). Quando il numero di file HTML supera 50, i 26 errori meno recenti vengono eliminati automaticamente.

## <a name="add-log-messages-in-code"></a>Aggiungere messaggi di log nel codice

Nel codice dell'applicazione si usano le normali funzionalità di registrazione per inviare messaggi di log ai log dell'applicazione. ad esempio:

- Le applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) per registrare le informazioni nel log di diagnostica applicazioni. ad esempio:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Per impostazione predefinita, ASP.NET Core usa il provider di registrazione [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) . Per altre informazioni, vedere [Registrazione in ASP.NET Core- Registrazione in Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Eseguire lo streaming dei log

Prima di eseguire lo streaming dei log in tempo reale, abilitare il tipo di log desiderato. Tutte le informazioni scritte nei file che terminano con i file con estensione txt, log o htm archiviati nella directory */LogFiles* (d:/Home/LogFiles) vengono trasmesse dal servizio app.

> [!NOTE]
> Alcuni tipi di buffer di registrazione scrivono nel file di log, producendo nel caso eventi di "fuori servizio" nel flusso. Ad esempio, una voce del log di applicazione che si verifica quando un utente visita una pagina può essere visualizzata nel flusso prima della corrispondente voce di log HTTP per la richiesta della pagina.
>

### <a name="in-azure-portal"></a>Nel portale di Azure

Per eseguire lo streaming dei log nella [portale di Azure](https://portal.azure.com), passare all'app e selezionare **flusso di log**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Per eseguire lo streaming dei log in tempo reale in [cloud Shell](../cloud-shell/overview.md), usare il comando seguente:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Per filtrare eventi specifici, ad esempio gli errori, utilizzare il parametro **-Filter** . ad esempio:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **--Path** . ad esempio:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>Nel terminale locale

Per eseguire lo streaming dei log nella console locale, [installare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure e [accedere all'account](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Una volta effettuato l'accesso, seguire le [istruzioni per cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Accedere ai file di log

Se si configura l'opzione BLOB di archiviazione di Azure per un tipo di log, è necessario uno strumento client che funziona con archiviazione di Azure. Per altre informazioni, vedere [strumenti client di archiviazione di Azure](../storage/common/storage-explorers.md).

Per i log archiviati nel servizio app file system, il modo più semplice consiste nel scaricare il file ZIP nel browser all'indirizzo:

- App Linux/contenitore: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- App di Windows: `https://<app-name>.scm.azurewebsites.net/api/dump`

Per le app Linux/contenitore, il file ZIP contiene i log di output della console per l'host Docker e il contenitore docker. Per un'app con scalabilità orizzontale, il file ZIP contiene un set di log per ogni istanza. Nel file system del servizio app, questi file di log sono il contenuto della directory */Home/LogFiles* .

Per le app di Windows, il file ZIP contiene il contenuto della directory *D:\Home\LogFiles* nel servizio app file System. Presenta la struttura seguente:

| Tipo di log | Directory | Description |
|-|-|-|
| **Log applicazioni** |*/LogFiles/Application/* | Contiene uno o più file di testo. Il formato dei messaggi di log dipende dal provider di registrazione usato. |
| **Tracce richieste non riuscite** | */LogFiles/W3SVC#########/* | Contiene i file XML e un file XSL. È possibile visualizzare i file XML formattati nel browser. |
| **Log degli errori dettagliati** | */LogFiles/DetailedErrors/* | Contiene i file di errore HTM. È possibile visualizzare i file HTM nel browser.<br/>Un altro modo per visualizzare le tracce delle richieste non riuscite consiste nel passare alla pagina dell'app nel portale. Dal menu a sinistra selezionare **diagnostica e Risolvi i problemi**, quindi cercare i **log di traccia delle richieste non riuscite**, quindi fare clic sull'icona per esplorare e visualizzare la traccia desiderata. |
| **Log del server Web** | */LogFiles/http/RawLogs/* | Contiene file di testo formattati utilizzando il [formato di file di log esteso W3C](/windows/desktop/Http/w3c-logging). Queste informazioni possono essere lette usando un editor di testo o un'utilità come [log parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>Il servizio app non supporta i campi `s-computername`, `s-ip`o `cs-version`. |
| **Log di distribuzione** | */LogFiles/git/* e */Deployments/* | Contengono i log generati dai processi di distribuzione interni e i log per le distribuzioni git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Inviare i log a monitoraggio di Azure (anteprima)

Con la nuova [integrazione di monitoraggio di Azure](https://aka.ms/appsvcblog-azmon), è possibile [creare impostazioni di diagnostica (anteprima)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) per inviare i log ad account di archiviazione, Hub eventi e log Analytics. 

> [!div class="mx-imgBorder"]
> ![impostazioni di diagnostica (anteprima)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipi di log supportati

La tabella seguente illustra i tipi di log e le descrizioni supportati: 

| Tipo di log | Supporto di Windows | Supporto per Linux (Docker) | Description |
|-|-|-|
| AppServiceConsoleLogs | Da definire | SÌ | Output standard e errore standard |
| AppServiceHTTPLogs | SÌ | SÌ | Web Server Logs |
| AppServiceEnvironmentPlatformLogs | SÌ | SÌ | Ambiente del servizio app: ridimensionamento, modifiche di configurazione e log di stato|
| AppServiceAuditLogs | SÌ | SÌ | Attività di accesso tramite FTP e Kudu |
| AppServiceFileAuditLogs | Da definire | Da definire | Modifiche ai file tramite FTP e Kudu |
| AppServiceAppLogs | Da definire | Java SE & Tomcat | Log applicazioni |

## <a name="nextsteps"></a> Passaggi successivi
* [Eseguire query sui log con monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)
* [Come monitorare il Servizio app di Azure](web-sites-monitor.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
