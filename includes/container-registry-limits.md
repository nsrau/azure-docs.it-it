---
title: File di inclusione
description: File di inclusione
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
| Risorsa | Basic | Standard | Premium |
|---|---|---|---|---|
| Archiviazione | 10 GiB | 100 GiB| 500 GiB |
| Operazioni di lettura al minuto<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| Operazioni di scrittura al minuto<sup>1, 3</sup> | 100 | 500 | 2000 |
| MBps di larghezza di banda per il download<sup>1</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| Replica geografica | N/D | N/D | [Supportata *(anteprima)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> I dati delle *operazioni di lettura*, *operazioni di scrittura* e *larghezza di banda* sono stime minime. Registro contenitori di Azure migliora le prestazioni in base alle necessità d'uso.

<sup>2</sup> Il [Pull Docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in operazioni di lettura multiple in base al numero di livelli dell'immagine e al recupero del manifesto.

<sup>3</sup> Il [push di Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in operazioni di scrittura multiple, in base al numero di livelli da inserire. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.