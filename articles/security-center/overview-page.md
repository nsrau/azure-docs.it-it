---
title: Dashboard principale o pagina di panoramica del Centro sicurezza di Azure
description: Informazioni sulle funzionalità della pagina di panoramica del Centro sicurezza
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 899fddf08a817126ce775733da38ffd10ab3ab23
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952115"
---
# <a name="azure-security-centers-overview-page"></a>Pagina di panoramica del Centro sicurezza di Azure

Quando si apre il Centro sicurezza di Azure, la prima pagina che viene visualizzata è quella di panoramica. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Pagina di panoramica di Centro sicurezza":::

La pagina di panoramica del Centro sicurezza consente di individuare e valutare la sicurezza dei carichi di lavoro e di identificare e ridurre i rischi.

La panoramica offre una visualizzazione unificata del comportamento di sicurezza dei carichi di lavoro nel cloud ibrido. Mostra inoltre avvisi di sicurezza, informazioni sulla copertura e altri dettagli.


## <a name="features-of-the-overview-page"></a>Funzionalità della pagina di panoramica

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Pagina di panoramica di Centro sicurezza":::

La **barra dei menu superiore** offre:
- **Sottoscrizioni**: selezionando questo pulsante è possibile visualizzare e filtrare l'elenco delle sottoscrizioni. Il Centro sicurezza modificherà la visualizzazione in modo da riflettere il comportamento di sicurezza delle sottoscrizioni selezionate.
- **Novità**: apre le [note sulla versione](release-notes.md) per mantenersi aggiornati sulle nuove funzionalità, le correzioni di bug e le funzionalità deprecate.
- **Numeri generali** per gli account cloud connessi, per mostrare il contesto delle informazioni nei riquadri principali descritti di seguito. Viene visualizzato anche il numero di raccomandazioni e avvisi attivi.
    Leggere altre informazioni sulla connessione degli [account AWS](quickstart-onboard-aws.md) e dei [progetti GCP](quickstart-onboard-gcp.md).


Al centro della pagina sono presenti **quattro riquadri centrali**, ognuno dei quali conduce a un dashboard dedicato contenente altri dettagli:
- **Punteggio di sicurezza**: il Centro sicurezza valuta continuamente le risorse, le sottoscrizioni e l'intera organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato. [Altre informazioni](secure-score-security-controls.md)
- **Conformità**: il Centro sicurezza offre informazioni dettagliate sul comportamento di conformità in base a valutazioni continue dell'ambiente Azure. Il Centro sicurezza analizza i fattori di rischio nell'ambiente cloud ibrido in base alle procedure di sicurezza consigliate. Tali valutazioni sono associate ai controlli di conformità di un set di standard supportato. [Altre informazioni](security-center-compliance-dashboard.md)
- **Azure Defender**: è la soluzione CWPP (Cloud Workload Protection Platform) integrata nel Centro sicurezza per offrire una protezione avanzata e intelligente dei carichi di lavoro di Azure e ibridi. Il riquadro mostra la copertura delle risorse connesse (per le sottoscrizioni attualmente selezionate) e gli avvisi recenti, contraddistinti da colori diversi in base al livello di gravità. [Altre informazioni](azure-defender.md)
- **Inventario**: il riquadro mostra il numero di macchine virtuali non monitorate e un semplice barometro delle risorse monitorate dal Centro sicurezza. [Altre informazioni](asset-inventory.md)


Il riquadro **Informazioni dettagliate** offre elementi personalizzati per l'ambiente, tra cui:
- Risorse che hanno subito il maggior numero di attacchi
- Controlli di sicurezza con le maggiori probabilità di aumentare il punteggio di sicurezza
- Raccomandazioni attive per le risorse maggiormente interessate
- Post di blog recenti degli esperti del Centro sicurezza di Azure

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni introduttive sulla pagina di panoramica del Centro sicurezza. Per informazioni correlate, vedere:

- [Esplorare e gestire le risorse con gli strumenti di inventario e gestione delle risorse](asset-inventory.md)
- [Punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md)