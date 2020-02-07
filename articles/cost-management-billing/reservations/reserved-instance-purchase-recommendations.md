---
title: Come vengono create le raccomandazioni per le prenotazioni di Azure
description: Informazioni su come vengono create le raccomandazioni per le prenotazioni di Azure per le macchine virtuali.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851352"
---
# <a name="how-reservation-recommendations-are-created"></a>Come vengono create le raccomandazioni per le prenotazioni

Le raccomandazioni per l'acquisto di istanze riservate di Azure vengono generate dall'[API Raccomandazioni di prenotazione](/rest/api/consumption/reservationrecommendations) per il consumo di Azure. Le raccomandazioni dell'API vengono usate anche da [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). Advisor visualizza le raccomandazioni nel portale di Azure.

Se sono presenti macchine virtuali in esecuzione in Azure, è possibile approfittare di prezzi scontati per le istanze riservate e pagare in anticipo le macchine virtuali. L'API Raccomandazioni per il consumo di Microsoft valuta l'uso per 7, 30 e 60 giorni e consiglia le configurazioni ottimali per le istanze riservate. Consente di calcolare il costo pagato se non si hanno istanze riservate rispetto al costo pagato in presenza di istanze riservate in modo da ottimizzare il risparmio.

Azure Advisor visualizza le raccomandazioni basate su un periodo di 30 giorni.

Per semplicità, nell'esempio seguente vengono illustrati i calcoli eseguiti per una raccomandazione relativa a un periodo di sette giorni. Lo stesso metodo si applica nel calcolo delle raccomandazioni per periodi di 30 o 60 giorni.

## <a name="calculation-method-example"></a>Esempio di metodo di calcolo

Si supponga che l'utilizzo orario di una macchina virtuale di Windows per uno SKU e un'area geografica specifici vari in un periodo di sette giorni (168 ore). L'utilizzo minimo è di 65 unità e l'utilizzo massimo è di 127 unità nei sette giorni. In questo esempio, nell'ora 79 venivano usate 80 macchine virtuali ed erano state acquistate 75 istanze riservate.

Se si acquistano 75 istanze riservate, per l'ora 79 si pagano i costi seguenti:

- 75 istanze riservate. Il costo viene pagato in anticipo al momento dell'acquisto delle istanze riservate.
- Le istanze riservate coprono il costo dell'hardware per le macchine virtuali in esecuzione, perciò si pagheranno 75 ore al prezzo solo del software.
- L'utilizzo per l'ora 79 è pari a 80, pertanto verranno addebitate cinque ore di Windows più il prezzo a consumo della combinazione hardware. Il prezzo della combinazione si basa sul Contratto Enterprise o sulla tariffa con pagamento in base al consumo.

Se si acquistano 75 istanze riservate, è possibile calcolare il costo totale aggiungendo i costi orari precedenti. È anche possibile calcolare il costo corrente in base alla tariffa. In questo esempio la differenza tra i due importi corrisponde al risparmio per sette giorni.

L'API esegue i calcoli per ogni specifico punto di utilizzo e restituisce la quantità consigliata in cui il risparmio è massimizzato. Nell'esempio seguente il grafico indica un picco di risparmio a 68. In seguito il risparmio si riduce, perciò la raccomandazione dell'API è 68.

![Diagramma che indica il picco di risparmio](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Altro comportamento previsto dell'API

- L'API indica i possibili risparmi con [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) per Windows quando si usa il vantaggio. Se il vantaggio non viene usato, le raccomandazioni dell'API sono basate sul costo di Windows di base. Se disponibile, valutare l'opportunità di usare Vantaggio Azure Hybrid per aumentare il risparmio.
- Con un periodo analizzato di sette giorni si potrebbero non ottenere suggerimenti in caso di arresto delle macchine virtuali per più di un giorno.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [come viene applicato lo sconto per la prenotazione di Azure alle macchine virtuali](../manage/understand-vm-reservation-charges.md).
