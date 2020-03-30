---
title: Note sulla versione di Insights del venditore
description: Contiene informazioni sulle modifiche apportate alla funzione Seller Insights.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285402"
---
# <a name="seller-insights-release-notes"></a>Note sulla versione di Seller Insights 

(Data di rilascio: 1 marzo 2019)

Questo articolo contiene informazioni sulle modifiche apportate alle funzionalità Seller Insights nel [portale Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Comunicati in vendita per il 1 marzo 2019

* *Tendenza cliente* aggiunta al riepilogo
* *I primi cinque clienti* in Riepilogo riflettono tutte le sottoscrizioni di Azure di cui dispone un cliente
* *Tendenza di utilizzo normalizzato & tendenza degli ordini attivi* sul riepilogo spostato in Ordini *mensili a colpo d'occhio*
* *Report riconciliazione pagamenti* aggiornato
* *I primi cinque clienti* sui proventi riflettono tutte le sottoscrizioni di Azure di cui dispone un cliente
* *Report di utilizzo* aggiornato con ID cliente
* *Customer Tenure* on Orders & Usage riflette tutte le sottoscrizioni di Azure di cui dispone un cliente


Data di rilascio: 28 luglio 2018

## <a name="release-highlights-for-july-28-2018"></a>Punti salienti della versione 28 luglio 2018Release highlights for July 28, 2018


-   I *prezzi stimati* forniscono una visualizzazione degli addebiti applicati ai clienti con implicazioni a livello di conversione della valuta.
-   I *proventi previsti* forniscono una visualizzazione preventiva dei potenziali proventi.
-  Gli *identificatori dei riferimento d'uso* indicano la fedeltà dei dati tra l'utilizzo da parte dei clienti e gli ordini con i proventi.
-   L'*utilizzo con granularità giornaliera* consente maggiore granularità e informazioni dettagliate più accurate sull'utilizzo da parte dei clienti.


### <a name="changes-to-data-structure-and-taxonomy"></a>Modifiche alla struttura e alla tassonomia dei dati

La tabella seguente elenca le metriche aggiunte o modificate in modo sostanziale in questa versione. 

| **Nuovo termine**                   |    **Definizione**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Price (CC)                     | Prezzo per un'unità di utilizzo per uno SKU specifico (nella valuta del cliente).       |
| Estimated Extended Charge (CC) | Addebito esteso stimato per la quantità di unità di utilizzo per un determinato SKU (nella valuta del cliente). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Publisher Currency (PC)        | Valuta preferita dall'editore per i proventi.                               |
| Estimated Price (PC)           | Prezzo stimato per un'unità di utilizzo per un determinato SKU in base alla conversione in valuta estera alla data di utilizzo viene calcolato (nella valuta del publisher). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Estimated Extended Charge (PC) | Viene calcolata l'addebito esteso stimato per la quantità di unità di utilizzo per un determinato SKU in base alla conversione in valuta estera alla data di utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento. |
| Estimated Payout (PC)          | Pagamento stimato per la quantità di unità di utilizzo per un determinato SKU in base alla conversione in valuta estera alla data in cui viene calcolato l'utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Usage Reference                | Identificatore per uno o più giorni di utilizzo da parte del cliente per uno SKU specifico associato a una voce nel report dei proventi. |
|  |  |
