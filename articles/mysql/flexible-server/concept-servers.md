---
title: Concetti server-server flessibile per database di Azure per MySQL
description: Questo argomento fornisce considerazioni e linee guida per l'uso del server flessibile database di Azure per MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7217817e9add6214c2da8362a2769cad0c2cf330
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939315"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Concetti relativi ai server nel database di Azure per MySQL server flessibili (anteprima)

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo fornisce considerazioni e linee guida per l'uso dei server flessibili di database di Azure per MySQL.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Che cos'è un server flessibile per database di Azure per MySQL?

Il server flessibile database di Azure per MySQL è un servizio di database completamente gestito che esegue la versione community di MySQL. In generale, il servizio è progettato per fornire flessibilità e personalizzazioni di configurazione in base ai requisiti dell'utente. È lo stesso costrutto di server MySQL con cui probabilmente si ha familiarità nell'ambiente locale. In particolare, il server flessibile è gestito, offre prestazioni predefinite, una migliore gestibilità e controllo del server ed espone accesso e funzionalità a livello di server.

Un server flessibile per database di Azure per MySQL:

- Viene creato all'interno di una sottoscrizione di Azure.
- È la risorsa madre per i database.
- Consente la configurazione di MySQL esposta tramite i parametri del server (collegamento ai concetti relativi ai parametri del server).
- Esegue backup automatici e supporta i ripristini temporizzati.
- Fornisce uno spazio dei nomi per i database.
- È un contenitore con semantica di lunga durata: l'eliminazione di un server comporta l'eliminazione dei database in esso contenuti.
- Colloca risorse in un'area.
- Supporto per la pianificazione della manutenzione del server fornita dal cliente
- Possibilità di distribuire server flessibili in una configurazione con ridondanza della zona per migliorare la disponibilità elevata
- Fornisce un'integrazione della rete virtuale per l'accesso al server di database
- Fornisce un modo per ridurre i costi sospendendo il server flessibile quando non è in uso
- Fornisce l'ambito per i criteri di gestione applicati ai database: account di accesso, firewall, utenti, ruoli, configurazioni e così via.
- È attualmente supportato con la versione MySQL 5,7. Per altre informazioni, vedere la pagina relativa alle [versioni supportate del motore di database di Azure per MySQL](./concepts-supported-versions.md).

All'interno di un server di database di Azure per MySQL flessibile, è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o per creare più database per condividere le risorse. I prezzi sono strutturati per server, in base alla configurazione del livello di calcolo, Vcore e archiviazione (GB). Per altre informazioni, vedere [calcolo e archiviazione](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Arrestare/avviare un server flessibile per database di Azure per MySQL

Il server flessibile database di Azure per MySQL offre la possibilità di **arrestare** il server quando non è in uso e di **avviare** il server quando si riprende l'attività. Questa operazione viene essenzialmente eseguita per ridurre i costi dei server di database e pagare solo per la risorsa quando è in uso. Questa operazione diventa ancora più importante per i carichi di lavoro di sviluppo e test e quando si usa solo il server per una parte del giorno. Quando si arresta il server, tutte le connessioni attive verranno eliminate. In seguito, quando si vuole riportare online il server, è possibile usare l' [portale di Azure](how-to-stop-start-server-portal.md) o l'interfaccia della riga di comando.

Quando il server si trova nello stato **interrotto** , il calcolo del server non viene fatturato. Tuttavia, lo spazio di archiviazione continua a essere fatturato in quanto l'archiviazione del server rimane per assicurarsi che i file di dati siano disponibili quando il server viene riavviato.

> [!IMPORTANT]
> Quando si **Arresta** il server, rimane in tale stato per i 7 giorni successivi in un'estensione. Se non viene **avviata** manualmente durante questo periodo di tempo, il server verrà avviato automaticamente alla fine dei 7 giorni. Se non si utilizza il server, è possibile scegliere di **arrestarlo** di nuovo.

Nel momento in cui il server viene arrestato, non è possibile eseguire alcuna operazione di gestione sul server. Per modificare le impostazioni di configurazione nel server, sarà necessario [avviare il server](how-to-stop-start-server-portal.md). Vedere le [limitazioni relative a arresto/avvio](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Gestione di un server

È possibile gestire il server flessibile database di Azure per MySQL usando il [portale di Azure](./quickstart-create-server-portal.md) o l'interfaccia della riga di comando di [Azure](./quickstart-create-server-cli.md).

## <a name="next-steps"></a>Passaggi successivi

-   Informazioni su [create server](./quickstart-create-server-portal.md)
-   Informazioni sul [monitoraggio e sugli avvisi](./how-to-alert-on-metric.md)

