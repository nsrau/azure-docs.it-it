---
title: Raccolta di indirizzi IP di applicazione Azure Insights | Microsoft Docs
description: Informazioni su come gestire gli indirizzi IP e la georilevazione con applicazione Azure Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6131105ef78a8559b0fb95043a87e562e887ebfd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333308"
---
# <a name="geolocation-and-ip-address-handling"></a>Georilevazione e gestione degli indirizzi IP

Questo articolo illustra il funzionamento della ricerca georilevata e della gestione degli indirizzi IP in Application Insights insieme a come modificare il comportamento predefinito.

## <a name="default-behavior"></a>Comportamento predefinito

Per impostazione predefinita, gli indirizzi IP vengono raccolti temporaneamente, ma non archiviati in Application Insights. Il processo di base è il seguente:

Quando i dati di telemetria vengono inviati ad Azure, l'indirizzo IP viene usato per eseguire una ricerca geografica usando [GeoLite2 da MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). I risultati di questa ricerca vengono utilizzati per popolare i campi `client_City` , `client_StateOrProvince` e `client_CountryOrRegion` . L'indirizzo viene quindi eliminato e scritto nel `0.0.0.0` `client_IP` campo.

* Telemetria del browser: si raccoglie temporaneamente l'indirizzo IP del mittente. L'indirizzo IP viene calcolato dall'endpoint di inserimento.
* Telemetria del server: il modulo di telemetria Application Insights raccoglie temporaneamente l'indirizzo IP del client. L'indirizzo IP non viene raccolto localmente quando l' `X-Forwarded-For` intestazione è impostata.

Questo comportamento è progettato per evitare una raccolta non necessaria di dati personali. Laddove possibile, è consigliabile evitare la raccolta di dati personali. 

## <a name="overriding-default-behavior"></a>Override del comportamento predefinito

Mentre l'impostazione predefinita prevede di non raccogliere gli indirizzi IP. Si offre comunque la flessibilità necessaria per eseguire l'override di questo comportamento. Tuttavia, si consiglia di verificare che la raccolta non interrompa i requisiti di conformità o le normative locali. 

