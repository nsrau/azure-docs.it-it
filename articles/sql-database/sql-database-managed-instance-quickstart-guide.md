---
title: 'Guida introduttiva: Istanza gestita di database SQL di Azure | Microsoft Docs'
description: Informazioni su come iniziare rapidamente a usare il database SQL di Azure - Istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: ec0007e2d53a3fd3cae158375b696379d923b4b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447778"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introduzione all'istanza gestita di database SQL di Azure

L'opzione di distribuzione [Istanza gestita](sql-database-managed-instance-index.yml) crea un database con quasi il 100% di compatibilità con il più recente motore di database SQL Server in locale (Enterprise Edition), fornendo un'implementazione della [rete virtuale](../virtual-network/virtual-networks-overview.md) nativa che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server in locale. In questo articolo verrà illustrato come configurare e creare rapidamente un'istanza gestita ed eseguire la migrazione dei database.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

Le guide introduttive seguenti consentono di creare rapidamente un'istanza gestita, configurare una macchina virtuale o una connessione VPN da punto a sito per l'applicazione client e ripristinare un database nella nuova istanza gestita usando un file `.bak`.

### <a name="configure-environment"></a>Configurare l'ambiente

Come primo passaggio, è necessario creare la prima istanza gestita con l'ambiente di rete in cui verrà inserita, nonché abilitare la connessione dal computer o dalla macchina virtuale in cui vengono eseguite le query sull'istanza gestita. È possibile usare le guide seguenti:

