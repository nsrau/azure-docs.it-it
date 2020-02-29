---
title: Opzioni per le prestazioni-scalabilità (CITUS)-database di Azure per PostgreSQL
description: Opzioni per un gruppo di server con iperscalabilità (CITUS), tra cui calcolo del nodo, archiviazione e aree.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77462412"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Opzioni per le prestazioni di database di Azure per PostgreSQL – iperscalabilità (CITUS)

## <a name="compute-and-storage"></a>Calcolo e archiviazione
 
È possibile selezionare le impostazioni di calcolo e archiviazione in modo indipendente per i nodi del ruolo di lavoro e il nodo coordinatore in un gruppo di server con iperscalabilità (CITUS).  Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Le dimensioni di archiviazione per il provisioning si riferiscono alla capacità disponibile per il coordinatore e i nodi del ruolo di lavoro nel gruppo di server di iperscalabilità (CITUS). L'archiviazione include i file di database, i file temporanei, i log delle transazioni e i log del server postgres. La quantità totale di spazio di archiviazione di cui si esegue il provisioning definisce anche la capacità di I/O disponibile per ogni nodo del ruolo di lavoro e
 
|                       | Nodo del ruolo di lavoro           | Nodo coordinatore      |
|-----------------------|-----------------------|-----------------------|
| Calcolo, vcore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memoria per vCore, GiB | 8                     | 4                     |
| Dimensioni di archiviazione, TiB     | 0,5, 1,2             | 0,5, 1,2             |
| Tipo di archiviazione          | Utilizzo generico (SSD) | Utilizzo generico (SSD) |
| IOPS                  | Fino a 3 IOPS/GiB      | Fino a 3 IOPS/GiB      |


## <a name="regions"></a>Regioni
I gruppi di server iperscalare (CITUS) sono disponibili nelle aree di Azure seguenti:

* Americhe:
    * Canada centrale
    * Stati Uniti centrali
    * Stati Uniti orientali
    * Stati Uniti orientali 2
    * Stati Uniti centro-settentrionali
    * Stati Uniti occidentali 2
* Asia Pacifico:
    * Australia orientale
    * Giappone orientale
    * Corea centrale
    * Asia sud-orientale
* Europa
    * Europa settentrionale
    * Regno Unito meridionale
    * Europa occidentale

Alcune di queste aree potrebbero non essere attivate inizialmente in tutte le sottoscrizioni di Azure. Se si vuole usare un'area dall'elenco precedente e non visualizzarla nella sottoscrizione o se si vuole usare un'area non presente nell'elenco, aprire una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Prezzi
Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
Per visualizzare il costo per la configurazione desiderata, il [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) Mostra il costo mensile nella scheda **Configura** in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Nel sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) selezionare **Aggiungi elementi**, espandere la categoria **database** e scegliere **database di Azure per PostgreSQL – iperscala (CITUS)** per personalizzare le opzioni.
 
## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare un gruppo di server con iperscalabilità (CITUS) nel portale](quickstart-create-hyperscale-portal.md).
