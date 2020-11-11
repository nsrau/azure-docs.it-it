---
title: Server-database di Azure per MariaDB
description: Questo argomento fornisce considerazioni e linee guida per l'uso del database di Azure per server MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f519ac30468d197c14fcf53d386168ebde5cf8ac
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504357"
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

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Arrestare/avviare un database di Azure per MariaDB (anteprima)
Il database di Azure per MariaDB offre la possibilità di **arrestare** il server quando non è in uso e di **avviare** il server quando si riprende l'attività. Questa operazione viene essenzialmente eseguita per ridurre i costi dei server di database e pagare solo per la risorsa quando è in uso. Questa operazione diventa ancora più importante per i carichi di lavoro di sviluppo e test e quando si usa solo il server per una parte del giorno. Quando si arresta il server, tutte le connessioni attive verranno eliminate. In seguito, quando si vuole riportare online il server, è possibile usare l' [portale di Azure](../mysql/how-to-stop-start-server.md) o l' [interfaccia](../mysql/how-to-stop-start-server.md)della riga di comando.

Quando il server si trova nello stato **interrotto** , il calcolo del server non viene fatturato. Tuttavia, lo spazio di archiviazione continua a essere fatturato in quanto l'archiviazione del server rimane per assicurarsi che i file di dati siano disponibili quando il server viene riavviato.

> [!IMPORTANT]
> Quando si **Arresta** il server, rimane in tale stato per i 7 giorni successivi in un'estensione. Se non viene **avviata** manualmente durante questo periodo di tempo, il server verrà avviato automaticamente alla fine dei 7 giorni. Se non si utilizza il server, è possibile scegliere di **arrestarlo** di nuovo.

Nel momento in cui il server viene arrestato, non è possibile eseguire alcuna operazione di gestione sul server. Per modificare le impostazioni di configurazione nel server, sarà necessario [avviare il server](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Limitazioni dell'operazione di arresto/avvio
- Non supportato con le configurazioni della replica di lettura (origine e repliche).

## <a name="how-do-i-manage-a-server"></a>Gestione di un server
È possibile gestire il database di Azure per i server MariaDB mediante il portale o l'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del servizio, consultare le [informazioni generali su Database di Azure per MariaDB](./overview.md)
- Per informazioni sulle quote di risorse e sulle limitazioni specifiche in base al **livello di servizio** , vedere livelli di [servizio](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
