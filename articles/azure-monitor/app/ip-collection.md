---
title: Raccolta di indirizzi IP di applicazione Azure Insights | Microsoft Docs
description: Informazioni su come gestire gli indirizzi IP e la georilevazione con applicazione Azure Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/11/2019
ms.openlocfilehash: bee3e24739aa560a43960143d1a18c30ec1ce160
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819440"
---
# <a name="geolocation-and-ip-address-handling"></a>Georilevazione e gestione degli indirizzi IP

Questo articolo illustra il modo in cui viene eseguita la ricerca georilevazione e la gestione degli indirizzi IP in Application Insights insieme a come modificare il comportamento predefinito.

## <a name="default-behavior"></a>Comportamento predefinito

Per impostazione predefinita, gli indirizzi IP vengono raccolti temporaneamente, ma non archiviati in Application Insights. Il processo di base è il seguente:

Gli indirizzi IP vengono inviati a Application Insights come parte dei dati di telemetria. Quando si raggiunge l'endpoint di inserimento in Azure, l'indirizzo IP viene usato per eseguire una ricerca georilevazione usando [GeoLite2 da MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). I risultati di questa ricerca vengono utilizzati per popolare i campi seguenti `client_City`, `client_StateOrProvince`, `client_CountryOrRegion`. A questo punto, l'indirizzo IP viene eliminato e `0.0.0.0` viene scritto nel campo `client_IP`.

* Telemetria del browser: si raccoglie temporaneamente l'indirizzo IP del mittente. L'indirizzo IP viene calcolato dall'endpoint di inserimento.
* Telemetria del server: il modulo Application Insights raccoglie temporaneamente l'indirizzo IP del client. L'indirizzo non viene raccolto se è impostato `X-Forwarded-For`.

Questo comportamento è progettato per evitare una raccolta non necessaria di dati personali. Laddove possibile, è consigliabile evitare la raccolta di dati personali. 

## <a name="overriding-default-behavior"></a>Override del comportamento predefinito

