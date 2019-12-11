---
title: Opzioni per le prestazioni-scalabilità (CITUS)-database di Azure per PostgreSQL
description: Opzioni per un gruppo di server con iperscalabilità (CITUS), tra cui calcolo del nodo, archiviazione e aree.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975636"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Opzioni per le prestazioni di database di Azure per PostgreSQL – iperscalabilità (CITUS)

## <a name="compute-and-storage"></a>Calcolo e archiviazione
 
È possibile selezionare le impostazioni di calcolo e archiviazione in modo indipendente per i nodi del ruolo di lavoro e il nodo coordinatore in un gruppo di server con iperscalabilità (CITUS).  Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Le dimensioni di archiviazione per il provisioning si riferiscono alla capacità disponibile per il coordinatore e i nodi del ruolo di lavoro nel gruppo di server di iperscalabilità (CITUS). L'archiviazione include i file di database, i file temporanei, i log delle transazioni e i log del server postgres. La quantità totale di spazio di archiviazione di cui si esegue il provisioning definisce anche la capacità di I/O disponibile per ogni nodo del ruolo di lavoro e
 
|                       | Nodo del ruolo di lavoro           | Nodo coordinatore      |
|-----------------------|-----------------------|-----------------------|
| Calcolo, vcore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memoria per vCore, GiB | 8                     | 4                     |
| Dimensioni di archiviazione, TiB     | 0,5, 1,2             | 0,5, 1,2             |
| Tipo di risorse di archiviazione          | Utilizzo generico (SSD) | Utilizzo generico (SSD) |
| Operazioni di I/O al secondo                  | Fino a 3 IOPS/GiB      | Fino a 3 IOPS/GiB      |


## <a name="regions"></a>Aree
I gruppi di server iperscalare (CITUS) sono disponibili nelle aree di Azure seguenti:

* Americhe:
    * Canada centrale *
    * Stati Uniti Orientali
    * Stati Uniti orientali 2
    * Stati Uniti centro-settentrionali *
    * Stati Uniti occidentali 2
* Asia Pacifico:
    * Australia orientale *
    * Asia sud-orientale
* Europa
    * Europa settentrionale
    * Regno Unito meridionale
    * Europa occidentale

Le aree con un asterisco (\*) non supportano ancora la [disponibilità elevata](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Prezzi
Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
Per visualizzare il costo per la configurazione desiderata, il [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) Mostra il costo mensile nella scheda **Configura** in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Nel sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) selezionare **Aggiungi elementi**, espandere la categoria **database** e scegliere **database di Azure per PostgreSQL – iperscala (CITUS)** per personalizzare le opzioni.
 
## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare un gruppo di server con iperscalabilità (CITUS) nel portale](quickstart-create-hyperscale-portal.md).
