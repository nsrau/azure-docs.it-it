---
title: Controllo della concorrenza | Microsoft Docs
description: Strategie di controllo della concorrenza per le API di pubblicazione nel portale Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ecf0bb6ac7fc77e804c9fc8d62aba52810de5640
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625007"
---
<a name="concurrency-control"></a>Controllo della concorrenza
===================

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
