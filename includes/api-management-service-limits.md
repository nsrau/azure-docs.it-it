---
title: includere il file
description: File di inclusione
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204461"
---
| Risorsa | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Numero massimo di unità di scala | 10 per area<sup>1</sup> |
| Dimensioni cache | 5 GiB per unità<sup>2</sup> |
| Connessioni back-end simultanee<sup>3</sup> per autorità http | 2.048 per unità<sup>4</sup> |
| Dimensione massima della risposta memorizzata nella cache | 2 MiB |
| Dimensioni massime del documento dei criteri | 256 KiB<sup>5</sup> |
| Numero massimo di domini gateway personalizzati per istanza del servizio<sup>6</sup> | 20 |
| Numero massimo di certificati CA per istanza di servizio<sup>7</sup> | 10 |
| Numero massimo di istanze del servizio per sottoscrizione<sup>8</sup> | 20 |
| Numero massimo di sottoscrizioni per ogni istanza di servizio<sup>8</sup> | 500 |
| Numero massimo di certificati client per ogni istanza di servizio<sup>8</sup> | 50 |
| Numero massimo di API per istanza di servizio<sup>8</sup> | 50 |
| Numero massimo di operazioni API per istanza di servizio<sup>8</sup> | 1\.000 |
| Durata massima richiesta totale<sup>8</sup> | 30 secondi |
| Dimensioni massime del payload memorizzato nel buffer<sup>8</sup> | 2 MiB |
| Dimensioni massime URL richiesta<sup>9</sup> | 4096 byte |
| Numero massimo di gateway self-hosted<sup>10</sup> | 25 |

<sup>1</sup> I limiti di scalabilità dipendono dal piano tariffario. Per informazioni dettagliate sui piani tariffari e sui relativi limiti di scalabilità, vedere [prezzi di gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Le dimensioni della cache per unità dipendono dal piano tariffario. Per visualizzare i piani tariffari e i relativi limiti di scalabilità, vedere [prezzi di gestione API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Le connessioni vengono raggruppate e riutilizzate a meno che non vengano chiuse in modo esplicito dal back-end.<br/>
<sup>4</sup> Questo limite è per unità dei livelli Basic, standard e Premium. Il livello Developer è limitato a 1.024. Questo limite non si applica al livello di consumo.<br/>
<sup>5</sup> Questo limite si applica ai livelli Basic, standard e Premium. Nel livello di consumo, le dimensioni del documento di criteri sono limitate a 4 KiB.<br/>
<sup>6</sup> Più domini personalizzati sono supportati solo nei livelli Developer e Premium.<br/>
<sup>7</sup> I certificati della CA non sono supportati nel livello a consumo.<br/>
<sup>8</sup> Questa risorsa si applica solo al livello di consumo.<br/>
<sup>9</sup> Si applica solo al livello di consumo. Include una stringa di query di lunghezza fino a 2048 byte.<br/>
<sup>10</sup> I gateway self-hosted sono supportati solo nei livelli Developer e Premium. Il limite si applica al numero di [risorse del gateway self-hosted](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway). Per aumentare questo limite, contattare il [supporto tecnico](https://azure.microsoft.com/support/options/). Si noti che il numero di nodi (o repliche) associati a una risorsa del gateway self-hosted è illimitato nel livello Premium e limitato a un singolo nodo nel livello Developer.
