---
title: Ricerca nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come Centro sicurezza di Azure usa la Ricerca di Log Analytics per recuperare e analizzare i dati di sicurezza.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Ricerca nel Centro sicurezza di Azure
Centro sicurezza di Azure usa la [Ricerca di Log Analytics](../log-analytics/log-analytics-log-searches.md) per recuperare e analizzare i dati di sicurezza. Log Analytics include un linguaggio di query che permette di recuperare e consolidare rapidamente i dati. Nel Centro sicurezza è possibile sfruttare la Ricerca di Log Analytics per creare le query e analizzare i dati raccolti.

La ricerca è disponibile sia nel livello Standard che nel livello gratuito del Centro sicurezza.  I dati disponibili nelle ricerche log dipendono dal livello applicato all'area di lavoro.  Per altre informazioni, vedere la [pagina dei prezzi](../security-center/security-center-pricing.md) del Centro sicurezza.


> [!NOTE]
> Il Centro sicurezza non salva i dati di sicurezza per un'area di lavoro nel livello gratuito. È possibile inviare diversi log a un'area di lavoro nel livello gratuito ed eseguire una ricerca nei dati, ma i risultati della ricerca non includono i dati del Centro sicurezza. Il Centro sicurezza salva i dati solo in un'area di lavoro nel livello Standard.
>
>

## <a name="access-search"></a>Accedere alla ricerca
1. Selezionare **Ricerca** nel menu principale del Centro sicurezza.

  ![Selezionare Ricerca log][1]

2. Il Centro sicurezza elenca tutte le aree di lavoro nelle sottoscrizioni di Azure. Selezionare un'area di lavoro. Se si dispone solo di un'area di lavoro, questa selezione dell'area di lavoro non viene visualizzata.

  ![Selezionare un'area di lavoro][2]

3. Si apre **Ricerca log**. Per eseguire una query su più dati dell'area di lavoro selezionata, immettere la query di esempio seguente:

  SecurityEvent | dove EventID == 4625 | conteggio di riepilogo per TargetAccount

  Il risultato mostra tutti gli account per cui non è stato possibile eseguire l'accesso, ovvero l'evento 4625.

  ![Search Results][3]

Consultare l'articolo sul [linguaggio di query di Log Analytics](../log-analytics/log-analytics-search-reference.md) per avere altre informazioni su come eseguire query sui dati dell'area di lavoro selezionata.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come accedere a una ricerca nel Centro sicurezza. Il Centro sicurezza usa la ricerca di Log Analytics. Per altre informazioni sulla ricerca di Log Analytics, vedere:

- [Che cos'è Log Analytics?](../log-analytics/log-analytics-overview.md) : panoramica di Log Analytics
- [Informazioni sulle ricerche log in Log Analytics](../log-analytics/log-analytics-log-search-new.md): descrive come vengono usate le ricerche log in Log Analytics e illustra i concetti con cui occorre avere familiarità prima di creare una ricerca log
- [Trovare dati con ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md): esercitazione sull'uso della ricerca log
- [Guida di riferimento alla ricerca in Log Analytics](../log-analytics/log-analytics-search-reference.md): descrive il linguaggio di query in Log Analytics

Per altre informazioni sul Centro sicurezza, vedere:

- [Panoramica del Centro sicurezza](security-center-intro.md): descrive le funzionalità principali del Centro sicurezza

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
