---
title: Riferimento per le impostazioni dell’app per Funzioni di Azure
description: Documentazione di riferimento per le impostazioni o le variabili di ambiente dell'app Funzioni di Azure.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 2be13fbdbf8ce75a051448bfb46d2a41ad425be8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242764"
---
# <a name="app-settings-reference-for-azure-functions"></a>Riferimento per le impostazioni dell’app per Funzioni di Azure

Le impostazioni dell'app in un'app per le funzioni contengono le opzioni di configurazione globali che interessano tutte le funzioni della specifica app per le funzioni. Quando l'esecuzione avviene in locale, queste impostazioni sono nelle [variabili di ambiente](functions-run-local.md#local-settings-file). In questo articolo sono elencate le impostazioni dell’app che sono disponibili nelle app per le funzioni.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Sono disponibili altre opzioni di configurazione globali nel file [host.json](functions-host-json.md) e nel file [local.settings.json](functions-run-local.md#local-settings-file).

> [!NOTE]  
> È possibile utilizzare le impostazioni dell'applicazione per eseguire l'override host.jssull'impostazione di valori senza dover modificare il host.jsnel file stesso. Questa operazione è utile per gli scenari in cui è necessario configurare o modificare host.jsspecifiche sulle impostazioni per un ambiente specifico. Questo consente anche di modificare host.jssulle impostazioni senza dover ripubblicare il progetto. Per altre informazioni, vedere l' [articolohost.jssu riferimento](functions-host-json.md#override-hostjson-values).  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Chiave di strumentazione per Application Insights. Usare solo uno di `APPINSIGHTS_INSTRUMENTATIONKEY` o `APPLICATIONINSIGHTS_CONNECTION_STRING` . Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). 

|Chiave|Valore di esempio|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Stringa di connessione per Application Insights. Usare `APPLICATIONINSIGHTS_CONNECTION_STRING` anziché `APPINSIGHTS_INSTRUMENTATIONKEY` quando l'app per le funzioni richiede la personalizzazione aggiuntiva supportata tramite la stringa di connessione. Per altre informazioni, vedere [stringhe di connessione](../azure-monitor/app/sdk-connection-string.md). 

|Chiave|Valore di esempio|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [chiave]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Per impostazione predefinita, i [proxy di funzioni](functions-proxies.md) usano un collegamento per inviare chiamate API da proxy direttamente alle funzioni nella stessa app per le funzioni. Questo tasto di scelta rapida viene utilizzato anziché creare una nuova richiesta HTTP. Questa impostazione consente di disabilitare il comportamento del collegamento.

|Chiave|Valore|Descrizione|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Le chiamate con un URL back-end che punta a una funzione nell'app per le funzioni locale non verranno inviate direttamente alla funzione. Al contrario, le richieste vengono indirizzate di nuovo al front-end HTTP per l'app per le funzioni.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Le chiamate con un URL back-end che punta a una funzione nell'app per le funzioni locale vengono indirizzate direttamente alla funzione. Si tratta del valore predefinito. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Questa impostazione determina se i caratteri `%2F` vengono decodificati come barre nei parametri di route quando vengono inseriti nell'URL back-end. 

|Chiave|Valore|Descrizione|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|I parametri di route con barre codificate vengono decodificati. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Tutti i parametri di route vengono passati insieme a Unchanged, che è il comportamento predefinito. |

Si consideri, ad esempio, il proxies.jssu file per un'app per le funzioni nel `myfunction.com` dominio.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Quando `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` è impostato su `true` , l'URL viene `example.com/api%2ftest` risolto in `example.com/api/test` . Per impostazione predefinita, l'URL rimane invariato come `example.com/test%2fapi` . Per altre informazioni, vedere [proxy di funzioni](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Nella versione 2. x e nelle versioni successive del runtime di funzioni, configura il comportamento dell'app in base all'ambiente di Runtime. Questo valore viene [letto durante l'inizializzazione](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). È possibile impostare `AZURE_FUNCTIONS_ENVIRONMENT` su qualsiasi valore, ma sono supportati [tre valori](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [sviluppo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [gestione temporanea](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [produzione](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` non è impostato, il valore predefinito è `Development` in un ambiente locale e `Production` in Azure. Questa impostazione deve essere utilizzata anziché `ASPNETCORE_ENVIRONMENT` per impostare l'ambiente di Runtime. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Nella versione 2. x e nelle versioni successive del runtime di funzioni, le impostazioni dell'applicazione possono eseguire l'override [host.jssulle](functions-host-json.md) impostazioni nell'ambiente corrente. Queste sostituzioni sono espresse come impostazioni dell'applicazione denominate `AzureFunctionsJobHost__path__to__setting` . Per ulteriori informazioni, vedere [Override host.jssui valori](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Stringa di connessione dell’account di archiviazione facoltativo per l'archiviazione dei log e la visualizzazione nella scheda **Monitor** nel portale. Questa impostazione è valida solo per le app destinate alla versione 1. x del runtime di funzioni di Azure. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Per altre informazioni, vedere [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Per migliorare le prestazioni e l'esperienza, il runtime versione 2. x e versioni successive USA APPINSIGHTS_INSTRUMENTATIONKEY e Application Insights per il monitoraggio anziché `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` comporta la disabilitazione dell'impostazione predefinita per la pagina di destinazione per l'URL radice di un'app per le funzioni. Il valore predefinito è `false`.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando questa impostazione dell’app viene omessa o impostata su `false`, una pagina simile all'esempio seguente viene visualizzata in risposta all'URL `<functionappname>.azurewebsites.net`.

![Pagina di destinazione dell’app per le funzioni](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` comporta l'utilizzo in modalità di rilascio durante la compilazione del codice .NET. `false` comporta l'utilizzo in modalità debug. Il valore predefinito è `true`.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Un elenco delimitato da virgole di funzionalità beta da abilitare. Le funzionalità Beta abilitate per queste flag non sono pronte per la produzione, ma possono essere abilitate per l'utilizzo sperimentale prima di essere lanciate.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Specifica il repository o il provider da utilizzare per l'archiviazione delle chiavi. I repository supportati attualmente sono archiviazione BLOB ("Blob") e file system locale ("Files"). Il valore predefinito è BLOB nella versione 2 e file system nella versione 1.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsSecretStorageType|File|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Il runtime di Funzioni di Azure usa questa stringa di connessione per l'account di archiviazione per tutte le funzioni ad eccezione delle funzioni attivate da HTTP. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Vedere [Account di archiviazione](functions-infrastructure-as-code.md#storage-account) e [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Percorso per il compilatore usato per TypeScript. Consente di eseguire l'override dell'impostazione predefinita, se necessario.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Determina se la modifica nella portale di Azure è abilitata. I valori validi sono "readwrite" e "readonly".

|Chiave|Valore di esempio|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

La versione del runtime di Funzioni da utilizzare in questa app per le funzioni. Una tilde con versione principale indica l’uso della versione più recente di quella versione principale (ad esempio, "~2"). Quando sono disponibili nuove versioni per la stessa versione principale, vengono installate automaticamente nell'app per le funzioni. Per aggiungere l'app a una versione specifica, utilizzare il numero di versione completo (ad esempio, "2.0.12345"). Il valore predefinito è "~2". Un valore di `~1` aggiunge l'app alla versione 1.x del runtime.

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>Modalità di compatibilità di funzioni \_ v2 \_ \_

Questa impostazione consente all'app per le funzioni di essere eseguita in una modalità compatibile con la versione 2. x sul runtime della versione 3. x. Usare questa impostazione solo se si verificano problemi durante [l'aggiornamento dell'app per le funzioni dalla versione 2. x alla versione 3. x del runtime](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Questa impostazione è destinata solo a una soluzione alternativa a breve termine durante l'aggiornamento dell'applicazione per l'esecuzione corretta nella versione 3. x. Questa impostazione è supportata fino a quando il [Runtime 2. x è supportato](functions-versions.md). Se si verificano problemi che impediscono l'esecuzione dell'app nella versione 3. x senza usare questa impostazione, [segnalare il problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Richiede che [la \_ \_ versione dell'estensione funzioni](functions-app-settings.md#functions_extension_version) sia impostata su `~3` .

|Chiave|Valore di esempio|
|---|------------|
|Modalità di compatibilità di funzioni \_ v2 \_ \_|true|

## <a name="functions_worker_process_count"></a>\_numero di \_ processi di lavoro di funzioni \_

Specifica il numero massimo di processi di lavoro del linguaggio e il valore predefinito `1` . Il valore massimo consentito è `10` . Le chiamate di funzione sono distribuite uniformemente tra i processi di lavoro del linguaggio. I processi di lavoro del linguaggio vengono generati ogni 10 secondi finché non viene raggiunto il conteggio impostato dal conteggio dei processi di lavoro di funzioni \_ \_ \_ . L'utilizzo di più processi di lavoro in linguaggio non equivale al [ridimensionamento](functions-scale.md). Provare a usare questa impostazione quando il carico di lavoro include una combinazione di chiamate con binding CPU e I/O. Questa impostazione si applica a tutte le lingue non-.NET.

|Chiave|Valore di esempio|
|---|------------|
|\_numero di \_ processi di lavoro di funzioni \_|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Il runtime del ruolo di lavoro del linguaggio da caricare nell'app per le funzioni.  Corrisponderà al linguaggio usato nell'applicazione (ad esempio, "dotnet"). Per le funzioni in più lingue, sarà necessario pubblicarle in più app, ciascuna con un valore del runtime del ruolo di lavoro corrispondente.  I valori validi sono `dotnet` (C#/f #), `node` (JavaScript/typescript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>\_URL dell' \_ indice \_ aggiuntivo PIP

Il valore di questa impostazione indica un URL dell'indice del pacchetto personalizzato per le app Python. Utilizzare questa impostazione quando è necessario eseguire una compilazione remota utilizzando dipendenze personalizzate presenti in un indice di pacchetto aggiuntivo.   

|Chiave|Valore di esempio|
|---|------------|
|\_URL dell' \_ indice \_ aggiuntivo PIP|http://my.custom.package.repo/simple |

Per altre informazioni, vedere [dipendenze personalizzate](functions-reference-python.md#remote-build-with-extra-index-url) nella Guida di riferimento per gli sviluppatori Python.

## <a name="scale_controller_logging_enable"></a>\_ \_ Abilitazione della registrazione del controller di ridimensionamento \_

_Questa impostazione è attualmente disponibile in anteprima._  

Questa impostazione controlla la registrazione dal controller di scalabilità di funzioni di Azure. Per altre informazioni, vedere [ridimensionare i log del controller](functions-monitoring.md#scale-controller-logs-preview).

|Chiave|Valore di esempio|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights: Verbose|

Il valore di questa chiave viene fornito nel formato `<DESTINATION>:<VERBOSITY>` , che viene definito come segue:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>SITO Web \_ CONTENTAZUREFILECONNECTIONSTRING

Per l'utilizzo & solo piani Premium. Stringa di connessione per l'account di archiviazione in cui sono archiviati il codice e la configurazione dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#create-a-function-app).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Per l'utilizzo & solo piani Premium. Il percorso del file per il codice e la configurazione dell’app per le funzioni. Usato con WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Il valore predefinito è una stringa univoca che inizia con il nome dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#create-a-function-app).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Il numero massimo di istanze che l'app per le funzioni è in grado di scalare orizzontalmente. Il valore predefinito è no limit.

> [!NOTE]
> Questa impostazione è una funzionalità di anteprima ed è affidabile solo se è impostata su un valore <= 5

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Solo Windows._  
Imposta la versione di Node.js da usare quando si esegue l'app per le funzioni in Windows. È consigliabile usare una tilde (~) per fare in modo che il runtime usi la versione disponibile più recente della versione principale di destinazione. Se, ad esempio, si imposta su `~10` , viene utilizzata la versione più recente di Node.js 10. Quando una versione principale è destinata a una tilde, non è necessario aggiornare manualmente la versione secondaria. 

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Consente l'esecuzione dell'app per le funzioni da un file di pacchetto montato.

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

I valori validi sono un URL che si risolve nel percorso di un file di pacchetto di distribuzione o `1`. Se impostato su `1`, il pacchetto deve trovarsi nella cartella `d:\home\data\SitePackages`. Quando si usa la distribuzione di file zip con questa impostazione, il pacchetto viene caricato automaticamente in questo percorso. In anteprima questa impostazione era denominata `WEBSITE_RUN_FROM_ZIP`. Per altre informazioni, vedere [Run your Functions from a package file](run-functions-from-deployment-package.md) (Esecuzione di Funzioni di Azure da un file di pacchetto).

## <a name="website_time_zone"></a>\_fuso orario sito Web \_

Consente di impostare il fuso orario per l'app per le funzioni. 

|Chiave|Sistema operativo|Valore di esempio|
|---|--|------------|
|\_fuso orario sito Web \_|Windows|Ora solare fuso orientale|
|\_fuso orario sito Web \_|Linux|America/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su come aggiornare le impostazioni dell'app](functions-how-to-use-azure-function-app-settings.md#settings)

[Vedere le impostazioni globali nel file host.json](functions-host-json.md)

[Vedere altre impostazioni app per le app del servizio app](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
