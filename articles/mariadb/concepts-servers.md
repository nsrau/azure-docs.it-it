---
title: Concetti relativi ai server nel database di Azure per MariaDB
description: Questo argomento fornisce considerazioni e linee guida per l'uso del database di Azure per server MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4fd2f28f52c45a0ec3128302b02ad5ba498c982b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247772"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Concetti relativi ai server nel database di Azure per MariaDB
Questo argomento fornisce considerazioni e linee guida per l'uso del database di Azure per server MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Definizione del database di Azure per un server MariaDB

Un database di Azure per il server MariaDB funge da punto di gestione centrale per più database. È lo stesso costrutto di server MariaDB con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il database di Azure per il servizio MariaDB è gestito, assicura le prestazioni garantite ed espone accesso e funzionalità a livello di server.

Un database di Azure per il server MariaDB:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso a server e database.
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È disponibile nella versione 10.2 del motore MariaDB. Per altre informazioni, vedere [Definizione del database di Azure per un server MariaDB](./concepts-supported-versions.md).

In un database di Azure per il server MariaDB è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o per creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione di piano tariffario, vCore e archiviazione (GB). Per altre informazioni, vedere i [piani tariffari](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Modalità di protezione del database di Azure per un server MariaDB

I seguenti elementi contribuiscono a garantire un accesso sicuro al database.
|||
| :--| :--|
| **Autenticazione e autorizzazione** | Il database di Azure per il server MariaDB supporta l'autenticazione nativa a MariaDB. È possibile connettersi ed eseguire l'autenticazione a un server con l'account amministratore del server. |
| **Protocollo** | Il servizio supporta un protocollo basato su messaggi usato da MySQL. |
| **TCP/IP** | Il protocollo è supportato su TCP/IP e sui socket di dominio Unix. |
| **Firewall** | Per proteggere i dati, le regole del firewall impediscono qualsiasi accesso al server del database finché non si specificano i computer autorizzati. Vedere [Regole firewall per il server Database di Azure per MariaDB](./concepts-firewall-rules.md). |
| **SSL** | Il servizio supporta l'attivazione di connessioni SSL tra le applicazioni e il server del database. Consultare [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Gestione di un server
È possibile gestire il database di Azure per i server MariaDB mediante il portale o l'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del servizio, consultare le [informazioni generali su Database di Azure per MariaDB](./overview.md)
- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere [Livelli di servizio](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
