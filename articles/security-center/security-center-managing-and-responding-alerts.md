---
title: Gestire gli avvisi di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: v-mohabe
ms.openlocfilehash: a31b6006ff3345947fc0c64b9427d79e3870a2b6
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052096"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gestire e rispondere agli avvisi di sicurezza nel centro sicurezza di Azure

In questo argomento viene illustrato come visualizzare ed elaborare gli avvisi ricevuti per proteggere le risorse. 

* Per informazioni sui diversi tipi di avvisi, vedere [tipi di avviso di sicurezza](security-center-alerts-overview.md#security-alert-types).
* Per una panoramica del modo in cui il Centro sicurezza genera avvisi, vedere [come il Centro sicurezza di Azure rileva e risponde alle minacce](security-center-alerts-overview.md#detect-threats).

> [!NOTE]
> Per abilitare le funzionalità di rilevamento avanzato, eseguire l'aggiornamento al livello Standard del Centro sicurezza di Azure. È disponibile una prova gratuita. Per eseguire l'aggiornamento, selezionare il piano tariffario nei [criteri di sicurezza](tutorial-security-policy.md). Per altre informazioni, vedere [Prezzi del Centro sicurezza di Azure](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco.

> [!NOTE]
> Per altre informazioni sul funzionamento delle funzionalità di rilevamento del Centro sicurezza, vedere [come il Centro sicurezza di Azure rileva e risponde alle minacce](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gestire gli avvisi di sicurezza

1. Dal dashboard del Centro sicurezza, vedere il riquadro **protezione dalle minacce** per visualizzare e ottenere una panoramica degli avvisi.

    ![Riquadro Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Per visualizzare altri dettagli sugli avvisi, fare clic sul riquadro.

   ![Avvisi di sicurezza nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Per filtrare gli avvisi visualizzati, fare clic su **filtro**e dal pannello **filtro** visualizzato selezionare le opzioni di filtro che si desidera applicare. L'elenco viene aggiornato in base al filtro selezionato. Il filtro può essere molto utile. Ad esempio, potrebbe essere necessario gestire gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore, perché si sta esaminando una potenziale violazione del sistema.

    ![Filtro degli avvisi nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Rispondere agli avvisi di sicurezza

1. Dall'elenco **avvisi di sicurezza** , fare clic su un avviso di sicurezza. Vengono visualizzate le risorse necessarie e i passaggi da eseguire per correggere un attacco.

    ![Rispondere agli avvisi di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Dopo aver esaminato le informazioni, fare clic su una risorsa che è stata attaccata.

    ![Suggerimenti sulle operazioni da eseguire per gli avvisi di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    La sezione **informazioni generali fornisce informazioni** dettagliate su ciò che ha attivato l'avviso di sicurezza. Vengono visualizzate informazioni quali la risorsa di destinazione, l'indirizzo IP di origine (se applicabile), se l'avviso è ancora attivo e consigli su come correggere il problema.  

    > [!NOTE]
    >In alcuni casi, l'indirizzo IP di origine non è disponibile, alcuni log eventi di sicurezza di Windows non includono l'indirizzo IP.

1. Le fasi di correzione suggerite dal centro sicurezza variano in base all'avviso di sicurezza. Seguirli per ogni avviso. In alcuni casi, per mitigare un avviso di rilevamento delle minacce, potrebbe essere necessario usare altri controlli o servizi di Azure per implementare la correzione consigliata. 

    Gli argomenti seguenti illustrano i diversi avvisi in base ai tipi di risorse:
    
    * [Avvisi server e VM IaaS](security-center-alerts-iaas.md)
    * [Avvisi di calcolo nativi](security-center-alerts-compute.md)
    * [Avvisi di servizi dati](security-center-alerts-data-services.md)
    
    Gli argomenti seguenti illustrano come il Centro sicurezza usa i diversi dati di telemetria raccolti dall'integrazione con l'infrastruttura di Azure, in modo da applicare livelli di protezione aggiuntivi per le risorse distribuite in Azure:
    
    * [Avvisi del livello di servizio](security-center-alerts-service-layer.md)
    * [Integrazione con i prodotti di sicurezza di Azure](security-center-alerts-integration.md)
    
## <a name="see-also"></a>Vedere anche

In questo documento si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Avvisi di sicurezza nel centro sicurezza di Azure](security-center-alerts-overview.md).
* [Gestione degli eventi imprevisti della sicurezza](security-center-incident.md)
* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.
