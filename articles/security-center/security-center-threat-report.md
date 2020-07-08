---
title: Report di intelligence per le minacce generato dal Centro sicurezza di Azure | Documentazione Microsoft
description: Questa pagina consente di usare i report di intelligence per le minacce del Centro sicurezza di Azure durante un'indagine per trovare altre informazioni sugli avvisi di sicurezza
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: a4fdbab4a69fac1376779f37d5fa69fef587bf52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888214"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Report di intelligence per le minacce nel centro sicurezza di Azure

Questa pagina illustra il modo in cui i report di intelligence per le minacce del Centro sicurezza di Azure possono aiutarti a ottenere altre informazioni su una minaccia che ha generato un avviso di sicurezza.


## <a name="what-is-a-threat-intelligence-report"></a>Informazioni sui report di intelligence per le minacce

Il Centro sicurezza per la protezione dalle minacce funziona monitorando le informazioni di sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connesse. Per identificare le minacce, analizza queste informazioni, correlando spesso quelle raccolte da più origini. Per altre informazioni, vedere [come il Centro sicurezza di Azure rileva e risponde alle minacce](security-center-alerts-overview.md#detect-threats).

Quando il Centro sicurezza identifica una minaccia, viene attivato un [avviso di sicurezza](security-center-managing-and-responding-alerts.md)che contiene informazioni dettagliate relative all'evento, inclusi i suggerimenti per la correzione. Per consentire ai team di risposta agli eventi imprevisti di analizzare e correggere le minacce, il Centro sicurezza fornisce report di intelligence per le minacce contenenti informazioni sulle minacce rilevate. Il report include informazioni come:

* Identità o associazioni dell'utente malintenzionato, se queste informazioni sono disponibili
* Obiettivi degli utenti malintenzionati
* Campagne di attacco attuali e cronologiche, se queste informazioni sono disponibili
* Tattiche, strumenti e procedure usate dagli utenti malintenzionati
* Indicatori di compromissione (IoC) associati, ad esempio URL e hash file
* Vittimologia, ovvero la diffusione geografica e nel settore utile per determinare se le risorse di Azure sono esposte a rischi
* Informazioni sulla mitigazione dei rischi e correzione

> [!NOTE]
> La quantità di informazioni in un report specifico varia; il livello di dettaglio è basato sulle attività e la diffusione del malware.

Il Centro sicurezza rende disponibili tre tipi di report sulle minacce, che possono variare a seconda dell'attacco. I report disponibili sono:

* **Report gruppo di attività**: fornisce approfondimenti su utenti malintenzionati, obiettivi e tattiche.
* **Report della campagna**: è incentrato sui dettagli di specifiche campagne di attacco.
* **Report di riepilogo delle minacce**: tratta tutti gli elementi presenti nei due report precedenti.

Questo tipo di informazioni è utile durante il processo di risposta agli eventi imprevisti, in cui è in corso un'indagine per comprendere l'origine dell'attacco, le motivazioni dell'utente malintenzionato e le operazioni da eseguire per attenuare il problema in futuro.



## <a name="how-to-access-the-threat-intelligence-report"></a>Come accedere alle informazioni sui report di intelligence per le minacce

1. Dalla barra laterale del Centro sicurezza, aprire la pagina **avvisi di sicurezza** .
1. Selezionare un avviso. 
    Verrà visualizzata la pagina Dettagli avvisi con ulteriori dettagli sull'avviso. Di seguito è riportata la pagina dei dettagli degli avvisi **ransomware rilevati** .

    [![Pagina dei dettagli dell'avviso del ransomware rilevato dagli indicatori](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Selezionare il collegamento al report e un file PDF si aprirà nel browser predefinito.

    [![Pagina Dettagli avviso azione potenzialmente non sicura](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Facoltativamente, è possibile scaricare il report in formato PDF. 

    >[!TIP]
    > La quantità di informazioni disponibili per ogni avviso di sicurezza varia in base al tipo di avviso.



## <a name="next-steps"></a>Passaggi successivi

In questa pagina è stato illustrato come aprire i report di intelligence per le minacce durante l'analisi degli avvisi di sicurezza. Per informazioni correlate, vedere le pagine seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md). Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Gestione degli eventi imprevisti della sicurezza nel centro sicurezza di Azure](security-center-incident.md)