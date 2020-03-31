---
title: Riferimento per le impostazioni dell’app per Funzioni di Azure
description: Documentazione di riferimento per le impostazioni o le variabili di ambiente dell'app Funzioni di Azure.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277830"
---
# <a name="app-settings-reference-for-azure-functions"></a>Riferimento per le impostazioni dell’app per Funzioni di Azure

Le impostazioni dell'app in un'app per le funzioni contengono le opzioni di configurazione globali che interessano tutte le funzioni della specifica app per le funzioni. Quando l'esecuzione avviene in locale, queste impostazioni sono nelle [variabili di ambiente](functions-run-local.md#local-settings-file). In questo articolo sono elencate le impostazioni dell’app che sono disponibili nelle app per le funzioni.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Sono disponibili altre opzioni di configurazione globali nel file [host.json](functions-host-json.md) e nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

La chiave di strumentazione di Application Insights se si usa Application Insights. Vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

|Chiave|Valore di esempio|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Nella versione 2.x e nelle versioni successive del runtime di Funzioni, configura il comportamento dell'app in base all'ambiente di runtime. Questo valore viene [letto durante l'inizializzazione.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43) È possibile `AZURE_FUNCTIONS_ENVIRONMENT` impostare su qualsiasi valore, ma sono supportati [tre valori:](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) [Sviluppo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Gestione temporanea](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [Produzione](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` non è impostato, il `Development` valore predefinito `Production` è in un ambiente locale e in Azure.When isn't set, it defaults to on a local environment and on Azure. Questa impostazione deve `ASPNETCORE_ENVIRONMENT` essere utilizzata al posto di impostare l'ambiente di runtime. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Stringa di connessione dell’account di archiviazione facoltativo per l'archiviazione dei log e la visualizzazione nella scheda **Monitor** nel portale. Questa impostazione è valida solo per le app destinate alla versione 1.x del runtime di Funzioni di Azure.This setting is only valid for apps that target version 1.x of the Azure Functions runtime. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Per altre informazioni, vedere [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> Per migliorare le prestazioni e l'esperienza, la versione di runtime `AzureWebJobsDashboard`2.x e versioni successive usa APPINSIGHTS_INSTRUMENTATIONKEY e App Insights per il monitoraggio anziché .

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

Indica se la modifica nel portale di Azure è abilitata. I valori validi sono "readwrite" e "readonly".

|Chiave|Valore di esempio|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

La versione del runtime di Funzioni da utilizzare in questa app per le funzioni. Una tilde con versione principale indica l’uso della versione più recente di quella versione principale (ad esempio, "~2"). Quando sono disponibili nuove versioni per la stessa versione principale, vengono installate automaticamente nell'app per le funzioni. Per aggiungere l'app a una versione specifica, utilizzare il numero di versione completo (ad esempio, "2.0.12345"). Il valore predefinito è "~2". Un valore di `~1` aggiunge l'app alla versione 1.x del runtime.

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNZIONI\_\_V2\_COMPATIBILITY MODE

Questa impostazione consente all'app per le funzioni di essere eseguita in una modalità compatibile con la versione 2.x nel runtime della versione 3.x. Utilizzare questa impostazione solo se si verificano problemi durante [l'aggiornamento dell'app per le funzioni dalla versione 2.x alla versione 3.x del runtime.](functions-versions.md#migrating-from-2x-to-3x) 

>[!IMPORTANT]
> Questa impostazione è intesa solo come soluzione alternativa a breve termine durante l'aggiornamento dell'app per la corretta esecuzione nella versione 3.x. Questa impostazione è supportata finché il [runtime 2.x è supportato.](functions-versions.md) Se riscontri problemi che impediscono l'esecuzione dell'app nella versione 3.x senza utilizzare questa impostazione, [segnala il problema.](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)

Richiede che [LA versione di EXTENSION\_\_di FUNCTIONS](functions-app-settings.md#functions_extension_version) sia impostata su . `~3`

|Chiave|Valore di esempio|
|---|------------|
|FUNZIONI\_\_V2\_COMPATIBILITY MODE|true|

## <a name="functions_worker_process_count"></a>CONTEGGIO\_\_PROCESSI\_DI LAVORO FUNZIONI

Specifica il numero massimo di processi di lavoro linguistico, con un valore predefinito di `1`. Il valore massimo `10`consentito è . Le chiamate di funzione sono distribuite in modo uniforme tra i processi di lavoro linguistico. I processi di lavoro linguistico vengono generati ogni 10\_\_secondi\_fino a quando non viene raggiunto il conteggio impostato da FUNCTIONS WORKER PROCESS COUNT. L'utilizzo di più processi di lavoro linguistico non equivale al [ridimensionamento.](functions-scale.md) È consigliabile usare questa impostazione quando il carico di lavoro include una combinazione di chiamate associate alla CPU e associate a I/O.Consider using this setting when your workload has a mix of CPU-bound and I/O-bound invocations. Questa impostazione si applica a tutte le lingue non-.NET.

|Chiave|Valore di esempio|
|---|------------|
|CONTEGGIO\_\_PROCESSI\_DI LAVORO FUNZIONI|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Il runtime del ruolo di lavoro del linguaggio da caricare nell'app per le funzioni.  Corrisponderà al linguaggio usato nell'applicazione (ad esempio, "dotnet"). Per le funzioni in più lingue, sarà necessario pubblicarle in più app, ciascuna con un valore del runtime del ruolo di lavoro corrispondente.  I valori `dotnet` validi sono (C `node` , F , `java` ), (JavaScript/TypeScript), (Java), `powershell` (PowerShell) e `python` (Python).

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Solo per i piani Consumo & Premium. Stringa di connessione per l'account di archiviazione in cui sono archiviati il codice e la configurazione dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#create-a-function-app).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Solo per i piani Consumo & Premium. Il percorso del file per il codice e la configurazione dell’app per le funzioni. Usato con WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Il valore predefinito è una stringa univoca che inizia con il nome dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#create-a-function-app).

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
Imposta la versione di Node.js da utilizzare quando si esegue l'app per le funzioni in Windows. È necessario utilizzare una tilde () per fare in modo che il runtime utilizzi l'ultima versione disponibile della versione principale di destinazione. Ad esempio, se `~10`impostato su , viene utilizzata la versione più recente di Node.js 10. Quando una versione principale è destinata a una tilde, non è necessario aggiornare manualmente la versione secondaria. 

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|10 USD|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Consente l'esecuzione dell'app per le funzioni da un file di pacchetto montato.

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

I valori validi sono un URL che si risolve nel percorso di un file di pacchetto di distribuzione o `1`. Se impostato su `1`, il pacchetto deve trovarsi nella cartella `d:\home\data\SitePackages`. Quando si usa la distribuzione di file zip con questa impostazione, il pacchetto viene caricato automaticamente in questo percorso. In anteprima questa impostazione era denominata `WEBSITE_RUN_FROM_ZIP`. Per altre informazioni, vedere [Run your Functions from a package file](run-functions-from-deployment-package.md) (Esecuzione di Funzioni di Azure da un file di pacchetto).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Per impostazione predefinita, i proxy di funzioni usano un collegamento per inviare chiamate API dai proxy direttamente alle funzioni della stessa app per le funzioni, anziché creare una nuova richiesta HTTP. Questa impostazione consente di disabilitare tale comportamento.

|Chiave|valore|Descrizione|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Le chiamate con un URL back-end che punta a una funzione nell'app funzione locale non verranno più inviate direttamente alla funzione e verranno invece indirizzate al front-end HTTP per l'app per le funzioni|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Si tratta del valore predefinito. Le chiamate con un URL back-end che punta a una funzione nell'app per le funzioni locale verranno inoltrate direttamente a tale funzione|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Questa impostazione permette di controllare se %2F viene decodificato come barre nei parametri di route quando vengono inseriti nell'URL di back-end. 

|Chiave|valore|Descrizione|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|I parametri di route con barre codificate saranno decodificati. `example.com/api%2ftest` diventerà `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Questo è il comportamento predefinito. Tutti i parametri di route saranno trasmessi senza modifiche|

### <a name="example"></a>Esempio

Di seguito un esempio di proxies.json in un'app per le funzioni all'URL myfunction.com

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
|Decodifica URL|Input|Output|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su come aggiornare le impostazioni dell'app](functions-how-to-use-azure-function-app-settings.md#settings)

[Vedere le impostazioni globali nel file host.json](functions-host-json.md)

[Vedere altre impostazioni app per le app del servizio app](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
