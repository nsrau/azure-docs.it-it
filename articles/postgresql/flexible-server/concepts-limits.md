---
title: Limiti-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive i limiti nel database di Azure per PostgreSQL-server flessibili, ad esempio il numero di connessioni e le opzioni del motore di archiviazione.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d1405bce6761b6702146418296cb7b47bb9124ee
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357182"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Limiti nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Nelle sezioni seguenti vengono descritti i limiti delle capacità e funzionali nel servizio del database. Per informazioni sui livelli di risorse (calcolo, memoria, archiviazione), vedere l'articolo [calcolo e archiviazione](concepts-compute-storage.md) .

## <a name="maximum-connections"></a>Numero massimo di connessioni

Il numero massimo di connessioni per piano tariffario e Vcore è riportato di seguito. Il sistema Azure richiede tre connessioni per il monitoraggio del database di Azure per PostgreSQL-server flessibile.

| Nome SKU             | vCore | Dimensione della memoria | Numero massimo di connessioni | Numero massimo di connessioni utente |
|----------------------|--------|-------------|-----------------|----------------------|
| **Burst**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2S                  | 2      | 4 GiB       | 100             | 97                   |
| **Utilizzo generico**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Con ottimizzazione per la memoria** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | GiB 384     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> FATAL: sorry, too many clients already (ERRORE IRREVERSIBILE: ci sono già troppi client)

> [!IMPORTANT]
> Per un'esperienza ottimale, è consigliabile usare una connessione pool come PgBouncer per gestire in modo efficiente le connessioni.

Una connessione PostgreSQL, anche inattiva, può occupare circa 10 MB di memoria. Inoltre, la creazione di nuove connessioni richiede tempo. La maggior parte delle applicazioni richiede molte connessioni di breve durata, che generano questa situazione. Di conseguenza sarà disponibile un minor numero di risorse per il carico di lavoro effettivo e le prestazioni saranno ridotte. Il pool di connessioni può essere utilizzato per ridurre le connessioni inattive e riutilizzare le connessioni esistenti. Per altre informazioni, visitare il [post di Blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Limitazioni funzionali

### <a name="scale-operations"></a>Operazioni di scalabilità

- Per il ridimensionamento dell'archiviazione server è necessario riavviare il server.
- L'archiviazione server può essere ridimensionata solo in incrementi di 2x, per informazioni dettagliate vedere [calcolo e archiviazione](concepts-compute-storage.md) .
- La riduzione delle dimensioni di archiviazione del server non è attualmente supportata.

### <a name="server-version-upgrades"></a>Aggiornamenti della versione dei server

- La migrazione automatica tra le versioni del motore del database principale non è attualmente supportata. Se si vuole eseguire l'aggiornamento alla versione principale successiva, eseguire un [dump e ripristinarlo](../howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

### <a name="networking"></a>Rete

- Lo stato di VNET non è attualmente supportato.
- La combinazione dell'accesso pubblico con la distribuzione all'interno di una VNET non è attualmente supportata.
- Le regole del firewall non sono supportate in VNET. in alternativa, è possibile usare i gruppi di sicurezza di rete.
- I server di database di accesso pubblico possono connettersi a Internet pubblico, ad esempio tramite `postgres_fdw` , e questo accesso non può essere limitato. I server basati su VNET possono avere un accesso in uscita limitato usando gruppi di sicurezza di rete.

### <a name="high-availability-ha"></a>Disponibilità elevata (HA)

- Zone-Redundant disponibilità elevata non è attualmente supportata per i server con estensione.
- L'indirizzo IP del server di database cambia quando il server esegue il failover in standby a disponibilità elevata. Assicurarsi di usare il record DNS anziché l'indirizzo IP del server.
- Se la replica logica è configurata con un server flessibile configurato per la disponibilità elevata, in caso di failover al server di standby, gli slot di replica logici non vengono copiati nel server di standby. 
- Per altre limitazioni a disponibilità elevata, vedere la pagina [relativa alla documentazione relativa ai concetti a disponibilità elevata](concepts-high-availability.md) .

### <a name="availability-zones"></a>Zone di disponibilità

- Il trasferimento manuale di server in una zona di disponibilità diversa non è al momento supportato.
- La zona di disponibilità del server di standby a disponibilità elevata non può essere configurata manualmente.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Motore Postgres, estensioni e PgBouncer

- Postgres 10 e versioni precedenti non sono supportate. È consigliabile usare l'opzione [server singolo](../overview-single-server.md) se sono necessarie versioni Postgres precedenti.
- Il supporto dell'estensione è attualmente limitato alle estensioni Postgres `contrib` .
- La connessione PgBouncer incorporata pool non è attualmente disponibile per i server di database all'interno di un VNET o per i server con picchi.

### <a name="stopstart-operation"></a>Operazione di arresto/avvio

- Non è possibile arrestare il server per più di sette giorni.

### <a name="scheduled-maintenance"></a>Manutenzione pianificata

- Se si modifica la finestra di manutenzione per meno di cinque giorni prima di un aggiornamento già pianificato, non influirà sull'aggiornamento. Le modifiche vengono applicate solo alla successiva manutenzione pianificata.

### <a name="backing-up-a-server"></a>Esecuzione del backup di un server

- I backup vengono gestiti dal sistema, attualmente non è possibile eseguire manualmente questi backup. Si consiglia invece di usare `pg_dump` .
- I backup sono sempre backup completi basati su snapshot (non backup differenziali), probabilmente per un maggiore utilizzo dello spazio di archiviazione di backup. Si noti che i log delle transazioni (write ahead logs-WAL) sono separati dai backup completi o differenziali e vengono archiviati in modo continuo.

### <a name="restoring-a-server"></a>Rispristino di un server

- Quando si usa la funzionalità di ripristino temporizzato, il nuovo server viene creato con le stesse configurazioni di calcolo e di archiviazione del server su cui si basa.
- Quando si esegue il ripristino da un backup, i server di database basati su VNET vengono ripristinati nello stesso VNET.
- Il nuovo server creato durante un ripristino non dispone delle regole del firewall presenti nel server originale. È necessario creare le regole del firewall separatamente per il nuovo server.
- Il ripristino di un server eliminato non è supportato.
- Il ripristino tra aree non è supportato.

### <a name="other-features"></a>Altre funzionalità

* L'autenticazione Azure AD non è ancora supportata. È consigliabile utilizzare l'opzione [server singolo](../overview-single-server.md) se è necessaria l'autenticazione Azure ad.
* Le repliche di lettura non sono ancora supportate. Se è necessario leggere le repliche, è consigliabile usare l'opzione [server singolo](../overview-single-server.md) .


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [Opzioni di calcolo e archiviazione disponibili](concepts-compute-storage.md)
- Informazione sulle [versioni supportate del Database PostgreSQL](concepts-supported-versions.md)
- Vedere [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](how-to-restore-server-portal.md)
