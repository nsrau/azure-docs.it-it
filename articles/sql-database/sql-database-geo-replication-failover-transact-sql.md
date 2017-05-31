---
title: Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL | Documentazione Microsoft
description: Avviare un failover pianificato o non pianificato per il database SQL di Azure usando Transact-SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5eb2d256-025d-4f5a-99d4-17f702b37f14
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 6ea2cfcf41900ecbf4d254cc4a195848144a0fa0
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL

Questo articolo illustra come avviare il failover su un database SQL secondario con Transact-SQL. Per configurare la replica geografica, vedere [Configurare la replica geografica per il database SQL di Azure con PowerShell](sql-database-geo-replication-transact-sql.md).

Per avviare il failover, è necessario quanto segue:

* Un account di accesso DBManager nel server primario
* Avere db_ownership del database locale di cui eseguire la replica geografica
* Essere DBManager nel server partner verso cui si configurerà la replica geografica
* La versione più recente di SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>  

## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Avviare un failover pianificato per alzare di livello un database secondario per diventare il nuovo database primario
È possibile usare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario a nuovo database primario in maniera pianificata, abbassando di livello il database primario esistente a database secondario. Questa istruzione viene eseguita sul database master nel server logico di database SQL di Azure in cui risiede il database secondario con replica geografica che viene alzato di livello. Questa funzionalità è progettata per il failover pianificato, ad esempio durante le esercitazioni sul ripristino di emergenza, e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, in modo che tutte le transazioni in sospeso siano scaricate nel server secondario e le nuove transazioni vengano bloccate.
2. Scambia i ruoli dei due database nella relazione di replica geografica.  

Questa sequenza garantisce che i due database siano sincronizzati prima dello scambio di ruoli, in modo da evitare la perdita di dati. Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Se il database primario dispone di più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario.  Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).

Usare la procedura seguente per avviare un failover pianificato.

1. In Management Studio connettersi al server logico di database SQL di Azure in cui risiede un database secondario con replica geografica.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi fare clic su **Nuova query**.
3. Usare la seguente istruzione **ALTER DATABASE** per passare il database secondario al ruolo di database primario.
   
        ALTER DATABASE <MyDB> FAILOVER;
4. Fare clic su **Execute** per eseguire la query.

> [!NOTE]
> In rari casi, è possibile che l'operazione non possa essere completata e appaia bloccata. In questo caso l'utente può eseguire il comando di failover forzato e accettare la perdita dei dati.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Avviare un failover non pianificato dal database primario al database secondario
È possibile usare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario in modo che diventi il nuovo database primario in nodo non pianificato, forzando l'abbassamento di livello del database primario esistente a database secondario in un momento in cui il database primario non è più disponibile. Questa istruzione viene eseguita sul database master nel server logico di database SQL di Azure in cui risiede il database secondario con replica geografica che viene alzato di livello.

Questa funzionalità è progettata per il ripristino di emergenza quando il ripristino della disponibilità del database è critica ed è accettabile una perdita di dati. Quando si richiama il failover forzato, il database secondario specificato diventa immediatamente il database primario e inizia ad accettare le transazioni di scrittura. Non appena il database primario originale può riconnettersi a questo nuovo database primario, viene eseguito un backup incrementale nel database primario originale e il database primario precedente viene convertito in un database secondario per il nuovo database primario. In seguito si tratterà semplicemente di una replica di sincronizzazione del nuovo database.

Poiché tuttavia il ripristino temporizzato non è supportato nei database secondari, se l'utente vuole recuperare i dati di cui è stato eseguito il commit nel database primario precedente e che non sono stati replicati nel nuovo database primario prima del failover forzato, dovrà rivolgersi al supporto tecnico per il ripristino dei dati persi.

Se il database primario dispone di più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario.

Usare la procedura seguente per avviare un failover non pianificato.

1. In Management Studio connettersi al server logico di database SQL di Azure in cui risiede un database secondario con replica geografica.
2. Aprire la cartella Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi fare clic su **Nuova query**.
3. Usare la seguente istruzione **ALTER DATABASE** per passare il database secondario al ruolo di database primario.
   
        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;
4. Fare clic su **Execute** per eseguire la query.

> [!NOTE]
> Se il comando viene eseguito quando i database primario e secondario sono online, il database primario precedente diventerà il nuovo database secondario, senza alcuna sincronizzazione dei dati. Se il database primario sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
* Per informazioni sul ripristino di emergenza tramite replica geografica attiva, incluse le procedure pre e post ripristino e un'esercitazione sul ripristino di emergenza, vedere [Ripristino di emergenza](sql-database-disaster-recovery.md)
* Per un post di blog di Sasha Nosov sulla replica geografica attiva, vedere [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Le nuove capacità di replica geografica)
* Per informazioni sulla progettazione di applicazioni cloud per l'uso della replica geografica attiva, vedere [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Per informazioni sulla replica geografica attiva con i pool elastici, vedere [Strategie di ripristino di emergenza con pool elastico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Per un quadro generale, vedere la [panoramica della continuità aziendale](sql-database-business-continuity.md)


