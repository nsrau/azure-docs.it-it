---
title: Configurare la diagnostica
titleSuffix: Azure Digital Twins
description: Vedere come abilitare la registrazione con le impostazioni di diagnostica.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 11a7b4876c773922d4b0ed28f7047912b738ee6a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091736"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Risoluzione dei problemi relativi ai dispositivi gemelli digitali di Azure: registrazione diagnostica

I dispositivi gemelli digitali di Azure raccolgono le [metriche](troubleshoot-metrics.md) per l'istanza del servizio che forniscono informazioni sullo stato delle risorse. È possibile usare queste metriche per valutare l'integrità complessiva del servizio dispositivi gemelli digitali di Azure e le risorse connesse. Queste statistiche per l'utente consentono di vedere cosa accade con i dispositivi gemelli digitali di Azure e consentono di eseguire l'analisi della causa radice dei problemi senza dover contattare il supporto tecnico di Azure.

Questo articolo illustra come attivare la **registrazione diagnostica** per i dati di metrica dall'istanza di Azure Digital gemelli. È possibile usare questi log per risolvere i problemi relativi al servizio e configurare le impostazioni di diagnostica per inviare le metriche di dispositivi gemelli digitali di Azure a destinazioni diverse. Per altre informazioni su queste impostazioni, vedere [*creare le impostazioni di diagnostica per inviare metriche e log di piattaforma a destinazioni diverse*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Attivare le impostazioni di diagnostica con la portale di Azure

Ecco come abilitare le impostazioni di diagnostica per l'istanza di Azure Digital Twins:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **impostazioni di diagnostica** dal menu e quindi **Aggiungi impostazione di diagnostica** .

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

