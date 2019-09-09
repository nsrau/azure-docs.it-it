---
title: Gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure | Microsoft Docs
description: Questo documento consente di usare il Centro sicurezza di Azure per gestire gli eventi imprevisti della sicurezza.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/27/2019
ms.author: rkarlin
ms.openlocfilehash: 9f757d5fb2c8d3a807e325068d386e16a7d6caa7
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806560"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure

La valutazione e l'analisi degli avvisi di sicurezza possono richiedere molto tempo per gli analisti di sicurezza più qualificati e, per molti, è difficile capire da dove iniziare. Usando funzionalità di [analisi](security-center-detection-capabilities.md) per collegare le informazioni di [avvisi di sicurezza](security-center-managing-and-responding-alerts.md) distinti, il Centro sicurezza può offrire una singola visualizzazione di una campagna di attacco e di tutti gli avvisi correlati, consentendo di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate.

In questo argomento vengono illustrati gli eventi imprevisti nel centro sicurezza e come utilizzare la correzione degli avvisi.

## <a name="what-is-a-security-incident"></a>Che cos'è un evento imprevisto della sicurezza?

Nel Centro sicurezza un evento imprevisto della sicurezza è un'aggregazione di tutti gli avvisi relativi a una risorsa, in linea con i modelli delle [catene di attacco](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Gli eventi imprevisti vengono visualizzati nell'elenco degli [avvisi di sicurezza](security-center-managing-and-responding-alerts.md) . Fare clic su Ona un evento imprevisto per visualizzare gli avvisi correlati, che consentono di ottenere ulteriori informazioni su ogni occorrenza.

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

Per ulteriori informazioni sugli avvisi, sulla [gestione e sulla risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md).

Gli argomenti seguenti illustrano i diversi avvisi in base ai tipi di risorse:

* [Avvisi server e VM IaaS](security-center-alerts-iaas.md)
* [Avvisi di calcolo nativi](security-center-alerts-compute.md)
* [Avvisi di servizi dati](security-center-alerts-data-services.md)

Gli argomenti seguenti illustrano come il Centro sicurezza usa i diversi dati di telemetria raccolti dall'integrazione con l'infrastruttura di Azure, in modo da applicare livelli di protezione aggiuntivi per le risorse distribuite in Azure:

* [Avvisi del livello di servizio](security-center-alerts-service-layer.md)
* [Integrazione con i prodotti di sicurezza di Azure](security-center-alerts-integration.md)

## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come usare le funzionalità relative agli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Avvisi di sicurezza nel centro sicurezza di Azure](security-center-alerts-overview.md).
* [Gestire gli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)
* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità in Azure.
