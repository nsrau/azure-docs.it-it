---
title: Limiti di risorse e oggetti di Azure Analysis Services | Microsoft Docs
description: Descrive i limiti di risorse e oggetti di Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0ffbffc788baaffd4a0532c3918ed82cc3eaf5c3
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527813"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limiti di risorse e oggetti di Azure Analysis Services

In questo articolo vengono descritti i limiti di risorse e oggetti del modello.

## <a name="tier-limits"></a>Limiti di livello

### <a name="developer-tier"></a>Livello Developer

Questo livello è consigliato per gli scenari di valutazione, sviluppo e test. Un singolo piano include le stesse funzionalità del livello Standard, ma con limitazioni per potenza di elaborazione, QPU e dimensioni della memoria. Lo scale-out delle repliche delle query non è disponibile per questo livello. Questo livello non offre alcun Contratto di servizio.

|Pianificazione  |QPU  |Memoria (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Livello Basic

Il livello è consigliato per soluzioni di produzione con modelli tabulari di dimensioni più ridotte, concorrenza limitata tra utenti e requisiti semplici per l'aggiornamento dei dati. Lo scale-out delle repliche delle query *non è disponibile* per questo livello. Prospettive, partizioni multiple e funzionalità del modello tabulare DirectQuery non sono supportate in questo livello.  

|Pianificazione  |QPU  |Memoria (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Livello Standard

Questo livello è ottimale per applicazioni di produzione cruciali, che necessitano di concorrenza elastica tra utenti e hanno modelli di dati in rapida crescita. Supporta l'aggiornamento di dati avanzato per aggiornamenti del modello di dati in tempo quasi reale e supporta tutte le funzionalità di modellazione tabulare.

|Pianificazione  |QPU  |Memoria (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Non disponibile in tutte le aree.  

## <a name="object-limits"></a>Limiti di oggetto

Questi limiti sono teorici. Le prestazioni risulteranno inferiori con numeri più bassi.

|Oggetto|Dimensioni/numeri massimi|  
|------------|----------------------------|  
|Database in un'istanza|16.000|  
|Numero combinato di tabelle e colonne in un database|16.000|  
|Righe in una tabella|Illimitato<br /><br /> **Avviso:** Con la restrizione che nessuna colonna singola nella tabella può avere più di 1.999.999.997 valori distinti.|  
|Gerarchie in una tabella|15.999|  
|Livelli in una gerarchia|15.999|  
|Relazioni|8.000|  
|Colonne chiave in tutte le tabelle|15.999|  
|Misure nelle tabelle|2^31-1 = 2.147.483.647|  
|Celle restituite da una query|2^31-1 = 2.147.483.647|  
|Dimensioni dei record della query di origine|64 K|  
|Lunghezza dei nomi degli oggetti|512 caratteri|  


