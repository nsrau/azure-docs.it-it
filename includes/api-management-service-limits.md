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
ms.openlocfilehash: 646ebc79e3615b289b60e47c0a38595e7892d8cc
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084886"
---
| Risorsa | Limite |
| --- | --- |
| Numero massimo di unità di scala | 10 per area<sup>1</sup> |
| Dimensioni cache | 5 GB per unità<sup>2</sup> |
| Connessioni back-end simultanee<sup>3</sup> per ogni autorità HTTP | 2048 per unità<sup>4</sup> |
| Dimensione massima della risposta memorizzata nella cache | 2 MB |
| Dimensioni massime del documento dei criteri | 256 KB<sup>5</sup> | 
| Numero massimo di domini gateway personalizzati per istanza del servizio<sup>6</sup> | 20 | 
| Numero massimo di istanze del servizio per sottoscrizione<sup>7</sup> | 20 | 
| Numero massimo di sottoscrizioni per ogni istanza del servizio<sup>7</sup> | 500 |
| Numero massimo di certificati client per istanza del servizio<sup>7</sup> | 50 | 
| Numero massimo di API per istanza del servizio<sup>7</sup> | 50 | 
| Numero massimo di operazioni API per istanza del servizio<sup>7</sup> | 1000 | 
| Durata totale massima delle richieste<sup>7</sup> | 30 secondi | 
| Dimensione massima del payload memorizzato nel buffer<sup>7</sup> | 2 MB | 


<sup>1</sup> I limiti del ridimensionamento dipendono dal piano tariffario. Per visualizzare i piani tariffari con i relativi limiti di ridimensionamento, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> La dimensione della cache per unità varia a seconda del piano tariffario. Per visualizzare i piani tariffari con i relativi limiti di ridimensionamento, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Le connessioni vengono raggruppate in pool e riutilizzate, a meno che non vengano chiuse esplicitamente dal back-end.<br/>
<sup>4</sup> Per unità dei livelli Basic, Standard e Premium. Il livello Developer è limitato a 1024. Non si applica al livello A consumo.<br/> 
<sup>5</sup> Nei livelli Basic, Standard e Premium. Nei criteri per il livello A consumo le dimensioni dei documenti sono limitate a 4 KB.<br/>
<sup>6</sup> Disponibile solo nel livello Premium.<br/>
<sup>7</sup> Si applica solo al livello A consumo.<br/>



