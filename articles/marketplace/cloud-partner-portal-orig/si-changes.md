---
title: Note sulla versione di Seller Insights
description: Contiene informazioni sulle modifiche apportate alla funzione Seller Insights.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808951"
---
# <a name="seller-insights-release-notes"></a>Note sulla versione di Seller Insights 

(Data di rilascio: 1 marzo 2019)

Questo articolo contiene informazioni sulle modifiche apportate alle funzionalità Seller Insights nel [portale Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Novità della versione per il 1 ° marzo 2019

* *Tendenza del cliente* aggiunta al riepilogo
* I *primi cinque clienti* in Riepilogo riflettono tutte le sottoscrizioni di Azure di cui dispone un cliente
* *Tendenza di utilizzo normalizzata & tendenza degli ordini attivi* in Riepilogo spostata in *modo immediato in base a ordini mensili*
* *Report di riconciliazione del pagamento* aggiornato
* I *primi cinque clienti* con pagamento riflettono tutte le sottoscrizioni di Azure di cui dispone un cliente
* *Report sull'utilizzo* aggiornato con ID cliente
* *Possesso dei clienti* sugli ordini & utilizzo riflette tutte le sottoscrizioni di Azure di cui dispone un cliente


Data di rilascio: 28 luglio 2018

## <a name="release-highlights-for-july-28-2018"></a>Novità della versione per il 28 luglio 2018


-   I *prezzi stimati* forniscono una visualizzazione degli addebiti applicati ai clienti con implicazioni a livello di conversione della valuta.
-   I *proventi previsti* forniscono una visualizzazione preventiva dei potenziali proventi.
-  Gli *identificatori dei riferimento d'uso* indicano la fedeltà dei dati tra l'utilizzo da parte dei clienti e gli ordini con i proventi.
-   L'*utilizzo con granularità giornaliera* consente maggiore granularità e informazioni dettagliate più accurate sull'utilizzo da parte dei clienti.


### <a name="changes-to-data-structure-and-taxonomy"></a>Modifiche alla struttura e alla tassonomia dei dati

La tabella seguente elenca le metriche aggiunte o modificate in modo sostanziale in questa versione. 

| **Nuovo termine**                   |    **Definizione**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Price (CC)                     | Prezzo per un'unità di utilizzo per uno SKU specifico (nella valuta del cliente).       |
| Estimated Extended Charge (CC) | Costo estesi stimato per la quantità di unità di utilizzo per un determinato SKU (nella valuta del cliente). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Publisher Currency (PC)        | Valuta preferita dall'editore per i proventi.                               |
| Estimated Price (PC)           | Il prezzo stimato per un'unità di utilizzo per uno SKU specifico basato sulla conversione della data di cambio estera nell'utilizzo della data viene calcolato (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Estimated Extended Charge (PC) | È stato calcolato il costo esteso stimato per la quantità di unità di utilizzo per un determinato SKU in base alla conversione dello scambio di dati in base alla data di utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento. |
| Estimated Payout (PC)          | Pagamento stimato per la quantità di unità di utilizzo per un determinato SKU in base alla conversione dello scambio estero alla data di calcolo dell'utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Usage Reference                | Identificatore per uno o più giorni di utilizzo da parte del cliente per uno SKU specifico associato a una voce nel report dei proventi. |
|  |  |
