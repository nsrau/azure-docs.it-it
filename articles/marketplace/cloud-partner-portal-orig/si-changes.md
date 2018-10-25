---
title: Note sulla versione di Seller Insights | Microsoft Docs
description: Contiene informazioni sulle modifiche apportate alla funzione Seller Insights.
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
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807178"
---
<a name="seller-insights-release-notes"></a>Note sulla versione di Seller Insights 
===============================
Data di rilascio: 28 luglio 2018

Questo articolo contiene informazioni sulle modifiche apportate alle funzionalità Seller Insights nel [portale Cloud Partner](https://cloudpartner.azure.com/#insights).


<a name="release-highlights"></a>Principali caratteristiche della versione
------------------

-   I *prezzi stimati* forniscono una visualizzazione degli addebiti applicati ai clienti con implicazioni a livello di conversione della valuta.
-   I *proventi previsti* forniscono una visualizzazione preventiva dei potenziali proventi.
-  Gli *identificatori dei riferimento d'uso* indicano la fedeltà dei dati tra l'utilizzo da parte dei clienti e gli ordini con i proventi.
-   L'*utilizzo con granularità giornaliera* consente maggiore granularità e informazioni dettagliate più accurate sull'utilizzo da parte dei clienti.


<a name="changes-to-data-structure-and-taxonomy"></a>Modifiche alla struttura e alla tassonomia dei dati
--------------------------------------

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
