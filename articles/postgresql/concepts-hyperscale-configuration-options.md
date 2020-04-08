---
title: Opzioni di configurazione - Hyperscale (Citus) - Database di Azure per PostgreSQLConfiguration options – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Opzioni per un gruppo di server Hyperscale (Citus), tra cui calcolo dei nodi, archiviazione e aree.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804589"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Database di Azure per PostgreSQL - Opzioni di configurazione Hyperscale (Citus)

## <a name="compute-and-storage"></a>Calcolo e archiviazione
 
È possibile selezionare le impostazioni di calcolo e archiviazione in modo indipendente per i nodi di lavoro e il nodo coordinatore in un gruppo di server Hyperscale (Citus).  Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. La dimensione di archiviazione per il provisioning si riferisce alla capacità disponibile per il coordinatore e i nodi di lavoro nel gruppo di server Hyperscale (Citus). L'archiviazione include file di database, file temporanei, registri delle transazioni e i registri del server Postgres.
 
|                       | Nodo del ruolo di lavoro           | Nodo coordinatore      |
|-----------------------|-----------------------|-----------------------|
| Calcolo, vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memoria per vCore, GiBMemory per vCore, GiB | 8                     | 4                     |
| Dimensioni di archiviazione, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo di archiviazione          | Scopo generale (SSD) | Scopo generale (SSD) |
| IOPS                  | Fino a 3 IOPS/GiB      | Fino a 3 IOPS/GiB      |

La quantità totale di RAM in un singolo nodo Hyperscale (Citus) si basa sul numero selezionato di vCore.

| vCore | Un nodo di lavoro, RAM GiB | Nodo coordinatore, RAM GiB |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

La quantità totale di spazio di archiviazione di cui è stato eseguito il provisioning definisce anche la capacità di I/O disponibile per ogni nodo lavoratore e coordinatore.

| Dimensioni di archiviazione, TiB | Numero massimo di IOPS |
|-------------------|--------------|
| 0.5               | 1.536        |
| 1                 | 3.072        |
| 2                 | 6,148        |

Per l'intero cluster Hyperscale (Citus), le operazioni di I/O al secondo aggregate vengono calcolate sui valori seguenti:

| Nodi di lavoro | 0.5 TiB, IOPS totale | 1 TiB, IOPS totale | 2 TiB, IOPS totale |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3.072               | 6.144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6.144               | 12.288            | 24,592            |
| 5            | 7,680               | 15.360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12.288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15.360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

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
