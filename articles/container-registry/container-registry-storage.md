---
title: Archiviazione immagini del contenitore
description: Contiene informazioni dettagliate sulla modalità di archiviazione delle immagini del contenitore Docker in Registro Azure Container, tra cui sicurezza, ridondanza e capacità.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214061"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Archiviazione immagini del contenitore in Registro Azure Container

Ogni Registro Azure Container [Basic, Standard e Premium](container-registry-skus.md) beneficia delle funzionalità di archiviazione avanzate di Azure, come ad esempio la crittografia dei dati inattivi per la sicurezza dei dati di immagine e la ridondanza geografica per la protezione dei dati di immagine. Le sezioni seguenti descrivono sia le funzionalità che i limiti di archiviazione immagini in Registro Azure Container.

## <a name="encryption-at-rest"></a>Crittografia dei dati inattivi

Tutte le immagini del contenitore presenti nel registro vengono crittografate a riposo. Azure esegue automaticamente la crittografia di un'immagine prima di archiviarla e la decrittografa in tempo reale quando l'utente, un'applicazione o un servizio eseguono il pull dell'immagine. Facoltativamente, applicare un livello di crittografia aggiuntivo con una [chiave gestita dal cliente](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

Azure usa uno schema di archiviazione con ridondanza geografica per evitare la perdita delle immagini del contenitore. Registro Azure Container replica automaticamente le immagini del contenitore in più data center geograficamente distanti, impedendone la perdita in caso di guasto di un sistema di archiviazione regionale.

## <a name="geo-replication"></a>Replica geografica

Per gli scenari che richiedono una disponibilità ancora più elevata, è consigliabile usare la funzione di [replica geografica](container-registry-geo-replication.md) dei registri Premium. La replica geografica scongiura la perdita dell'accesso al registro in caso di guasto *totale* a livello regionale, non solo di un guasto nel sistema di archiviazione. La replica geografica offre anche altri vantaggi, come l'archiviazione immagini vicina alla rete per push/pull più rapidi negli scenari di distribuzione o sviluppo distribuiti.

## <a name="scalable-storage"></a>Archiviazione scalabile:

Azure Container Registry consente di creare tutti i repository, le immagini, i livelli o i tag necessari, fino al [limite di archiviazione del registro di sistema](container-registry-skus.md#service-tier-features-and-limits). 

Un numero molto elevato di repository e tag può compromettere le prestazioni del registro. Eliminare periodicamente i repository, i tag e le immagini non usati come parte della routine di manutenzione del registro di sistema e, facoltativamente, impostare un [criterio di conservazione](container-registry-retention-policy.md) per i manifesti senza tag. Le risorse del registro di sistema eliminate, ad esempio repository, immagini e tag, *non possono* essere recuperate dopo l'eliminazione. Per altre informazioni sull'eliminazione delle risorse del registro, vedere [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="storage-cost"></a>Costo di archiviazione

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Registro Azure Container][pricing].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui Registro Container Basic, Standard e Premium, vedere [SKU di Registro Azure Container](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
