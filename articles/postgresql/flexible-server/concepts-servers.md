---
title: Server nel database di Azure per PostgreSQL-server flessibile (anteprima)
description: Questo articolo fornisce considerazioni e linee guida per la configurazione e la gestione di database di Azure per PostgreSQL-server flessibili.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 5cd4454d5b77d18940f6693a25a691a01f4fedec
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940386"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>Server-database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo fornisce considerazioni e linee guida per l'uso di database di Azure per PostgreSQL-server flessibili.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Che cos'è un server di Database di Azure per PostgreSQL?

Un server in database di Azure per PostgreSQL-opzione di distribuzione di server flessibili è un punto di amministrazione centrale per più database. È lo stesso costrutto di server PostgreSQL con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il servizio PostgreSQL è gestito, assicura le prestazioni garantite, espone accesso e funzionalità a livello di server.

Un database di Azure per il server PostgreSQL:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso a server e database
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È disponibile in più versioni. Per altre informazioni, vedere [versioni supportate del database PostgreSQL](concepts-supported-versions.md).
- È estensibile dagli utenti. Per altre informazioni, vedere [Estensioni di PostgreSQL](concepts-extensions.md).

In un database di Azure per il server PostgreSQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione di piano tariffario, vCore e archiviazione (GB). Per altre informazioni, vedere [Opzioni di calcolo e archiviazione](concepts-compute-storage.md).

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>Ricerca per categorie connettersi ed eseguire l'autenticazione al server di database?

Gli elementi seguenti contribuiscono a garantire un accesso sicuro al database:

|||
|:--|:--|
| **Autenticazione e autorizzazione** | Il database di Azure per il server PostgreSQL supporta l'autenticazione nativa a PostgreSQL. È possibile connettersi ed eseguire l'autenticazione al server con l'account amministratore del server. |
| **Protocollo** | Il servizio supporta un protocollo basato su messaggi usato da PostgreSQLL. |
| **TCP/IP** | Il protocollo è supportato su TCP/IP e sui socket di dominio Unix. |
| **Firewall** | Per proteggere i dati, una regola del firewall impedisce qualsiasi accesso al server e ai relativi database finché non si specificano i computer autorizzati. Vedere [Azure Database for PostgreSQL Server firewall rules](how-to-manage-firewall-portal.md) (Database di Azure per le regole firewall del server PostgreSQL). |

## <a name="managing-your-server"></a>Gestione del server

È possibile gestire i server di database di Azure per PostgreSQL usando il [portale di Azure](https://portal.azure.com) o l'interfaccia della riga di comando di [Azure](/cli/azure/postgres).

Durante la creazione di un server, impostare le credenziali per l'utente amministratore. L'utente amministratore è l'utente con privilegi più elevati presente nel server. Fa parte del ruolo azure_pg_admin. Questo ruolo non dispone delle autorizzazioni utente con privilegi avanzati complete. 

L'attributo utente con privilegi avanzati PostgreSQL viene assegnato ad azure_superuser, che appartiene al servizio gestito. Non si dispone dell'accesso a questo ruolo.

Un server di Database di Azure per PostgreSQL dispone di database predefiniti: 

- **postgres**: database predefinito a cui è possibile connettersi dopo aver creato il server.
- **azure_maintenance**: database usato per separare i processi che offrono il servizio gestito dalle azioni degli utenti. Non si dispone dell'accesso a questo database.

## <a name="server-parameters"></a>Parametri del server

I parametri del server PostgreSQL determinano la configurazione del server. In Database di Azure per PostgreSQL l'elenco dei parametri può essere visualizzato e modificato tramite il portale o l'interfaccia della riga di comando di Azure.

Come servizio gestito per Postgres i parametri configurabili in Database di Azure per PostgreSQL sono un subset di parametri nell'istanza di Postgres locale. Per altre informazioni sui parametri per Postgres, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/12/static/runtime-config.html). Il server di Database di Azure per PostgreSQL è abilitato con i valori predefiniti per ogni parametro al momento della sua creazione. Alcuni parametri che richiedono il riavvio del server o l'accesso con diritti utente con privilegi avanzati per l'implementazione delle modifiche non possono essere configurati dall'utente.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio, vedere [Azure Database for PostgreSQL Overview](overview.md) (Database di Azure per una panoramica di PostgreSQL).
- Per informazioni sulle quote di risorse e sulle limitazioni specifiche in base alla **configurazione**, vedere [Opzioni di calcolo e archiviazione](concepts-compute-storage.md).
- Visualizzare o modificare i parametri del server tramite il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).
