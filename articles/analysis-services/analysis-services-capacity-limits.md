---
title: Limiti di risorse e oggetti di Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive i limiti di risorse e oggetti per un server di Azure Analysis Services.This article describes resource and object limits for an Azure Analysis Services server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573205"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limiti di risorse e oggetti di Azure Analysis Services

In questo articolo vengono descritti i limiti di risorse e oggetti del modello.

## <a name="tier-limits"></a>Limiti di livello

Per i limiti di QPU e di memoria per i livelli di sviluppo, base e standard, vedere la [pagina dei prezzi di Azure Analysis Services.](https://azure.microsoft.com/pricing/details/analysis-services/)

## <a name="object-limits"></a>Limiti di oggetto

Questi limiti sono teorici. Le prestazioni risulteranno inferiori con numeri più bassi.

|Oggetto|Quantità/dimensioni massime|  
|------------|----------------------------|  
|Database in un'istanza|16.000|  
|Numero combinato di tabelle e colonne in un database|16.000|  
|Righe in una tabella|Nessuna limitazione<br /><br /> **Avviso:** con la restrizione che nessuna colonna singola nella tabella può avere più di 1.999.999.997 valori distinti.|  
|Gerarchie in una tabella|15.999|  
|Livelli in una gerarchia|15.999|  
|Relazioni|8.000|  
|Colonne chiave in tutte le tabelle|15.999|  
|Misure nelle tabelle|2^31-1 = 2.147.483.647|  
|Celle restituite da una query|2^31-1 = 2.147.483.647|  
|Dimensioni dei record della query di origine|64 K|  
|Lunghezza dei nomi degli oggetti|512 caratteri|  


