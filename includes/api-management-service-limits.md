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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67180550"
---
| Risorsa | Limite |
| --- | --- |
| Numero massimo di unità di scala | 10 per area<sup>1</sup> |
| Dimensioni cache | 5 GB per unità<sup>2</sup> |
| Connessioni back-end simultanee<sup>3</sup> per autorità http | 2\.048 per unità<sup>4</sup> |
| Dimensione massima della risposta memorizzata nella cache | 2 MB |
| Dimensioni massime del documento dei criteri | 256 KB<sup>5</sup> | 
| Numero massimo di domini gateway personalizzati per istanza del servizio<sup>6</sup> | 20 |
| Numero massimo di certificati CA per istanza di servizio | 10 | 
| Numero massimo di istanze del servizio per sottoscrizione<sup>7</sup> | 20 | 
| Numero massimo di sottoscrizioni per ogni istanza del servizio<sup>7</sup> | 500 |
| Numero massimo di certificati client per istanza del servizio<sup>7</sup> | 50 | 
| Numero massimo di API per istanza del servizio<sup>7</sup> | 50 | 
| Numero massimo di operazioni API per istanza del servizio<sup>7</sup> | 1\.000 | 
| Durata massima richiesta totale<sup>7</sup> | 30 secondi | 
| Dimensione massima del payload memorizzato nel buffer<sup>7</sup> | 2 MB | 


<sup>1</sup> I limiti di scalabilità dipendono dal piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di scalabilità, vedere [prezzi di gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Le dimensioni della cache per unità dipendono dal piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di scalabilità, vedere [prezzi di gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Le connessioni vengono raggruppate e riutilizzate a meno che non vengano chiuse in modo esplicito dal back-end.<br/>
<sup>4</sup> Questo limite è per unità dei livelli Basic, standard e Premium. Il livello Developer è limitato a 1.024. Questo limite non si applica al livello di consumo.<br/> 
<sup>5</sup>questo limite si applica ai livelli Basic, standard e Premium. Nel livello di consumo, le dimensioni del documento di criteri sono limitate a 4 KB.<br/>
<sup>6</sup> Questa risorsa è disponibile solo nel livello Premium.<br/>
<sup>7</sup> Questa risorsa si applica solo al livello di consumo.<br/>



