---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380166"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>Aggiunta di raccomandazioni secondarie alla raccomandazione "Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali"

#### <a name="summary"></a>Riepilogo

| Aspetto | Dettagli |
|---------|---------|
|Data dell'annuncio | 9 novembre 2020  |
|Data della modifica  |  Metà/fine novembre 2020 |
|Impatto     | Durante la transizione dalla versione corrente di questa raccomandazione alla versione sostitutiva, il punteggio di sicurezza potrebbe cambiare. |
|  |  |

Sta per essere rilasciata una versione ottimizzata della raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali**. La nuova versione *sostituirà* la versione corrente nel controllo di sicurezza Applica gli aggiornamenti del sistema e apporterà i miglioramenti seguenti:

- Raccomandazioni secondarie per ogni aggiornamento mancante
- Un'esperienza riprogettata nelle pagine del Centro sicurezza di Azure del portale di Azure
- Dati della raccomandazione arricchiti da Azure Resource Graph

#### <a name="transition-period"></a>Periodo di transizione

È previsto un periodo di transizione di circa 36 ore. Per ridurre al minimo le potenziali interruzioni, l'aggiornamento è stato pianificato per un fine settimana. Durante la transizione, i punteggi di sicurezza potrebbero cambiare.

#### <a name="redesigned-portal-experience"></a>Esperienza del portale riprogettata

La pagina di dettagli della raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** include l'elenco di risultati, come illustrato di seguito. Quando si seleziona un singolo risultato, viene visualizzato il riquadro dei dettagli con un collegamento alle informazioni sulla correzione e un elenco delle risorse interessate.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Apertura di una delle raccomandazioni secondarie nell'esperienza del portale per la raccomandazione aggiornata":::


#### <a name="richer-data-from-azure-resource-graph"></a>Dati arricchiti da Azure Resource Graph

Azure Resource Graph è un servizio di Azure progettato per offrire un'esplorazione efficiente delle risorse. È possibile usare Azure Resource Graph per eseguire query su larga scala su un determinato set di sottoscrizioni, in modo da regolamentare efficacemente l'ambiente. 

Per il Centro sicurezza di Azure, è possibile usare Azure Resource Graph e il [linguaggio di query Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query/) per eseguire query su un'ampia gamma di dati relativi alla postura di sicurezza.

Se si esegue una query sulla versione corrente della raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali**, le uniche informazioni disponibili di Azure Resource Graph sono che la correzione della raccomandazione deve applicata in un computer. Quando viene rilasciata la versione aggiornata, la query seguente restituirà tutti gli aggiornamenti di sistema mancanti raggruppati per computer.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).