---
title: Avviare un failover pianificato o non pianificato per il database SQL di Azure con PowerShell | Documentazione Microsoft
description: Avviare un failover pianificato o non pianificato per il database SQL di Azure usando PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 5849b600-89cb-4995-ae9f-0188a17b4e1b
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a16d278262f6fb645163f8d94139c86019df0cde


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Avviare un failover pianificato o non pianificato per il database SQL di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-geo-replication-failover-portal.md)
> * [PowerShell](sql-database-geo-replication-failover-powershell.md)
> * [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
> 
> 

Questo articolo illustra come avviare un failover pianificato o non pianificato per il database SQL con PowerShell. Per configurare la replica geografica, vedere [Configurare la replica geografica per il database SQL di Azure con PowerShell](sql-database-geo-replication-powershell.md).

## <a name="initiate-a-planned-failover"></a>Avviare un failover pianificato
Usare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con il parametro **-Failover** per alzare di livello un database secondario a nuovo database primario, abbassando di livello il database primario esistente a database secondario. Questa funzionalità è progettata per il failover pianificato, ad esempio durante le esercitazioni sul ripristino di emergenza, e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, in modo che tutte le transazioni in sospeso siano scaricate nel server secondario.
2. Scambia i ruoli dei due database nella relazione di replica geografica.  

Questa sequenza garantisce che i due database siano sincronizzati prima dello scambio di ruoli, in modo da evitare la perdita di dati. Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. Per altre informazioni, vedere [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393\(v=azure.300\).aspx).

Il risultato di questo cmdlet verrà restituito quando il processo di scambio da database secondario a primario sarà completato.

Il comando seguente cambia in primario i ruoli del database denominato "mydb” nel server "srv2” del gruppo di risorse "rg2”. Il database primario originale a cui è stato connesso "db2” diventerà secondario dopo la sincronizzazione completa dei due database.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [!NOTE]
> In rari casi, è possibile che l'operazione non possa essere completata e appaia bloccata. In questo caso l'utente può eseguire il comando di failover forzato, ovvero un failover non pianificato, e accettare la perdita dei dati.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Avviare un failover non pianificato dal database primario al database secondario
È possibile usare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con i parametri **–Failover** e **-AllowDataLoss** per alzare di livello un database secondario a nuovo database primario in maniera non pianificata, forzando l'abbassamento di livello del database primario esistente a database secondario in un momento in cui il database primario non è più disponibile.

Questa funzionalità è progettata per il ripristino di emergenza quando il ripristino della disponibilità del database è critica ed è accettabile una perdita di dati. Quando si richiama il failover forzato, il database secondario specificato diventa immediatamente il database primario e inizia ad accettare le transazioni di scrittura. Non appena il database primario originale può riconnettersi a questo nuovo database primario dopo l'operazione di failover forzato, viene eseguito un backup incrementale nel database primario originale e il database primario precedente viene convertito in un database secondario per il nuovo database primario. In seguito si tratterà semplicemente di una replica del nuovo database.

Poiché tuttavia il ripristino temporizzato non è supportato nei database secondari, se l'utente vuole recuperare i dati di cui è stato eseguito il commit nel database primario precedente e che non sono stati replicati nel nuovo database primario, dovrà rivolgersi al supporto tecnico per il ripristino di un database al backup del log noto.

> [!NOTE]
> Se il comando viene eseguito quando i database primario e secondario sono online, il database primario precedente diventerà il nuovo database secondario, senza alcuna sincronizzazione dei dati. Se il database primario sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.
> 
> 

Se il database primario ha più database secondari, il comando riuscirà in parte. Il database secondario su cui è stato eseguito il comando diventerà il database primario. Il database primario precedente rimarrà tuttavia primario, ovvero i due database primari rimarranno in uno stato non coerente e connessi da un collegamento di replica sospeso. L'utente dovrà ripristinare manualmente questa configurazione usando un'API di "rimozione del database secondario" in uno di questi database primari.

Il comando seguente cambia i ruoli del database denominato "mydb” in primario quando il database primario non è disponibile. Il database primario originale a cui è stato connesso "mydb” diventerà secondario dopo essere ritornato online. A questo punto la sincronizzazione potrebbe causare una perdita di dati.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Passaggi successivi
* Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
* Per informazioni sul ripristino di emergenza tramite replica geografica attiva, comprensive delle procedure preliminari e successive a un ripristino e di un'esercitazione sul ripristino di emergenza, vedere le [esercitazioni per il ripristino di emergenza](sql-database-disaster-recovery.md)
* Per un post di blog di Sasha Nosov sulla replica geografica attiva, vedere [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Per informazioni sulla progettazione di applicazioni cloud per l'uso della replica geografica attiva, vedere [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Per informazioni sull'uso della replica geografica attiva con pool di database elastici, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Per un quadro generale, vedere la [panoramica della continuità aziendale](sql-database-business-continuity.md)




<!--HONumber=Nov16_HO3-->


