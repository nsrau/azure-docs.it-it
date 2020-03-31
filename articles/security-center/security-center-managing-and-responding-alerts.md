---
title: Gestire gli avvisi di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415692"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure

In questo argomento viene illustrato come visualizzare ed elaborare gli avvisi ricevuti per proteggere le risorse. 

* Per informazioni sui diversi tipi di avvisi, vedere [Tipi di avviso](alerts-reference.md)di sicurezza .
* Per una panoramica del modo in cui il Centro sicurezza genera avvisi, vedere Come il Centro sicurezza di [Azure rileva e risponde alle minacce.](security-center-alerts-overview.md)

> [!NOTE]
> Per abilitare le funzionalità di rilevamento avanzato, eseguire l'aggiornamento al livello Standard del Centro sicurezza di Azure. È disponibile una versione di valutazione gratuita. Per eseguire l'aggiornamento, selezionare Livello prezzi in Criteri di [sicurezza](tutorial-security-policy.md). Per altre informazioni, vedere [Prezzi del Centro sicurezza di Azure](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco.

> [!NOTE]
> Per altre informazioni sul funzionamento delle funzionalità di rilevamento del Centro sicurezza, vedere Come il Centro sicurezza di [Azure rileva e risponde alle minacce.](security-center-alerts-overview.md#detect-threats)

## <a name="manage-your-security-alerts"></a>Gestire gli avvisi di sicurezza

1. Nel dashboard del Centro sicurezza vedere il riquadro **Protezione dalle minacce** per visualizzare e visualizzare gli avvisi.

    ![Riquadro Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Per visualizzare ulteriori dettagli sugli avvisi, fare clic sul riquadro.

   ![Avvisi di sicurezza nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Per filtrare gli avvisi visualizzati, fare clic su **Filtro**e nel pannello **Filtro** visualizzato selezionare le opzioni di filtro da applicare. L'elenco viene aggiornato in base al filtro selezionato. Filtrare può essere molto utile. Ad esempio, potrebbe essere necessario gestire gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore, perché si sta esaminando una potenziale violazione del sistema.

    ![Filtro degli avvisi nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Rispondere agli avvisi di sicurezza

1. Nell'elenco Avvisi di **sicurezza** fare clic su un avviso di sicurezza. Vengono visualizzate le risorse necessarie e i passaggi da eseguire per correggere un attacco.

    ![Rispondere agli avvisi di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Dopo aver esaminato le informazioni, fare clic su una risorsa che è stata attaccata.

    ![Suggerimenti su cosa fare sugli avvisi di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    La sezione **Informazioni generali** può offrire informazioni dettagliate su cosa ha attivato l'avviso di sicurezza. Visualizza informazioni quali la risorsa di destinazione, l'indirizzo IP di origine (se applicabile), se l'avviso è ancora attivo e vengono forniti suggerimenti su come correggere.  

    > [!NOTE]
    >In alcuni casi, l'indirizzo IP di origine non è disponibile, alcuni registri degli eventi di protezione di Windows non includono l'indirizzo IP.

1. La procedura di correzione consigliata dal Centro sicurezza varia in base all'avviso di sicurezza. Seguili per ogni avviso. 

    In alcuni casi, per ridurre un avviso di sicurezza, potrebbe essere necessario usare altri controlli o servizi di Azure per implementare la correzione consigliata. 

## <a name="see-also"></a>Vedere anche

In questo documento si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Avvisi di sicurezza nel Centro sicurezza di Azure.](security-center-alerts-overview.md)
* [Gestione degli incidenti di sicurezza](security-center-incident.md)