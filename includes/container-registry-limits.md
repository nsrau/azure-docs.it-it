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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67180511"
---
| Risorsa | Basic | Standard | Premium |
|---|---|---|---|
| Archiviazione<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Dimensioni massime livello immagine | GiB 200 | GiB 200 | GiB 200 |
| Operazioni di lettura al minuto<sup>2, 3</sup> | 1\.000 | 3,000 | 10,000 |
| Operazioni di scrittura al minuto<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| MBps di larghezza di banda per il download<sup>2</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>2</sup> | 10 | 20 | 50 |
| webhooks | 2 | 10 | 100 |
| Replica geografica | N/D | N/D | [Supportato][geo-replication] |
| Attendibilità del contenuto | N/D | N/D | [Supportato][content-trust] |

<sup>1</sup> I limiti di archiviazione specificati sono la quantità di spazio di archiviazione *incluso* per ogni livello. Viene addebitata una tariffa giornaliera aggiuntiva per GiB di archiviazione di immagini oltre questi limiti. Per informazioni sulla frequenza, vedere [prezzi di Azure container Registry][pricing].

<sup>2</sup> *Operazioni lettura*, *operazioni scrittura*e *Bandwidth* sono stime minime. Azure Container Registry si impegna a migliorare le prestazioni in quanto richiede l'utilizzo.

<sup>3</sup> Una [Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in più operazioni di lettura in base al numero di livelli nell'immagine, più il recupero del manifesto.

<sup>4</sup> Un [push Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in più operazioni di scrittura, in base al numero di livelli di cui è necessario eseguire il push. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
