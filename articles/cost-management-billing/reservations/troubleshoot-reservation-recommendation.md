---
title: Risolvere i problemi relativi alle raccomandazioni per le prenotazioni di Azure
description: Questo articolo aiuta a comprendere e risolvere i problemi relativi alle raccomandazioni per le prenotazioni di Azure visualizzate nel portale di Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492238"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Risolvere i problemi relativi alle raccomandazioni per le prenotazioni di Azure

Questo articolo aiuta a comprendere e risolvere i problemi relativi alle raccomandazioni per le prenotazioni di Azure visualizzate nel portale di Azure. Potrebbero non essere visualizzate raccomandazioni oppure raccomandazioni non corrispondenti alle aspettative. Ad esempio, si potrebbe avere già un'istanza riservata per una configurazione di VM specifica e ci si potrebbe aspettare di vedere una raccomandazione per una configurazione di VM simile.

## <a name="symptoms"></a>Sintomi

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare a **Prenotazioni**.
2. Selezionare **+ Aggiungi** e quindi selezionare un prodotto.
3. Nella scheda **Consigliato** potrebbero non essere visualizzate raccomandazioni oppure raccomandazioni non corrispondenti alle aspettative.

## <a name="cause"></a>Causa

L'elenco dei prodotti consigliati viene generato dalla quantità di utilizzo per l'ambito (Condiviso o Sottoscrizione singola) rispetto al costo di una prenotazione per il prodotto. Se il motore per le raccomandazioni rileva un risparmio, consiglia un prodotto per l'acquisto. Se invece non identifica alcun risparmio, non consiglia alcun prodotto.

Quando si esegue una risorsa con una determinata configurazione, non c'è alcuna garanzia di risparmio in seguito all'acquisto di una prenotazione per tale configurazione. L'utilizzo potrebbe essere sporadico, ad esempio. In tal caso, il costo totale della prenotazione potrebbe non costituire un risparmio sulla durata del periodo della prenotazione.

È importante comprendere anche l'impatto della selezione dell'ambito sulle raccomandazioni. Quando l'ambito è impostato su **Condiviso** , le raccomandazioni nell'elenco mostrano le istanze riservate in cui Azure rileva un risparmio per l'intera registrazione associata alla sottoscrizione di fatturazione. Quando l'ambito è impostato su **Sottoscrizione singola** , le raccomandazioni nell'elenco si applicano solo alle risorse eseguite nella sottoscrizione. È possibile che alcune dimensioni di VM vengano consigliate per un ambito ma non per un altro. Ad esempio, l'utilizzo aggregato di **Standard_B1ls** nella registrazione potrebbe essere sufficientemente elevato da giustificare il costo dell'acquisto di una prenotazione nell'ambito della registrazione. Tuttavia, l'utilizzo in una singola sottoscrizione della registrazione potrebbe non essere abbastanza elevato da giustificare il costo dell'acquisto di una prenotazione nell'ambito. Se si cambia l'ambito da **Condiviso** a **Sottoscrizione singola** , le raccomandazioni prodotte potrebbero essere diverse.

Azure potrebbe consigliare l'acquisto di una prenotazione per determinati periodi e non per altri, in base al risparmio sui costi identificato. Nello specifico, un periodo di tre anni ha uno sconto maggiore rispetto a un periodo di un anno. È più probabile che Azure trovi risparmi per un periodo di tre anni piuttosto che per un periodo di un anno.

Se si vuole comprendere il motivo per cui Azure consiglia una dimensione e una quantità specifiche per una risorsa, selezionare **&lt;Quantità&gt; Visualizza i dettagli** per accedere a una visualizzazione approfondita che mostra i potenziali risparmi nel tempo.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Esempio che mostra il collegamento Visualizza i dettagli con le raccomandazioni sulla prenotazione" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Soluzione

È possibile acquistare qualsiasi quantità di prenotazione per il periodo che si preferisce. Se si acquista una prenotazione con una quantità e per un periodo diversi da quelli indicati dalla raccomandazione, il risparmio e l'utilizzo risultanti potrebbero non essere ottimali.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle raccomandazioni per le prenotazioni, vedere [Raccomandazioni per l'acquisto di prenotazioni](determine-reservation-purchase.md).
