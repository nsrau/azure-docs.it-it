---
title: File di inclusione
description: File di inclusione
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 5608683578a796ae0fffff7544a749043e8698fd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656195"
---
| Risorsa | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Numero massimo di unità di scala | 10 per area<sup>1</sup> |
| Dimensioni cache | 5 GiB per unità<sup>2</sup> |
| Connessioni back-end simultanee<sup>3</sup> per autorità HTTP | 2.048 per unità<sup>4</sup> |
| Dimensione massima della risposta memorizzata nella cache | 2 MiB |
| Dimensioni massime del documento dei criteri | 256 KiB<sup>5</sup> |
| Numero massimo di domini gateway personalizzati per istanza del servizio<sup>6</sup> | 20 |
| Numero massimo di certificati CA per istanza del servizio | 10 |
| Numero massimo di istanze del servizio per sottoscrizione<sup>7</sup> | 20 |
| Numero massimo di sottoscrizioni per ogni istanza del servizio<sup>7</sup> | 500 |
| Numero massimo di certificati client per istanza del servizio<sup>7</sup> | 50 |
| Numero massimo di API per istanza del servizio<sup>7</sup> | 50 |
| Numero massimo di operazioni API per istanza del servizio<sup>7</sup> | 1\.000 |
| Durata totale totale massima delle richieste<sup>7</sup> | 30 secondi |
| Dimensione massima del payload memorizzato nel buffer<sup>7</sup> | 2 MiB |
| Dimensione massima URL richiesta<sup>8</sup> | 4096 byte |

<sup>1 : il</sup> nome del I limiti di scalabilità dipendono dal piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di scalabilità, vedere [Prezzi di Gestione API.](https://azure.microsoft.com/pricing/details/api-management/)<br/>
<sup>2 Il</sup> nome del sistema Le dimensioni della cache per unità dipendono dal piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di scalabilità, vedere [Prezzi di Gestione API.](https://azure.microsoft.com/pricing/details/api-management/)<br/>
<sup>3 (COM del</sup> nome Le connessioni vengono raggruppate in pool e riutilizzate a meno che non vengano chiuse in modo esplicito dal back-end.<br/>
<sup>4 DEL psu'</sup> Questo limite è per unità dei livelli Basic, Standard e Premium. Il livello Sviluppatore è limitato a 1.024.The Developer tier is limited to 1,024. Questo limite non si applica al livello Consumo.<br/>
5 Del numero <sup>3(</sup> Questo limite si applica ai livelli Basic, Standard e Premium.This limit applies to the Basic, Standard, and Premium tiers. Nel livello Consumo, le dimensioni del documento dei criteri sono limitate a 16 KiB.<br/>
6 È <sup>possibile:</sup> Questa risorsa è disponibile solo nel livello Premium.This resource is available in the Premium tier only.<br/>
<sup>7 (in</sup> questo stato Questa risorsa si applica solo al livello Consumo.<br/>
8 (IN <sup>vio</sup> Si applica solo al livello Consumo. Include una stringa di query lunga fino a 2048 byte.<br/>
