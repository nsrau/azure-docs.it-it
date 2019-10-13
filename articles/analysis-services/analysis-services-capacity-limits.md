---
title: Limiti di risorse e oggetti di Azure Analysis Services | Microsoft Docs
description: Descrive i limiti di risorse e oggetti di Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fe43602c66af72357e16822ee9d4b5a741d3f86
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298694"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limiti di risorse e oggetti di Azure Analysis Services

In questo articolo vengono descritti i limiti di risorse e oggetti del modello.

## <a name="tier-limits"></a>Limiti di livello

Per i limiti di QPU e di memoria per i livelli Developer, Basic e standard, vedere la pagina relativa ai [prezzi di Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limiti di oggetto

Questi limiti sono teorici. Le prestazioni risulteranno inferiori con numeri più bassi.

|Object|Dimensioni/numeri massimi|  
|------------|----------------------------|  
|Database in un'istanza|16,000|  
|Numero combinato di tabelle e colonne in un database|16,000|  
|Righe in una tabella|Senza limitazioni<br /><br /> **Avviso:** Con la restrizione che nessuna colonna singola nella tabella può avere più di 1.999.999.997 valori distinti.|  
|Gerarchie in una tabella|15.999|  
|Livelli in una gerarchia|15,999|  
|Relazioni|8\.000|  
|Colonne chiave in tutte le tabelle|15,999|  
|Misure nelle tabelle|2^31-1 = 2,147,483,647|  
|Celle restituite da una query|2^31-1 = 2.147.483.647|  
|Dimensioni dei record della query di origine|64 K|  
|Lunghezza dei nomi degli oggetti|512 caratteri|  


