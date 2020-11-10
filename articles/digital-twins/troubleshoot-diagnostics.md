---
title: Abilitare ed eseguire query sui log di diagnostica
titleSuffix: Azure Digital Twins
description: Vedere come abilitare la registrazione con le impostazioni di diagnostica ed eseguire query sui log per la visualizzazione immediata.
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 0d775ffa1ce063c01fc6762d77201e5a4caaad87
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411752"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Risoluzione dei problemi relativi ai dispositivi gemelli digitali di Azure: registrazione diagnostica

I dispositivi gemelli digitali di Azure possono raccogliere log per l'istanza del servizio per monitorarne le prestazioni, l'accesso e altri dati. È possibile usare questi log per avere un'idea di ciò che accade nell'istanza di Azure Digital Twins ed eseguire l'analisi della causa radice dei problemi senza dover contattare il supporto tecnico di Azure.

Questo articolo illustra come [**configurare le impostazioni di diagnostica**](#turn-on-diagnostic-settings) nel [portale di Azure](https://portal.azure.com) per iniziare a raccogliere log dall'istanza di Azure Digital gemelli. È anche possibile specificare la posizione in cui archiviare i log, ad esempio Log Analytics o un account di archiviazione di propria scelta.

Questo articolo contiene anche elenchi di tutte le [categorie di log](#log-categories) e degli schemi di [log](#log-schemas) raccolti da Azure Digital gemelli.

Dopo aver configurato i log, è anche possibile [**eseguire una query sui log**](#view-and-query-logs) per raccogliere rapidamente informazioni personalizzate.

## <a name="turn-on-diagnostic-settings"></a>Attivare le impostazioni di diagnostica 

Attivare le impostazioni di diagnostica per avviare la raccolta dei log nell'istanza di Azure Digital gemelli. È anche possibile scegliere la destinazione in cui archiviare i log esportati. Di seguito viene illustrato come abilitare le impostazioni di diagnostica per l'istanza di Azure Digital gemelli.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **impostazioni di diagnostica** dal menu e quindi **Aggiungi impostazione di diagnostica**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Nella pagina seguente compilare i valori seguenti:
     * **Nome dell'impostazione di diagnostica** : assegnare un nome alle impostazioni di diagnostica.
     * **Dettagli categoria** : scegliere le operazioni che si desidera monitorare e selezionare le caselle per abilitare la diagnostica per tali operazioni. Le operazioni per cui le impostazioni di diagnostica possono inviare report sono:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Per ulteriori informazioni su queste categorie e sulle informazioni che contengono, vedere la sezione relativa alle [*categorie di log*](#log-categories) riportata di seguito.
     * **Dettagli destinazione** : scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni:
        - Invia a Log Analytics
        - Archivia in un account di archiviazione
        - Streaming in un hub eventi

        Se necessario per la selezione della destinazione, è possibile che venga richiesto di specificare ulteriori dettagli.  
    
4. Salvare le nuove impostazioni. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica in cui l'utente ha compilato il nome di un'impostazione di diagnostica ed eseguito alcune selezioni della casella di controllo per i dettagli della categoria e la destinazione. Il pulsante Salva è evidenziato." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Le nuove impostazioni diventano effettive in circa 10 minuti. Successivamente, i log vengono visualizzati nella destinazione configurata di nuovo nella pagina **impostazioni di diagnostica** per l'istanza. 

Per informazioni più dettagliate sulle impostazioni di diagnostica e sulle relative opzioni di installazione, è possibile vedere [*creare le impostazioni di diagnostica per inviare i log della piattaforma e le metriche a destinazioni diverse*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="log-categories"></a>Categorie di log

Di seguito sono riportate altre informazioni sulle categorie di log che Azure Digital Twins raccoglie.

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
| Action | POST |

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

| Nome del campo | Tipo di dati | Descrizione |
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
| `CorrelationId` | GUID | Identificatore univoco fornito dal cliente per l'evento |
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

|Nome del campo | Tipo di dati | Descrizione |
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

## <a name="view-and-query-logs"></a>Visualizzare ed eseguire query sui log

In precedenza in questo articolo sono stati configurati i tipi di log per archiviare e specificare il percorso di archiviazione.

Per risolvere il problema e generare informazioni dettagliate da questi log, è possibile generare **query personalizzate**. Per iniziare, è anche possibile sfruttare alcune query di esempio fornite dal servizio, che risolvono le domande comuni che i clienti possono avere sull'istanza.

Di seguito viene illustrato come eseguire una query sui log per l'istanza di.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **registri** dal menu per aprire la pagina query log. La pagina viene visualizzata in una finestra denominata *query*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Screenshot che mostra la pagina log per un'istanza di Azure Digital gemelli. Viene sovrapposta a una finestra query che mostra le query predefinite denominate dopo diverse opzioni di log, ad esempio latenza API DigitalTwin e latenza API modello." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Si tratta di query di esempio predefinite scritte per diversi log. È possibile selezionare una delle query per caricarla nell'editor di query ed eseguirla per visualizzare questi log per l'istanza.

    È anche possibile chiudere la finestra *query* senza eseguire alcuna operazione per passare direttamente alla pagina dell'editor di query, in cui è possibile scrivere o modificare il codice di query personalizzato.

3. Dopo aver chiuso la finestra *query* , verrà visualizzata la pagina principale dell'editor di query. Qui è possibile visualizzare e modificare il testo delle query di esempio o scrivere query personalizzate da zero.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Screenshot che mostra la pagina log per un'istanza di Azure Digital gemelli. La finestra query non è più disponibile, ma è presente un elenco di log diversi, un riquadro di modifica che mostra il codice di query modificabile e un riquadro che mostra la cronologia delle query." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    Nel riquadro sinistro, 
    - La scheda *tabelle* Mostra le diverse [categorie di log](#log-categories) di Azure Digital Twins disponibili per l'uso nelle query. 
    - La scheda *query* contiene le query di esempio che è possibile caricare nell'editor.
    - La scheda *filtro* consente di personalizzare una visualizzazione filtrata dei dati restituiti dalla query.

Per informazioni più dettagliate sulle query di log e su come scriverle, vedere [*Panoramica delle query di log in monitoraggio di Azure*](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla configurazione della diagnostica, vedere [*raccogliere e utilizzare i dati di log dalle risorse di Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Per informazioni sulle metriche dei dispositivi gemelli digitali di Azure, vedere [*risoluzione dei problemi: visualizzare le metriche con monitoraggio di Azure*](troubleshoot-metrics.md).
* Per informazioni su come abilitare gli avvisi per le metriche, vedere [*risoluzione dei problemi: configurare gli avvisi*](troubleshoot-alerts.md).