- [Creare un'istanza gestita usando il portale di Azure](sql-database-managed-instance-get-started.md). Nel portale di Azure è possibile configurare i parametri necessari (nome utente/password, numero di core e spazio di archiviazione massimo) e creare automaticamente l'ambiente di rete di Azure senza la necessità di conoscere i dettagli della rete e i requisiti dell'infrastruttura. Assicurarsi solo di avere un [tipo di sottoscrizione](sql-database-managed-instance-resource-limits.md#supported-subscription-types) attualmente autorizzato a creare un'istanza gestita. Se si vuole usare o personalizzare una propria rete, vedere come [configurare una rete virtuale esistente per Istanza gestita di database SQL di Azure](sql-database-managed-instance-configure-vnet-subnet.md) o [creare una rete virtuale per Istanza gestita di database SQL di Azure](sql-database-managed-instance-create-vnet-subnet.md).
- Un'istanza gestita viene creata nella propria rete virtuale senza un endpoint pubblico. Per l'accesso dell'applicazione client, è possibile **creare una macchina virtuale nella stessa rete virtuale (subnet diversa)** o **creare una connessione VPN da punto a sito nella rete virtuale dal computer client** usando una di queste guide introduttive:

  - Creare una [macchina virtuale di Azure nella rete virtuale dell'istanza gestita](sql-database-managed-instance-configure-vm.md) per la connettività dell'applicazione client, incluso SQL Server Management Studio.
  - Configurare la [connessione VPN da punto a sito nell'istanza gestita](sql-database-managed-instance-configure-p2s.md) dal computer client in cui sono installati SQL Server Management Studio e altre applicazioni di connettività client. Questa è l'altra delle due opzioni per la connettività all'istanza gestita e alla relativa rete virtuale.

  > [!NOTE]
  > È anche possibile usare ExpressRoute o una connessione da sito a sito dalla rete locale, ma questi approcci esulano dall'ambito di queste guide introduttive.

### <a name="migrate-your-databases"></a>Eseguire la migrazione dei database

Dopo aver creato un'istanza gestita e configurato l'accesso, è possibile avviare la migrazione dei database da SQL Server locale o dalle macchine virtuali di Azure. La migrazione non riesce se il database di origine da migrare contiene alcune funzionalità non supportate. Per evitare errori e controllare la compatibilità, è possibile installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), che analizzerà i database in SQL Server per rilevare eventuali errori che potrebbero impedire la migrazione a un'istanza gestita, ad esempio la presenza di [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) o di più file di log. Se questi problemi vengono risolti, i database sono pronti per la migrazione a un'istanza gestita. [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) è un altro strumento utile che consente di registrare il carico di lavoro in SQL Server e di riprodurlo in un'istanza gestita per determinare se la migrazione a un'istanza gestita genererà eventuali problemi di prestazioni.

Una volta sicuri di poter migrare il database in un'istanza gestita, è possibile usare le funzionalità di ripristino native di SQL Server per ripristinare un database in un'istanza gestita da un file `.bak`. È possibile usare questo metodo per eseguire la migrazione dei database dal motore di database di SQL Server installato in locale o in una macchina virtuale di Azure. Per una guida introduttiva, vedere [Eseguire il ripristino da un backup in un'istanza gestita](sql-database-managed-instance-get-started-restore.md). In questa guida introduttiva si esegue il ripristino da un file con estensione `.bak` archiviato nell'archivio BLOB di Azure usando il comando Transact-SQL `RESTORE`.

> [!TIP]
> Per usare il comando Transact-SQL `BACKUP` per creare un backup del database nell'archivio BLOB di Azure, vedere [Backup di SQL Server nell'URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Queste guide introduttive consentono di creare, configurare e ripristinare rapidamente un backup del database in un'istanza gestita. In alcuni scenari può essere necessario personalizzare o automatizzare la distribuzione delle istanze gestite e dell'ambiente di rete richiesto. Questi scenari vengono descritti di seguito.

## <a name="customize-network-environment"></a>Personalizzare l'ambiente di rete

Anche se è possibile configurare automaticamente la rete virtuale e la subnet durante la creazione dell'istanza con il [portale di Azure](sql-database-managed-instance-get-started.md), è consigliabile crearle prima di avviare la creazione di istanze gestite, perché in questo modo è possibile configurare i parametri necessari. Il modo più semplice per creare e configurare l'ambiente di rete consiste nell'usare il modello di [distribuzione Azure Resource Manager](sql-database-managed-instance-create-vnet-subnet.md), che crea e configura la rete e la subnet in cui verrà inserita l'istanza. È sufficiente premere il pulsante Distribuisci di Azure Resource Manager e popolare il modulo con i parametri.

In alternativa, per automatizzare la creazione della rete è possibile usare uno [script di PowerShell](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/).

In alternativa, per automatizzare la creazione della rete è possibile usare uno [script di PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/).

Se sono già disponibili una rete virtuale e una subnet in cui si vuole distribuire l'istanza gestita, è necessario assicurarsi che soddisfino i [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Usare questo [script di PowerShell per verificare la corretta configurazione della subnet](sql-database-managed-instance-configure-vnet-subnet.md). Oltre a convalidare la rete e a segnalare i problemi, lo script indicherà le modifiche da apportare e offrirà anche la possibilità di eseguirle automaticamente nella rete virtuale/subnet. Eseguire questo script se non si vuole configurare manualmente la rete virtuale/subnet. È anche possibile eseguirlo dopo qualsiasi riconfigurazione significativa dell'infrastruttura di rete. Se si vuole creare e configurare la propria rete, leggere l'[architettura della connettività](sql-database-managed-instance-connectivity-architecture.md) e questa [guida per la creazione e la configurazione di un ambiente dell'istanza gestita](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Automazione della creazione di un'istanza gestita

 Se non è stato creato l'ambiente di rete come descritto nel passaggio precedente, è possibile farlo automaticamente nel portale di Azure. L'unico svantaggio è che l'ambiente verrà configurato con alcuni parametri predefiniti che non sarà possibile cambiare in seguito. In alternativa, è possibile usare:

- [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)
- [PowerShell con il modello di Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Interfaccia della riga di comando di Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).
- [Modello di Resource Manager](sql-database-single-database-get-started-template.md)

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Migrazione a un'istanza gestita con tempo di inattività minimo

Gli articoli di queste guide introduttive consentono di configurare rapidamente un'istanza gestita e di spostare i database con la funzionalità nativa `RESTORE`. Con la funzionalità `RESTORE` nativa, tuttavia, è necessario attendere che i database vengano ripristinati e copiati nell'archivio BLOB di Azure, se non sono già presenti in tale archivio. Ciò comporta tempi di inattività dell'applicazione, in particolare per i database di grandi dimensioni. Per spostare il database di produzione, usare il [Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) per eseguire la migrazione del database con il tempo di inattività minimo. Servizio Migrazione del database ottiene questo risultato effettuando il push incrementale delle modifiche apportate al database di origine nel database dell'istanza gestita da ripristinare. In questo modo, è possibile spostare rapidamente l'applicazione dal database di origine a quello di destinazione con il tempo di inattività minimo.

## <a name="next-steps"></a>Passaggi successivi

- Trovare un [elenco dettagliato delle funzionalità supportate in Istanza gestita](sql-database-features.md), oltre a [informazioni dettagliate e problemi noti](sql-database-managed-instance-transact-sql-information.md).
- Scoprire le [caratteristiche tecniche di Istanza gestita](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Per altre procedure avanzate, vedere [Come usare un'istanza gestita nel database SQL di Azure](sql-database-howto-managed-instance.md).
- [Identificare l'istanza gestita/database SQL di Microsoft Azure idoneo per il database locale](/sql/dma/dma-sku-recommend-sql-db/).