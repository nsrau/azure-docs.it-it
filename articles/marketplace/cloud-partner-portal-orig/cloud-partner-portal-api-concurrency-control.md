---
title: Controllo della concorrenza Azure Marketplace
description: Strategie di controllo della concorrenza per le API di pubblicazione nel portale Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d0f035d77e74f157b793b9edf3ab5d3494096d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288649"
---
# <a name="concurrency-control"></a>Controllo della concorrenza

Ogni chiamata alle API di pubblicazione nel portale Cloud Partner deve specificare in modo esplicito la strategia di controllo della concorrenza da usare. Se non viene specificata l'intestazione **If-Match** viene generata una risposta di errore HTTP 400. Sono disponibili due strategie per il controllo della concorrenza.

-   **Ottimistica**: il client che esegue l'aggiornamento verifica se i dati sono cambiati dall'ultima operazione di lettura.
-   **Priorità ultimo**: il client aggiorna direttamente i dati, indipendentemente dal fatto che siano stati modificati da un'altra applicazione dal momento dell'ultima lettura.

<a name="optimistic-concurrency-workflow"></a>Flusso di lavoro della concorrenza ottimistica
-------------------------------

È consigliabile usare la strategia di concorrenza ottimistica, con il flusso di lavoro seguente, per garantire che non venga apportata alle risorse nessuna modifica imprevista.

1.  Recuperare un'entità mediante le API. La risposta include un valore ETag che identifica la versione attualmente memorizzata dell'entità (al momento della risposta).
2.  Al momento dell'aggiornamento, includere questo stesso valore ETag nell'intestazione della richiesta **If-Match** obbligatoria.
3.  L'API confronta il valore ETag ricevuto nella richiesta con il valore ETag corrente dell'entità in una transazione atomica.
    *   Se i valori ETag sono diversi, l'API restituisce una risposta HTTP `412 Precondition Failed`. Questo errore indica che un altro processo ha aggiornato l'entità dall'ultimo recupero eseguito dal client, oppure che il valore ETag specificato nella richiesta non è corretto.
    *  Se i valori ETag sono uguali, o se l'intestazione **If-Match** contiene il carattere jolly asterisco (`*`), l'API esegue l'operazione richiesta. L'operazione API aggiorna anche il valore ETag archiviato dell'entità.


> [!NOTE]
> Specificando il carattere jolly (*) nell'intestazione **If-Match**, l'API usa la strategia di concorrenza "priorità ultimo". In questo caso, il confronto ETag non viene eseguito e la risorsa viene aggiornata senza alcun controllo. 
