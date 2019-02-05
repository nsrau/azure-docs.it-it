---
title: 'Guida introduttiva: Istanza gestita di Database SQL di Azure | Microsoft Docs'
description: Informazioni su come iniziare rapidamente a usare Database SQL di Azure - Istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468173"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introduzione a Istanza gestita di database SQL di Azure

[Istanza gestita di Database SQL di Azure](sql-database-managed-instance-index.yml) è una versione PaaS completamente gestita di SQL Server ospitata nel cloud di Azure e inserita nella rete virtuale dell'organizzazione con l'indirizzo IP privato. In questa sezione verrà illustrato come configurare e creare rapidamente Istanza gestita ed eseguire la migrazione dei database.

## <a name="quickstart-overview"></a>Panoramica della guida introduttiva

Questa sezione contiene una panoramica degli articoli disponibili da consultare per iniziare rapidamente a usare istanze gestite. Il modo più semplice per creare la prima istanza gestita consiste nell'usare il [portale di Azure](sql-database-managed-instance-get-started.md), in cui è possibile configurare i parametri necessari (nome utente/password, numero di core, dimensioni massime di archiviazione) e creare automaticamente l'ambiente di rete di Azure senza la necessità di conoscere i dettagli della rete e i requisiti dell'infrastruttura. Assicurarsi solo di avere un [tip di sottoscrizione](sql-database-managed-instance-resource-limits.md#supported-subscription-types) autorizzato a creare l'istanza.

Se si vuole usare o personalizzare la propria rete, vedere come [configurare l'ambiente di rete](#configure-network-environment) per Istanza gestita.

Per connettersi all'istanza gestita creata, è necessario usare uno degli approcci seguenti:

* Creare una [macchina virtuale di Azure](sql-database-managed-instance-configure-vm.md) e installare SQL Server Management Studio, oltre ad altre app che possano essere usate per accedere all'istanza gestita, in una subnet della stessa rete virtuale in cui si trova l'istanza gestita. La macchina virtuale non può trovarsi nella stessa subnet delle istanze gestite.
* Configurare una [connessione da punto a sito](sql-database-managed-instance-configure-p2s.md) nel computer che consentirà di aggiungerlo alla rete virtuale in cui si trova l'istanza gestita e di usare quest'ultima come qualsiasi altra istanza di SQL Server nella rete.

In alternativa, sarebbe possibile usare ExpressRoute o una connessione da sito a sito dalla rete locale, ma questi approcci esulano dall'ambito di queste guide introduttive.

Dopo aver creato un'istanza gestita e configurato l'accesso, è possibile avviare la migrazione dei database memorizzati in SQL Server locale o nelle macchine virtuali di Azure. Tenere presente che la migrazione non verrà eseguita se il database di origine da spostare contiene alcune funzionalità non supportate. Per evitare errori e controllare la compatibilità, è possibile installare [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), che analizzerà i database in SQL Server per rilevare eventuali errori che potrebbero impedire la migrazione a Istanza gestita, ad esempio la presenza di FileStream o di più file di log. Se questi problemi vengono risolti, i database sono pronti per il passaggio a Istanza gestita. [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) è un altro strumento utile che consente di registrare il carico di lavoro in SQL Server e di riprodurlo in Istanza gestita per determinare se la migrazione a Istanza gestita genererà eventuali problemi di prestazioni.

Dopo aver verificato di poter eseguire la migrazione a Istanza gestita, è possibile usare la funzionalità [Native RESTORE](sql-database-managed-instance-get-started-restore.md), che consente di creare un backup del database tramite il comando Transact-SQL, caricarlo in un'archiviazione BLOB di Azure e ripristinarlo con il comando Transact-SQL.

Queste guide introduttive consentono di configurare, creare e inserire rapidamente i database nelle istanze gestite. In alcuni scenari può essere necessario personalizzare o automatizzare la distribuzione di Istanza gestita e dell'ambiente di rete richiesto. Questi scenari vengono descritti di seguito.

## <a name="customizing-network-environment"></a>Personalizzare l'ambiente di rete

Anche se è possibile configurare automaticamente la rete virtuale e la subnet durante la creazione dell'istanza con il [portale di Azure](sql-database-managed-instance-get-started.md), è consigliabile crearle prima di avviare la creazione di istanze gestite, perché in questo modo è possibile configurare i parametri necessari. Il modo più semplice per creare e configurare l'ambiente di rete consiste nell'usare il modello di [distribuzione Azure Resource Manager](sql-database-managed-instance-create-vnet-subnet.md), che crea e configura la rete e la subnet in cui verrà inserita l'istanza. È sufficiente premere il pulsante Distribuisci di Azure Resource Manager e popolare il modulo con i parametri. In alternativa, per automatizzare la creazione della rete è possibile usare uno [script di PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/).

Se sono già disponibili una rete virtuale e una subnet in cui si vuole distribuire l'istanza gestita, è necessario assicurarsi che soddisfino in [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Usare questo [script di PowerShell per verificare la corretta configurazione della subnet](sql-database-managed-instance-configure-vnet-subnet.md). Oltre a convalidare la rete e a segnalare i problemi, lo script indicherà le modifiche da apportare e offrirà anche la possibilità di eseguirle automaticamente nella rete virtuale/subnet. Eseguire questo script se non si vuole configurare manualmente la rete virtuale/subnet ed è consigliabile eseguirlo anche dopo qualsiasi riconfigurazione significativa dell'infrastruttura di rete. Se si vuole creare e configurare manualmente la rete, vedere la [documentazione di Istanza gestita](sql-database-managed-instance-connectivity-architecture.md) e [questa guida](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Automazione della creazione di Istanza gestita

 Se non è stato creato l'ambiente di rete come descritto nel passaggio precedente, è possibile farlo automaticamente nel portale di Azure. L'unico svantaggio è che l'ambiente verrà configurato con alcuni parametri predefiniti che non sarà possibile cambiare in seguito. In alternativa, è possibile usare [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [PowerShell con il modello di Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) oppure l'[interfaccia della riga di comando di Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Migrazione a Istanza gestita con tempo di inattività minimo

Gli articoli di queste guide introduttive consentono di configurare rapidamente Istanza gestita e di spostare i database. Tuttavia, con il ripristino nativo sarà necessario aspettare che i database vengano ripristinati, con un conseguente tempo di inattività dell'applicazione, soprattutto nel caso di database di grandi dimensioni. Se si intende spostare il database di produzione, per eseguire la migrazione è consigliabile scegliere un metodo più efficace che garantisca un tempo di inattività minimo. Il [Servizio Migrazione del database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) consente di eseguire la migrazione del database con un tempo di inattività minimo effettuando il push incrementale delle modifiche apportate nel database di origine verso il database da ripristinare nell'istanza gestita. In questo modo, è possibile spostare rapidamente l'applicazione dal database di origine a quello di destinazione con il tempo di inattività minimo.

## <a name="next-steps"></a>Passaggi successivi

* Trovare un [elenco dettagliato delle funzionalità supportate in Istanza gestita](sql-database-features.md), oltre a [informazioni dettagliate e problemi noti](sql-database-managed-instance-transact-sql-information.md). 
* Scoprire le [caratteristiche tecniche di Istanza gestita](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Trovare esercitazioni più avanzate nella [sezione di procedure](sql-database-howto-managed-instance.md). 
