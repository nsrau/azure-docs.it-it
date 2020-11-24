---
title: includere file
description: includere file
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 297641bbaccb44739d67fdd26f0c1f64062bba46
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557803"
---
**Azure Data Lake Storage Gen2** non è un servizio dedicato né un tipo di account di archiviazione. Si tratta della versione più recente delle funzionalità dedicate all'analisi di Big Data.  Queste funzionalità sono disponibili in un account di archiviazione per utilizzo generico v2 o BlockBlobStorage e si possono ottenere abilitando la funzionalità **Spazio dei nomi gerarchico** dell'account. Per gli obiettivi di scalabilità, vedere questi articoli. 

- [Obiettivi di scalabilità per archiviazione BLOB di Azure](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage).
- [Obiettivi di scalabilità per gli account di archiviazione standard](../articles/storage/common/scalability-targets-standard-account.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** è un servizio dedicato. Si tratta di un repository con iperscalabilità a livello aziendale per i carichi di lavoro di analisi di Big Data. È possibile usare Data Lake Storage Gen1 per acquisire dati di qualsiasi dimensione, tipo e velocità di inserimento in un'unica posizione per le analisi esplorative e operative. Non esiste alcun limite alla quantità di dati archiviabili in un account Data Lake Storage Gen1.

| **Risorsa** | **Limite** | **Commenti** |
| --- | --- | --- |
| Numero massimo di account Data Lake Storage Gen1, per sottoscrizione, per area |10 | Per richiedere un aumento del limite, contattare il supporto. |
| Numero massimo di elenchi di controllo di accesso, per file o cartella |32 | Si tratta di un limite rigido. Usare i gruppi per gestire l'accesso con meno voci. |
| Numero massimo di elenchi di controllo di accesso predefiniti, per file o cartella |32 | Si tratta di un limite rigido. Usare i gruppi per gestire l'accesso con meno voci. |