---
title: Riferimento per le impostazioni dell’app per Funzioni di Azure
description: Documentazione di riferimento per le impostazioni o le variabili di ambiente dell'app Funzioni di Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: ec309bc5484c0ac96d1323c56670c147737e7c64
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377864"
---
# <a name="app-settings-reference-for-azure-functions"></a>Riferimento per le impostazioni dell’app per Funzioni di Azure

Le impostazioni dell'app in un'app per le funzioni contengono le opzioni di configurazione globali che interessano tutte le funzioni della specifica app per le funzioni. Quando l’esecuzione avviene in locale, queste impostazioni sono nelle [variabili di ambiente](functions-run-local.md#local-settings-file). In questo articolo sono elencate le impostazioni dell’app che sono disponibili nelle app per le funzioni.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Sono disponibili altre opzioni di configurazione globali nel file [host.json](functions-host-json.md) e nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

La chiave di strumentazione di Application Insights se si usa Application Insights. Vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

|Chiave|Valore di esempio|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Stringa di connessione dell’account di archiviazione facoltativo per l'archiviazione dei log e la visualizzazione nella scheda **Monitor** nel portale. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Vedere [Account di archiviazione](functions-infrastructure-as-code.md#storage-account) e [Requisiti dell'account di archiviazione](functions-create-function-app-portal.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

> [!TIP]
> Per le prestazioni e l'esperienza, si consiglia di usare APPINSIGHTS_INSTRUMENTATIONKEY e Application Insights per il monitoraggio anziché AzureWebJobsDashboard

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

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Il percorso alla directory radice in cui si trovano il file *host.json* e le cartelle di file e funzioni. In un'app per le funzioni, il valore predefinito è `%HOME%\site\wwwroot`.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsScriptRoot|%HOME%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Specifica il repository o il provider da utilizzare per l'archiviazione delle chiavi. I repository supportati attualmente sono blob ("Blob") e file system ("disabled"). Il valore predefinito è file system ("disabled").

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsSecretStorageType|disabled|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Il runtime di Funzioni di Azure usa questa stringa di connessione per l'account di archiviazione per tutte le funzioni ad eccezione delle funzioni attivate da HTTP. L'account di archiviazione deve essere un'istanza generica che supporta i BLOB, le code e le tabelle. Vedere [Account di archiviazione](functions-infrastructure-as-code.md#storage-account) e [Requisiti dell'account di archiviazione](functions-create-function-app-portal.md#storage-account-requirements).

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Percorso per il compilatore usato per TypeScript. Consente di eseguire l'override dell'impostazione predefinita, se necessario.

|Chiave|Valore di esempio|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

I valori validi sono "readwrite" e "readonly".

|Chiave|Valore di esempio|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

La versione del runtime di Funzioni da utilizzare in questa app per le funzioni. Una tilde con versione principale indica l’uso della versione più recente di quella versione principale (ad esempio, "~2"). Quando sono disponibili nuove versioni per la stessa versione principale, vengono installate automaticamente nell'app per le funzioni. Per aggiungere l'app a una versione specifica, utilizzare il numero di versione completo (ad esempio, "2.0.12345"). Il valore predefinito è "~2". Un valore di `~1` aggiunge l'app alla versione 1.x del runtime.

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functionsworkerruntime"></a>FUNCTIONS\_WORKER\_RUNTIME

Il runtime del ruolo di lavoro del linguaggio da caricare nell'app per le funzioni.  Corrisponderà al linguaggio usato nell'applicazione (ad esempio, "dotnet"). Per le funzioni in più lingue, sarà necessario pubblicarle in più app, ciascuna con un valore del runtime del ruolo di lavoro corrispondente.  I valori validi sono `dotnet` (C#/F#), `node` (JavaScript) e `java` (Java).

|Chiave|Valore di esempio|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Solo per i piani di consumo. Stringa di connessione per l'account di archiviazione in cui sono archiviati il codice e la configurazione dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#create-a-function-app).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[nome];AccountKey=[chiave]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

Solo per i piani di consumo. Il percorso del file per il codice e la configurazione dell’app per le funzioni. Usato con WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Il valore predefinito è una stringa univoca che inizia con il nome dell’app per le funzioni. Vedere [Creare un'app per le funzioni](functions-infrastructure-as-code.md#create-a-function-app).

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Il numero massimo di istanze che l'app per le funzioni è in grado di scalare orizzontalmente. Il valore predefinito è no limit.

> [!NOTE]
> Questa impostazione è una funzionalità di anteprima ed è affidabile solo se è impostata su un valore <= 5

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

Il valore predefinito è "8.11.1".

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Consente l'esecuzione dell'app per le funzioni da un file di pacchetto montato.

|Chiave|Valore di esempio|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

I valori validi sono un URL che si risolve nel percorso di un file di pacchetto di distribuzione o `1`. Se impostato su `1`, il pacchetto deve trovarsi nella cartella `d:\home\data\SitePackages`. Quando si usa la distribuzione di file zip con questa impostazione, il pacchetto viene caricato automaticamente in questo percorso. In anteprima questa impostazione era denominata `WEBSITE_RUN_FROM_ZIP`. Per altre informazioni, vedere [Run your Functions from a package file](run-functions-from-deployment-package.md) (Esecuzione di Funzioni di Azure da un file di pacchetto).

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni su come aggiornare le impostazioni dell'app](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Vedere le impostazioni globali nel file host.json](functions-host-json.md)

[Vedere altre impostazioni app per le app del servizio app](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
