---
title: Database di Azure per PostgreSQL-(Citus) (anteprima) con Iperscalabilità opzioni relative alle prestazioni
description: Opzioni per un gruppo di server con scalabilità elevatissima (Citus), tra cui calcolo, archiviazione e aree.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077291"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Database di Azure per PostgreSQL-(Citus) (anteprima) con Iperscalabilità opzioni relative alle prestazioni

## <a name="compute-and-storage"></a>Calcolo e archiviazione
 
È possibile selezionare le impostazioni calcolo e archiviazione in modo indipendente per i nodi di lavoro e il nodo coordinatore in un gruppo di server con scalabilità elevatissima (Citus).  Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Le dimensioni di archiviazione per il provisioning si intende la capacità disponibile per i nodi di lavoro e coordinator nel gruppo di server con scalabilità elevatissima (Citus). Lo spazio di archiviazione include i file di database, i file temporanei, i log delle transazioni e i log del server Postgres. La quantità totale di spazio di archiviazione che viene effettuato il provisioning anche definisce la capacità dei / o disponibile per ogni nodo del ruolo di lavoro e coordinator.
 
|                       | Nodo del ruolo di lavoro           | Nodo coordinatore      |
|-----------------------|-----------------------|-----------------------|
| Risorse di calcolo, Vcore       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Memoria per ogni vCore, GiB | 8                     | 4                     |
| Dimensioni di archiviazione TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo di archiviazione          | Utilizzo generico (SSD) | Utilizzo generico (SSD) |
| IOPS                  | Fino a 3 IOPS/GiB      | Fino a 3 IOPS/GiB      |


## <a name="regions"></a>Regioni
Gruppi di server con scalabilità elevatissima (Citus) sono disponibili nelle aree di Azure seguenti:
* Stati Uniti orientali 2
* Asia sudorientale
* Europa occidentale
* Stati Uniti occidentali 2

## <a name="pricing"></a>Prezzi
Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
Per visualizzare il costo per la configurazione desiderata, il [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) Mostra il costo mensile nel **configura** scheda base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Nel [calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) sito Web, selezionare **aggiungere elementi**, espandere il **database** categoria e scegliere **Database di Azure per PostgreSQL- Con Iperscalabilità (Citus)** per personalizzare le opzioni.
 
## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare un gruppo di server con scalabilità elevatissima (Citus) nel portale di](quickstart-create-hyperscale-portal.md).
