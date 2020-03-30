---
title: Come spostare le risorse in un'altra areaHow to move resources to another region
description: Informazioni su come spostare il database SQL di Azure, il pool elastico SQL di Azure o l'istanza gestita sql di Azure in un'altra area.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821428"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Come spostare le risorse SQL di Azure in un'altra areaHow to move Azure SQL resources to another region

Questo articolo illustra un flusso di lavoro generico per come spostare il database SQL di Azure singolo database, pool elastico e istanza gestita in una nuova area. 

## <a name="overview"></a>Panoramica

Esistono vari scenari in cui si desidera spostare le risorse SQL di Azure esistenti da un'area a un'altra. Ad esempio, si espande la propria attività in una nuova area geografica e si desidera ottimizzarla per la nuova base di clienti. In alternativa, è necessario spostare le operazioni in un'area diversa per motivi di conformità. Oppure Azure ha rilasciato un'area nuova di zecca che offre una migliore vicinanza e migliora l'esperienza del cliente.  

In questo articolo viene fornito un flusso di lavoro generale per lo spostamento di risorse in un'area diversa. Il flusso di lavoro è costituito dai passaggi seguenti:The workflow consists of the following steps: 

- Verificare i prerequisiti per lo spostamento 
- Preparare lo spostamento delle risorse nell'ambitoPrepare to move the resources in scope
- Monitorare il processo di preparazione
- Testare il processo di spostamento
- Avviare lo spostamento effettivo 
- Rimuovere le risorse dall'area di origineRemove the resources from the source region 


> [!NOTE]
> Questo articolo si applica alle migrazioni all'interno del cloud pubblico di Azure o all'interno dello stesso cloud sovrano. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Spostare un singolo database

### <a name="verify-prerequisites"></a>Verificare i prerequisiti 

1. Creare un server logico di destinazione per ogni server di origine. 
1. Configurare il firewall con le eccezioni corrette utilizzando [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configurare i server logici con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. Per altre informazioni, vedere Come gestire la sicurezza del database SQL di Azure dopo il ripristino di [emergenza.](sql-database-geo-replication-security-config.md) 
1. Se i database sono crittografati con TDE e usano la propria chiave di crittografia nell'insieme di credenziali delle chiavi di Azure, assicurarsi che venga eseguito il provisioning del materiale di crittografia corretto nelle aree di destinazione. Per altre informazioni, vedere Crittografia dei dati trasparente di Azure SQL con chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di AzureFor more information, see [Azure SQL Transparent Data Encryption with customer-managed keys in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Se il controllo a livello di database è abilitato, disabilitarlo e abilitare invece il controllo a livello di server. Dopo il failover, il controllo a livello di database richiederà il traffico tra aree, che non è desiderato o possibile dopo lo spostamento. 
1. Per i controlli a livello di server, verificare che:For server-level audits, ensure that:
   - Il contenitore di archiviazione, Log Analytics o hub eventi con i log di controllo esistenti viene spostato nell'area di destinazione. 
   - Il controllo è configurato nel server di destinazione. Per altre informazioni, vedere l' [Introduzione al controllo del database SQL](sql-database-auditing.md). 
1. Se l'istanza dispone di un criterio di conservazione a lungo termine (LTR), i backup LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai backup LTR meno recenti nell'area di origine utilizzando il server di origine, anche se il server viene eliminato. 

  > [!NOTE]
  > Ciò non sarà sufficiente per spostarsi tra la nube sovrana e una regione pubblica. Tale migrazione richiederà lo spostamento dei backup LTR nel server di destinazione, che non è attualmente supportato. 

### <a name="prepare-resources"></a>Preparare le risorse

1. Creare un gruppo di [failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) tra il server logico dell'origine e il server logico della destinazione.  
1. Aggiungere i database che si desidera spostare al gruppo di failover. 
    - La replica di tutti i database aggiunti verrà avviata automaticamente. Per ulteriori informazioni, vedere Procedure consigliate per l'utilizzo di gruppi di [failover con singoli database.](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools) 
 
### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto `Get-AzSqlDatabaseFailoverGroup` di output di include una proprietà per **ReplicationState**: 
   - **ReplicationState - 2** (CATCH_UP) indica che il database è sincronizzato e può essere eseguito il failover in modo sicuro. 
   - **ReplicationState 0** (SEEDING) indica che il seeding del database non è ancora stato eseguito il seeding e un tentativo di failover avrà esito negativo. 

### <a name="test-synchronization"></a>Testare la sincronizzazione

Una volta `2` **ReplicationState** è , connettersi a ogni `<fog-name>.secondary.database.windows.net` database o sottoinsieme di database utilizzando l'endpoint secondario ed eseguire qualsiasi query sui database per garantire la connettività, la corretta configurazione di sicurezza e la replica dei dati. 

### <a name="initiate-the-move"></a>Avviare lo spostamento

1. Connettersi al server di `<fog-name>.secondary.database.windows.net`destinazione utilizzando l'endpoint secondario.
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) per passare l'istanza gestita secondaria in modo che sia primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback. 
1. Verificare che il comando `nslook up <fog-name>.secondary.database.windows.net` sia stato completato correttamente utilizzando per verificare che l'ingresso CNAME DNS punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il CNAME non verrà aggiornato. 

