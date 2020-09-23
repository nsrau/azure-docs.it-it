---
title: Dashboard principale del Centro sicurezza di Azure o pagina ' panoramica '
description: Informazioni sulle funzionalità della pagina Panoramica del Centro sicurezza
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 637f7dde63d562f71aa31561a960ed3502a80229
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937171"
---
# <a name="azure-security-centers-overview-page"></a>Pagina Panoramica del Centro sicurezza di Azure

Quando si apre il Centro sicurezza di Azure, la prima pagina visualizzata è la pagina panoramica. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Pagina di panoramica di Centro sicurezza":::

Individuare e valutare la sicurezza dei carichi di lavoro e identificare e ridurre i rischi con la pagina Panoramica del Centro sicurezza.

La panoramica offre una visione unificata del comportamento di sicurezza dei carichi di lavoro cloud ibridi. Vengono inoltre visualizzati gli avvisi di sicurezza, le informazioni di code coverage e altro ancora.


## <a name="features-of-the-overview-page"></a>Funzionalità della pagina Panoramica

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Barra superiore della pagina Panoramica del Centro sicurezza":::

La **barra dei menu superiore** offre:
- **Sottoscrizioni** : è possibile visualizzare e filtrare l'elenco delle sottoscrizioni selezionando questo pulsante. Il Centro sicurezza modificherà la visualizzazione in modo da riflettere il comportamento di sicurezza delle sottoscrizioni selezionate.
- **Novità: apre** le note sulla [versione](release-notes.md) in modo che sia possibile rimanere aggiornati sulle nuove funzionalità, le correzioni di bug e le funzionalità deprecate.
- **Numeri di alto livello** per gli account cloud connessi, per visualizzare il contesto delle informazioni nei riquadri principali indicati di seguito. Nonché il numero di avvisi e avvisi attivi.
    Scopri di più sulla connessione degli [account AWS](quickstart-onboard-aws.md) e dei tuoi [progetti GCP](quickstart-onboard-gcp.md).


Al centro della pagina sono disponibili **quattro riquadri centrali**, ciascuno dei quali viene collegato a un dashboard dedicato per altri dettagli:
- **Punteggio sicuro** : il Centro sicurezza valuta continuamente le risorse, le sottoscrizioni e le organizzazioni per i problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato. [Altre informazioni](secure-score-security-controls.md)
- **Conformità** : il Centro sicurezza fornisce informazioni approfondite sul comportamento di conformità basato sulle valutazioni continue dell'ambiente Azure. Il Centro sicurezza analizza i fattori di rischio nell'ambiente cloud ibrido in base alle procedure di sicurezza consigliate. Queste valutazioni vengono mappate ai controlli di conformità da un set di standard supportato. [Altre informazioni](security-center-compliance-dashboard.md).
- **Azure Defender** : si tratta della piattaforma di protezione del carico di lavoro cloud (CWPP) integrata nel centro sicurezza per la protezione avanzata e intelligente dei carichi di lavoro ibridi e Azure. Il riquadro Mostra il code coverage delle risorse connesse (per le sottoscrizioni attualmente selezionate) e gli avvisi recenti, codificati in base al livello di gravità. [Altre informazioni](azure-defender.md)
- **Inventario** : il riquadro Mostra il numero di macchine virtuali non monitorate e un barometro semplice delle risorse monitorate da CenterBen di sicurezza. [Altre informazioni](asset-inventory.md)


Il riquadro **Insights** offre elementi personalizzati per l'ambiente, tra cui:
- Risorse più attaccate
- I controlli di sicurezza che hanno il massimo potenziale per aumentare il Punteggio sicuro
- Raccomandazioni attive con la maggior parte delle risorse interessate
- Post di Blog recenti degli esperti del Centro sicurezza di Azure

## <a name="next-steps"></a>Passaggi successivi

In questa pagina è stata introdotta la pagina Panoramica del Centro sicurezza. Per informazioni correlate, vedere:

- [Esplorare e gestire le risorse con gli strumenti di inventario e gestione degli asset](asset-inventory.md)
- [Punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md)