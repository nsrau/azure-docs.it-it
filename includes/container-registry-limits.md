---
title: File di inclusione
description: File di inclusione
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787641"
---
| Resource | Di base | Standard | Premium |
|---|---|---|---|
| Archiviazione<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Dimensioni dell'immagine massimo livello | 200 GiB | 200 GiB | 200 GiB |
| Operazioni di lettura al minuto<sup>2, 3</sup> | 1.000 | 3,000 | 10,000 |
| Operazioni di scrittura al minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| MBps di larghezza di banda per il download<sup>2</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Replica geografica | N/D | N/D | [Supportato][geo-replication] |
| Attendibilità del contenuto | N/D | N/D | [Supportato][content-trust] |

<sup>1</sup>i limiti di archiviazione specificato sono la quantità *inclusi* archiviazione per ogni livello. Viene addebitata una tariffa giornaliera aggiuntiva per GiB di archiviazione di immagini oltre questi limiti. Per informazioni sulle tariffe, vedere [prezzi di registro contenitori di Azure][pricing].

<sup>2</sup>*operazioni di lettura*, *operazioni di scrittura*, e *larghezza di banda* sono stime minime. Registro contenitori di Azure si impegna costantemente migliorare le prestazioni in quanto richiede l'utilizzo.

<sup>3</sup>un' [pull docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in più operazioni di lettura in base al numero di livelli dell'immagine, oltre al recupero del manifesto.

<sup>4</sup>un' [push docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in più operazioni di scrittura, in base al numero di livelli da inserire. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
