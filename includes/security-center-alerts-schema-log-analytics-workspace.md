---
title: File di inclusione
description: File di inclusione
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538831"
---
### <a name="the-data-model-of-the-schema"></a>Il modello di dati dello schema

|Campo|Descrizione|
|----|----|
|**AlertName**|Nome visualizzato avviso|
|**AlertType**|identificatore univoco dell'avviso|
|**Livello di confidenza**|(Facoltativo) Il livello di confidenza di questo avviso (Alto/Basso)|
|**Confidendendendenazione**|(Facoltativo) Indicatore di confidenza numerico dell'avviso di sicurezza|
|**Descrizione**|Testo della descrizione per l'avviso|
|**Displayname**|Nome visualizzato dell'avviso|
|**Endtime**|L'ora di fine dell'impatto dell'avviso (l'ora dell'ultimo evento che contribuisce all'avviso)|
|**Entities**|Elenco di entità correlate all'avviso. Questo elenco può contenere una combinazione di entità di diversi tipi|
|**ExtendedLink (collegamenti estesi)**|(Facoltativo) Un sacchetto per tutti i link relativi all'avviso. Questa borsa può contenere una miscela di link per diversi tipi di|
|**Extendedproperties**|Un sacchetto di campi aggiuntivi rilevanti per l'avviso|
|**IsIncident (Evento)**|Determina se l'avviso è un incidente o un avviso regolare. Un evento imprevisto è un avviso di sicurezza che aggrega più avvisi in un evento imprevisto di sicurezza|
|**ProcessingEndTime**|Indicatore data e ora UTC in cui è stato creato l'avviso|
|**ProductComponentName (Nome ComponenteProdotto)**|(Facoltativo) Nome di un componente all'interno del prodotto che ha generato l'avviso.|
|**ProductName**|costante ('Centro sicurezza di Azure')|
|**Providername**|unused|
|**RemediationSteps (Rimediare)**|Elementi di azioni manuali da eseguire per correggere la minaccia alla sicurezza|
|**Resourceid**|Identificatore completo della risorsa interessata|
|**Gravità**|La gravità dell'avviso (Alta/Medio/Basso/Informativo)|
|**SourceComputerId**|un GUID univoco per il server interessato (se l'avviso viene generato sul server)|
|**SourceSystem**|unused|
|**Starttime**|L'ora di inizio dell'avviso (l'ora del primo evento che contribuisce all'avviso)|
|**IdAvviso sistema**|Identificatore univoco di questa istanza di avviso di sicurezza|
|**TenantId**|l'identificatore del tenant di Azure Active Directory padre della sottoscrizione in cui risiede la risorsa analizzata|
|**TimeGenerated**|Timestamp UTC in cui è stata effettuata la valutazione (ora di analisi del Centro sicurezza) (identica a DiscoveredTimeUTC)|
|**Tipo**|costante ('SecurityAlert')|
|**NomeFornitore**|Il nome del fornitore che ha fornito l'avviso (ad esempio 'Microsoft')|
|**VendorOriginalId**|unused|
|**WorkspaceResourceGroup**|nel caso in cui l'avviso venga generato in una macchina virtuale, un server, un set di scalabilità di macchine virtuali o un'istanza del servizio app che genera report in un'area di lavoro, contiene il nome del gruppo di risorse dell'area di lavoro|
|**WorkspaceSubscriptionId WorkspaceSubscriptionId**|nel caso in cui l'avviso venga generato in una macchina virtuale, un server, un set di scalabilità di macchine virtuali o un'istanza del servizio app che genera report in un'area di lavoro, contiene tale subscriptionId dell'area di lavoro|
|||
