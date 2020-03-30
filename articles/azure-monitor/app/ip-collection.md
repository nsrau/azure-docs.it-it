---
title: 'Raccolta di indirizzi IP di Azure Application Insights : Documenti Microsoft'
description: Informazioni sulla modalità di gestione degli indirizzi IP e della georilevazione con Azure Application InsightsUnderstanding how IP addresses and geolocation are handled with Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656518"
---
# <a name="geolocation-and-ip-address-handling"></a>Georilevazione e gestione degli indirizzi IP

Questo articolo illustra come si verificano la ricerca di georilevazione e la gestione degli indirizzi IP in Application Insights insieme a come modificare il comportamento predefinito.

## <a name="default-behavior"></a>Comportamento predefinito

Per impostazione predefinita, gli indirizzi IP vengono raccolti temporaneamente, ma non archiviati in Application Insights.By default IP addresses are temporarily collected, but not stored in Application Insights. Il processo di base è il seguente:

Gli indirizzi IP vengono inviati ad Application Insights come parte dei dati di telemetria. Una volta raggiunto l'endpoint di inserimento in Azure, l'indirizzo IP viene usato per eseguire una ricerca di georilevazione usando [GeoLite2 da MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). I risultati di questa ricerca vengono `client_City`utilizzati per popolare i seguenti campi, `client_StateOrProvince`, , `client_CountryOrRegion`. A questo punto, l'indirizzo `0.0.0.0` IP viene `client_IP` scartato e scritto nel campo.

* Telemetria del browser: raccogliamo temporaneamente l'indirizzo IP del mittente. L'indirizzo IP viene calcolato dall'endpoint di inserimento.
* Telemetria del server: il modulo Application Insights raccoglie temporaneamente l'indirizzo IP del client. L'indirizzo non viene raccolto se è impostato `X-Forwarded-For`.

Questo comportamento è progettato per evitare inutili raccolte di dati personali. Quando possibile, si consiglia di evitare la raccolta di dati personali. 

## <a name="overriding-default-behavior"></a>Override del comportamento predefinito

Mentre il comportamento predefinito è quello di ridurre al minimo la raccolta di dati personali, offriamo ancora la flessibilità per raccogliere e memorizzare i dati degli indirizzi IP. Prima di scegliere di archiviare dati personali come gli indirizzi IP, ti consigliamo vivamente di verificare che ciò non infranghi i requisiti di conformità o le normative locali a cui potresti essere soggetto. Per ulteriori informazioni sulla gestione dei dati personali in Application Insights, consultare le linee guida per i [dati personali.](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)

## <a name="storing-ip-address-data"></a>Memorizzazione dei dati dell'indirizzo IP

Per abilitare la raccolta e `DisableIpMasking` l'archiviazione IP, la `true`proprietà del componente Application Insights deve essere impostata su . Questa proprietà può essere impostata tramite i modelli di Azure Resource Manager o chiamando l'API REST. 

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

### <a name="portal"></a>Portale 

Se è necessario modificare il comportamento per una singola risorsa di Application Insights, il modo più semplice per eseguire questa operazione è tramite il portale di Azure.If you only need to modify the behavior for a single Application Insights resource, the easiest way to accomplish this is via the Azure portal.  

1. Passare alla risorsa di Application Insights > modello di esportazione **delle impostazioniGo** > your Application Insights resource > Settings**Export Template** 

    ![Esporta modello](media/ip-collection/export-template.png)

2. Selezionare **Distribuisci**

    ![Pulsante Distribuisci evidenziato in rosso](media/ip-collection/deploy.png)

3. Selezionare **Modifica modello**. Se il modello include proprietà o risorse aggiuntive che non vengono visualizzate in questo modello di esempio, prestare attenzione per assicurarsi che tutte le risorse accettino la distribuzione del modello come modifica/aggiornamento incrementale.

    ![Modificare il modello](media/ip-collection/edit-template.png)

