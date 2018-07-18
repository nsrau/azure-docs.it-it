---
title: Archiviazione immagini nel registro contenitori di Azure
description: Contiene informazioni dettagliate sulla modalità di archiviazione delle immagini del contenitore Docker nel registro contenitori di Azure, tra cui sicurezza, ridondanza e capacità.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165096"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Archiviazione immagini del contenitore nel registro contenitori di Azure

Ogni registro contenitori di Azure [Basic, Standard e Premium](container-registry-skus.md) beneficia delle funzionalità di archiviazione avanzate di Azure, come ad esempio la crittografia dei dati inattivi per la sicurezza dei dati di immagine e la ridondanza geografica per la protezione dei dati di immagine. Le sezioni seguenti descrivono sia le funzionalità che i limiti di archiviazione immagini nel Registro contenitori di Azure (ACR).

## <a name="encryption-at-rest"></a>Crittografia dei dati inattivi

Tutte le immagini del contenitore presenti nel registro vengono crittografate a riposo. Azure esegue automaticamente la crittografia di un'immagine prima di archiviarla e la decrittografa in tempo reale quando l'utente, un'applicazione o un servizio eseguono il pull dell'immagine.

## <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

Azure usa uno schema di archiviazione con ridondanza geografica per evitare la perdita delle immagini del contenitore. Il Registro contenitori di Azure replica automaticamente le immagini del contenitore in più data center geograficamente distanti, impedendone la perdita in caso di guasto di un sistema di archiviazione regionale.

## <a name="geo-replication"></a>Replica geografica

Per gli scenari che richiedono una disponibilità ancora più elevata, è consigliabile usare la funzione di [replica geografica](container-registry-geo-replication.md) dei registri Premium. La replica geografica scongiura la perdita dell'accesso al registro in caso di guasto *totale* a livello regionale, non solo di un guasto nel sistema di archiviazione. La replica geografica offre anche altri vantaggi, come l'archiviazione immagini vicina alla rete per push/pull più rapidi negli scenari di distribuzione o sviluppo distribuiti.

## <a name="image-limits"></a>Limiti delle immagini

Nella tabella seguente vengono descritti i limiti di archiviazione e di immagine del contenitore applicati al Registro contenitori di Azure.

| Risorsa | Limite |
| -------- | :---- |
| Repository | Nessun limite |
| Immagini | Nessun limite |
| Livelli | Nessun limite |
| Tag | Nessun limite|
| Archiviazione | 5 TB |

Un numero molto elevato di repository e tag può compromettere le prestazioni del registro. Eliminare periodicamente i repository, le immagini e i tag non usati tramite l'[interfaccia della riga di comando di Azure](/cli/azure/acr), l'[API REST](/rest/api/containerregistry/) del record di controllo di accesso o il [portale di Azure][portal] nel quadro di una routine di manutenzione del registro. Le risorse del registro eliminate, come repository, immagini e tag, *non possono* essere recuperate dopo l'eliminazione.

## <a name="storage-cost"></a>Costo di archiviazione

Per informazioni dettagliate sui prezzi, vedere [Prezzi del Registro contenitori di Azure][pricing].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui diversi SKU del Registro contenitori di Azure (Basic, Standard e Premium), vedere [SKU del Registro contenitori di Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
