---
title: File di inclusione
description: File di inclusione
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392334"
---
| Gruppi | Basic | Standard | Premium |
|---|---|---|---|
| Archiviazione<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Dimensioni massime livello immagine | GiB 200 | GiB 200 | GiB 200 |
| Operazioni di lettura al minuto<sup>2, 3</sup> | 1\.000 | 3\.000 | 10,000 |
| Operazioni di scrittura al minuto<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| MBps di larghezza di banda per il download<sup>2</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| Replica geografica | N/D | N/D | [Supportato][geo-replication] |
| Attendibilità del contenuto | N/D | N/D | [Supportato][content-trust] |
| Accesso alla rete virtuale | N/D | N/D | [Anteprima][vnet] |
| Autorizzazioni con ambito repository | N/D | N/D | [Anteprima][token]|
| Token &bull; | N/D | N/D | 20.000 |
| Mappe ambito &bull; | N/D | N/D | 20.000 |
| &bull; repository per ambito mappa | N/D | N/D | 500 |


<sup>1</sup> I limiti di archiviazione specificati sono la quantità di spazio di archiviazione *incluso* per ogni livello. Viene addebitata una tariffa giornaliera aggiuntiva per GiB di archiviazione di immagini oltre questi limiti. Per informazioni sulla frequenza, vedere [prezzi di Azure container Registry][pricing].

<sup>2</sup> I dati delle *operazioni di lettura*, *operazioni di scrittura* e *larghezza di banda* sono stime minime. Azure Container Registry si impegna a migliorare le prestazioni in quanto richiede l'utilizzo.

<sup>3</sup> Una [Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in più operazioni di lettura in base al numero di livelli nell'immagine, più il recupero del manifesto.

<sup>4</sup> Un [push Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in più operazioni di scrittura, in base al numero di livelli di cui è necessario eseguire il push. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md