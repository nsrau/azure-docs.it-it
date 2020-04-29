---
title: includere il file
description: File di inclusione
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79538831"
---
### <a name="the-data-model-of-the-schema"></a>Modello di dati dello schema

|Campo|Descrizione|
|----|----|
|**AlertName**|Nome visualizzato avviso|
|**AlertType**|identificatore di avviso univoco|
|**ConfidenceLevel**|Opzionale Livello di confidenza dell'avviso (alto/basso)|
|**ConfidenceScore**|Opzionale Indicatore di attendibilità numerica dell'avviso di sicurezza|
|**Descrizione**|Testo della descrizione dell'avviso|
|**DisplayName**|Nome visualizzato dell'avviso|
|**EndTime**|L'ora di fine dell'avviso (l'ora dell'ultimo evento che contribuisce all'avviso)|
|**Entities**|Elenco di entità correlate all'avviso. Questo elenco può avere una combinazione di entità di tipi diversi|
|**ExtendedLinks**|Opzionale Un contenitore per tutti i collegamenti correlati all'avviso. Questa busta può avere una combinazione di collegamenti per tipi diversi|
|**ExtendedProperties**|Un contenitore di campi aggiuntivi rilevanti per l'avviso|
|**Evento imprevisto**|Determina se l'avviso è un evento imprevisto o un avviso normale. Un evento imprevisto è un avviso di sicurezza che aggrega più avvisi in un evento imprevisto di sicurezza|
|**ProcessingEndTime**|Timestamp UTC in cui è stato creato l'avviso|
|**ProductComponentName**|Opzionale Nome di un componente all'interno del prodotto che ha generato l'avviso.|
|**ProductName**|costante ("Centro sicurezza di Azure")|
|**ProviderName**|unused|
|**Correzione**|Elementi di azione manuali da eseguire per correggere la minaccia per la sicurezza|
|**ResourceId**|Identificatore completo della risorsa interessata|
|**Gravità**|Gravità dell'avviso (alta/media/bassa/informativa)|
|**SourceComputerId**|GUID univoco per il server interessato (se l'avviso viene generato nel server)|
|**SourceSystem**|unused|
|**StartTime**|L'ora di inizio dell'avviso (l'ora del primo evento che contribuisce all'avviso)|
|**SystemAlertId**|Identificatore univoco di questa istanza di avviso di sicurezza|
|**TenantId**|identificatore del tenant di Azure Active Directory padre della sottoscrizione in cui risiede la risorsa analizzata.|
|**TimeGenerated**|Timestamp UTC in cui è avvenuta la valutazione (tempo di analisi del Centro sicurezza) (identico a DiscoveredTimeUTC)|
|**Type**|Constant (' SecurityAlert ')|
|**NomeFornitore**|Nome del fornitore che ha fornito l'avviso, ad esempio "Microsoft"|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|Se l'avviso viene generato in una VM, un server, un set di scalabilità di macchine virtuali o un'istanza del servizio app che segnala a un'area di lavoro, contiene il nome del gruppo di risorse dell'area di lavoro|
|**WorkspaceSubscriptionId**|Se l'avviso viene generato in una VM, un server, un set di scalabilità di macchine virtuali o un'istanza del servizio app che segnala a un'area di lavoro, contiene l'area di lavoro subscriptionId|
|||
