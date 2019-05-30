---
title: Repliche in lettura in Database di Azure per MySQL.
description: Questo articolo descrive le repliche in lettura per Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/30/2019
ms.openlocfilehash: 2d70e1b5434b2fb263d1f4587888d4758fac2828
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225368"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliche in lettura in Database di Azure per MySQL

La funzionalità di lettura della replica consente di replicare i dati da un Database di Azure per il server MySQL a un server di sola lettura. È possibile eseguire la replica dal server master per fino a cinque repliche. Le repliche vengono aggiornate in modo asincrono utilizzando la tecnologia basata sulla posizione della replica di file registro binario nativo (binlog) del motore di MySQL. Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL).

> [!IMPORTANT]
> È possibile creare una replica di lettura nella stessa area del server master o in altre aree di Azure di propria scelta. La replica tra aree è attualmente in anteprima pubblica.

Le repliche sono nuovi server che vengono gestiti Analogamente alla normale Database di Azure per MySQL Server. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Per altre informazioni sulle funzioni di replica di MySQL e i problemi relativi, vedere la [documentazione sulle repliche di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura

La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

La funzionalità di lettura della replica utilizza la replica asincrona di MySQL. La funzionalità non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

Lettura delle repliche possono migliorare il piano di ripristino di emergenza. Se è presente un'emergenza locale e il server master non è disponibile, è possibile indirizzare il carico di lavoro a una replica in un'altra area. A tale scopo, prima di tutto consentire la replica di accettare operazioni di scrittura tramite la funzione di replica irreversibile. È quindi possibile reindirizzare l'applicazione, aggiornare la stringa di connessione. Altre informazioni, vedere la [arrestare la replica](#stop-replication) sezione.

## <a name="create-a-replica"></a>Creare una replica

Se un server master non contiene alcun server di replica esistente, il master prima di tutto verrà riavviata per predisporsi per la replica.

Quando si avvia il flusso di lavoro di replica create, viene creato un Database di Azure vuoto per il server MySQL. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per la creazione dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

Ogni replica è abilitata per l'archiviazione [aumento automatico delle dimensioni](concepts-pricing-tiers.md#storage-auto-grow). La funzionalità aumento consente alla replica di allinearsi con i dati replicati ad esso ed evitare un'interruzione della replica causata da errori di memoria insufficiente.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica

Quando si crea una replica, questa non eredita le regole del firewall o l'endpoint del servizio rete virtuale del server master. Queste regole devono essere configurate in modo indipendente per la replica.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente che sono disponibili nel server master.

È possibile connettersi alla replica con il nome host e un account utente validi, come si farebbe in un normale Database di Azure per il server MySQL. Per un server denominato **myreplica** con il nome utente amministratore **myadmin**, è possibile connettersi alla replica tramite la CLI di mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica

Database di Azure per MySQL offre il **intervallo di replica in pochi secondi** delle metriche in Monitoraggio di Azure. Questa metrica è disponibile per solo le repliche.

Questa metrica viene calcolata usando il `seconds_behind_master` metrica disponibile in MySQL `SHOW SLAVE STATUS` comando.

Impostare un avviso per informare l'utente quando l'intervallo di replica raggiunge un valore che non è accettabile per il carico di lavoro.

## <a name="stop-replication"></a>Arrestare la replica

È possibile scegliere di arrestare la replica tra un master e una replica. Dopo l'arresto della replica tra un server master e una replica in lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non è aggiornato con il server master.

Quando si sceglie di arrestare la replica a una replica, perde tutti i collegamenti al relativo schema precedente e le altre repliche. Non vi è alcun failover automatico tra un master e la relativa replica.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

### <a name="pricing-tiers"></a>Piani tariffari

Le repliche in lettura sono attualmente disponibili solo nei livelli di prezzo per utilizzo generico e ottimizzato per la memoria.

### <a name="master-server-restart"></a>Riavvio del server master

Quando si crea una replica per un master che non esistono repliche esistenti, il master prima di tutto verrà riavviata per predisporsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

### <a name="new-replicas"></a>Nuove repliche

Una lettura della replica viene creata come un nuovo Database di Azure per il server MySQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica

Una replica viene creata usando la stessa configurazione server del master. Dopo aver creata una replica, diverse impostazioni possono essere modificate in modo indipendente dal server master: versione del motore di MySQL, periodo di conservazione dei backup, archiviazione, Vcore e generazione di calcolo. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

### <a name="stopped-replicas"></a>Repliche arrestate

Se si arresta la replica tra un server master e una replica di lettura, la replica interrotta diventa un server autonomo che accetta letture e scritture. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-master-and-standalone-servers"></a>Master eliminato e server autonomi

Quando un server master viene eliminato, la replica viene arrestata per tutte le repliche in lettura. Queste repliche diventano server autonomi. Il server master viene eliminato.

### <a name="user-accounts"></a>Account utente

Gli utenti del server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente disponibili nel server master.

### <a name="server-parameters"></a>Parametri del server

Per impedire che diventi sincronizzati i dati ed evitare potenziali perdite di dati o danneggiamenti, alcuni parametri di server vengono bloccate vengano aggiornati quando uso leggere repliche.

I seguenti parametri del server sono bloccati nel server master e di replica:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Il [ `event_scheduler` ](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parametro è bloccato nei server di replica. 

### <a name="other"></a>Altri

- Gli identificatori di transazione globale (GTID) non sono supportati.
- La creazione di una replica di replica non è supportata.
- Le tabelle in memoria potrebbero causare la perdita di sincronia delle repliche. Si tratta di una limitazione della tecnologia di replica di MySQL. Per altre informazioni, vedere la [documentazione di riferimento di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
- Verificare che le tabelle di server master dispongano di chiavi primarie. La mancanza di chiavi primarie può comportare una latenza di replica tra il server master e le repliche.
- Esaminare l'elenco completo delle limitazioni di replica di MySQL nella [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare e gestire le repliche in lettura tramite il portale di Azure](howto-read-replicas-portal.md)
- Leggere le informazioni su come [creare e gestire repliche in lettura tramite l'interfaccia della riga di comando di Azure](howto-read-replicas-cli.md)