4. Apportare le modifiche seguenti al json per la risorsa e quindi fare clic su **Salva:**

    ![Screenshot aggiunge una virgola dopo "IbizaAIExtension" e aggiungere una nuova riga di seguito con "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Se si verifica un errore che indica che il gruppo di risorse si ** _trova in una posizione non supportata da una o più risorse nel modello. Scegliere un gruppo_ di risorse diverso.** Selezionare temporaneamente un gruppo di risorse diverso dall'elenco a discesa e quindi selezionare nuovamente il gruppo di risorse originale per risolvere l'errore.

5. Selezionare **Accetto** > **Acquisto**. 

    ![Modificare il modello](media/ip-collection/purchase.png)

    In questo caso non viene acquistato nulla di nuovo, stiamo semplicemente aggiornando la configurazione della risorsa di Application Insights esistente.

6. Una volta completata la distribuzione, verranno registrati nuovi dati di telemetria.

    Se si selezionasse e si modificasse nuovamente il modello, verrà visualizzato solo il modello predefinito e non verrà visualizzata la proprietà appena aggiunta e il relativo valore associato. Se non vengono visualizzati i dati dell'indirizzo IP e si desidera confermare che `"DisableIpMasking": true` è impostato. Eseguire PowerShell seguente: `Fabrikam-dev` (Sostituire con il nome della risorsa e del gruppo di risorse appropriato).
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Di conseguenza verrà restituito un elenco di proprietà. Una delle proprietà `DisableIpMasking: true`deve leggere . Se si esegue PowerShell prima di distribuire la nuova proprietà con Azure Resource Manager, la proprietà non esisterà.

### <a name="rest-api"></a>API REST

Il payload [dell'API Rest](https://docs.microsoft.com/rest/api/azure/) per apportare le stesse modifiche è il seguente:The Rest API payload to make the same modifications is as follows:

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

Se è necessaria un'alternativa più flessibile rispetto `DisableIpMasking` alla registrazione di tutti o parte degli indirizzi IP, è possibile usare un [inizializzatore](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) di telemetria per copiare tutto o parte l'IP in un campo personalizzato. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Core

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
> Se non è `ISupportProperties`possibile accedere, controllare e assicurarsi di eseguire la versione stabile più recente di Application Insights SDK. `ISupportProperties`sono destinati a valori di `GlobalProperties` cardinalità elevata, mentre sono più appropriati per i valori di cardinalità bassa come il nome dell'area, il nome dell'ambiente e così via. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Abilitare l'inizializzatore di telemetria per ASP.NET

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

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Abilitare l'inizializzatore di telemetria per ASP.NET CoreEnable telemetry initializer for ASP.NET Core

È possibile creare l'inizializzatore di telemetria allo stesso modo per ASP.NET Core come ASP.NET ma per abilitare l'inizializzatore, usare l'esempio seguente come riferimento:You can create your telemetry initializer the same way for ASP.NET Core as ASP.NET but to enable the initializer, use the following example for reference:

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

A differenza degli SDK sul lato server, l'SDK Javascript lato client non calcola l'indirizzo IP. Per impostazione predefinita, il calcolo dell'indirizzo IP per la telemetria lato client viene eseguito nell'endpoint di inserimento in Azure all'arrivo dei dati di telemetria. Ciò significa che se si inviano dati sul lato client a un proxy e quindi si inoltrano all'endpoint di inserimento, il calcolo dell'indirizzo IP potrebbe mostrare l'indirizzo IP del proxy e non il client. Se non viene utilizzato alcun proxy questo non dovrebbe essere un problema.

Se si desidera calcolare l'indirizzo IP direttamente sul lato client è necessario aggiungere la propria `ai.location.ip` logica personalizzata per eseguire questo calcolo e utilizzare il risultato per impostare il tag. Quando `ai.location.ip` è impostato, il calcolo dell'indirizzo IP non viene eseguito dall'endpoint di inserimento e l'indirizzo IP fornito viene rispettato e utilizzato per eseguire la ricerca geografica. In questo scenario, l'indirizzo IP verrà comunque azzerato per impostazione predefinita. 

Per mantenere l'intero indirizzo IP calcolato dalla logica personalizzata, è possibile usare un `ai.location.ip` inizializzatore di telemetria che copia i dati dell'indirizzo IP forniti in un campo personalizzato separato. Ma ancora una volta a differenza degli SDK sul lato server, senza basarsi su librerie di terze parti o la propria logica di raccolta IP lato client personalizzato, l'SDK lato client non calcolerà l'IP per l'utente.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Visualizzare i risultati dell'inizializzatore di telemetriaView the results of your telemetry initializer

Se quindi attivi nuovo traffico sul tuo sito e attendi circa 2-5 minuti per assicurarti che abbia tempo per essere ingerito, puoi eseguire una query Kusto per vedere se la raccolta di indirizzi IP funziona:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Gli indirizzi IP appena `customDimensions_client-ip` raccolti dovrebbero essere visualizzati nella colonna. La `client-ip` colonna predefinita avrà ancora tutti e 4 gli ottetti azzerati o visualizzano solo i primi tre ottetti a seconda di come è stata configurata la raccolta di indirizzi IP a livello di componente. Se si esegue il test in locale dopo l'implementazione dell'inizializzatore di telemetria e il valore visualizzato per `customDimensions_client-ip` è `::1` questo è il comportamento previsto. `::1`rappresenta l'indirizzo di loopback in IPv6. È equivalente `127.0.01` a in IPv4 ed è il risultato che si vedrà durante il test da localhost.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla raccolta di dati personali in Application Insights.Learn more about [personal data collection](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) in Application Insights.

* Altre informazioni sul funzionamento della [raccolta di indirizzi IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) in Application Insights.Learn more about how IP address collection in Application Insights works. (Questo è un vecchio post di blog esterno scritto da uno dei nostri ingegneri. Precede il comportamento predefinito corrente in cui `0.0.0.0`l'indirizzo IP viene registrato come , `ClientIpHeaderTelemetryInitializer`ma va più in profondità sulla meccanica del built-in .)
