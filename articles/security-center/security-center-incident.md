---
title: Gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento consente di usare il Centro sicurezza di Azure per gestire gli eventi imprevisti della sicurezza.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415673"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure

La valutazione e l'analisi degli avvisi di sicurezza possono richiedere molto tempo per gli analisti di sicurezza più qualificati e, per molti, è difficile capire da dove iniziare. Usando funzionalità di [analisi](security-center-detection-capabilities.md) per collegare le informazioni di [avvisi di sicurezza](security-center-managing-and-responding-alerts.md) distinti, il Centro sicurezza può offrire una singola visualizzazione di una campagna di attacco e di tutti gli avvisi correlati, consentendo di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate.

In questo argomento vengono illustrati gli eventi imprevisti nel centro sicurezza e come utilizzare la correzione degli avvisi.

## <a name="what-is-a-security-incident"></a>Che cos'è un evento imprevisto della sicurezza?

Nel Centro sicurezza, un evento imprevisto della sicurezza è un'aggregazione di tutti gli avvisi relativi a una risorsa, in linea con i modelli delle [catene di attacco](alerts-reference.md#intentions). Gli eventi imprevisti vengono visualizzati nell'elenco degli [avvisi di sicurezza](security-center-managing-and-responding-alerts.md) . Fare clic su un evento imprevisto per visualizzare gli avvisi correlati, che consentono di ottenere ulteriori informazioni su ogni occorrenza.

## <a name="managing-security-incidents"></a>Gestione degli eventi imprevisti della sicurezza

1. Nel dashboard del Centro sicurezza fare clic sul riquadro **avvisi di sicurezza** . Gli eventi imprevisti e gli avvisi sono elencati. Si noti che la descrizione dell'evento imprevisto della sicurezza ha un'icona diversa rispetto agli altri avvisi.

    ![Visualizzare gli eventi imprevisti della sicurezza](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Per visualizzare i dettagli, fare clic su un evento imprevisto. Il pannello **rilevato evento imprevisto di sicurezza** Visualizza altri dettagli. La sezione **informazioni generali fornisce informazioni** dettagliate su ciò che ha attivato l'avviso di sicurezza. Vengono visualizzate informazioni quali la risorsa di destinazione, l'indirizzo IP di origine (se applicabile), se l'avviso è ancora attivo e consigli su come correggere il problema.  

    ![Rispondere agli eventi imprevisti della sicurezza nel centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Per ottenere ulteriori informazioni su ogni avviso, fare clic su un avviso. Le correzioni suggerite dal Centro sicurezza variano in base all'avviso di sicurezza.

   > [!NOTE]
   > Lo stesso avviso può esistere come parte di un evento imprevisto, nonché essere visibile come un avviso autonomo.

    ![Dettagli dell'avviso](./media/security-center-incident/security-center-incident-alert.png)

1. Attenersi alla procedura di correzione specificata per ogni avviso.


## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come usare le funzionalità relative agli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Per informazioni correlate, vedere gli argomenti seguenti:

* [Protezione dalle minacce nel Centro sicurezza di Azure](threat-protection.md)
* [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
* [Gestire gli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)