Mentre il comportamento predefinito prevede la riduzione della raccolta dei dati personali, è ancora disponibile la flessibilità necessaria per raccogliere e archiviare i dati degli indirizzi IP. Prima di scegliere di archiviare i dati personali come gli indirizzi IP, è consigliabile verificare che questo non interrompa i requisiti di conformità o le normative locali che potrebbero essere soggette a. Per ulteriori informazioni sulla gestione dei dati personali in Application Insights, consultare le [linee guida per i dati personali](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Archiviazione dei dati degli indirizzi IP

Per abilitare la raccolta e l'archiviazione IP, è necessario impostare la proprietà `DisableIpMasking` del componente Application Insights su `true`. Questa proprietà può essere impostata tramite Azure Resource Manager modelli o chiamando l'API REST. 

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>di Microsoft Azure 

Se è necessario modificare solo il comportamento per una singola risorsa Application Insights il modo più semplice per eseguire questa operazione è tramite la portale di Azure.  

1. Passare alle **Impostazioni** del > di risorse Application Insights > **Esporta modello** 

    ![Esporta modello](media/ip-collection/export-template.png)

2. Selezionare **Distribuisci**

    ![Pulsante Distribuisci evidenziato in rosso](media/ip-collection/deploy.png)

3. Selezionare **modifica modello**. Se il modello contiene proprietà o risorse aggiuntive che non sono presenti in questo modello di esempio, procedere con cautela per assicurarsi che tutte le risorse accettino la distribuzione del modello come modifica/aggiornamento incrementale.

    ![Modifica modello](media/ip-collection/edit-template.png)

4. Apportare le modifiche seguenti al codice JSON per la risorsa e quindi fare clic su **Salva**:

    ![Screenshot aggiunge una virgola dopo "IbizaAIExtension" e aggiunge una nuova riga sotto "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Se si verifica un errore che indica che  **_il gruppo di risorse si trova in una posizione non supportata da una o più risorse nel modello. Scegliere un gruppo di risorse diverso._** Selezionare temporaneamente un gruppo di risorse diverso nell'elenco a discesa e quindi selezionare di nuovo il gruppo di risorse originale per risolvere l'errore.

5. Selezionare **accetto** > **acquisto**. 

    ![Modifica modello](media/ip-collection/purchase.png)

    In questo caso non è in corso l'acquisto di nuovi elementi, si sta semplicemente aggiornando la configurazione della risorsa Application Insights esistente.

6. Al termine della distribuzione, verranno registrati i nuovi dati di telemetria.

    Se si sceglie di nuovo modello e si modifica il modello, verrà visualizzato solo il modello predefinito e non verrà visualizzata la proprietà appena aggiunta e il relativo valore associato. Se non vengono visualizzati i dati degli indirizzi IP e si desidera confermare che `"DisableIpMasking": true` è impostato. Eseguire il comando PowerShell seguente: sostituire `Fabrikam-dev` con il nome della risorsa e del gruppo di risorse appropriati.
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Verrà restituito un elenco di proprietà di conseguenza. Una delle proprietà deve leggere `DisableIpMasking: true`. Se si esegue PowerShell prima di distribuire la nuova proprietà con Azure Resource Manager, la proprietà non sarà presente.

### <a name="rest-api"></a>API REST

Il payload dell' [API REST](https://docs.microsoft.com/rest/api/azure/) per apportare le stesse modifiche è il seguente:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inizializzatore della telemetria

Se è necessaria un'alternativa più flessibile rispetto a `DisableIpMasking` per registrare tutti gli indirizzi IP o parte di essi, è possibile usare un [inizializzatore di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) per copiare tutto o parte dell'indirizzo IP in un campo personalizzato. 

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Se non si riesce ad accedere a `ISupportProperties`, verificare e assicurarsi di eseguire la versione stabile più recente di Application Insights SDK. `ISupportProperties` sono destinati a valori di cardinalità elevata, mentre `GlobalProperties` sono più appropriati per i valori di cardinalità bassa, ad esempio il nome dell'area, il nome dell'ambiente e così via. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Abilitare l'inizializzatore di telemetria per. ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Abilita inizializzatore di telemetria per ASP.NET Core

È possibile creare l'inizializzatore di telemetria nello stesso modo in cui ASP.NET Core ASP.NET ma per abilitare l'inizializzatore, usare l'esempio seguente per riferimento:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>JavaScript lato client

A differenza degli SDK sul lato server, l'SDK JavaScript sul lato client non calcola l'indirizzo IP. Per impostazione predefinita, il calcolo degli indirizzi IP per la telemetria sul lato client viene eseguito nell'endpoint di inserimento in Azure al momento dell'arrivo della telemetria. Ciò significa che se si inviano dati lato client a un proxy e quindi si inoltra all'endpoint di inserimento, il calcolo degli indirizzi IP potrebbe visualizzare l'indirizzo IP del proxy e non il client. Se non viene utilizzato alcun proxy, questo non dovrebbe essere un problema.

Se si desidera calcolare l'indirizzo IP direttamente sul lato client, è necessario aggiungere la logica personalizzata per eseguire questo calcolo e utilizzare il risultato per impostare il tag `ai.location.ip`. Quando si imposta `ai.location.ip`, il calcolo degli indirizzi IP non viene eseguito dall'endpoint di inserimento e l'indirizzo IP fornito viene rispettato e usato per eseguire la ricerca geografica. In questo scenario, l'indirizzo IP verrà ancora azzerato per impostazione predefinita. 

Per mantenere l'intero indirizzo IP calcolato dalla logica personalizzata, è possibile usare un inizializzatore di telemetria che copia i dati degli indirizzi IP specificati in `ai.location.ip` in un campo personalizzato distinto. Tuttavia, a differenza degli SDK sul lato server, senza basarsi su librerie di terze parti o su una logica di raccolta IP sul lato client personalizzata, l'SDK lato client non calcolerà automaticamente l'indirizzo IP.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Visualizzare i risultati dell'inizializzatore di telemetria

Se quindi si attiva il nuovo traffico sul sito e si attendono circa 2-5 minuti per assicurarsi che il tempo venga inserito, è possibile eseguire una query kusto per verificare se la raccolta di indirizzi IP funziona:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nella colonna `customDimensions_client-ip` verranno visualizzati gli indirizzi IP appena raccolti. Per la colonna `client-ip` predefinita, tutti i 4 ottetti verranno azzerati o verranno visualizzati solo i primi tre ottetti a seconda di come è stata configurata la raccolta di indirizzi IP a livello di componente. Se si esegue il test in locale dopo l'implementazione dell'inizializzatore di telemetria e il valore visualizzato per `customDimensions_client-ip` è `::1` questo comportamento previsto. `::1` rappresenta l'indirizzo di loopback in IPv6. Equivale a `127.0.01` in IPv4 ed è il risultato che verrà visualizzato durante il test da localhost.

## <a name="next-steps"></a>Fasi successive

* Scopri di più sulla [raccolta di dati personali](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) in Application Insights.

* Altre informazioni sul funzionamento della [raccolta di indirizzi IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) in Application Insights. Si tratta di un post di Blog esterno precedente scritto da uno dei nostri tecnici. Precede il comportamento predefinito corrente in cui l'indirizzo IP viene registrato come `0.0.0.0`, ma entra in una profondità maggiore sui meccanismi della `ClientIpHeaderTelemetryInitializer`incorporata.