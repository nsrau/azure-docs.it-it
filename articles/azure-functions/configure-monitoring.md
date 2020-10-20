---
title: Configurare il monitoraggio per funzioni di Azure
description: Informazioni su come connettere l'app per le funzioni a Application Insights per il monitoraggio e su come configurare la raccolta dei dati.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperfq2
ms.openlocfilehash: 50705eeedf9c985a053600a8c0b27c823231e9a3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217185"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Come configurare il monitoraggio per funzioni di Azure

Funzioni di Azure si integra con Application Insights per consentire un monitoraggio migliore delle app per le funzioni. Application Insights, una funzionalità di monitoraggio di Azure, è un servizio estendibile di gestione delle prestazioni delle applicazioni (APM) che raccoglie i dati generati dall'app per le funzioni, incluse le informazioni scritte dall'app nei log. L'integrazione di Application Insights viene in genere abilitata quando si crea l'app per le funzioni. Se l'app non ha il set di chiavi di strumentazione, è necessario abilitare prima l' [integrazione Application Insights](#enable-application-insights-integration). 

È possibile usare Application Insights senza alcuna operazione di configurazione personalizzata, poiché la configurazione predefinita può già produrre elevati volumi di dati. Se si usa una sottoscrizione di Azure di Visual Studio, si potrebbe raggiunge il limite d'uso dati per Application Insights. Per altre informazioni sui costi di Application Insights, vedere [gestire l'utilizzo e i costi per Application Insights](../azure-monitor/app/pricing.md).

Nelle sezioni successive di questo articolo viene illustrato come configurare e personalizzare i dati inviati dalle funzioni ad Application Insights. Per le app per le funzioni, la registrazione viene configurata nel file [host.json]. 

