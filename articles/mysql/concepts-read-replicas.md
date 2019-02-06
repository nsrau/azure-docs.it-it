---
title: Repliche in lettura in Database di Azure per MySQL.
description: Questo articolo descrive le repliche in lettura per Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 1/23/2019
ms.openlocfilehash: eca67cb70756dd1184bd3a66c2582743c8baa8fd
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903758"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliche in lettura in Database di Azure per MySQL

> [!IMPORTANT]
> La funzionalità di replica in lettura è in anteprima pubblica.

La funzionalità di replica in lettura consente di replicare i dati da server Database di Azure per MySQL (master) in un massimo di cinque server di sola lettura (repliche) nella stessa area di Azure. Le repliche di sola lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica basata su posizione del file di registro binario nativo, o binlog, del motore MySQL. Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL).

Le repliche create in Database di Azure per MySQL sono nuovi server che possono essere gestiti nello stesso modo dei server MySQL normali/autonomi. Per ogni replica di lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese. 


Per altre informazioni sulle funzioni di replica di MySQL e i problemi relativi, vedere la [documentazione sulle repliche di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Quando usare le repliche in lettura

Le applicazioni e i carichi di lavoro caratterizzati da un'intensa attività di lettura possono essere servite dalle repliche di sola lettura. Le repliche in lettura consentono di aumentare la quantità di capacità di lettura disponibile rispetto all'uso di un solo server per lettura e scrittura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

### <a name="pricing-tiers"></a>Piani tariffari

Le repliche in lettura sono attualmente disponibili solo nei livelli di prezzo per utilizzo generico e ottimizzato per la memoria.

### <a name="master-server-restart"></a>Riavvio del server master

Durante questa anteprima, quando si crea una replica per un master che non ha repliche esistenti, il master si riavvia per prepararsi alla replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

### <a name="stopping-replication"></a>Arresto della replica

È possibile scegliere di arrestare la replica tra un server master e uno di replica. L'arresto della replica elimina la relazione di replica tra il server master e quello di replica.

Dopo che la replica è stata arrestata, il server di replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica nel momento in cui è stato avviato il comando "stop replication". Il server autonomo non è aggiornato con il server master. Questo server non può più essere trasformato in una replica.

### <a name="replicas-are-new-servers"></a>Le repliche sono nuovi server

Le repliche vengono create come nuovi server di Database di Azure per MySQL. I server esistenti non possono essere trasformati in repliche.

### <a name="replica-server-configuration"></a>Configurazione del server di replica

I server di replica vengono creati usando le stesse configurazioni server del master, incluse le seguenti:

- Piano tariffario
- Generazione di calcolo
- vCore
- Archiviazione
- Periodo di conservazione dei backup
- Opzione di ridondanza per il backup
- Versione del motore MySQL
- Regole del firewall

Dopo aver creato una replica, è possibile modificare il piano tariffario (tranne da e verso Basic), la generazione di calcolo, i vCore, l'archiviazione e la conservazione dei backup in modo indipendente dal server master.

### <a name="master-server-configuration"></a>Configurazione del server master

Se viene aggiornata la configurazione server di un master, ad esempio i vCore o l'archiviazione, anche la configurazione delle repliche deve essere aggiornata a valori uguali o maggiori. In caso contrario, il server di replica potrebbe non essere in grado di stare al passo con le modifiche apportate al master e di conseguenza potrebbe bloccarsi.

Le nuove regole del firewall aggiunte al server master dopo la creazione di un server di replica non vengono replicate nella replica. La replica deve essere aggiornata anche con questa nuova regola del firewall.

### <a name="deleting-the-master-server"></a>Eliminazione del server master

Quando un server master viene eliminato, la replica viene arrestata per tutte le repliche in lettura. Queste repliche diventano server autonomi. Il server master viene eliminato.

### <a name="user-accounts"></a>Account utente

Gli utenti del server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente disponibili nel server master.

### <a name="other"></a>Altri

- Gli identificatori di transazione globale (GTID) non sono supportati.
- La creazione di una replica di replica non è supportata.
- Le tabelle in memoria potrebbero causare la perdita di sincronia delle repliche. Si tratta di una limitazione della tecnologia di replica di MySQL. Per altre informazioni, vedere la [documentazione di riferimento di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
- La modifica del parametro [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) in un server master dopo la creazione di un server di replica può causare la perdita di sincronia della replica. Il server di replica non riconosce i diversi spazi di tabella.
- Esaminare l'elenco completo delle limitazioni di replica di MySQL nella [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare e gestire le repliche in lettura tramite il portale di Azure](howto-read-replicas-portal.md)
- Leggere le informazioni su come [creare e gestire repliche in lettura tramite l'interfaccia della riga di comando di Azure](howto-read-replicas-cli.md)
