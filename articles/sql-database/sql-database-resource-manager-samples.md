---
title: Modelli di Azure Resource Manager per database SQL | Microsoft Docs
description: Usare i modelli di Azure Resource Manager per creare e configurare il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: c501bed9220c08013d44836066e46df92cf69eaa
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037443"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Modelli di Azure Resource Manager per Database SQL di Azure

I modelli di Azure Resource Manager consentono di definire un'infrastruttura come codice e di distribuire le soluzioni nel cloud di Azure.

## <a name="single-database--elastic-pooltabsingle-database"></a>[Database singolo e pool elastico](#tab/single-database)

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per il database SQL di Azure.

| |  |
|---|---|
| [Database singolo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Questo modello di Azure Resource Manager crea un singolo database SQL di Azure con un server logico e configura le regole del firewall. |
| [Server logico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Questo modello di Azure Resource Manager crea un server logico per il database SQL di Azure. |
| [Pool elastico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Questo modello consente di distribuire un nuovo pool elastico con la nuova istanza di SQL Server e i nuovi database SQL associati da assegnarvi. |
| [Gruppi di failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Questo modello crea due server logici SQL di Azure, un database SQL e un gruppo di failover.|
| [Rilevamento delle minacce](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Questo modello consente di distribuire un server logico SQL di Azure e di impostare un database SQL di Azure con la funzionalità Rilevamento minacce abilitata, con un indirizzo di posta elettronica per gli avvisi per ogni database. Rilevamento minacce fa parte dell'offerta Advanced Threat Protection (ATP) SQL e fornisce un livello di sicurezza che risponde alle potenziali minacce su server e database SQL.|
| [Controllo per Archiviazione BLOB di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Questo modello consente di distribuire un server logico SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in un'archiviazione BLOB. Il controllo per il database SQL di Azure consente di tenere traccia degli eventi di database e di registrarli in un log di controllo che può essere inserito nell'account di Archiviazione di Azure, nell'area di lavoro di OMS o in Hub eventi.|
| [Controllo per Hub eventi di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Questo modello consente di distribuire un server SQL di Azure con il controllo abilitato per la scrittura dei log di controllo in hub eventi esistente. Per inviare gli eventi di controllo all'hub eventi, specificare le impostazioni di controllo con `Enabled` `State` e impostare `IsAzureMonitorTargetEnabled` su `true`. Configurare inoltre le impostazioni di diagnostica con la categoria dei log di diagnostica `SQLSecurityAuditEvents` nel database `master` (per il controllo a livello di server). Il controllo per il database SQL di Azure e SQL Data Warehouse consente di tenere traccia degli eventi di database e di registrarli in un log di controllo che può essere inserito nell'account di archiviazione di Azure, nell'area di lavoro di OMS o in Hub eventi.|
| [App Web di Azure con database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Questo esempio crea un'app Web di Azure gratuita e un database SQL al livello di servizio "Basic".|
| [App Web di Azure e Cache Redis con database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Questo modello crea un'app Web, una cache Redis e un database SQL nello stesso gruppo di risorse, quindi crea due stringhe di connessione nell'app Web per il database SQL e la cache Redis.|
| [Importazione di dati da archiviazione BLOB con ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Questo modello di Azure Resource Manager crea Azure Data Factory V2 che copia i dati da Archiviazione BLOB di Azure al database SQL.|
| [Cluster HDInsight con un database SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Questo modello consente di creare un cluster HDInsight, un server di database SQL, un database SQL e due tabelle. Viene usato nell'articolo [Usare Sqoop con Hadoop in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [App per la logica di Azure che esegue una stored procedure SQL in base a una pianificazione](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Questo modello consente di creare un'app per la logica che eseguirà una stored procedure SQL in base a una pianificazione. Tutti gli argomenti per la procedura possono essere inseriti nella sezione del corpo del modello.|

## <a name="managed-instancetabmanaged-instance"></a>[Istanza gestita](#tab/managed-instance)

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Database SQL di Azure - Istanza gestita.

| |  |
|---|---|
| [Istanza gestita in una nuova rete virtuale](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Questo modello di Azure Resource Manager crea una nuova rete virtuale di Azure configurata e un'istanza gestita al suo interno. |
| [Ambiente di rete per Istanza gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Questa distribuzione crea una rete virtuale di Azure configurata con due subnet, una dedicata alle istanze gestite e l'altra in cui è possibile inserire altre risorse, ad esempio VM, ambienti di Servizio App e così via. Questo modello crea un ambiente di rete correttamente configurato in cui è possibile distribuire istanze gestite. |
| [Istanza gestita con connessione da punto a sito](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Questa distribuzione crea una rete virtuale di Azure con due subnet `ManagedInstance` e `GatewaySubnet`. L'istanza gestita verrà distribuita nella subnet ManagedInstance. Nella subnet `GatewaySubnet` verrà creato un gateway di rete virtuale configurato per la connessione VPN da punto a sito. |
| [Istanza gestita con macchina virtuale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Questa distribuzione crea una rete virtuale di Azure con due subnet `ManagedInstance` e `Management`. L'istanza gestita verrà distribuita nella subnet `ManagedInstance`. La macchina virtuale con l'ultima versione di SQL Server Management Studio (SSMS) verrà distribuita nella subnet `Management`. |

---
