---
title: File di inclusione
description: File di inclusione
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: fe1227d91c0d039a94e5b9a3046b879f4f17355a
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246947"
---
| Risorsa | Basic | Standard | Premium |
|---|---|---|---|---|
| Archiviazione<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Dimensioni massime livello immagini | 20 GiB | 20 GiB | 50 GiB |
| Operazioni di lettura al minuto<sup>2, 3</sup> | 1.000 | 3,000 | 10,000 |
| Operazioni di scrittura al minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| MBps di larghezza di banda per il download<sup>2</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| Replica geografica | N/D | N/D | [Supportato][geo-replication] |
| Attendibilità dei contenuti (anteprima) | N/D | N/D | [Supportato][content-trust] |

<sup>1</sup> I limiti di archiviazione specificati sono la quantità di archiviazione *inclusa* per ogni livello. Viene addebitata una tariffa giornaliera aggiuntiva per GiB di archiviazione di immagini oltre questi limiti. Per informazioni sui costi, vedere [Prezzi di Registro Azure Container][pricing].

<sup>2</sup> I dati delle *operazioni di lettura*, *operazioni di scrittura* e *larghezza di banda* sono stime minime. Registro Azure Container migliora le prestazioni in base alle necessità d'uso.

<sup>3</sup> Il [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in operazioni di lettura multiple in base al numero di livelli dell'immagine e al recupero del manifesto.

<sup>4</sup> Il [push di Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in operazioni di scrittura multiple, in base al numero di livelli da inserire. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
