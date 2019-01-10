---
title: Uso di percorsi di indice in Azure Cosmos DB
description: Panoramica dei percorsi di indice in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: c22d8d69284c546a4fccc86302672d81ce65b9e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032772"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Percorsi di indice in Azure Cosmos DB

Tramite i percorsi di indice in Azure Cosmos DB è possibile scegliere di includere o escludere un percorso specifico dall'indicizzazione. La scelta di percorsi specifici consente di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono noti. I percorsi di indice iniziano con l'operatore carattere jolly radice (`/`) e terminano in genere con l'operatore carattere jolly `?`. Questo motivo indica che per il prefisso esistono più valori possibili. Per soddisfare la query `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, ad esempio, è necessario includere un percorso di indice per `/familyName/?` nei criteri di indice del contenitore.

I percorsi di indice possono anche usare il carattere jolly `*` per specificare un comportamento ricorsivo al di sotto del prefisso. È ad esempio possibile usare `/payload/*` per escludere dall'indicizzazione qualsiasi elemento al di sotto della proprietà payload.

## <a name="common-patterns-for-index-paths"></a>Modelli comuni per i percorsi di indice

Di seguito sono indicati i modelli comuni per la definizione di percorsi di indice:

| **Percorso** | **Descrizione/Caso d'uso** |
| ---------- | ------- |
| /   | Percorso predefinito per la raccolta. Ricorsivo e applicabile all'intero albero del documento.|
| /prop/?  | Percorso di indice necessario per gestire query come la seguente (rispettivamente con tipi hash e di intervallo):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*  | Percorso di indice per tutti i percorsi al di sotto dell'etichetta specificata. Funziona con le query seguenti<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?  | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di valori scalari, come ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5  |
| /props/[]/subprop/? | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di oggetti, come [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | Percorso di indice necessario per gestire le query (rispettivamente con tipi hash e di intervallo):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

Quando si impostano percorsi di indice personalizzati, è necessario specificare la regola di indicizzazione predefinita per l'intero elemento, indicato dal percorso speciale `/*`.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione in Azure Cosmos DB sono disponibili negli articoli seguenti:

- [Overview of indexing](index-overview.md) (Panoramica dell'indicizzazione)
- [Indexing policies in Azure Cosmos DB](indexing-policies.md) (Criteri di indicizzazione in Azure Cosmos DB)
- [Tipi di indici in Azure Cosmos DB](index-types.md)
