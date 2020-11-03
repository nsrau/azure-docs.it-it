---
title: Risolvere i problemi relativi ai tipi di prenotazione di Azure non disponibili
description: Questo articolo illustra i motivi per cui le istanze riservate possono risultare non disponibili nel portale di Azure e spiega come risolvere questo problema.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798203"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Risolvere i problemi relativi ai tipi di prenotazioni non disponibili

Questo articolo illustra i motivi per cui le istanze riservate possono risultare non disponibili nel portale di Azure e spiega come risolvere questo problema.

## <a name="symptoms"></a>Sintomi

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare a **Prenotazioni**.
2. Selezionare **+ Aggiungi** e quindi selezionare un prodotto.
3. Selezionare la scheda **Tutti i prodotti**.
4. Selezionare un prodotto dall'elenco. Potrebbe essere visualizzato uno dei messaggi seguenti:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Esempio di messaggio che informa che il prodotto non è disponibile per la sottoscrizione o l'area selezionata" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Esempio di messaggio che informa che la quota dei core non è sufficiente" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Causa

Alcune prenotazioni non sono disponibili per l'acquisto perché:

- Non tutti i prodotti di istanze riservate sono disponibili per l'acquisto in ogni area
- Alla sottoscrizione è applicata una restrizione sulla quota

### <a name="cause-1"></a>Causa 1

Non tutti i prodotti di istanze riservate sono disponibili per l'acquisto. Azure decide se consentire o meno alcuni prodotti in base ai costi associati all'applicazione dello sconto monetario ai clienti. In altri casi, Azure non offre alcuni prodotti a causa delle condizioni di capacità in specifici data center. Inoltre, alcuni gruppi di sviluppo di prodotti di Azure potrebbero consentire un determinato prodotto perché ne vogliono ritirare uno meno recente.

In altri casi, Azure applica restrizioni di capacità a vari prodotti in base alla richiesta di tali risorse in alcune aree. Ad esempio, una restrizione di questo tipo potrebbe essere applicata quando la richiesta di una determinata dimensione di macchina virtuale supera la capacità di un data center. In questo esempio, Azure non è in grado di garantire la capacità ai clienti che hanno acquistato una prenotazione per tale dimensione in tale area. Infine, esistono alcuni prodotti a se stanti per vari motivi. Questi prodotti vengono resi disponibili solo a un piccolo gruppo di clienti selezionato.

Ma perché le prenotazioni non disponibili per l'acquisto vengono ugualmente visualizzate nel portale di Azure? Il motivo è che gli utenti creano richieste di supporto in cui segnalano di non riuscire a trovare i prodotti che vogliono. Il team di sviluppo delle prenotazioni di Azure ha stabilito che mostrare tutti i prodotti con il messaggio `Product unavailable` comporta un minor numero di richieste di supporto rispetto al non mostrarli.

### <a name="cause-2"></a>Causa 2

Alla sottoscrizione è applicata una restrizione sulla quota. Le sottoscrizioni hanno un limite al numero di core CPU che possono utilizzare. Il limite si applica ad alcuni prodotti di istanze riservate, in particolare alle macchine virtuali. Azure vuole garantire alle persone che acquistano un'istanza riservata che possano usarla. A questo scopo Azure esegue un controllo sommario nel portale di Azure per verificare che la sottoscrizione dell'utente abbia abbastanza core disponibili per distribuire la VM e ottenere un vantaggio dall'acquisto della prenotazione.

Il controllo eseguito per consentire all'utente di aggiungere un particolare prodotto al carrello e acquistare una prenotazione è semplice. Azure valuta il numero totale di core CPU disponibili per la sottoscrizione dell'utente e controlla se il numero è maggiore del numero di core per l'elemento selezionato.

Azure non controlla la quota per le istanze riservate con ambito **Condiviso**. Il vantaggio dell'istanza riservata per l'ambito condiviso si applica a tutte le sottoscrizioni nella registrazione. Azure non è in grado di determinare se l'utente ha un numero sufficiente di core disponibili in tutte le sottoscrizioni per distribuire la risorsa. Indipendentemente dalla quota, Azure consente sempre di selezionare una dimensione di VM quando l'ambito selezionato è Condiviso.

Inoltre, Azure non esegue un controllo della quota per gli acquisti **Consigliati**. Le raccomandazioni sono basate sull'utilizzo attivo. Azure presuppone che l'utente disponga di core sufficienti per eseguire una specifica dimensione di VM perché ha già generato l'utilizzo necessario per creare la raccomandazione.

## <a name="solution"></a>Soluzione

A seconda del messaggio di errore ricevuto, usare una delle soluzioni seguenti per risolvere il problema.

### <a name="solution-1"></a>Soluzione 1

Se viene visualizzato un messaggio di _prodotto non disponibile_ , selezionare il collegamento **Contatta il supporto tecnico** nel messaggio di errore per richiedere l'aggiunta di un'eccezione per la sottoscrizione. Le eccezioni non vengono sempre concesse.

### <a name="solution-2"></a>Soluzione 2

Se viene visualizzato un messaggio di _quota di core insufficiente_ , è possibile cambiare l'ambito in **Condiviso**. Dopo avere acquistato la prenotazione, è possibile cambiare l'ambito della prenotazione da **Condiviso** a **Sottoscrizione singola**.

Oppure selezionare il collegamento **Richiedi aumento di quota** nel messaggio di errore per richiedere un aumento della quota di core CPU per la sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle opzioni relative all'ambito della prenotazione, vedere [Definire l'ambito delle prenotazioni](prepare-buy-reservation.md#scope-reservations).