Per ulteriori informazioni sulla gestione dei dati personali in Application Insights, consultare le [linee guida per i dati personali](../platform/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>Archiviazione dei dati degli indirizzi IP

Per abilitare la raccolta e l'archiviazione IP, `DisableIpMasking` è necessario impostare la proprietà del componente Application Insights su `true` . Questa proprietà può essere impostata tramite Azure Resource Manager modelli o chiamando l'API REST. 

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

Se è necessario modificare solo il comportamento per una singola risorsa di Application Insights, usare il portale di Azure. 

1. Passare alla risorsa Application Insights > **Automation**  >  **modello di esportazione** di automazione 

2. Selezionare **Distribuisci**

    ![Pulsante con Word "deploy" evidenziato in rosso](media/ip-collection/deploy.png)

3. Selezionare **modifica modello**.

    ![Pulsante con Word "Edit" evidenziato in rosso](media/ip-collection/edit-template.png)

4. Apportare le modifiche seguenti al codice JSON per la risorsa e quindi selezionare **Salva**:

    ![Screenshot aggiunge una virgola dopo "IbizaAIExtension" e aggiunge una nuova riga sotto "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Se si verifica un errore che indica che ** _il gruppo di risorse si trova in una posizione non supportata da una o più risorse nel modello. Scegliere un gruppo di risorse diverso._** Selezionare temporaneamente un gruppo di risorse diverso nell'elenco a discesa e quindi selezionare di nuovo il gruppo di risorse originale per risolvere l'errore.

5. Selezionare **Accetto**  >  **acquisto**. 

    ![Casella di controllo con parole "Accetto i termini e le condizioni indicati sopra" evidenziato in rosso sopra un pulsante con la parola "Purchase" evidenziata in rosso.](media/ip-collection/purchase.png)

    In questo caso, non viene effettivamente acquistato alcun nuovo. Viene aggiornata solo la configurazione della risorsa Application Insights esistente.

6. Al termine della distribuzione, verranno registrati i nuovi dati di telemetria.

    Se si seleziona e si modifica di nuovo il modello, il modello predefinito verrà visualizzato solo senza la proprietà appena aggiunta. Se non vengono visualizzati i dati degli indirizzi IP e si vuole confermare che `"DisableIpMasking": true` è impostato, eseguire il comando PowerShell seguente: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Verrà restituito un elenco di proprietà di conseguenza. Una delle proprietà deve essere letta `DisableIpMasking: true` . Se si esegue PowerShell prima di distribuire la nuova proprietà con Azure Resource Manager, la proprietà non esiste.

### <a name="rest-api"></a>API REST

Il payload dell' [API REST](/rest/api/azure/) per apportare le stesse modifiche è il seguente:

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

Se è necessaria un'alternativa più flessibile di `DisableIpMasking` , è possibile usare un [inizializzatore di telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) per copiare tutto o parte dell'indirizzo IP in un campo personalizzato. 

# <a name="net"></a>[.NET](#tab/net)

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
> Se non si riesce ad accedere `ISupportProperties` , verificare e assicurarsi di eseguire la versione stabile più recente di Application Insights SDK. `ISupportProperties` sono destinati a valori di cardinalità elevata, mentre `GlobalProperties` sono più appropriati per i valori di cardinalità bassa, ad esempio il nome dell'area, il nome dell'ambiente e così via. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Abilita inizializzatore di telemetria per ASP.NET

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
# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

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
# <a name="client-side-javascript"></a>[JavaScript lato client](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript lato client

A differenza degli SDK sul lato server, l'SDK JavaScript lato client non calcola l'indirizzo IP. Per impostazione predefinita, il calcolo degli indirizzi IP per la telemetria sul lato client si verifica in corrispondenza dell'endpoint di inserimento in Azure. 

Se si desidera calcolare l'indirizzo IP direttamente sul lato client, è necessario aggiungere la logica personalizzata e utilizzare il risultato per impostare il `ai.location.ip` tag. Quando `ai.location.ip` è impostato, il calcolo degli indirizzi IP non viene eseguito dall'endpoint di inserimento e l'indirizzo IP specificato viene usato per la ricerca georilevazione. In questo scenario, l'indirizzo IP verrà ancora azzerato per impostazione predefinita. 

Per evitare che l'intero indirizzo IP venga calcolato dalla logica personalizzata, è possibile usare un inizializzatore di telemetria che copia i dati degli indirizzi IP specificati in `ai.location.ip` un campo personalizzato distinto. Tuttavia, a differenza degli SDK sul lato server, senza basarsi sulle librerie di terze parti o sulla logica di raccolta personalizzata, l'SDK lato client non calcolerà l'indirizzo.    


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

Se i dati lato client attraversano un proxy prima dell'inoltro all'endpoint di inserimento, il calcolo degli indirizzi IP potrebbe visualizzare l'indirizzo IP del proxy e non il client. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Visualizzare i risultati dell'inizializzatore di telemetria

Se si invia nuovo traffico al sito, attendere alcuni minuti. È quindi possibile eseguire una query per confermare che la raccolta funziona:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Gli indirizzi IP appena raccolti verranno visualizzati nella `customDimensions_client-ip` colonna. Per la `client-ip` colonna predefinita saranno ancora presenti tutti i quattro ottetti azzerati. 

Se si verifica da localhost e il valore di `customDimensions_client-ip` è `::1` , questo valore è il comportamento previsto. `::1` rappresenta l'indirizzo di loopback in IPv6. Equivale a `127.0.01` in IPv4.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla [raccolta di dati personali](../platform/personal-data-mgmt.md) in Application Insights.

* Altre informazioni sul funzionamento della [raccolta di indirizzi IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) in Application Insights. (Questo articolo è un post di Blog esterno precedente scritto da uno dei nostri tecnici. Precede il comportamento predefinito corrente in cui l'indirizzo IP viene registrato come `0.0.0.0` , ma entra in una profondità maggiore sui meccanismi predefiniti `ClientIpHeaderTelemetryInitializer` .
