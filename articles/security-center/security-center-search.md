---
title: Ricerca nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come Centro sicurezza di Azure Usa la ricerca nei log di monitoraggio di Azure per recuperare e analizzare i dati di sicurezza.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 6cbf3d70bd835ce1b838b19c93507f7d9487a418
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332609"
---
# <a name="azure-security-center-search"></a>Ricerca nel Centro sicurezza di Azure
Il Centro sicurezza di Azure Usa [monitoraggio di Azure log ricerca](../log-analytics/log-analytics-log-searches.md) per recuperare e analizzare i dati di sicurezza. Log di monitoraggio di Azure include un linguaggio di query per recuperare e consolidare i dati rapidamente. Dal Centro sicurezza, è possibile sfruttare ricerca nei log di monitoraggio di Azure per creare query e analizzare i dati raccolti.

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

Visualizzare [linguaggio di query Kusto](../log-analytics/log-analytics-search-reference.md) per altre informazioni su come eseguire query sui dati nell'area di lavoro selezionato.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come accedere a una ricerca nel Centro sicurezza. Il Centro sicurezza Usa la ricerca nei log di monitoraggio di Azure. Per altre informazioni sulla ricerca nei log di monitoraggio di Azure, vedere:

- [Che cos'è log di monitoraggio di Azure?](../log-analytics/log-analytics-overview.md) : I log Panoramica di monitoraggio di Azure
- [Informazioni sulle ricerche log in Monitoraggio di Azure log](../log-analytics/log-analytics-log-search-new.md) : descrive come vengono usate le ricerche log in log di monitoraggio di Azure e illustra i concetti con cui occorre avere familiarità prima di creare una ricerca log
- [Trovare dati tramite ricerche nei log in Monitoraggio di Azure log](../log-analytics/log-analytics-log-searches.md) : esercitazione sull'uso della ricerca log
- [Riferimento alla ricerca di Kusto](../log-analytics/log-analytics-search-reference.md) : viene descritto il linguaggio di query nei log di monitoraggio di Azure

Per altre informazioni sul Centro sicurezza, vedere:

- [Panoramica del Centro sicurezza](security-center-intro.md): descrive le funzionalità principali del Centro sicurezza

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
