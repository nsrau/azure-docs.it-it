---
title: Ricerca nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sul modo in cui il Centro sicurezza di Azure usa la ricerca log di monitoraggio di Azure per recuperare e analizzare i dati di sicurezza.
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
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662920"
---
# <a name="azure-security-center-search-retired"></a>Ricerca nel centro sicurezza di Azure (ritirata)

> [!NOTE]
> Il dashboard di ricerca del Centro sicurezza è stato ritirato il 31 luglio 2019. Per ulteriori informazioni e servizi alternativi, vedere [ritiro delle funzionalità del Centro sicurezza (luglio 2019)](security-center-features-retirement-july2019.md#menu_search).

Il Centro sicurezza di Azure usa la [Ricerca log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per recuperare e analizzare i dati di sicurezza. Log di monitoraggio di Azure include un linguaggio di query che consente di recuperare e consolidare rapidamente i dati. Dal centro sicurezza è possibile sfruttare la ricerca nei log di monitoraggio di Azure per costruire query e analizzare i dati raccolti.

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

   Risultato Mostra tutti gli account che non sono riusciti ad accedere (evento 4625).

   ![Search Results][3]

Per altre informazioni su come eseguire una query per i dati nell'area di lavoro selezionata, vedere [linguaggio di query kusto](../log-analytics/log-analytics-search-reference.md) .

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come accedere a una ricerca nel Centro sicurezza. Il Centro sicurezza usa la ricerca dei log di monitoraggio di Azure. Per ulteriori informazioni sulla ricerca nei log di monitoraggio di Azure, vedere:

- [Che cosa sono i log di monitoraggio di Azure?](../log-analytics/log-analytics-overview.md) -Panoramica sui log di monitoraggio di Azure
- [Informazioni sulle ricerche nei log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-search-new.md) : descrive come vengono usate le ricerche log nei log di monitoraggio di Azure e vengono forniti i concetti che è necessario comprendere prima di creare una ricerca nei log
- [Trovare dati usando le ricerche nei log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) : esercitazione sull'uso della ricerca nei log
- [Riferimento alla ricerca di Kusto](../log-analytics/log-analytics-search-reference.md) : descrive il linguaggio di query nei log di monitoraggio di Azure

Per altre informazioni sul Centro sicurezza, vedere:

- [Panoramica del Centro sicurezza](security-center-intro.md): descrive le funzionalità principali del Centro sicurezza

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
