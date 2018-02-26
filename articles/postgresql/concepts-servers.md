---
title: Concetti relativi ai server nel database di Azure per PostgreSQL | Microsoft Docs
description: Questo argomento fornisce considerazioni e linee guida per la configurazione e la gestione dei server di Database di Azure per PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 12/02/2017
ms.openlocfilehash: d7eec2735e48f57500eb2ea822f0949d2ec2e585
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="azure-database-for-postgresql-servers"></a>Database di Azure per server PostgreSQL
Questo articolo presenta alcune considerazioni e diverse linee guida per l'uso di server di Database di Azure per PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Che cos'è un database di Azure per il server PostgreSQL?
Un database di Azure per il server PostgreSQL funge da punto di gestione centrale per più database. È lo stesso costrutto di server PostgreSQL con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il servizio PostgreSQL è gestito, assicura le prestazioni garantite, espone accesso e funzionalità a livello di server.

Un database di Azure per il server PostgreSQL:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso a server e database (.postgresql.database.azure.com).
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È disponibile in più versioni. Per altre informazioni, vedere [Versioni supportate del database PostgreSQL](concepts-supported-versions.md).
- È estensibile dagli utenti. Per altre informazioni, vedere [Estensioni di PostgreSQL](concepts-extensions.md).

In un database di Azure per il server PostgreSQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione di piano tariffario, unità di calcolo e archiviazione (GB). Per altre informazioni, vedere i [piani tariffari](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Come connettersi ed eseguire l'autenticazione a un database di Azure per il server PostgreSQL?
Gli elementi seguenti contribuiscono a garantire un accesso sicuro al database:

|||
|:--|:--|
| **Autenticazione e autorizzazione** | Il database di Azure per il server PostgreSQL supporta l'autenticazione nativa a PostgreSQL. È possibile connettersi ed eseguire l'autenticazione al server tramite l'account amministratore del server. |
| **Protocollo** | Il servizio supporta un protocollo basato su messaggi usato da PostgreSQLL. |
| **TCP/IP** | Il protocollo è supportato su TCP/IP e sui socket di dominio Unix. |
| **Firewall** | Per proteggere i dati, una regola del firewall impedisce qualsiasi accesso al server e ai relativi database finché non si specificano i computer autorizzati. Vedere [Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md) (Database di Azure per le regole firewall del server PostgreSQL). |

## <a name="how-do-i-manage-a-server"></a>Gestione di un server
È possibile gestire i server di Database di Azure per PostgreSQL mediante il [portale di Azure](https://portal.azure.com) o l'[interfaccia della riga di comando di Azure](/cli/azure/postgres).

## <a name="server-parameters"></a>Parametri del server
I parametri del server PostgreSQL determinano la configurazione del server. In Database di Azure per PostgreSQL l'elenco dei parametri può essere visualizzato e modificato tramite il portale o l'interfaccia della riga di comando di Azure. 

Come servizio gestito per Postgres i parametri configurabili in Database di Azure per PostgreSQL sono un subset di parametri nell'istanza di Postgres locale. Per altre informazioni sui parametri per Postgres, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html). Il server di Database di Azure per PostgreSQL è abilitato con i valori predefiniti per ogni parametro al momento della sua creazione. I parametri che richiedono il riavvio del server o l'accesso con diritti superuser per l'implementazione delle modifiche non possono essere configurati dall'utente.


## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del servizio, vedere [Azure Database for PostgreSQL Overview](overview.md) (Database di Azure per una panoramica di PostgreSQL).
- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere la pagina relativa ai [livelli di servizio](concepts-service-tiers.md).
- Per informazioni sulla connessione al servizio, vedere [Raccolte connessioni per il database di Azure per PostgreSQL](concepts-connection-libraries.md).
- Visualizzare o modificare i parametri del server tramite il [portale di Azure](howto-configure-server-parameters-using-portal.md) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).