> [!NOTE]
> È possibile usare le impostazioni dell'applicazione configurate in modo specifico per rappresentare impostazioni specifiche in un host.jsfile per un ambiente specifico. Questo consente di modificare in modo efficace host.jsimpostazioni senza dover ripubblicare il host.jsnel file nel progetto. Per altre informazioni, vedere [eseguire l'Override host.jssui valori](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Configura categorie

Il logger di Funzioni di Azure include un *categoria* per ogni log. La categoria indica quale parte del codice runtime o del codice funzione è stata scritta dal log. Le categorie sono diverse tra la versione 1. x e le versioni successive. Nel grafico seguente vengono descritte le principali categorie di log create dal runtime. 

# <a name="v2x"></a>[V2. x +](#tab/v2)

| Category | Tabella | Descrizione |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dipendenze**| I dati sulle dipendenze vengono raccolti automaticamente per alcuni servizi. Per le esecuzioni riuscite, questi log sono al `Information` livello. Per altre informazioni, vedere [dipendenze](functions-monitoring.md#dependencies). Le eccezioni vengono registrate a `Error` livello di. Il runtime crea anche `Warning` log di livello, ad esempio quando i messaggi della coda vengono inviati alla [coda non elaborabile](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Gli SDK C# e JavaScript consentono di raccogliere metriche personalizzate e registrare eventi personalizzati. Per altre informazioni, vedere [dati di telemetria personalizzati](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| Include i log di funzione avviati e completati per esecuzioni di funzioni specifiche. Per le esecuzioni riuscite, questi log sono al `Information` livello. Le eccezioni vengono registrate a `Error` livello di. Il runtime crea anche `Warning` log di livello, ad esempio quando i messaggi della coda vengono inviati alla [coda non elaborabile](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| Log generati dall'utente, che possono essere a qualsiasi livello di log. Per altre informazioni sulla scrittura nei log delle funzioni, vedere [scrittura nei log](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Questi log generati in fase di esecuzione forniscono conteggi e medie delle chiamate di funzione in un periodo di tempo [configurabile](#configure-the-aggregator) . Il periodo predefinito è 30 secondi o 1000 risultati, ovvero quello che viene prima. Gli esempi indicano il numero di esecuzioni, la percentuale di riuscita e la durata. Tutti questi log vengono scritti al livello `Information`. Se si filtra per `Warning` o categoria successiva, non verrà visualizzato alcun dato. |
| **`Host.Results`** | **requests** | Questi log generati in fase di esecuzione indicano l'esito positivo o negativo di una funzione. Tutti questi log vengono scritti al livello `Information`. Se si filtra per `Warning` o categoria successiva, non verrà visualizzato alcun dato. |
| **`Microsoft`** | **traces** | Categoria di log completo che riflette un componente Runtime .NET richiamato dall'host.  |
| **`Worker`** | **traces** | Log generati dal processo di lavoro del linguaggio per le lingue non-.NET. I log del ruolo di lavoro della lingua possono anche essere registrati in una `Microsoft.*` categoria, ad esempio `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Questi log vengono scritti a `Information` livello di.|

# <a name="v1x"></a>[v1.x](#tab/v1)

| Category | Tabella | Descrizione |
| ----- | ----- | ----- |
| **`Function`** | **traces**| Log generati dall'utente, che possono essere a qualsiasi livello di log. Per altre informazioni sulla scrittura nei log delle funzioni, vedere [scrittura nei log](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Questi log generati in fase di esecuzione forniscono conteggi e medie delle chiamate di funzione in un periodo di tempo [configurabile](#configure-the-aggregator) . Il periodo predefinito è 30 secondi o 1000 risultati, ovvero quello che viene prima. Gli esempi indicano il numero di esecuzioni, la percentuale di riuscita e la durata. Tutti questi log vengono scritti al livello `Information`. Se si filtra per `Warning` o categoria successiva, non verrà visualizzato alcun dato. |
| **`Host.Executor`** | **traces** | Include la **funzione started** e i log di **funzione completati** per esecuzioni di funzione specifiche. Per le esecuzioni con esito positivo, questi log si trovano al livello `Information`. Le eccezioni vengono registrate al livello `Error`. Il runtime crea anche `Warning` log di livello, ad esempio quando i messaggi della coda vengono inviati alla [coda non elaborabile](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **requests** | Questi log generati in fase di esecuzione indicano l'esito positivo o negativo di una funzione. Tutti questi log vengono scritti al livello `Information`. Se si filtra per `Warning` o categoria successiva, non verrà visualizzato alcun dato. |

---

Nella colonna della **tabella** viene indicato in quale tabella Application Insights viene scritto il log. 

## <a name="configure-log-levels"></a>Configurare i livelli di registrazione

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Per ogni categoria, si indica il livello di registrazione minimo da inviare. Le impostazioni host.jssulle impostazioni variano a seconda della [versione del runtime di funzioni](functions-versions.md). 

Nell'esempio seguente viene definita la registrazione basata sulle regole seguenti:

+ Per i log di `Host.Results` o `Function` , solo gli eventi di registrazione a `Error` o a un livello superiore. 
+ Per i log di `Host.Aggregator` , registrare tutte le metriche generate ( `Trace` ).
+ Per tutti gli altri log, inclusi i log utente, il livello di registrazione `Information` e gli eventi più elevati.

# <a name="v2x"></a>[V2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Se [host.json] include più log che iniziano con la stessa stringa, vengono prima corrispondenti i registri più definiti. Si consideri l'esempio seguente che registra tutti gli elementi del runtime, ad eccezione `Host.Aggregator` di, a `Error` livello di:

# <a name="v2x"></a>[V2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

È possibile usare un'impostazione del livello di registrazione `None` per impedire che i log vengano scritti per una categoria. 

## <a name="configure-the-aggregator"></a>Configurare l'aggregatore

Come indicato nella sezione precedente, il runtime aggrega i dati sulle esecuzioni di funzioni in un periodo di tempo. Il periodo predefinito è 30 secondi o 1000 esecuzioni, ovvero quello che viene prima. È possibile configurare questa impostazione nel file [host.json].  Ad esempio:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurare il campionamento

Application Insights ha una funzionalità di [campionamento](../azure-monitor/app/sampling.md) che consente di evitare la produzione di un numero eccessivo di dati di telemetria sulle esecuzioni completate nei picchi di carico. Quando la frequenza delle esecuzioni in ingresso supera una soglia specificata, Application Insights inizia a ignorare in modo casuale alcune delle esecuzioni in ingresso. Il valore predefinito per il numero massimo di elementi al secondo è 20 (cinque nella versione 1.x). È possibile configurare il campionamento nel file [host.json](./functions-host-json.md#applicationinsights).  Ad esempio:

# <a name="v2x"></a>[V2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


È possibile escludere determinati tipi di dati di telemetria dal campionamento. In questo esempio, i dati di tipo `Request` e `Exception` vengono esclusi dal campionamento. In questo modo si garantisce che *tutte le* esecuzioni di funzioni (richieste) e le eccezioni vengano registrate mentre altri tipi di dati di telemetria rimangono soggetti al campionamento. 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Per altre informazioni, vedere [campionamento in Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Configurare i log del controller di scalabilità

_Questa funzionalità è in anteprima._ 

È possibile fare in modo che il [controller di scalabilità di funzioni di Azure](./functions-scale.md#runtime-scaling) emetta i log in Application Insights o nell'archiviazione BLOB per comprendere meglio le decisioni che il controller di scala sta effettuando per l'app per le funzioni.

Per abilitare questa funzionalità, è necessario aggiungere un'impostazione dell'applicazione denominata `SCALE_CONTROLLER_LOGGING_ENABLED` per le impostazioni dell'app per le funzioni. Il valore di questa impostazione deve essere nel formato `<DESTINATION>:<VERBOSITY>` , in base agli elementi seguenti:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Ad esempio, il comando dell'interfaccia della riga di comando di Azure seguente attiva la registrazione dettagliata dal controller di scalabilità a Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

In questo esempio sostituire `<FUNCTION_APP_NAME>` e `<RESOURCE_GROUP_NAME>` con il nome dell'app per le funzioni e il nome del gruppo di risorse, rispettivamente. 

Il seguente comando dell'interfaccia della riga di comando di Azure Disabilita la registrazione impostando il livello di dettaglio su `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

È anche possibile disabilitare la registrazione rimuovendo l' `SCALE_CONTROLLER_LOGGING_ENABLED` impostazione usando il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="enable-application-insights-integration"></a>Abilitare l'integrazione di Application Insights

Affinché un'app per le funzioni invii dati ad Application Insights, è necessario conoscere la chiave di strumentazione di una risorsa di Application Insights. La chiave deve essere specificata in un'impostazione dell'app denominata **APPINSIGHTS_INSTRUMENTATIONKEY**.

Quando si crea un'app per le funzioni [nel portale di Azure](functions-create-first-azure-function.md) usando [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md) o [Visual Studio Code](functions-create-first-function-vs-code.md) nella riga di comando, l'integrazione con Application Insights viene abilitata per impostazione predefinita. La risorsa di Application Insights prende lo stesso nome dell'app per le funzioni e viene creata nella stessa area o nell'area più vicina.

### <a name="new-function-app-in-the-portal"></a>Nuova app per le funzioni nel portale

Per verificare la risorsa di Application Insights appena creata, selezionarla per espandere la finestra **Application Insights**. È possibile cambiare il **Nome nuova risorsa** oppure scegliere una **Località** diversa in un'[area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vogliono archiviare i dati.

![Abilitare Application Insights mentre si crea un'app per le funzioni](media/functions-monitoring/enable-ai-new-function-app.png)

Se si sceglie **Crea**, viene creata una risorsa di Application Insights con l'app per le funzioni, con il parametro `APPINSIGHTS_INSTRUMENTATIONKEY` impostato nelle impostazioni dell'applicazione. È tutto pronto per iniziare.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Aggiungere un'app per le funzioni esistente 

Se non è stato creato un Application Insights risorse con l'app per le funzioni, seguire questa procedura per creare la risorsa. È quindi possibile aggiungere la chiave di strumentazione dalla risorsa come [impostazione dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings) nell'app per le funzioni.

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare app per le **funzioni**e quindi scegliere l'app per le funzioni. 

1. Selezionare il banner **Application Insights non è configurato** nella parte superiore della finestra. Se questo banner non è visibile, Application Insights potrebbe già essere abilitato per l'app.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Abilitare Application Insights nel portale":::

1. Espandere **Modificare la risorsa** e creare una risorsa di Application Insights usando le impostazioni specificate nella tabella seguente.  

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nuovo nome risorsa** | Nome app univoco | È più facile usare lo stesso nome dell'app per le funzioni, che deve essere univoco nella sottoscrizione. | 
    | **Posizione** | Europa occidentale | Se possibile, usare la stessa [area](https://azure.microsoft.com/regions/) dell'app per le funzioni oppure una vicina a tale area. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Abilitare Application Insights nel portale":::

1. Selezionare **Applica**. 

   La risorsa di Application Insights viene creata nella stesso gruppo di risorse e nella stessa sottoscrizione dell'app per le funzioni. Dopo aver creato la risorsa, chiudere la finestra di Application Insights.

1. Nell'app per le funzioni selezionare **Configurazione** in **Impostazioni** e quindi **Impostazioni applicazione**. Se viene visualizzata un'impostazione denominata `APPINSIGHTS_INSTRUMENTATIONKEY`, l'integrazione di Application Insights è abilitata per l'app per le funzioni in esecuzione in Azure. Se per qualche motivo questa impostazione non esiste, aggiungerla usando la chiave di strumentazione Application Insights come valore.

> [!NOTE]
> Nelle versioni precedenti di Funzioni veniva usato il servizio di monitoraggio incorporato, che non è più consigliato. Quando si abilita l'integrazione di Application Insights per un'app per le funzioni di questo tipo, è necessario anche [disabilitare la registrazione predefinita](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Disabilitare la registrazione predefinita

Quando si abilita Application Insights, disabilitare la registrazione predefinita che usa Archiviazione di Azure. La registrazione predefinita risulta utile per i test con carichi di lavoro leggeri, ma non è destinata all'uso in ambienti di produzione con carichi elevati. Per il monitoraggio della produzione, è consigliabile usare Application Insights. Se la registrazione predefinita viene usata in ambienti di produzione, è possibile che il record di registrazione non sia completo a causa delle limitazioni a livello di Archiviazione di Azure.

Per disabilitare la registrazione predefinita, eliminare l'impostazione app `AzureWebJobsDashboard`. Per informazioni su come eliminare le impostazioni app nel portale di Azure, vedere la sezione relativa alle **impostazioni dell'applicazione** in [Come gestire un'app per le funzioni nel portale di Azure](functions-how-to-use-azure-function-app-settings.md#settings). Prima di eliminare l'impostazione dell'app, verificare che l'impostazione non venga usata da nessun'altra funzione esistente nell'app per le funzioni per trigger o binding di Archiviazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul monitoraggio, vedere:

+ [Monitorare Funzioni di Azure](functions-monitoring.md)
+ [Analizzare i dati di telemetria di funzioni di Azure in Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
