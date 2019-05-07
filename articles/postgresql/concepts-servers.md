---
title: Concetti relativi ai server nel Database di Azure per PostgreSQL - Server singolo
description: Questo articolo fornisce considerazioni e linee guida per la configurazione e gestione dei Database di Azure per PostgreSQL - singolo Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: bc135e58d0fbabc809f3718915e9f4e35b8ed875
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067165"
---
# <a name="azure-database-for-postgresql---single-server"></a>Database di Azure per PostgreSQL - Server singolo
Questo articolo fornisce considerazioni e linee guida per l'uso di Database di Azure per PostgreSQL - singolo Server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Che cos'è un database di Azure per il server PostgreSQL?
Un server nel Database di Azure per PostgreSQL - opzione di distribuzione Server singolo è un punto amministrativo centrale per più database. È lo stesso costrutto di server PostgreSQL con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il servizio PostgreSQL è gestito, assicura le prestazioni garantite, espone accesso e funzionalità a livello di server.

Un database di Azure per il server PostgreSQL:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso a server e database 
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È disponibile in più versioni. Per altre informazioni, vedere [Versioni supportate del database PostgreSQL](concepts-supported-versions.md).
- È estensibile dagli utenti. Per altre informazioni, vedere [Estensioni di PostgreSQL](concepts-extensions.md).

In un database di Azure per il server PostgreSQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione di piano tariffario, vCore e archiviazione (GB). Per altre informazioni, vedere i [piani tariffari](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Come connettersi ed eseguire l'autenticazione a un database di Azure per il server PostgreSQL?
Gli elementi seguenti contribuiscono a garantire un accesso sicuro al database:

|||
|:--|:--|
| **Autenticazione e autorizzazione** | Il database di Azure per il server PostgreSQL supporta l'autenticazione nativa a PostgreSQL. È possibile connettersi ed eseguire l'autenticazione al server tramite l'account amministratore del server. |
| **Protocollo** | Il servizio supporta un protocollo basato su messaggi usato da PostgreSQLL. |
| **TCP/IP** | Il protocollo è supportato su TCP/IP e sui socket di dominio Unix. |
| **Firewall** | Per proteggere i dati, una regola del firewall impedisce qualsiasi accesso al server e ai relativi database finché non si specificano i computer autorizzati. Vedere [Regole firewall per Database di Azure per PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Gestione del server
È possibile gestire i server di Database di Azure per PostgreSQL mediante il [portale di Azure](https://portal.azure.com) o l'[interfaccia della riga di comando di Azure](/cli/azure/postgres).

Durante la creazione di un server, impostare le credenziali per l'utente amministratore. L'utente amministratore è l'utente con privilegi più elevati presente nel server. Fa parte del ruolo azure_pg_admin. Questo ruolo non dispone delle autorizzazioni utente con privilegi avanzati complete. 

L'attributo utente con privilegi avanzati PostgreSQL viene assegnato ad azure_superuser, che appartiene al servizio gestito. Non si dispone dell'accesso a questo ruolo.

Un server di Database di Azure per PostgreSQL dispone di database predefiniti: 
- **postgres**: database predefinito a cui è possibile connettersi dopo aver creato il server.
- **azure_maintenance**: database usato per separare i processi che offrono il servizio gestito dalle azioni degli utenti. Non si dispone dell'accesso a questo database.
- **azure_sys**: database per Query Store. Questo database non accumulare dati quando Query Store è disattivato (impostazione predefinita). Per altre informazioni, vedere la [panoramica di Query Store](concepts-query-store.md).


## <a name="server-parameters"></a>Parametri del server
I parametri del server PostgreSQL determinano la configurazione del server. In Database di Azure per PostgreSQL l'elenco dei parametri può essere visualizzato e modificato tramite il portale o l'interfaccia della riga di comando di Azure. 

Come servizio gestito per Postgres i parametri configurabili in Database di Azure per PostgreSQL sono un subset di parametri nell'istanza di Postgres locale. Per altre informazioni sui parametri per Postgres, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html). Il server di Database di Azure per PostgreSQL è abilitato con i valori predefiniti per ogni parametro al momento della sua creazione. Alcuni parametri che richiedono il riavvio del server o l'accesso con diritti utente con privilegi avanzati per l'implementazione delle modifiche non possono essere configurati dall'utente.


## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del servizio, vedere  [Azure Database for PostgreSQL Overview](overview.md) (Database di Azure per una panoramica di PostgreSQL).
- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al  **livello di servizio**, vedere la pagina relativa ai  [livelli di servizio](concepts-pricing-tiers.md).
- Per informazioni sulla connessione al servizio, vedere  [Raccolte connessioni per il database di Azure per PostgreSQL](concepts-connection-libraries.md).
- Visualizzare o modificare i parametri del server tramite il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).
