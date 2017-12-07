---
title: Concetti relativi ai server nel database di Azure per MySQL | Microsoft Docs
description: Questo argomento fornisce considerazioni e linee guida per l'uso del database di Azure per server MySQL.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d3de3fdf28997b63321bf23443472db43ebb5c52
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Concetti relativi ai server nel database di Azure per MySQL
Questo articolo presenta considerazioni e linee guida per l'utilizzo di Database di Azure per i server MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Che cos'è un database di Azure per il server MySQL?

Un database di Azure per il server MySQL funge da punto di gestione centrale per più database. È lo stesso costrutto di server MySQL con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il database di Azure per il servizio MySQL è gestito, assicura le prestazioni garantite ed espone accesso e funzionalità a livello di server.

Un database di Azure per il server MySQL:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso a server e database.
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È disponibile in più versioni. Per altre informazioni, vedere [Supported Azure Database for MySQL database versions](./concepts-supported-versions.md) (Database di Azure supportato per le diverse versioni del database MySQL).

In un database di Azure per il server MySQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o per creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione del piano tariffario, delle Unità di calcolo e dell'archiviazione (GB). Per altre informazioni, vedere i [piani tariffari](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Come connettersi ed eseguire l'autenticazione a un database di Azure per il server MySQL?

I seguenti elementi contribuiscono a garantire un accesso sicuro al database.
|||
| :-- | :-- |
| **Autenticazione e autorizzazione** | Il database di Azure per il server MySQL supporta l'autenticazione nativa a MySQL. È possibile connettersi ed eseguire l'autenticazione a un server con l'account amministratore del server. |
| **Protocollo** | Il servizio supporta un protocollo basato su messaggi usato da MySQL. |
| **TCP/IP** | Il protocollo è supportato su TCP/IP e sui socket di dominio Unix. |
| **Firewall** | Per proteggere i dati, le regole del firewall impediscono qualsiasi accesso al server del database finché non si specificano i computer autorizzati. Vedere [Azure Database for MySQL Server firewall rules](./concepts-firewall-rules.md) (Database di Azure per le regole firewall del server MySQL). |
| **SSL** | Il servizio supporta l'attivazione di connessioni SSL tra le applicazioni e il server del database.  Vedere [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurare la connettività SSL nell'applicazione per connettersi in modo sicuro al database di Azure per MySQL). |

## <a name="how-do-i-manage-a-server"></a>Gestione di un server
È possibile gestire il database di Azure per i server MySQL mediante il portale o l'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del servizio, vedere [Azure Database for MySQL Overview](./overview.md) (Database di Azure per una panoramica di MySQL)
- Per informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**, vedere [Livelli di servizio](./concepts-service-tiers.md)
- Per informazioni sulla connessione al servizio, vedere [Raccolte connessioni per il database di Azure per MySQL](./concepts-connection-libraries.md).
