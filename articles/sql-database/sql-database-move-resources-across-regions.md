---
title: Come spostare le risorse del database SQL di Azure in un'altra area | Microsoft Docs
description: Informazioni su come spostare il database SQL di Azure, il pool elastico SQL di Azure o l'istanza gestita di SQL di Azure in un'altra area.
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
ms.openlocfilehash: 2158d4120445de4c62461fb89555a1b73bc1e2b4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567155"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Come spostare le risorse SQL di Azure in un'altra area

Questo articolo illustra un flusso di lavoro generico per come spostare il database singolo del database SQL di Azure, il pool elastico e l'istanza gestita in una nuova area. 

## <a name="overview"></a>Panoramica

Esistono diversi scenari in cui si vuole spostare le risorse SQL di Azure esistenti da un'area a un'altra. Ad esempio, si espande l'azienda in una nuova area e si desidera ottimizzarla per la nuova clientela. In alternativa, è necessario spostare le operazioni in un'area diversa per motivi di conformità. In alternativa, Azure ha rilasciato una nuova regione che offre una migliore prossimità e migliora l'esperienza del cliente.  

Questo articolo fornisce un flusso di lavoro generale per lo trasferimento di risorse in un'area diversa. Il flusso di lavoro è costituito dai passaggi seguenti: 

- Verificare i prerequisiti per lo spostamento 
- Preparare lo spostamento delle risorse nell'ambito
- Monitorare il processo di preparazione
- Testare il processo di spostamento
- Avviare lo spostamento effettivo 
- Rimuovere le risorse dall'area di origine 


> [!NOTE]
> Questo articolo si applica alle migrazioni all'interno del cloud pubblico di Azure o all'interno dello stesso cloud sovrano. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Sposta database singolo

### <a name="verify-prerequisites"></a>Verificare i prerequisiti 

