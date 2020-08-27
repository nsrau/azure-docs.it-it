---
title: Raccomandazioni per le prenotazioni di Azure
description: Informazioni sulle raccomandazioni per le prenotazioni di Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 813cf91195a43a2fc68236febe114c67eb5a3378
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684610"
---
# <a name="reservation-recommendations"></a>Consigli sulle prenotazioni

Le raccomandazioni per l'acquisto di istanze riservate di Azure vengono fornite tramite l'[API delle raccomandazioni per le prenotazioni](/rest/api/consumption/reservationrecommendations) di Consumo di Azure, [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e tramite l'esperienza di acquisto delle prenotazioni nel portale di Azure.

Nei passaggi seguenti viene definita la modalità di calcolo delle raccomandazioni:

1. Il motore di raccomandazione valuta l'utilizzo orario per le risorse nell'ambito specificato negli ultimi 7, 30 e 60 giorni.
2. In base ai dati di utilizzo, il motore simula i costi con e senza prenotazioni.
3. Vengono simulati i costi per le diverse quantità e viene consigliata la quantità che consente di ottenere il massimo risparmio.
4. Se le risorse vengono arrestate regolarmente, con la simulazione non verrà individuato alcun risparmio e non verrà fornita alcuna raccomandazione di acquisto.

## <a name="recommendations-in-the-azure-portal"></a>Raccomandazioni nel portale di Azure

Le raccomandazioni per l'acquisto di prenotazioni sono illustrate anche nell'esperienza di acquisto del portale di Azure. Le raccomandazioni sono contrassegnate con la dicitura **Quantità raccomandata**. All'acquisto, la quantità raccomandata da Azure garantirà il massimo risparmio possibile. Benché sia possibile acquistare una quantità qualsiasi, se si acquista una quantità diversa il risparmio non sarà ottimale.

Di seguito sono riportati alcuni esempi che spiegano il motivo.

Nell'immagine di esempio seguente relativa alla raccomandazione selezionata, Azure raccomanda l'acquisto di una quantità pari a 6.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Esempio di raccomandazione per l'acquisto di prenotazioni" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Per visualizzare altre informazioni sulla raccomandazione, è possibile selezionare il collegamento **Visualizza i dettagli**. L'immagine seguente mostra i dettagli relativi alla raccomandazione. La quantità raccomandata viene calcolata per il massimo utilizzo possibile, in base all'utilizzo cronologico. Se l'utilizzo non è uniforme, è possibile che la raccomandazione non indichi un utilizzo al 100%. Nell'esempio si noti che l'utilizzo presenta una fluttuazione nel tempo. Sono visualizzati il costo della prenotazione, il possibile risparmio e la percentuale di utilizzo.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Esempio che mostra i dettagli di una raccomandazione per l'acquisto di prenotazioni" :::

Se la quantità delle prenotazioni viene aumentata o diminuita rispetto al livello indicato nella raccomandazione, il grafico e i valori stimati cambiano. Aumentando la quantità delle prenotazioni il risparmio risulterà ridotto perché si otterrà un utilizzo ridotto delle prenotazioni. In altre parole, si pagherà per le prenotazioni che non sono completamente utilizzate.

Se si diminuisce la quantità delle prenotazioni, anche il risparmio verrà ridotto. Anche se l'utilizzo aumenta, probabilmente si presenteranno periodi in cui le prenotazioni non riusciranno a coprire completamente l'utilizzo. L'utilizzo oltre la quantità delle prenotazioni sarà soddisfatto da risorse più costose con pagamento in base al consumo. Nell'immagine di esempio seguente viene illustrato questo aspetto. La quantità della prenotazione è stata ridotta manualmente a 4. L'utilizzo della prenotazione è aumentato, ma il risparmio complessivo risulta ridotto perché sono presenti costi con pagamento in base al consumo.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Esempio che mostra i dettagli modificati di una raccomandazione per l'acquisto di prenotazioni" :::

Per ottimizzare i risparmi con le prenotazioni, provare ad acquistare prenotazioni che siano più vicine possibile alla quantità della raccomandazione.

## <a name="recommendations-in-azure-advisor"></a>Raccomandazioni in Azure Advisor

Le raccomandazioni per l'acquisto di prenotazioni sono disponibili in Azure Advisor. Tenere presente quanto segue:

- In Advisor sono disponibili solo le raccomandazioni relative a una singola sottoscrizione.
- Le raccomandazioni vengono calcolate considerando la tendenza di utilizzo degli ultimi 30 giorni.
- La quantità delle raccomandazioni e il risparmio si riferiscono a una prenotazione di 3 anni, se disponibile. Se per il servizio non viene venduta la prenotazione di 3 anni, la raccomandazione viene calcolata in base al prezzo di prenotazione di 1 anno.
- Le raccomandazioni tengono conto di tutti gli sconti speciali che possono essere applicati alle tariffe di utilizzo su richiesta.
- Se si acquista una prenotazione con ambito condiviso, la rimozione delle raccomandazioni di acquisto delle prenotazioni di Advisor può richiedere fino a 30 giorni.

## <a name="other-expected-api-behavior"></a>Altro comportamento previsto dell'API

- Con un periodo analizzato di sette giorni si potrebbero non ottenere suggerimenti in caso di arresto delle macchine virtuali per più di un giorno.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come viene applicato lo sconto per la prenotazione di Azure alle macchine virtuali](../manage/understand-vm-reservation-charges.md).
