---
title: Informazioni di riferimento sul contenuto dell'avvio rapido
titleSuffix: Azure SQL Managed Instance
description: "Informazioni di riferimento per il contenuto che consente di iniziare a usare l'istanza gestita di SQL di Azure. "
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: 5ed88514650b9931a52e5f155abc34fbf734a3b7
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310768"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Introduzione all'istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

L'[istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md) crea un database con quasi il 100% di compatibilità con il più recente motore di database SQL Server (Enterprise Edition), fornendo un'implementazione della [rete virtuale](../../virtual-network/virtual-networks-overview.md) nativa che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server esistenti.

Questo articolo fornisce informazioni di riferimento sul contenuto che spiega come configurare e creare rapidamente un'istanza gestita di SQL ed eseguire la migrazione dei database.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

Le guide di avvio rapido seguenti consentono di creare rapidamente un'istanza gestita di SQL, configurare una macchina virtuale o una connessione VPN da punto a sito per l'applicazione client e ripristinare un database nella nuova istanza gestita di SQL usando un file `.bak`.

### <a name="configure-environment"></a>Configurare l'ambiente

Come primo passaggio, è necessario creare la prima istanza gestita di SQL con l'ambiente di rete in cui verrà collocata, nonché abilitare la connessione dal computer o dalla macchina virtuale in cui vengono eseguite le query sull'istanza gestita di SQL. È possibile usare le guide seguenti:

- [Creare un'istanza gestita di SQL usando il portale di Azure](instance-create-quickstart.md). Nel portale di Azure è possibile configurare i parametri necessari (nome utente/password, numero di core e spazio di archiviazione massimo) e creare automaticamente l'ambiente di rete di Azure senza la necessità di conoscere i dettagli della rete e i requisiti dell'infrastruttura. Assicurarsi solo di avere un [tipo di sottoscrizione](resource-limits.md#supported-subscription-types) attualmente autorizzato a creare un'istanza gestita di SQL. Se si vuole usare una rete personale o personalizzarne una, vedere come [configurare una rete virtuale esistente per un'istanza gestita di SQL di Azure](vnet-existing-add-subnet.md) o [creare una rete virtuale per un'istanza gestita di SQL di Azure](virtual-network-subnet-create-arm-template.md).
- Un'istanza gestita di SQL viene creata nella propria rete virtuale senza un endpoint pubblico. Per l'accesso dell'applicazione client, è possibile **creare una macchina virtuale nella stessa rete virtuale (subnet diversa)** o **creare una connessione VPN da punto a sito nella rete virtuale dal computer client** usando una di queste guide introduttive:
  - Abilitare l'[endpoint pubblico](public-endpoint-configure.md) nell'istanza gestita di SQL per accedere ai dati direttamente dall'ambiente.
  - Creare una [macchina virtuale di Azure nella rete virtuale dell'istanza gestita di SQL](connect-vm-instance-configure.md) per la connettività dell'applicazione client, incluso SQL Server Management Studio.
  - Configurare la [connessione VPN da punto a sito nell'istanza gestita di SQL](point-to-site-p2s-configure.md) dal computer client in cui sono installati SQL Server Management Studio e altre applicazioni di connettività client. Questa è l'altra delle due opzioni per la connettività all'istanza gestita di SQL e alla relativa rete virtuale.

  > [!NOTE]
  > - È anche possibile usare ExpressRoute o una connessione da sito a sito dalla rete locale, ma questi approcci esulano dall'ambito di queste guide introduttive.
  > - Se si modifica il periodo di conservazione da 0 (conservazione illimitata) a qualsiasi altro valore, tenere presente che la conservazione verrà applicata solo ai log scritti dopo la modifica del valore di conservazione (i log scritti durante il periodo in cui la conservazione è stata impostata su illimitata vengono conservati, anche dopo l'abilitazione della conservazione).

In alternativa alla creazione manuale dell'istanza gestita di SQL, è possibile usare [PowerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell con modello di Resource Manager](scripts/create-powershell-azure-resource-manager-template.md) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) per creare script e automatizzare questo processo.

### <a name="migrate-your-databases"></a>Eseguire la migrazione dei database

Dopo aver creato un'istanza gestita di SQL e configurato l'accesso, è possibile avviare la migrazione dei database di SQL Server. La migrazione potrebbe non riuscire se il database di origine da migrare contiene alcune funzionalità non supportate. Per evitare errori e controllare la compatibilità, è possibile installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), che analizzerà i database in SQL Server per rilevare eventuali errori che potrebbero impedire la migrazione all'istanza gestita di SQL, ad esempio la presenza di [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) o di più file di log. Se questi problemi vengono risolti, i database sono pronti per la migrazione a un'istanza gestita di SQL. [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) è un altro strumento utile che consente di registrare il carico di lavoro in SQL Server e di riprodurlo in un'istanza gestita di SQL per determinare se la migrazione a un'istanza gestita di SQL genererà eventuali problemi di prestazioni.

Una volta sicuri di poter procedere alla migrazione del database a un'istanza gestita di SQL, è possibile usare le funzionalità di ripristino native di SQL Server per ripristinare un database in un'istanza gestita di SQL da un file `.bak`. È possibile usare questo metodo per eseguire la migrazione dei database dal motore di database di SQL Server installato in locale o da macchine virtuali di Azure. Per la guida di avvio rapido, vedere [Eseguire il ripristino da un backup in un'istanza gestita di SQL](restore-sample-database-quickstart.md). In questa guida introduttiva si esegue il ripristino da un file con estensione `.bak` archiviato nell'archivio BLOB di Azure usando il comando Transact-SQL `RESTORE`.

> [!TIP]
> Per usare il comando Transact-SQL `BACKUP` per creare un backup del database nell'archivio BLOB di Azure, vedere [Backup di SQL Server nell'URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Queste guide di avvio rapido consentono di creare, configurare e ripristinare rapidamente un backup del database in un'istanza gestita di SQL. In alcuni scenari può essere necessario personalizzare o automatizzare la distribuzione dell'istanza gestita di SQL e dell'ambiente di rete necessario. Questi scenari vengono descritti di seguito.

## <a name="customize-network-environment"></a>Personalizzare l'ambiente di rete

Anche se è possibile configurare automaticamente la rete virtuale e la subnet durante la creazione dell'istanza con il [portale di Azure](instance-create-quickstart.md), è consigliabile crearle prima di avviare la creazione delle istanze nell'istanza gestita di SQL, perché in questo modo è possibile configurare i parametri necessari. Il modo più semplice per creare e configurare l'ambiente di rete consiste nell'usare il modello di [distribuzione Azure Resource Manager](virtual-network-subnet-create-arm-template.md), che crea e configura la rete e la subnet in cui verrà collocata l'istanza. È sufficiente premere il pulsante Distribuisci di Azure Resource Manager e popolare il modulo con i parametri.

In alternativa, per automatizzare la creazione della rete è possibile usare uno [script di PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/).

Se sono già disponibili una rete virtuale e una subnet in cui si vuole distribuire l'istanza gestita di SQL, è necessario assicurarsi che soddisfino i [requisiti di rete](connectivity-architecture-overview.md#network-requirements). Usare questo [script di PowerShell per verificare la corretta configurazione della subnet](vnet-existing-add-subnet.md). Oltre a convalidare la rete e a segnalare i problemi, lo script indicherà le modifiche da apportare e offrirà anche la possibilità di eseguirle automaticamente nella rete virtuale/subnet. Eseguire questo script se non si vuole configurare manualmente la rete virtuale/subnet. È anche possibile eseguirlo dopo qualsiasi riconfigurazione significativa dell'infrastruttura di rete. Se si vuole creare e configurare la propria rete, leggere le informazioni sull'[architettura della connettività](connectivity-architecture-overview.md) e questa [guida per la creazione e la configurazione di un ambiente dell'istanza gestita di SQL](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Eseguire la migrazione a un'istanza gestita di SQL

Le guide di avvio rapido indicate in precedenza consentono di configurare rapidamente un'istanza gestita di SQL e di spostare i database con la funzionalità `RESTORE` nativa. Si tratta di un buon punto di partenza per implementare modelli di verifica rapidi e accertarsi che la soluzione possa funzionare in un'istanza gestita.

Tuttavia, per eseguire la migrazione del database di produzione o anche dei database di sviluppo/test che si vuole usare per alcuni test delle prestazioni, è necessario prendere in considerazione l'uso di alcune tecniche aggiuntive, ad esempio:

- Test delle prestazioni: è consigliabile misurare le metriche delle prestazioni baseline nell'istanza di SQL Server di origine e confrontarle con le metriche delle prestazioni nell'istanza gestita di SQL di destinazione in cui è stata eseguita la migrazione del database. Vedere altre informazioni sulle [procedure consigliate per il confronto delle prestazioni](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migrazione online: con la funzionalità `RESTORE` nativa descritta in questo articolo è necessario attendere che i database vengano ripristinati e copiati nell'archivio BLOB di Azure, se non sono già presenti in tale archivio. Ciò comporta tempi di inattività dell'applicazione, in particolare per i database di grandi dimensioni. Per spostare il database di produzione, usare il [Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) per eseguire la migrazione del database con il tempo di inattività minimo. Servizio Migrazione del database ottiene questo risultato effettuando il push incrementale delle modifiche apportate al database di origine nel database dell'istanza gestita di SQL da ripristinare. In questo modo, è possibile spostare rapidamente l'applicazione dal database di origine a quello di destinazione con un tempo di inattività minimo.

Vedere altre informazioni sul [processo di migrazione consigliato](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Passaggi successivi

- Trovare un [elenco generale delle funzionalità supportate nell'istanza gestita di SQL](../database/features-comparison.md), oltre a [informazioni dettagliate e problemi noti](transact-sql-tsql-differences-sql-server.md).
- Informazioni sulle [caratteristiche tecniche dell'istanza gestita di SQL](resource-limits.md#service-tier-characteristics).
- Per altre procedure avanzate, vedere [Come usare un'istanza gestita di SQL](how-to-content-reference-guide.md).
- [Identificare lo SKU di istanza gestita di SQL più adatto per il database locale](/sql/dma/dma-sku-recommend-sql-db/).
