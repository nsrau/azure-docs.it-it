---
title: Gestire gli avvisi di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 75ca0438336825bf8d4bbdc6e08eca109f430fde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785919"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure

In questo argomento viene illustrato come visualizzare ed elaborare gli avvisi ricevuti per proteggere le risorse. 

* Per informazioni sui diversi tipi di avvisi, vedere [tipi di avviso di sicurezza](alerts-reference.md).
* Per una panoramica del modo in cui il Centro sicurezza genera avvisi, vedere [come il Centro sicurezza di Azure rileva e risponde alle minacce](security-center-alerts-overview.md).

> [!NOTE]
> Per abilitare i rilevamenti avanzati, abilitare Azure Defender. È disponibile una versione di valutazione gratuita. Per eseguire l'aggiornamento, selezionare piano tariffario nei [criteri di sicurezza](tutorial-security-policy.md). Per altre informazioni, vedere [Prezzi del Centro sicurezza di Azure](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco.

> [!NOTE]
> Per altre informazioni sul funzionamento delle funzionalità di rilevamento del Centro sicurezza, vedere [come il Centro sicurezza di Azure rileva e risponde alle minacce](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gestire gli avvisi di sicurezza

1. Dal dashboard del Centro sicurezza, vedere il riquadro  **protezione dalle minacce** per visualizzare e ottenere una panoramica degli avvisi.

    ![Riquadro Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Per visualizzare altri dettagli sugli avvisi, fare clic sul riquadro.

   ![Avvisi di sicurezza nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Per filtrare gli avvisi visualizzati, fare clic su **filtro** e dal pannello **filtro** visualizzato selezionare le opzioni di filtro che si desidera applicare. L'elenco viene aggiornato in base al filtro selezionato. Il filtro può essere molto utile. Ad esempio, potrebbe essere necessario gestire gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore, perché si sta esaminando una potenziale violazione del sistema.

    ![Filtro degli avvisi nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Rispondere agli avvisi di sicurezza

1. Dall'elenco **avvisi di sicurezza** , fare clic su un avviso di sicurezza. Vengono visualizzate le risorse necessarie e i passaggi da eseguire per correggere un attacco.

    ![Rispondere agli avvisi di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Dopo aver esaminato le informazioni, fare clic su una risorsa che è stata attaccata.

    Il riquadro sinistro della pagina avviso di sicurezza Mostra informazioni di alto livello relative all'avviso di sicurezza: titolo, gravità, stato, tempo attività, descrizione dell'attività sospetta e la risorsa interessata. Insieme alla risorsa interessata sono i tag di Azure rilevanti per la risorsa. Usare questi per dedurre il contesto aziendale della risorsa quando si esamina l'avviso.

    Il riquadro destro include la scheda **Dettagli avviso** che contiene ulteriori dettagli dell'avviso che consentono di esaminare il problema: indirizzi IP, file, processi e altro ancora.
     
    ![Suggerimenti sulle operazioni da eseguire per gli avvisi di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Inoltre, nel riquadro di destra è presente la scheda **azione** . Utilizzare questa scheda per eseguire ulteriori azioni relative all'avviso di sicurezza. Azioni come:
    - *Attenuazione della minaccia* : fornisce la procedura di correzione manuale per questo avviso di sicurezza
    - *Impedisci attacchi futuri* : fornisce consigli sulla sicurezza che consentono di ridurre la superficie di attacco, aumentare il comportamento di sicurezza e prevenire quindi attacchi futuri
    - *Attiva risposta automatica* : consente di attivare un'app per la logica come risposta a questo avviso di sicurezza
    - Non *visualizzare avvisi simili* : consente di disattivare gli avvisi futuri con caratteristiche simili se l'avviso non è pertinente per l'organizzazione

    ![Scheda azione](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Vedere anche

In questo documento si è appreso come visualizzare gli avvisi di sicurezza. Vedere le pagine seguenti per il materiale correlato:

- [Configurare le regole di eliminazione degli avvisi](alerts-suppression-rules.md)
- [Automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md)
