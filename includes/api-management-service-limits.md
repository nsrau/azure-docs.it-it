---
title: File di inclusione
description: File di inclusione
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553539"
---
| Risorsa | Limite |
| --- | --- |
| Numero massimo di unità di scala | 10 per area<sup>1</sup> |
| Dimensioni cache | 5 GB per unità<sup>2</sup> |
| Connessioni back-end simultanee<sup>3</sup> per ogni autorità HTTP | 2048 per unità<sup>4</sup> |
| Dimensione massima della risposta memorizzata nella cache | 2 MB |
| Dimensioni massime del documento dei criteri | 256 KB<sup>5</sup> | 
| Numero massimo di domini gateway personalizzati per istanza del servizio<sup>6</sup> | 20 |
| Numero massimo di certificati CA per ogni istanza del servizio | 10 | 
| Numero massimo di istanze del servizio per sottoscrizione<sup>7</sup> | 20 | 
| Numero massimo di sottoscrizioni per ogni istanza del servizio<sup>7</sup> | 500 |
| Numero massimo di certificati client per istanza del servizio<sup>7</sup> | 50 | 
| Numero massimo di API per istanza del servizio<sup>7</sup> | 50 | 
| Numero massimo di operazioni API per istanza del servizio<sup>7</sup> | 1.000 | 
| Durata massima delle richieste totale<sup>7</sup> | 30 secondi | 
| Dimensione massima del payload memorizzato nel buffer<sup>7</sup> | 2 MB | 


<sup>1</sup>i limiti di ridimensionamento variano a seconda del piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di ridimensionamento, vedere [gestione API-prezzi](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>della cache di unità per dimensione varia a seconda del piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di ridimensionamento, vedere [gestione API-prezzi](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>le connessioni sono in pool e riutilizzate, a meno che chiuse esplicitamente dal back-end.<br/>
<sup>4</sup>questo limite è per ogni unità dei livelli Basic, Standard e Premium. Il livello Developer è limitato a 1024. Questo limite non si applica al livello di consumo.<br/> 
<sup>5</sup>questo limite si applica ai livelli Basic, Standard e Premium. Nel livello di consumo, le dimensioni dei documenti dei criteri viene limitata a 4 KB.<br/>
<sup>6</sup>questa risorsa è disponibile solo al livello Premium.<br/>
<sup>7</sup>questa risorsa si applica solo al livello di consumo.<br/>



