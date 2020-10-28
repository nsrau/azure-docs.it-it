---
title: Modelli di Azure Resource Manager - Database SQL di Azure e Istanza gestita di SQL
description: Usare i modelli di Azure Resource Manager per creare e configurare il database SQL di Azure e l'istanza gestita di SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677260"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Modelli di Azure Resource Manager per il database SQL di Azure e l'istanza gestita di SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I modelli di Azure Resource Manager consentono di definire l'infrastruttura come codice e di distribuire le soluzioni nel cloud di Azure per Database SQL di Azure e Istanza gestita di SQL di Azure.

## <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/single-database)

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per il database SQL di Azure.

|Collegamento |Descrizione|
|---|---|
| [Database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Questo modello di Azure Resource Manager crea un database singolo nel database SQL di Azure e configura le regole del firewall IP a livello di server. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Questo modello di Azure Resource Manager crea un server per il database SQL di Azure. |
| [Pool elastico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Questo modello consente di distribuire un pool elastico e di assegnarvi i database. |
| [Gruppi di failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Questo modello crea due server, un database singolo e un gruppo di failover nel database SQL di Azure.|
| [Rilevamento delle minacce](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Questo modello consente di distribuire un server e un set di database con la funzionalità Rilevamento minacce abilitata e con un indirizzo di posta elettronica per gli avvisi per ogni database. Rilevamento minacce fa parte dell'offerta Advanced Threat Protection (ATP) SQL e fornisce un livello di sicurezza che risponde alle potenziali minacce su server e database.|
| [Controllo per Archiviazione BLOB di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Questo modello consente di distribuire un server con il controllo abilitato per la scrittura dei log di controllo in un'archiviazione BLOB. Il controllo per il database SQL di Azure consente di tenere traccia degli eventi di database e di registrarli in un log di controllo che può essere inserito nell'account di Archiviazione di Azure, nell'area di lavoro di OMS o in Hub eventi.|
| [Controllo per Hub eventi di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Questo modello consente di distribuire un server con il controllo abilitato per la scrittura dei log di controllo in un hub eventi esistente. Per inviare gli eventi di controllo ad Hub eventi, specificare le impostazioni di controllo con `Enabled` `State` e impostare `IsAzureMonitorTargetEnabled` su `true`. Configurare inoltre le impostazioni di diagnostica con la categoria di log `SQLSecurityAuditEvents` nel database `master` (per il controllo a livello del server). Il controllo consente di tenere traccia degli eventi di database e di eseguirne la scrittura in un log di controllo che può essere collocato nell'account di Archiviazione di Azure, nell'area di lavoro di OMS o in Hub eventi.|
| [App Web di Azure con database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Questo esempio crea un'app Web di Azure gratuita e un database in Database SQL di Azure nel livello di servizio "Basic".|
| [App Web di Azure e Cache Redis con database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Questo modello crea un'app Web, una cache Redis e un database nello stesso gruppo di risorse, quindi crea due stringhe di connessione nell'app Web per il database e la cache Redis.|
| [Importazione di dati da archiviazione BLOB con ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Questo modello di Azure Resource Manager crea un'istanza di Azure Data Factory V2 che copia i dati da Archiviazione BLOB di Azure a Database SQL.|
| [Cluster HDInsight con un database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Questo modello consente di creare un cluster HDInsight, un server SQL logico, un database e due tabelle. Viene usato nell'articolo [Usare Sqoop con Hadoop in HDInsight](../../hdinsight/hadoop/hdinsight-use-sqoop.md). |
| [App per la logica di Azure che esegue una stored procedure SQL in base a una pianificazione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Questo modello consente di creare un'app per la logica che eseguirà una stored procedure SQL in base a una pianificazione. Tutti gli argomenti per la procedura possono essere inseriti nella sezione del corpo del modello.|

## <a name="azure-sql-managed-instance"></a>[Istanza gestita di database SQL di Azure](#tab/managed-instance)

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Istanza gestita di SQL di Azure.

|Collegamento|Descrizione|
|---|---|
| [Istanza gestita di SQL in una nuova rete virtuale](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Questo modello di Azure Resource Manager crea una nuova rete virtuale di Azure configurata e un'istanza gestita al suo interno. |
| [Ambiente di rete per Istanza gestita di SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Questa distribuzione crea una rete virtuale di Azure configurata con due subnet, una dedicata alle istanze gestite e l'altra in cui è possibile inserire altre risorse, ad esempio VM, ambienti di Servizio App e così via. Questo modello crea un ambiente di rete correttamente configurato in cui è possibile distribuire istanze gestite. |
| [Istanza gestita di SQL con connessione da punto a sito](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Questa distribuzione crea una rete virtuale di Azure con due subnet, `ManagedInstance` e `GatewaySubnet`. L'istanza gestita di SQL verrà distribuita nella subnet ManagedInstance. Nella subnet `GatewaySubnet` verrà creato un gateway di rete virtuale configurato per la connessione VPN da punto a sito. |
| [Istanza gestita di SQL con macchina virtuale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Questa distribuzione crea una rete virtuale di Azure con due subnet, `ManagedInstance` e `Management`. L'istanza gestita di SQL verrà distribuita nella subnet `ManagedInstance`. Una macchina virtuale con l'ultima versione di SQL Server Management Studio (SSMS) verrà distribuita nella subnet `Management`. |

---