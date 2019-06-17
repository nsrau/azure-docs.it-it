---
title: Note sulla versione di Seller Insights
description: Contiene informazioni sulle modifiche apportate alla funzione Seller Insights.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: c6e9e4fe672c7e171ed4b1cd60655f9e71a562e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943118"
---
# <a name="seller-insights-release-notes"></a>Note sulla versione di Seller Insights 

(Data di rilascio: Il 1 marzo 2019)

Questo articolo contiene informazioni sulle modifiche apportate alle funzionalità Seller Insights nel [portale Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Elementi salienti della versione per il 1 marzo 2019

* *Tendenza dei clienti* aggiunti al riepilogo
* *Primi cinque clienti* riepilogo riflettono tutte le sottoscrizioni di Azure dispone di un cliente
* *Normalizzata delle tendenze di utilizzo e tendenza ordini attivi* spostato di sotto di riepilogo *gli ordini mensili in modo immediato*
* *Report di riconciliazione dei proventi* aggiornato
* *Primi cinque clienti* sui proventi riflettono tutte le sottoscrizioni di Azure dispone di un cliente
* *Report utilizzo* aggiornato con ID cliente
* *Permanenza dei clienti* riflette tutte le sottoscrizioni di Azure dispone di un cliente su ordini e utilizzo


(Data di rilascio: 28 luglio 2018)

## <a name="release-highlights-for-july-28-2018"></a>Elementi salienti della versione per 28 luglio 2018


-   I *prezzi stimati* forniscono una visualizzazione degli addebiti applicati ai clienti con implicazioni a livello di conversione della valuta.
-   I *proventi previsti* forniscono una visualizzazione preventiva dei potenziali proventi.
-  Gli *identificatori dei riferimento d'uso* indicano la fedeltà dei dati tra l'utilizzo da parte dei clienti e gli ordini con i proventi.
-   L'*utilizzo con granularità giornaliera* consente maggiore granularità e informazioni dettagliate più accurate sull'utilizzo da parte dei clienti.


### <a name="changes-to-data-structure-and-taxonomy"></a>Modifiche alla struttura e alla tassonomia dei dati

La tabella seguente elenca le metriche aggiunte o modificate in modo sostanziale in questa versione. 

| **Nuovo termine**                   |    **Definizione**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Price (CC)                     | Prezzo per un'unità di utilizzo per uno SKU specifico (nella valuta del cliente).       |
| Estimated Extended Charge (CC) | Addebito esteso stimato per la quantità di unità di utilizzo per uno SKU specifico (nella valuta del cliente). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Publisher Currency (PC)        | Valuta preferita dall'editore per i proventi.                               |
| Estimated Price (PC)           | Prezzo stimato per un'unità di utilizzo per uno SKU specifico basato sulla conversione di valuta estera alla data in cui viene calcolato l'utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Estimated Extended Charge (PC) | Addebito esteso stimato per la quantità di unità di utilizzo per uno SKU specifico basato sulla conversione di valuta estera alla data in cui viene calcolato l'utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento. |
| Estimated Payout (PC)          | Pagamento stimato per la quantità di unità di utilizzo per uno SKU specifico basato sulla conversione di valuta estera alla data in cui viene calcolato l'utilizzo (nella valuta dell'editore). Questo valore potrebbe non essere esatto a causa di errori di troncamento o arrotondamento.   |
| Usage Reference                | Identificatore per uno o più giorni di utilizzo da parte del cliente per uno SKU specifico associato a una voce nel report dei proventi. |
|  |  |
