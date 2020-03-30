---
title: Gestire gli incidenti di sicurezza nel Centro sicurezza di Azure Documenti Microsoft
description: Questo documento consente di usare il Centro sicurezza di Azure per gestire gli eventi imprevisti di sicurezza.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415673"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gestire gli eventi imprevisti di sicurezza nel Centro sicurezza di AzureManage security incidents in Azure Security Center

La triazione e l'analisi degli avvisi di sicurezza possono richiedere molto tempo anche per gli analisti della sicurezza più esperti, e per molti è difficile anche sapere da dove iniziare. Usando funzionalità di [analisi](security-center-detection-capabilities.md) per collegare le informazioni di [avvisi di sicurezza](security-center-managing-and-responding-alerts.md) distinti, il Centro sicurezza può offrire una singola visualizzazione di una campagna di attacco e di tutti gli avvisi correlati, consentendo di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate.

In questo argomento vengono illustrati gli eventi imprevisti nel Centro sicurezza e come usare le correzione degli avvisi.

## <a name="what-is-a-security-incident"></a>Che cos'è un evento imprevisto della sicurezza?

Nel Centro sicurezza, un evento imprevisto della sicurezza è un'aggregazione di tutti gli avvisi relativi a una risorsa, in linea con i modelli delle [catene di attacco](alerts-reference.md#intentions). Gli eventi imprevisti vengono visualizzati nell'elenco Avvisi di [sicurezza.](security-center-managing-and-responding-alerts.md) Fare clic su un evento imprevisto per visualizzare gli avvisi correlati, che consente di ottenere ulteriori informazioni su ogni occorrenza.

## <a name="managing-security-incidents"></a>Gestione degli eventi imprevisti della sicurezza

1. Nel dashboard del Centro sicurezza fare clic sul riquadro **Avvisi di sicurezza.** Vengono elencati gli eventi imprevisti e gli avvisi. Si noti che la descrizione dell'evento imprevisto della sicurezza ha un'icona diversa rispetto agli altri avvisi.

    ![Visualizzare gli incidenti di sicurezza](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Per visualizzare i dettagli, fare clic su un evento imprevisto. Il pannello **Errore di sicurezza rilevato** visualizza ulteriori dettagli. La sezione **Informazioni generali** può offrire informazioni dettagliate su cosa ha attivato l'avviso di sicurezza. Visualizza informazioni quali la risorsa di destinazione, l'indirizzo IP di origine (se applicabile), se l'avviso è ancora attivo e vengono forniti suggerimenti su come correggere.  

    ![Rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza di AzureRespond to security incidents in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Per ottenere ulteriori informazioni su ogni avviso, fare clic su un avviso. Le correzioni suggerite dal Centro sicurezza variano in base all'avviso di sicurezza.

   > [!NOTE]
   > Lo stesso avviso può esistere come parte di un evento imprevisto, nonché essere visibile come avviso autonomo.

    ![Dettagli dell'avviso](./media/security-center-incident/security-center-incident-alert.png)

1. Seguire i passaggi di correzione indicati per ogni avviso.


## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come usare le funzionalità relative agli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Per informazioni correlate, vedere gli argomenti seguenti:

* [Protezione dalle minacce nel Centro sicurezza di Azure](threat-protection.md)
* [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
* [Gestire gli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)