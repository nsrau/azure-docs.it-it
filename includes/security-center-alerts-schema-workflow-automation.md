---
title: File di inclusione
description: File di inclusione
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
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

### <a name="the-data-model-of-the-schema"></a>Il modello di dati dello schema

|Campo|Tipo di dati|Descrizione|
|----|----|----|
|**Nome visualizzato AlertDisplay**|string|Nome visualizzato dell'avviso.|
|**AlertType**|string|Tipo di avviso. Gli avvisi dello stesso tipo devono avere lo stesso valore. Questo campo è una stringa con chiave che rappresenta il tipo di avviso e non di un'istanza di avviso. Tutte le istanze di avviso dalla stessa logica di rilevamento/analita devono avere lo stesso valore per il tipo di avviso.|
|**Entità compromessa**|string|Nome visualizzato della risorsa più correlata a questo avviso.|
|**Descrizione**|string|Descrizione dell'avviso.|
|**EndTimeUtc (Informazioni in base alla prima volta)**|Datetime|Ora dell'ultimo evento o attività inclusa nell'avviso.  Il campo deve essere una stringa conforme al formato ISO8601, incluse le informazioni sul fuso orario UTC.|
|**Entities**|IEnumerable (IEntity)|Elenco di entità correlate all'avviso. Questo elenco può contenere una combinazione di entità di tipi diversi. Il tipo di entità può essere uno qualsiasi dei tipi definiti nella sezione Entità. È inoltre possibile inviare entità non presenti nell'elenco seguente, tuttavia non è garantito che verranno elaborate (l'avviso non avrà esito negativo la convalida con nuovi tipi di entità).|
|**Extendedproperties**|Dizionario (Stringa, Stringa)|I provider possono (facoltativamente) includere campi personalizzati qui.|
|**Intento**|Enum|La catena di uccisioni correlata all'intento dell'avviso. Per un elenco dei valori supportati e le spiegazioni delle finalità di kill chain supportate del Centro sicurezza di Azure, vedere [Intenzioni](../articles/security-center/alerts-reference.md#intentions).<br/>Questo campo può avere più valori (separati da virgola).|
|**IsIncident (Evento)**|Bool|Questo campo determina se l'avviso è un evento imprevisto (un raggruppamento composto di più avvisi) o un singolo avviso. Il valore predefinito per il campo è 'false' (ovvero si tratta di un singolo avviso).|
|**ProcessingEndTime**|Datetime|Ora in cui l'avviso è stato accessibile all'utente finale nel prodotto originale che contiene l'avviso.|
|**ProductName**|string|Nome del prodotto che ha pubblicato questo avviso (Centro sicurezza di Azure, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS e così via).|
|**RemediationSteps (Rimediare)**|Elenco<String>|Elementi di azione manuale da eseguire per correggere l'avviso.|
|**ResourceIdentifiers**|Elenco (identificatori di risorsa)|Identificatori di risorsa per questo avviso che possono essere utilizzati per indirizzare l'avviso al gruppo di esposizione del prodotto corretto (tenant, area di lavoro, sottoscrizione e così via). Possono essere presenti più identificatori di tipo diverso per avviso.|
|**Gravità**|Enum|Gravità dell'avviso come indicato dal provider. Valori possibili: Informativo, Basso, Medio e Alto.|
|**StartTimeUtc (Inizio)**|Datetime|Ora del primo evento o attività inclusa nell'avviso. Il campo deve essere una stringa conforme al formato ISO8601, incluse le informazioni sul fuso orario UTC.|
|**Stato**|Enum|Stato del ciclo di vita dell'avviso.<br/>Gli stati supportati sono: Nuovo, Risolto, Ignorato, Sconosciuto.<br/>A un avviso che specifica un valore diverso dalle opzioni supportate viene assegnato lo stato 'Sconosciuto'.<br/>A un avviso a cui non è stato specificato un valore viene assegnato lo stato 'Nuovo'.|
|**IdAvviso sistema**|string|Identificatore dell'avviso.|
|**TimeGenerated**|Datetime|Ora in cui l'avviso è stato generato dal provider di avvisi. Se non viene segnalato dai provider di avvisi interni, un prodotto può scegliere di assegnare l'ora in cui è stato ricevuto per l'elaborazione da parte del prodotto.  Il campo deve essere una stringa conforme al formato ISO8601, incluse le informazioni sul fuso orario UTC.|
|**NomeFornitore**|string|Nome del fornitore che genera l'avviso.|
|||
