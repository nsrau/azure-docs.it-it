---
title: Schema JSON del modulo gemello-Azure
description: Questo argomento descrive lo schema JSON del modulo gemello di analisi video in tempo reale su IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a342c59b35c7ebb4b6021163da76bdd3e0d449c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266815"
---
# <a name="module-twin-json-schema"></a>Schema JSON del modulo gemello

I dispositivi gemelli sono documenti JSON che archiviano le informazioni sullo stato del dispositivo, inclusi metadati, configurazioni e condizioni. L'hub IoT di Azure mantiene un dispositivo gemello per ogni dispositivo che viene connesso all'hub IoT. Per una spiegazione dettagliata, vedere [comprendere e usare i moduli gemelli nell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) Internet

Questo argomento descrive lo schema JSON del modulo gemello di analisi video in tempo reale su IoT Edge.

> [!NOTE]
> Per essere autorizzati ad accedere alle risorse e all'API di Servizi multimediali, è innanzitutto necessario essere autenticati. Per altre informazioni, vedere [accesso all'API servizi multimediali di Azure](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Proprietà del modulo gemello

Analisi video in tempo reale su IoT Edge espone le proprietà gemelle del modulo seguenti. 

|Proprietà |Obbligatoria |Dinamico |Descrizione |
|---|---|---|---|
|applicationDataDirectory |Sì |No |Percorso di un volume montato per la configurazione permanente. |
|azureMediaServicesArmId |Sì |No |Identificatore univoco di gestione risorse di Azure per l'account di servizi multimediali.|
|aadTenantId |Sì |No |ID del tenant Azure AD del cliente.|
|aadServicePrincipalAppId |Sì |Sì |Il cliente ha creato Azure AD AppId.|
|aadServicePrincipalCertificate |Sì<sup>*</sup>  |Sì |Il cliente ha creato Azure AD certificato AppId.|
|aadServicePrincipalPassword |Sì<sup>*</sup>  |Sì |Il cliente ha creato Azure AD password AppId.|
|aadEndpoint |No |No |Endpoint Azure AD specifico del cloud. <br/>Valore predefinito: `https://login.microsoftonline.com` |
|aadResourceId |No |No |ID di risorsa e destinatari di Azure AD specifici del cloud <br/>Valore predefinito: `https://management.core.windows.net/` |
|armEndpoint |No |No |Endpoint di gestione delle risorse di Azure specifico per il cloud. <br/>Valore predefinito: `https://management.azure.com/` |
|diagnosticsLevel |No |Sì |Livello di dettaglio degli eventi: <br/>Informazioni & # x02758; Avviso & # x02758; Errore & # x02758; & critico # x02758; Nessuno |
|diagnosticsEventsOutputName |No |Sì |Output dell'hub per gli eventi di diagnostica. <br/>(Vuoto significa che la diagnostica non è pubblicata)|
|operationalEventsOutputName|No|Sì|Output dell'hub per gli eventi operativi.<br/>(Vuoto indica che gli eventi operativi non sono pubblicati)
|logLevel|No|Sì|I tipi validi sono: <br/>& # x000B7; Dettagliato<br/>& # x000B7; Informazioni (impostazione predefinita)<br/>& # x000B7; Avviso<br/>& # x000B7; Errore<br/>& # x000B7; Nessuno|
|logCategories|No|Sì|Elenco delimitato da virgole dei seguenti elementi: Application, MediaPipeline, Events <br/>Impostazione predefinita: applicazione, eventi|
|debugLogsDirectory|No|Sì|Directory per i log di debug. Se vengono generati log, se non sono presenti i log di debug sono disabilitati.

<sup>*</sup>È necessario fornire un certificato o una password dell'entità servizio. 

È possibile aggiornare le proprietà dinamiche senza riavviare il modulo. È possibile ottenere i valori per molte di queste proprietà seguendo le istruzioni riportate nell'articolo [ottenere l'accesso all'API di servizi multimediali](../latest/access-api-cli-how-to.md) . 

Per ulteriori informazioni sul ruolo delle impostazioni di diagnostica facoltative, vedere l'articolo relativo al [monitoraggio e alla registrazione](monitoring-logging.md) .

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Passaggi successivi

[Metodi diretti](direct-methods.md)
