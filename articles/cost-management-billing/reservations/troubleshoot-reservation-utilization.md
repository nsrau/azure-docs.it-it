---
title: Risolvere i problemi di utilizzo delle prenotazioni di Azure
description: Questo articolo aiuta a comprendere e risolvere i problemi relativi alle prenotazioni di Azure che mostrano un utilizzo zero (0) nel portale di Azure. Vengono inoltre spiegati i motivi per cui i dati si utilizzo non corrispondono.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207434"
---
# <a name="troubleshoot-reservation-utilization"></a>Risolvere i problemi di utilizzo delle prenotazioni

Questo articolo aiuta a comprendere e risolvere i problemi relativi alle prenotazioni di Azure che mostrano un utilizzo zero (0) nel portale di Azure. Vengono inoltre spiegati i motivi per cui i dati si utilizzo non corrispondono.

## <a name="symptoms"></a>Sintomi

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Prenotazioni** .
1. Nell'elenco controllare la quantità di utilizzo per una prenotazione nella colonna **Utilizzo (%)** . Potrebbe essere 0%.
1. Selezionare la prenotazione.
1. Nella pagina di panoramica della prenotazione la percentuale di utilizzo mostrata nel grafo potrebbe non corrispondere al valore indicato nell'elenco di prenotazioni.

## <a name="cause"></a>Causa

La colonna **Utilizzo (%)** nel portale di Azure indica il valore del giorno corrente. Il valore viene calcolato man mano che arrivano i dati dalle posizioni in cui vengono eseguite le risorse. Azure usa questi dati per calcolare la percentuale di utilizzo.

Per alcune risorse viene segnalato un utilizzo più lento di altre. Inoltre, alcuni tipi di prodotti, come i database SQL, sono lenti a segnalare i dati di utilizzo.

A causa della latenza, il calcolo dell'utilizzo può mostrare valori più bassi di quelli effettivi. La differenza è evidente a fine giornata. In questi casi, se Azure non ottiene i dati di utilizzo da quattro a otto ore, calcola un valore pari a 0%. Il valore 0% viene visualizzato perché i dati di utilizzo non sono arrivati e sembra che la prenotazione non applichi un vantaggio a nessuna risorsa.

Con l'arrivo dei dati di utilizzo, il valore passa alla percentuale corretta. Quando vengono raccolti tutti i dati di utilizzo, viene determinato il valore corretto, che viene mostrato accuratamente nel grafo.

## <a name="solution"></a>Soluzione

Se si ritiene che i valori di utilizzo non corrispondano alle aspettative, esaminare il grafo per ottenere la visualizzazione dell'utilizzo effettivo. Qualsiasi valore di punto antecedente a due giorni dovrebbe essere accurato. Le medie a lungo termine da sette a 30 giorni dovrebbero essere accurate.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione delle prenotazioni, vedere [Gestire le prenotazioni per le risorse di Azure](manage-reserved-vm-instance.md).