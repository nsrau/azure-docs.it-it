---
title: includere il file
description: includere file
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80272829"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>Modello di dati dello schema

|Campo|Tipo di dati|Descrizione|
|----|----|----|
|**AlertDisplayName**|string|Nome visualizzato dell'avviso.|
|**AlertType**|string|Tipo di avviso. Gli avvisi dello stesso tipo devono avere lo stesso valore. Questo campo è una stringa con chiave che rappresenta il tipo di avviso e non di un'istanza di avviso. Tutte le istanze di avviso dalla stessa logica/analitica di rilevamento devono avere lo stesso valore per il tipo di avviso.|
|**CompromisedEntity**|string|Nome visualizzato della risorsa più correlata a questo avviso.|
|**Descrizione**|string|Descrizione dell'avviso.|
|**EndTimeUtc**|Datetime|Ora dell'ultimo evento o attività inclusa nell'avviso.  Il campo deve essere una stringa conforme al formato ISO8601, incluse le informazioni del fuso orario UTC.|
|**Entità**|IEnumerable (IEntity)|Elenco di entità correlate all'avviso. Questo elenco può avere una combinazione di entità di tipi diversi. Il tipo di entità può essere uno qualsiasi dei tipi definiti nella sezione Entities. È anche possibile inviare entità non incluse nell'elenco seguente, ma non è garantito che vengano elaborate (la convalida dell'avviso non avrà esito negativo con i nuovi tipi di entità).|
|**ExtendedProperties**|Dictionary (String, String)|I provider potrebbero (facoltativamente) includere qui i campi personalizzati.|
|**Finalità**|Enumerazione|Finalità correlate alla catena di uccisione alla base dell'avviso. Per un elenco dei valori supportati e spiegazioni degli Intent della catena di uccisione del Centro sicurezza di Azure, vedere [Intentions](../articles/security-center/alerts-reference.md#intentions).<br/>Questo campo può avere più valori (separati da virgola).|
|**Evento imprevisto**|Bool|Questo campo determina se l'avviso è un evento imprevisto, ovvero un raggruppamento composto di diversi avvisi, o un singolo avviso. Il valore predefinito per il campo è' false ' (ovvero è un singolo avviso).|
|**ProcessingEndTime**|Datetime|Il momento in cui l'avviso è stato accessibile all'utente finale nel prodotto originale che contiene l'avviso.|
|**ProductName**|string|Nome del prodotto che ha pubblicato questo avviso (Centro sicurezza di Azure, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS e così via).|
|**Correzione**|Elenco<String>|Elementi di azione manuali da eseguire per correggere l'avviso.|
|**ResourceIdentifiers**|List (identificatori di risorsa)|Identificatori di risorsa per questo avviso che possono essere usati per indirizzare l'avviso al gruppo di esposizione del prodotto appropriato (tenant, area di lavoro, sottoscrizione e così via). Possono essere presenti più identificatori di tipo diverso per ogni avviso.|
|**Gravità**|Enumerazione|Gravità dell'avviso come riportato dal provider. Valori possibili: Informational, low, medium e High.|
|**StartTimeUtc**|Datetime|Ora del primo evento o attività inclusa nell'avviso. Il campo deve essere una stringa conforme al formato ISO8601, incluse le informazioni del fuso orario UTC.|
|**Status**|Enumerazione|Stato del ciclo di vita dell'avviso.<br/>Gli stati supportati sono: New, resolved, unmissed, Unknown.<br/>A un avviso che specifica un valore diverso dalle opzioni supportate viene assegnato lo stato "Unknown".<br/>A un avviso che non specifica un valore viene assegnato lo stato ' New '.|
|**SystemAlertId**|string|Identificatore dell'avviso.|
|**TimeGenerated**|Datetime|Ora in cui l'avviso è stato generato dal provider di avvisi. Se non vengono segnalati da provider di avvisi interni, un prodotto può scegliere di assegnare il tempo che è stato ricevuto per l'elaborazione da parte del prodotto.  Il campo deve essere una stringa conforme al formato ISO8601, incluse le informazioni del fuso orario UTC.|
|**NomeFornitore**|string|Nome del fornitore che genera l'avviso.|
|||
