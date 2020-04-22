---
title: Abilitare la registrazione diagnostica
description: Informazioni su come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione e su come accedere alle informazioni registrate da Azure.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769997"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Abilitare la registrazione diagnostica per le app nel Servizio app di Azure
## <a name="overview"></a>Panoramica
Azure offre diagnostica integrata per facilitare il debug di un'[app del servizio app](overview.md). In questo articolo viene descritto come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione. Viene anche descritto come accedere alle informazioni registrate da Azure.

L'articolo illustra anche l'uso del [portale di Azure](https://portal.azure.com) e dell'interfaccia della riga di comando di Azure per elaborare i log di diagnostica. Per informazioni sull'elaborazione dei log di diagnostica in Visual Studio vedere [Risoluzione dei problemi di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> In addition to the logging instructions in this article, there's new, integrated logging capability with Azure Monitoring. Per altre informazioni su questa funzionalità, vedere la sezione [Inviare log a Monitoraggio di Azure (anteprima).](#send-logs-to-azure-monitor-preview) 
>
>

|Type|Piattaforma|Location|Descrizione|
|-|-|-|-|
| Registrazione di applicazioni | Windows, Linux | File system del servizio app e/o BLOB di Archiviazione di AzureApp Service file system and/or Azure Storage blobs | Registra i messaggi generati dal codice dell'applicazione. I messaggi possono essere generati dal framework Web scelto o dal codice dell'applicazione direttamente utilizzando il modello di registrazione standard del linguaggio. A ogni messaggio viene assegnata una delle seguenti categorie: **Critical**, **Error**, **Warning**, **Info**, **Debug**e **Trace**. È possibile selezionare la modalità di dettaglio della registrazione impostando il livello di gravità quando si abilita la registrazione dell'applicazione.|
| Registrazione del server Web| WINDOWS | File system del servizio app o BLOB di Archiviazione di AzureApp Service file system or Azure Storage blobs| Dati delle richieste HTTP non elaborati nel formato di [file di registro esteso W3C](/windows/desktop/Http/w3c-logging). Ogni messaggio di log include dati quali il metodo HTTP, l'URI della risorsa, l'IP del client, la porta client, l'agente utente, il codice di risposta e così via. |
| Messaggi di errore dettagliati| WINDOWS | File system del servizio app | Copie delle pagine di errore *htm* che sarebbero state inviate al browser client. Per motivi di sicurezza, le pagine di errore dettagliate non devono essere inviate ai client nell'ambiente di produzione, ma il servizio app può salvare la pagina di errore ogni volta che si verifica un errore dell'applicazione con codice HTTP 400 o superiore. La pagina può contenere informazioni che consentono di determinare il motivo per cui il server restituisce il codice di errore. |
| Traccia delle richieste non riuscite | WINDOWS | File system del servizio app | Informazioni di analisi dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS utilizzati per elaborare la richiesta e il tempo impiegato in ogni componente. È utile per migliorare le prestazioni del sito o isolare uno specifico errore HTTP. Viene generata una cartella per ogni richiesta non riuscita, che contiene il file di log XML, e il foglio di stile XSL con cui visualizzare il file di log. |
| Registrazione della distribuzione | Windows, Linux | File system del servizio app | Registri per la pubblicazione di contenuto in un'app. La registrazione della distribuzione avviene automaticamente e non sono disponibili impostazioni configurabili per la registrazione della distribuzione. Consente di determinare il motivo per cui una distribuzione non è riuscita. Ad esempio, se si utilizza uno script di [distribuzione personalizzato,](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)è possibile utilizzare la registrazione della distribuzione per determinare il motivo per cui lo script non riesce. |

> [!NOTE]
> Il servizio app fornisce uno strumento di diagnostica interattivo dedicato che consente di risolvere i problemi dell'applicazione. Per altre informazioni, vedere [Panoramica](overview-diagnostics.md)della diagnostica del servizio app di Azure.For more information, see Azure App Service diagnostics overview .
>
> Inoltre, è possibile usare altri servizi di Azure per migliorare le funzionalità di registrazione e monitoraggio dell'app, ad esempio [Monitoraggio di Azure.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Abilitare la registrazione delle applicazioni (Windows)Enable application logging (Windows)

> [!NOTE]
> La registrazione dell'applicazione per l'archiviazione BLOB può usare solo gli account di archiviazione nella stessa area del servizio appApplication logging for blob storage can only use storage accounts in the same region del the App Service

Per abilitare la registrazione delle applicazioni per le app di Windows nel portale di [Azure,](https://portal.azure.com)passare all'app e selezionare **Registri del servizio app.**

Selezionare **Attivato** per **Registrazione applicazioni (file system)** o **Registrazione applicazioni (BLOB)** o entrambi. 

L'opzione **File system** è a scopo di debug temporaneo e si spegne in 12 ore. L'opzione **BLOB** è per la registrazione a lungo termine e richiede un contenitore di archiviazione BLOB in cui scrivere i log.  L'opzione **BLOB** include anche informazioni aggiuntive nei messaggi di log, ad esempio`InstanceId`l'ID`Tid`dell'istanza della[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)macchina virtuale di origine del messaggio di log ( ), l'ID del thread ( ) e un timestamp più granulare ( ).

> [!NOTE]
> Attualmente solo i log applicazioni .NET possono essere scritti nell'archiviazione BLOB. I log dell'applicazione Java, PHP, Node.js e Python possono essere archiviati solo nel file system del servizio app (senza modifiche al codice per scrivere i log nell'archiviazione esterna).
>
> Inoltre, se si [rigenerano le chiavi di accesso dell'account](../storage/common/storage-create-storage-account.md)di archiviazione, è necessario reimpostare la rispettiva configurazione di registrazione per utilizzare le chiavi di accesso aggiornate. A tale scopo, effettuare l'operazione seguente:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

Selezionare il **livello**o il livello di dettagli da registrare. La tabella seguente mostra le categorie di log incluse in ogni livello:

| Level | Categorie incluse |
|-|-|
|**Disabilitato** | nessuno |
|**Error (Errore) (Error (Errore)e)** | Errore, Errore critico |
|**Avviso** | Avviso, Errore, Errore critico|
|**Informazioni** | Informazioni, Avviso, Errore, Errore critico|
|**Dettagliato** | Analisi, Debug, Informazioni, Avviso, Errore, Errore critico (tutte le categorie) |

Al termine, selezionare **Salva**.

## <a name="enable-application-logging-linuxcontainer"></a>Abilitare la registrazione dell'applicazione (Linux/Contenitore)Enable application logging (Linux/Container)

Per abilitare la registrazione delle applicazioni per le app Linux o per i contenitori personalizzati nel portale di [Azure,](https://portal.azure.com)passare all'app e selezionare **Log del servizio app.**

In **Registrazione applicazione**selezionare File **system**.

In **Quota (MB)** specificare la quota disco per i registri applicazioni. In **Periodo di conservazione (giorni)** impostare il numero di giorni in cui i registri devono essere conservati.

Al termine, selezionare **Salva**.

## <a name="enable-web-server-logging"></a>Abilitazione della registrazione del server Web

Per abilitare la registrazione del server Web per le app di Windows nel portale di [Azure,](https://portal.azure.com)passare all'app e selezionare **Log del servizio app.**

Per **la registrazione del server Web**, selezionare **Archiviazione** per archiviare i log nell'archiviazione BLOB o File **system** per archiviare i log nel file system del servizio app. 

In **Periodo di conservazione (giorni)** impostare il numero di giorni in cui i registri devono essere conservati.

> [!NOTE]
> Se si [rigenerano le chiavi di accesso dell'account di archiviazione](../storage/common/storage-create-storage-account.md), è necessario reimpostare la configurazione di registrazione corrispondente per l'uso delle chiavi aggiornate. A tale scopo, effettuare l'operazione seguente:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

Al termine, selezionare **Salva**.

## <a name="log-detailed-errors"></a>Registrare gli errori dettagliati

Per salvare la pagina di errore o la traccia delle richieste non riuscite per le app di Windows nel portale di [Azure,](https://portal.azure.com)passare all'app e selezionare **Registri del servizio app**.

In **Registrazione dettagliata degli errori** o Traccia richieste non **riuscite**selezionare **Attivato**, quindi selezionare **Salva**.

Entrambi i tipi di log vengono archiviati nel file system del servizio app. Vengono mantenuti fino a 50 errori (file/cartelle). Quando il numero di file HTML supera 50, i 26 errori meno recenti vengono eliminati automaticamente.

## <a name="add-log-messages-in-code"></a>Aggiungere messaggi di log nel codiceAdd log messages in code

Nel codice dell'applicazione, utilizzare le normali funzionalità di registrazione per inviare messaggi di log ai log dell'applicazione. Ad esempio:

- Le applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) per registrare le informazioni nel log di diagnostica applicazioni. Ad esempio:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Per impostazione predefinita, ASP.NET Core usa il provider di registrazione [Microsoft.Extensions.Logging.AzureAppServices.By default, the Core use the Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) logging provider. Per altre informazioni, vedere [Registrazione in ASP.NET Core- Registrazione in Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Eseguire lo streaming dei log

Prima di trasmettere i log in tempo reale, abilitare il tipo di log desiderato. Tutte le informazioni scritte in file che terminano con .txt, .log o .htm che vengono memorizzati nella directory */LogFiles* (d:/home/logfiles) vengono trasmesse dal servizio app.

> [!NOTE]
> Alcuni tipi di buffer di registrazione scrivono nel file di log, producendo nel caso eventi di "fuori servizio" nel flusso. Ad esempio, una voce del log di applicazione che si verifica quando un utente visita una pagina può essere visualizzata nel flusso prima della corrispondente voce di log HTTP per la richiesta della pagina.
>

### <a name="in-azure-portal"></a>Nel portale di Azure

Per eseguire il flusso dei log nel [portale di Azure,](https://portal.azure.com)passare all'app e selezionare **Flusso di log**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Per trasmettere i log in diretta in [Cloud Shell,](../cloud-shell/overview.md)utilizzare il comando seguente:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Per filtrare eventi specifici, ad esempio gli errori, utilizzare il parametro **-Filter** . Ad esempio:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **--Path** . Ad esempio:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>Nel terminale locale

Per eseguire il flusso dei log nella console locale, [installare l'interfaccia della riga](https://docs.microsoft.com/cli/azure/install-azure-cli) di comando di Azure e accedere [all'account.](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) Una volta effettuato l'accesso, seguire le [istruzioni per Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Accedere ai file di log

Se si configura l'opzione BLOB di Archiviazione di Azure per un tipo di log, è necessario uno strumento client che funzioni con Archiviazione di Azure.If you configure the Azure Storage blobs option for a log type, you need a client tool that works with Azure Storage. Per altre informazioni, vedere [Strumenti client di Archiviazione di Azure](../storage/common/storage-explorers.md).For more information, see Azure Storage Client Tools .

Per i log archiviati nel file system del servizio app, il modo più semplice consiste nel scaricare il file zip nel browser all'inuso:

- Applicazioni Linux/contenitore:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- App di Windows:`https://<app-name>.scm.azurewebsites.net/api/dump`

Per le app Linux/contenitore, il file zip contiene i log di output della console sia per l'host docker che per il contenitore docker. Per un'app con scalabilità orizzontale, il file zip contiene un set di log per ogni istanza. Nel file system del servizio app, questi file di registro sono il contenuto della directory */home/LogFiles.*

Per le app di Windows, il file zip contiene il contenuto della directory *D:* Presenta la struttura seguente:

| Tipo di log | Directory | Descrizione |
|-|-|-|
| **Registri applicazioni** |*/LogFiles/Applicazione/* | Contiene uno o più file di testo. Il formato dei messaggi di log dipende dal provider di registrazione utilizzato. |
| **Tracce delle richieste non riusciteFailed Request Traces** | */LogFiles/W3SVC* | Contiene file XML e un file XSL. È possibile visualizzare i file XML formattati nel browser. |
| **Registri degli errori dettagliati** | */LogFiles/DetailedErrors/* | Contiene i file di errore HTM. È possibile visualizzare i file HTM nel browser.<br/>Un altro modo per visualizzare le tracce delle richieste non riuscite consiste nell'accedere alla pagina dell'app nel portale. Dal menu a sinistra, selezionare **Diagnostica e risolvi problemi,** quindi cercare **Log di traccia delle richieste non riuscite**, quindi fare clic sull'icona per sfogliare e visualizzare la traccia desiderata. |
| **Registri del server Web** | */LogFiles/http/RawLogs/* | Contiene i file di testo formattati utilizzando il formato di [file registro esteso W3C](/windows/desktop/Http/w3c-logging). Queste informazioni possono essere lette utilizzando un editor di testo o un'utilità come [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>Il servizio app non `s-computername` `s-ip`supporta `cs-version` i campi , o . |
| **Registri di distribuzione** | */LogFiles/Git/* e */deployments/* | Contengono i log generati dai processi di distribuzione interni, nonché i log per le distribuzioni Git. |

## <a name="send-logs-to-azure-monitor-preview"></a>Inviare log a Monitoraggio di Azure (anteprima)Send logs to Azure Monitor (preview)

Con la nuova [integrazione](https://aka.ms/appsvcblog-azmon)di Monitoraggio di Azure è possibile [creare impostazioni di diagnostica (anteprima)](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) per inviare i log agli account di archiviazione, agli hub eventi e a Log Analytics. 

> [!div class="mx-imgBorder"]
> ![Impostazioni di diagnostica (anteprima)Diagnostic Settings (preview)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Tipi di log supportati

Nella tabella seguente vengono illustrati i tipi di log e le descrizioni supportati: 

| Tipo di log | supporto per Windows | Supporto per Linux (Docker) | Descrizione |
|-|-|-|
| AppServiceConsoleLogs | TBA | Sì | Uscita standard ed errore standard |
| AppServiceHTTPLogs | Sì | Sì | Web Server Logs |
| AppServiceEnvironmentPlatformLogs | Sì | Sì | Ambiente del servizio app: scalabilità, modifiche alla configurazione e log di stato|
| AppServiceAuditLogs | Sì | Sì | Attività di accesso tramite FTP e Kudu |
| AppServiceFileAuditLogs | Sì | TBD | Modifiche ai file tramite FTP e Kudu |
| AppServiceAppLogs | TBA | Java SE & Tomcat | Log applicazioni |

## <a name="next-steps"></a><a name="nextsteps"></a> Passaggi successivi
* [Query logs with Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Come monitorare il Servizio app di Azure](web-sites-monitor.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