3. Nella pagina seguente compilare i valori seguenti:
     * **Nome dell'impostazione di diagnostica** : assegnare un nome alle impostazioni di diagnostica.
     * **Dettagli categoria** : scegliere le operazioni che si desidera monitorare e selezionare le caselle per abilitare la diagnostica per tali operazioni. Le operazioni per cui le impostazioni di diagnostica possono inviare report sono:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Per ulteriori informazioni su queste opzioni, vedere la sezione [*Dettagli categoria*](#category-details) riportata di seguito.
     * **Dettagli destinazione** : scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni:
        - Invia a Log Analytics
        - Archivia in un account di archiviazione
        - Streaming in un hub eventi

        Se necessario per la selezione della destinazione, è possibile che venga richiesto di specificare ulteriori dettagli.  
    
4. Salvare le nuove impostazioni. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

Le nuove impostazioni diventano effettive in circa 10 minuti. Successivamente, i log vengono visualizzati nella destinazione configurata di nuovo nella pagina **impostazioni di diagnostica** per l'istanza. 

## <a name="category-details"></a>Dettagli categoria

Di seguito sono riportate altre informazioni sulle categorie di log che è possibile selezionare in **Dettagli categoria** quando si configurano le impostazioni di diagnostica.

| Categoria di log | Descrizione |
| --- | --- |
| ADTModelsOperation | Registrare tutte le chiamate API relative ai modelli |
| ADTQueryOperation | Registrare tutte le chiamate API relative alle query |
| ADTEventRoutesOperation | Registrare tutte le chiamate API relative alle route degli eventi e all'uscita degli eventi dai dispositivi gemelli digitali di Azure a un servizio endpoint come griglia di eventi, Hub eventi e bus di servizio |
| ADTDigitalTwinsOperation | Registrare tutte le chiamate API relative ai dispositivi gemelli digitali di Azure |

Ogni categoria di log è costituita da operazioni di scrittura, lettura, eliminazione e azione.  Questi mapping a chiamate API REST sono i seguenti:

| Tipo di evento | Operazioni dell'API REST |
| --- | --- |
| Scrittura | PUT e PATCH |
| Lettura | GET |
| Elimina | DELETE |
| Azione | POST |

Ecco un elenco completo delle operazioni e delle [chiamate API REST di Azure Digital gemelle](/rest/api/azure-digitaltwins/) corrispondenti registrate in ogni categoria. 

>[!NOTE]
> Ogni categoria di log contiene diverse chiamate API REST e operazioni. Nella tabella riportata di seguito, ogni categoria di log esegue il mapping a tutte le chiamate API REST e delle operazioni sottostanti fino a quando non viene elencata la categoria di log successiva. 

| Categoria di log | Operazione | Chiamate API REST e altri eventi |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/Models/Write | API per l'aggiornamento di modelli gemelli digitali |
|  | Microsoft. DigitalTwins/Models/Read | Modelli di dispositivi gemelli digitali Get by ID ed elenco di API |
|  | Microsoft. DigitalTwins/Models/Delete | API per i modelli gemelli digitali Delete |
|  | Microsoft. DigitalTwins/modelli/azione | I modelli di dispositivi gemelli digitali aggiungono API |
| ADTQueryOperation | Microsoft. DigitalTwins/query/Action | API di query gemelle |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Route di eventi-Aggiungi API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Route di eventi Get by ID ed elenco di API |
|  | Microsoft. DigitalTwins/eventroutes/Delete | API di eliminazione delle route degli eventi |
|  | Microsoft. DigitalTwins/eventroutes/Action | Errore durante il tentativo di pubblicare eventi in un servizio endpoint (non in una chiamata API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Dispositivi gemelli digitali aggiungere, aggiungere relazioni, aggiornare e aggiornare un componente |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Dispositivi gemelli digitali Get by ID, Get Component, Get Relationship by ID, List relazioni in ingresso, elenco relazioni |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Eliminazione di gemelli digitali, relazione Delete |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | I dispositivi gemelli digitali inviano dati di telemetria del componente, invia dati |

## <a name="log-schemas"></a>Schemi di log 

Ogni categoria di log dispone di uno schema che definisce il modo in cui vengono segnalati gli eventi in tale categoria. Ogni singola voce di log viene archiviata come testo e formattata come BLOB JSON. Per ogni tipo di log riportato di seguito vengono forniti i campi nel log e i corpi JSON di esempio. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` e `ADTQueryOperation` utilizzano uno schema di log API coerente `ADTEventRoutesOperation` . dispone di uno schema separato.

### <a name="api-log-schemas"></a>Schemi di log API

Questo schema del registro è coerente per `ADTDigitalTwinsOperation` , `ADTModelsOperation` e `ADTQueryOperation` . Contiene informazioni rilevanti per le chiamate API a un'istanza di Azure Digital gemelli.

Di seguito sono riportate le descrizioni dei campi e delle proprietà per i log API.

| Nome campo | Tipo di dati | Descrizione |
|-----|------|-------------|
| `Time` | Datetime | Data e ora in cui si è verificato l'evento, in formato UTC |
| `ResourceID` | string | ID di risorsa Azure Resource Manager per la risorsa in cui si è verificata l'evento |
| `OperationName` | string  | Tipo di azione eseguita durante l'evento |
| `OperationVersion` | string | Versione dell'API utilizzata durante l'evento |
| `Category` | string | Tipo di risorsa emessa |
| `ResultType` | string | Risultato dell'evento |
| `ResultSignature` | string | Codice di stato HTTP per l'evento |
| `ResultDescription` | string | Dettagli aggiuntivi sull'evento |
| `DurationMs` | string | Tempo impiegato per eseguire l'evento in millisecondi |
| `CallerIpAddress` | string | Un indirizzo IP di origine mascherato per l'evento |
| `CorrelationId` | Guid | Identificatore univoco fornito dal cliente per l'evento |
| `Level` | string | Gravità della registrazione dell'evento |
| `Location` | string | Area in cui è avvenuto l'evento |
| `RequestUri` | Uri | Endpoint utilizzato durante l'evento |

Di seguito sono riportati i corpi JSON di esempio per questi tipi di log.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Schemi di log in uscita

Si tratta dello schema per i `ADTEventRoutesOperation` log. Sono inclusi i dettagli relativi alle eccezioni e le operazioni API relative agli endpoint in uscita connessi a un'istanza di dispositivi gemelli digitali di Azure.

|Nome campo | Tipo di dati | Descrizione |
|-----|------|-------------|
| `Time` | Datetime | Data e ora in cui si è verificato l'evento, in formato UTC |
| `ResourceId` | string | ID di risorsa Azure Resource Manager per la risorsa in cui si è verificata l'evento |
| `OperationName` | string  | Tipo di azione eseguita durante l'evento |
| `Category` | string | Tipo di risorsa emessa |
| `ResultDescription` | string | Dettagli aggiuntivi sull'evento |
| `Level` | string | Gravità della registrazione dell'evento |
| `Location` | string | Area in cui è avvenuto l'evento |
| `EndpointName` | string | Nome dell'endpoint in uscita creato nei dispositivi gemelli digitali di Azure |

Di seguito sono riportati i corpi JSON di esempio per questi tipi di log.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla configurazione della diagnostica, vedere [*raccogliere e utilizzare i dati di log dalle risorse di Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Per informazioni sulle metriche dei dispositivi gemelli digitali di Azure, vedere [*risoluzione dei problemi: visualizzare le metriche con monitoraggio di Azure*](troubleshoot-metrics.md).
* Per informazioni su come abilitare gli avvisi per le metriche, vedere [*risoluzione dei problemi: configurare gli avvisi*](troubleshoot-alerts.md).