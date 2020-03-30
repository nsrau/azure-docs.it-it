---
title: Opzioni di prestazioni - Hyperscale (Citus) - Database di Azure per PostgreSQLPerformance options – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Opzioni per un gruppo di server Hyperscale (Citus), tra cui calcolo dei nodi, archiviazione e aree.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462412"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Database di Azure per PostgreSQL - Opzioni di prestazioni Hyperscale (Citus)

## <a name="compute-and-storage"></a>Calcolo e archiviazione
 
È possibile selezionare le impostazioni di calcolo e archiviazione in modo indipendente per i nodi di lavoro e il nodo coordinatore in un gruppo di server Hyperscale (Citus).  Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. La dimensione di archiviazione per il provisioning si riferisce alla capacità disponibile per il coordinatore e i nodi di lavoro nel gruppo di server Hyperscale (Citus). L'archiviazione include file di database, file temporanei, registri delle transazioni e i registri del server Postgres. La quantità totale di spazio di archiviazione di cui è stato eseguito il provisioning definisce anche la capacità di I/O disponibile per ogni nodo lavoratore e coordinatore.
 
|                       | Nodo del ruolo di lavoro           | Nodo coordinatore      |
|-----------------------|-----------------------|-----------------------|
| Calcolo, vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memoria per vCore, GiBMemory per vCore, GiB | 8                     | 4                     |
| Dimensioni di archiviazione, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo di archiviazione          | Scopo generale (SSD) | Scopo generale (SSD) |
| IOPS                  | Fino a 3 IOPS/GiB      | Fino a 3 IOPS/GiB      |


## <a name="regions"></a>Regioni
I gruppi di server Hyperscale (Citus) sono disponibili nelle aree di Azure seguenti:Hyperscale (Citus) server groups are available in the following Azure regions:

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
* Europa:
    * Europa settentrionale
    * Regno Unito meridionale
    * Europa occidentale

Alcune di queste aree potrebbero non essere attivate inizialmente in tutte le sottoscrizioni di Azure.Some of these regions may not be initially activated on all Azure subscriptions. Se si desidera utilizzare un'area dell'elenco precedente e non la si vede nella sottoscrizione oppure se si desidera utilizzare un'area non presente nell'elenco, aprire una richiesta di [supporto.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Prezzi
Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
Per visualizzare il costo per la configurazione desiderata, il portale di [Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra il costo mensile nella scheda **Configura** in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Nel sito Web [del calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure selezionare Aggiungi **elementi**, espandere la categoria **Database** e scegliere Database di Azure **per PostgreSQL - Hyperscale (Citus)** per personalizzare le opzioni.
 
## <a name="next-steps"></a>Passaggi successivi
Informazioni su come creare un gruppo di [server Hyperscale (Citus) nel portale.](quickstart-create-hyperscale-portal.md)