### <a name="remove-the-source-databases"></a>Rimuovere i database di origine

Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari. 

1. Eliminare il gruppo di failover utilizzando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Eliminare ogni database di origine utilizzando [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) per ognuno dei database nel server di origine. In questo modo verranno terminati automaticamente i collegamenti di replica geografica. 
1. Eliminare il server di origine utilizzando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Rimuovere l'insieme di credenziali delle chiavi, i contenitori di archiviazione di controllo, l'hub eventi, l'istanza AAD e altre risorse dipendenti per interrompere la fatturazione. 

## <a name="move-elastic-pools"></a>Spostare i pool elastici

### <a name="verify-prerequisites"></a>Verificare i prerequisiti 

1. Creare un server logico di destinazione per ogni server di origine. 
1. Configurare il firewall con le eccezioni corrette utilizzando [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configurare i server logici con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. Per altre informazioni, vedere Come gestire la sicurezza del database SQL di Azure dopo il ripristino di [emergenza.](sql-database-geo-replication-security-config.md) 
1. Se i database sono crittografati con TDE e usano la propria chiave di crittografia nell'insieme di credenziali delle chiavi di Azure, assicurarsi che venga eseguito il provisioning del materiale di crittografia corretto nell'area di destinazione.
1. Creare un pool elastico di destinazione per ogni pool elastico di origine, assicurandosi che il pool venga creato nello stesso livello di servizio, con lo stesso nome e le stesse dimensioni. 
1. Se è abilitato un controllo a livello di database, disabilitarlo e abilitare invece il controllo a livello di server. Dopo il failover, il controllo a livello di database richiederà il traffico tra aree, che non è desiderato o possibile dopo lo spostamento. 
1. Per i controlli a livello di server, verificare che:For server-level audits, ensure that:
    - Il contenitore di archiviazione, Log Analytics o hub eventi con i log di controllo esistenti viene spostato nell'area di destinazione.
    - La configurazione di controllo è configurata nel server di destinazione. Per ulteriori informazioni, vedere [Controllo del database SQL](sql-database-auditing.md).
1. Se l'istanza dispone di un criterio di conservazione a lungo termine (LTR), i backup LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai backup LTR meno recenti nell'area di origine utilizzando il server di origine, anche se il server viene eliminato. 

  > [!NOTE]
  > Ciò non sarà sufficiente per spostarsi tra la nube sovrana e una regione pubblica. Tale migrazione richiederà lo spostamento dei backup LTR nel server di destinazione, che non è attualmente supportato. 

### <a name="prepare-to-move"></a>Preparati a muoverti
 
1.  Creare un gruppo di [failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) separato tra ogni pool elastico nel server logico di origine e il relativo pool elastico della controparte nel server di destinazione. 
1.  Aggiungere tutti i database del pool al gruppo di failover. 
    - La replica dei database aggiunti verrà avviata automaticamente. Per ulteriori informazioni, vedere Procedure consigliate per i gruppi di [failover con pool elastici](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Sebbene sia possibile creare un gruppo di failover che include più pool elastici, è consigliabile creare un gruppo di failover separato per ogni pool. Se si dispone di un numero elevato di database in più pool elastici che è necessario spostare, è possibile eseguire i passaggi di preparazione in parallelo e quindi avviare il passaggio di spostamento in parallelo. Questo processo sarà in grado di essere scalabile meglio e richiederà meno tempo rispetto alla presenza di più pool elastici nello stesso gruppo di failover. 

### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto `Get-AzSqlDatabaseFailoverGroup` di output di include una proprietà per **ReplicationState**: 
   - **ReplicationState - 2** (CATCH_UP) indica che il database è sincronizzato e può essere eseguito il failover in modo sicuro. 
   - **ReplicationState 0** (SEEDING) indica che il seeding del database non è ancora stato eseguito il seeding e un tentativo di failover avrà esito negativo. 

### <a name="test-synchronization"></a>Testare la sincronizzazione
 
Una volta `2` **ReplicationState** è , connettersi a ogni `<fog-name>.secondary.database.windows.net` database o sottoinsieme di database utilizzando l'endpoint secondario ed eseguire qualsiasi query sui database per garantire la connettività, la corretta configurazione di sicurezza e la replica dei dati. 

### <a name="initiate-the-move"></a>Avviare lo spostamento
 
1. Connettersi al server di `<fog-name>.secondary.database.windows.net`destinazione utilizzando l'endpoint secondario.
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) per passare l'istanza gestita secondaria in modo che sia primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback. 
1. Verificare che il comando `nslook up <fog-name>.secondary.database.windows.net` sia stato completato correttamente utilizzando per verificare che l'ingresso CNAME DNS punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il CNAME non verrà aggiornato. 

### <a name="remove-the-source-elastic-pools"></a>Rimuovere i pool elastici di origineRemove the source elastic pools
 
Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari. 

1. Eliminare il gruppo di failover utilizzando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Eliminare ogni pool elastico di origine nel server di origine utilizzando [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Eliminare il server di origine utilizzando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Rimuovere l'insieme di credenziali delle chiavi, i contenitori di archiviazione di controllo, l'hub eventi, l'istanza AAD e altre risorse dipendenti per interrompere la fatturazione. 

## <a name="move-managed-instance"></a>Spostare l'istanza gestitaMove managed instance

### <a name="verify-prerequisites"></a>Verificare i prerequisiti
 
1. Per ogni istanza gestita di origine creare un'istanza gestita di destinazione della stessa dimensione nell'area di destinazione.  
1. Configurare la rete per un'istanza gestita. Per ulteriori informazioni, vedere [Configurazione di rete](sql-database-howto-managed-instance.md#network-configuration).
1. Configurare il database master di destinazione con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. 
1. Se i database sono crittografati con TDE e usano la propria chiave di crittografia nell'insieme di credenziali delle chiavi di Azure, verificare che l'AKV con chiavi di crittografia identiche esista sia sia nelle aree di origine che in quelle di destinazione. Per altre informazioni, vedere TDE con chiavi gestite dal cliente in Archiviazione delle chiavi di Azure.For more information, see [TDE with customer-managed keys in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Se il controllo è abilitato per l'istanza, verificare che:If audit is enabled for the instance, ensure that:
    - Il contenitore di archiviazione o l'hub eventi con i log esistenti viene spostato nell'area di destinazione. 
    - Il controllo è configurato nell'istanza di destinazione. Per ulteriori informazioni, vedere [controllo con istanza gestita](sql-database-managed-instance-auditing.md).
1. Se l'istanza dispone di un criterio di conservazione a lungo termine (LTR), i backup LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai backup LTR meno recenti nell'area di origine utilizzando il server di origine, anche se il server viene eliminato. 

  > [!NOTE]
  > Ciò non sarà sufficiente per spostarsi tra la nube sovrana e una regione pubblica. Tale migrazione richiederà lo spostamento dei backup LTR nel server di destinazione, che non è attualmente supportato. 

### <a name="prepare-resources"></a>Preparare le risorse

Creare un gruppo di failover tra ogni istanza di origine e l'istanza di destinazione corrispondente.
    - La replica di tutti i database in ogni istanza verrà avviata automaticamente. Per altre informazioni, vedere Gruppi di [failover automatico.](sql-database-auto-failover-group.md)

 
### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto `Get-AzSqlDatabaseFailoverGroup` di output di include una proprietà per **ReplicationState**: 
   - **ReplicationState - 2** (CATCH_UP) indica che il database è sincronizzato e può essere eseguito il failover in modo sicuro. 
   - **ReplicationState 0** (SEEDING) indica che il seeding del database non è ancora stato eseguito il seeding e un tentativo di failover avrà esito negativo. 

### <a name="test-synchronization"></a>Testare la sincronizzazione

Una volta `2` **ReplicationState** è , connettersi a ogni `<fog-name>.secondary.database.windows.net` database o sottoinsieme di database utilizzando l'endpoint secondario ed eseguire qualsiasi query sui database per garantire la connettività, la corretta configurazione di sicurezza e la replica dei dati. 

### <a name="initiate-the-move"></a>Avviare lo spostamento 

1. Connettersi al server di `<fog-name>.secondary.database.windows.net`destinazione utilizzando l'endpoint secondario.
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) per passare l'istanza gestita secondaria in modo che sia primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback. 
1. Verificare che il comando `nslook up <fog-name>.secondary.database.windows.net` sia stato completato correttamente utilizzando per verificare che l'ingresso CNAME DNS punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il CNAME non verrà aggiornato. 

### <a name="remove-the-source-managed-instances"></a>Rimuovere le istanze gestite di origineRemove the source managed instances
Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari. 

1. Eliminare il gruppo di failover utilizzando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). In questo modo la configurazione del gruppo di failover verrà eliminare e terminare i collegamenti di replica geografica tra le due istanze. 
1. Eliminare l'istanza gestita di origine utilizzando [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Rimuovere eventuali risorse aggiuntive nel gruppo di risorse, ad esempio il cluster virtuale, la rete virtuale e il gruppo di sicurezza. 

## <a name="next-steps"></a>Passaggi successivi 

[Gestire](sql-database-manage-after-migration.md) il database SQL di Azure dopo la migrazione. 

