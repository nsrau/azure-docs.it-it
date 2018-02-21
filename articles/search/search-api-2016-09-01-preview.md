---
title: API REST per il servizio Ricerca di Azure versione 2016-09-01-Preview | Microsoft Docs
description: "L'API REST per il servizio Ricerca di Azure versione 2016-09-01-Preview include funzionalità sperimentali, ad esempio la ricerca di sinonimi e le ricerche moreLikeThis."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API REST per il servizio Ricerca di Azure: versione 2016-09-01-Preview
Questo articolo è la documentazione di riferimento per `api-version=2016-09-01-Preview`. Questa versione di anteprima estende l'attuale versione disponibile a livello generale, [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), includendo le funzionalità sperimentali seguenti:

* [API di sinonimi](search-synonyms.md) per caricare le mappe di sinonimi ed espandere la ricerca.
* [`moreLikeThis`Parametro di query ](search-more-like-this.md) per trovare documenti rilevanti per un documento specifico.

Verificare di avere come destinazione la versione API di anteprima `api-version=2016-09-01-Preview` per provare le funzionalità sperimentali. L'esempio seguente illustra come la versione API di anteprima sia specificata per eseguire una query more-like-this.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti e sono soggette a modifiche. **È consigliabile evitare l'utilizzo delle API di anteprima in applicazioni di produzione.**

Ricerca di Azure è disponibile in più versioni. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx) .