1. Creare un server logico di destinazione per ogni server di origine. 
1. Configurare il firewall con le eccezioni corrette usando [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configurare i server logici con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. Per altre informazioni, vedere [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md). 
1. Se i database sono crittografati con Transparent Data Encryption e usano la propria chiave di crittografia nell'insieme di credenziali delle chiavi di Azure, assicurarsi che venga eseguito il provisioning del materiale di crittografia corretto nelle aree di destinazione. Per altre informazioni, vedere [Transparent Data Encryption SQL di Azure con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Se è abilitato il controllo a livello di database, disabilitarlo e abilitare invece il controllo a livello di server. Al termine del failover, il controllo a livello di database richiede il traffico tra aree, che non sarà più necessario o possibile dopo lo spostamento. 
1. Per i controlli a livello di server, verificare che:
   - Il contenitore di archiviazione, Log Analytics o hub eventi con i log di controllo esistenti viene spostato nell'area di destinazione. 
   - Il controllo viene configurato nel server di destinazione. Per altre informazioni, vedere l' [Introduzione al controllo del database SQL](sql-database-auditing.md). 
1. Se l'istanza ha un criterio di conservazione a lungo termine, i backup di LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai precedenti backup di LTR nell'area di origine tramite il server di origine, anche se il server è stato eliminato. 

  > [!NOTE]
  > Questa operazione non sarà sufficiente per lo trasferimento tra il cloud sovrano e un'area pubblica. Una migrazione di questo tipo richiede lo spostamento dei backup di LTR nel server di destinazione, che attualmente non è supportata. 

### <a name="prepare-resources"></a>Preparare le risorse

1. Creare un [gruppo di failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) tra il server logico del server di origine e il server logico della destinazione.  
1. Aggiungere i database che si desidera spostare al gruppo di failover. 
    - La replica di tutti i database aggiunti verrà avviata automaticamente. Per ulteriori informazioni, vedere [procedure consigliate per l'utilizzo di gruppi di failover con database singoli](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto di output `Get-AzSqlDatabaseFailoverGroup` di include una proprietà per **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) indica che il database è sincronizzato e che può essere sottoposta a failover in modo sicuro. 
   - **ReplicationState = 0** (SEEDing) indica che il database non è ancora stato sottoposta a seeding e un tentativo di failover non riesce. 

### <a name="test-synchronization"></a>Test della sincronizzazione

Quando **ReplicationState** è `2`, connettersi a ogni database o sottoinsieme di database usando l'endpoint `<fog-name>.secondary.database.windows.net` secondario ed eseguire qualsiasi query sui database per garantire la connettività, la configurazione di sicurezza e i dati corretti. replica. 

### <a name="initiate-the-move"></a>Avviare lo spostamento

1. Connettersi al server di destinazione utilizzando l'endpoint `<fog-name>.secondary.database.windows.net`secondario.
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) per impostare l'istanza gestita secondaria come primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback. 
1. Verificare che il comando sia stato completato correttamente usando `nslook up <fog-name>.secondary.database.windows.net` per verificare che la voce DNS CNAME punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il record CNAME non viene aggiornato. 

### <a name="remove-the-source-databases"></a>Rimuovere i database di origine

Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari. 

1. Eliminare il gruppo di failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Eliminare ogni database di origine usando [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) per ogni database nel server di origine. I collegamenti per la replica geografica verranno terminati automaticamente. 
1. Eliminare il server di origine utilizzando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Rimuovere l'insieme di credenziali delle chiavi, controllare i contenitori di archiviazione, l'hub eventi, l'istanza di AAD e altre risorse dipendenti per interrompere la fatturazione. 

## <a name="move-elastic-pools"></a>Spostare pool elastici

### <a name="verify-prerequisites"></a>Verificare i prerequisiti 

1. Creare un server logico di destinazione per ogni server di origine. 
1. Configurare il firewall con le eccezioni corrette usando [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configurare i server logici con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. Per altre informazioni, vedere [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md). 
1. Se i database sono crittografati con Transparent Data Encryption e usano la propria chiave di crittografia nell'insieme di credenziali delle chiavi di Azure, assicurarsi che venga effettuato il provisioning del materiale di crittografia corretto nell'area di destinazione.
1. Creare un pool elastico di destinazione per ogni pool elastico di origine, assicurandosi che il pool venga creato nello stesso livello di servizio, con lo stesso nome e le stesse dimensioni. 
1. Se è abilitato un controllo a livello di database, disabilitarlo e abilitare invece il controllo a livello di server. Al termine del failover, il controllo a livello di database richiede il traffico tra aree, operazione non desiderata o possibile dopo lo spostamento. 
1. Per i controlli a livello di server, verificare che:
    - Il contenitore di archiviazione, Log Analytics o hub eventi con i log di controllo esistenti viene spostato nell'area di destinazione.
    - La configurazione di controllo è configurata nel server di destinazione. Per ulteriori informazioni, vedere [controllo del database SQL](sql-database-auditing.md).
1. Se l'istanza ha un criterio di conservazione a lungo termine, i backup di LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai precedenti backup di LTR nell'area di origine tramite il server di origine, anche se il server è stato eliminato. 

  > [!NOTE]
  > Questa operazione non sarà sufficiente per lo trasferimento tra il cloud sovrano e un'area pubblica. Una migrazione di questo tipo richiede lo spostamento dei backup di LTR nel server di destinazione, che attualmente non è supportata. 

### <a name="prepare-to-move"></a>Preparare lo spostamento
 
1.  Creare un [gruppo di failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) separato tra ogni pool elastico nel server logico di origine e il pool elastico corrispondente nel server di destinazione. 
1.  Aggiungere tutti i database nel pool al gruppo di failover. 
    - La replica dei database aggiunti verrà avviata automaticamente. Per altre informazioni, vedere [procedure consigliate per i gruppi di failover con pool elastici](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Sebbene sia possibile creare un gruppo di failover che includa più pool elastici, è consigliabile creare un gruppo di failover separato per ogni pool. Se si dispone di un numero elevato di database in più pool elastici che è necessario spostare, è possibile eseguire i passaggi di preparazione in parallelo e quindi avviare il passaggio di spostamento in parallelo. Questo processo verrà ridimensionato meglio e sarà necessario meno tempo rispetto alla presenza di più pool elastici nello stesso gruppo di failover. 

### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto di output `Get-AzSqlDatabaseFailoverGroup` di include una proprietà per **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) indica che il database è sincronizzato e che può essere sottoposta a failover in modo sicuro. 
   - **ReplicationState = 0** (SEEDing) indica che il database non è ancora stato sottoposta a seeding e un tentativo di failover non riesce. 

### <a name="test-synchronization"></a>Test della sincronizzazione
 
Quando **ReplicationState** è `2`, connettersi a ogni database o sottoinsieme di database usando l'endpoint `<fog-name>.secondary.database.windows.net` secondario ed eseguire qualsiasi query sui database per garantire la connettività, la configurazione di sicurezza e i dati corretti. replica. 

### <a name="initiate-the-move"></a>Avviare lo spostamento
 
1. Connettersi al server di destinazione utilizzando l'endpoint `<fog-name>.secondary.database.windows.net`secondario.
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) per impostare l'istanza gestita secondaria come primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback. 
1. Verificare che il comando sia stato completato correttamente usando `nslook up <fog-name>.secondary.database.windows.net` per verificare che la voce DNS CNAME punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il record CNAME non viene aggiornato. 

### <a name="remove-the-source-elastic-pools"></a>Rimuovere i pool elastici di origine
 
Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari. 

1. Eliminare il gruppo di failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Eliminare ogni pool elastico di origine nel server di origine usando [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Eliminare il server di origine utilizzando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Rimuovere l'insieme di credenziali delle chiavi, controllare i contenitori di archiviazione, l'hub eventi, l'istanza di AAD e altre risorse dipendenti per interrompere la fatturazione. 

## <a name="move-managed-instance"></a>Sposta istanza gestita

### <a name="verify-prerequisites"></a>Verificare i prerequisiti
 
1. Per ogni istanza gestita di origine, creare un'istanza gestita di destinazione della stessa dimensione nell'area di destinazione.  
1. Configurare la rete per un'istanza gestita. Per ulteriori informazioni, vedere [configurazione di rete](sql-database-howto-managed-instance.md#network-configuration).
1. Configurare il database master di destinazione con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. 
1. Se i database sono crittografati con Transparent Data Encryption e usano la propria chiave di crittografia nell'insieme di credenziali delle chiavi di Azure, assicurarsi che le AKV con chiavi di crittografia identiche esistano nelle aree di origine e di destinazione. Per ulteriori informazioni, vedere Transparent Data [Encryption con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Se il controllo è abilitato per l'istanza, verificare che:
    - Il contenitore di archiviazione o l'hub eventi con i log esistenti viene spostato nell'area di destinazione. 
    - Il controllo è configurato nell'istanza di destinazione. Per ulteriori informazioni, vedere [controllo con istanza gestita](sql-database-managed-instance-auditing.md).
1. Se l'istanza ha un criterio di conservazione a lungo termine, i backup di LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai precedenti backup di LTR nell'area di origine tramite il server di origine, anche se il server è stato eliminato. 

  > [!NOTE]
  > Questa operazione non sarà sufficiente per lo trasferimento tra il cloud sovrano e un'area pubblica. Una migrazione di questo tipo richiede lo spostamento dei backup di LTR nel server di destinazione, che attualmente non è supportata. 

### <a name="prepare-resources"></a>Preparare le risorse

Creare un gruppo di failover tra ogni istanza di origine e l'istanza di destinazione corrispondente.
    - La replica di tutti i database in ogni istanza verrà avviata automaticamente. Per ulteriori informazioni, vedere [gruppi di failover automatico](sql-database-auto-failover-group.md) .

 
### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto di output `Get-AzSqlDatabaseFailoverGroup` di include una proprietà per **ReplicationState**: 
   - **ReplicationState = 2** (CATCH_UP) indica che il database è sincronizzato e che può essere sottoposta a failover in modo sicuro. 
   - **ReplicationState = 0** (SEEDing) indica che il database non è ancora stato sottoposta a seeding e un tentativo di failover non riesce. 

### <a name="test-synchronization"></a>Test della sincronizzazione

Quando **ReplicationState** è `2`, connettersi a ogni database o sottoinsieme di database usando l'endpoint `<fog-name>.secondary.database.windows.net` secondario ed eseguire qualsiasi query sui database per garantire la connettività, la configurazione di sicurezza e i dati corretti. replica. 

### <a name="initiate-the-move"></a>Avviare lo spostamento 

1. Connettersi al server di destinazione utilizzando l'endpoint `<fog-name>.secondary.database.windows.net`secondario.
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) per impostare l'istanza gestita secondaria come primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback. 
1. Verificare che il comando sia stato completato correttamente usando `nslook up <fog-name>.secondary.database.windows.net` per verificare che la voce DNS CNAME punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il record CNAME non viene aggiornato. 

### <a name="remove-the-source-managed-instances"></a>Rimuovere le istanze gestite di origine
Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari. 

1. Eliminare il gruppo di failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Questa operazione eliminerà la configurazione del gruppo di failover e terminerà i collegamenti di replica geografica tra le due istanze. 
1. Eliminare l'istanza gestita di origine usando [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Rimuovere eventuali risorse aggiuntive nel gruppo di risorse, ad esempio il cluster virtuale, la rete virtuale e il gruppo di sicurezza. 

## <a name="next-steps"></a>Passaggi successivi 

[Gestire](sql-database-manage-after-migration.md) il database SQL di Azure dopo la migrazione. 

