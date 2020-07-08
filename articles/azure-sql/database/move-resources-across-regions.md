---
title: Spostare le risorse in una nuova area
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informazioni su come spostare il database o l'istanza gestita in un'altra area.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 46b95c438830a488494d50308d71a115d6f0da42
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982146"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Spostare le risorse in una nuova area: database SQL di Azure & Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questo articolo illustra un flusso di lavoro generico per lo spostamento di un database o di un'istanza gestita in una nuova area.

## <a name="overview"></a>Panoramica

Esistono diversi scenari in cui si desidera spostare il database o l'istanza gestita esistente da un'area a un'altra. Ad esempio, stai espandendo il tuo business in una nuova area e vuoi ottimizzarlo per la nuova clientela. In alternativa, è necessario spostare le operazioni in un'area diversa per motivi di conformità. In alternativa, Azure ha rilasciato una nuova area che offre una migliore prossimità e migliora l'esperienza del cliente.  

Questo articolo fornisce un flusso di lavoro generale per lo trasferimento di risorse in un'area diversa. Il flusso di lavoro è costituito dai passaggi seguenti:

1. Verificare i prerequisiti per lo spostamento.
1. Preparare lo spostamento delle risorse nell'ambito.
1. Monitorare il processo di preparazione.
1. Testare il processo di spostamento.
1. Avviare lo spostamento effettivo.
1. Rimuovere le risorse dall'area di origine.

> [!NOTE]
> Questo articolo si applica alle migrazioni all'interno del cloud pubblico di Azure o all'interno dello stesso cloud sovrano.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Spostare un database

### <a name="verify-prerequisites"></a>Verificare i prerequisiti

1. Creare un server di destinazione per ogni server di origine.
1. Configurare il firewall con le eccezioni corrette usando [PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Configurare i server con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore di SQL Server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. Per altre informazioni, vedere [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).
1. Se i database vengono crittografati con Transparent Data Encryption e usano la propria chiave di crittografia in Azure Key Vault, assicurarsi che venga effettuato il provisioning del materiale di crittografia corretto nelle aree di destinazione. Per altre informazioni, vedere [Transparent Data Encryption di Azure SQL con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Se è abilitato il controllo a livello di database, disabilitarlo e abilitare invece il controllo a livello di server. Al termine del failover, il controllo a livello di database richiede il traffico tra aree, che non è necessario o possibile dopo lo spostamento.
1. Per i controlli a livello di server, verificare che:
   - Il contenitore di archiviazione, Log Analytics o hub eventi con i log di controllo esistenti viene spostato nell'area di destinazione.
   - Il controllo viene configurato nel server di destinazione. Per ulteriori informazioni, vedere [Introduzione al controllo del database SQL](../../azure-sql/database/auditing-overview.md).
1. Se l'istanza ha un criterio di conservazione a lungo termine, i backup di LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai precedenti backup di LTR nell'area di origine tramite il server di origine, anche se il server è stato eliminato.

      > [!NOTE]
      > Questa operazione non sarà sufficiente per lo trasferimento tra il cloud sovrano e un'area pubblica. Una migrazione di questo tipo richiede lo spostamento dei backup di LTR nel server di destinazione, che attualmente non è supportata.

### <a name="prepare-resources"></a>Preparare le risorse

1. Creare un [gruppo di failover](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) tra il server di origine e il server della destinazione.  
1. Aggiungere i database che si desidera spostare al gruppo di failover.
  
    La replica di tutti i database aggiunti verrà avviata automaticamente. Per ulteriori informazioni, vedere [procedure consigliate per l'utilizzo di gruppi di failover con database singoli](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto di output di `Get-AzSqlDatabaseFailoverGroup` include una proprietà per **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) indica che il database è sincronizzato e che può essere sottoposta a failover in modo sicuro.
- **ReplicationState = 0** (seeding) indica che il database non è ancora stato sottoposta a seeding e un tentativo di eseguire il failover avrà esito negativo.

### <a name="test-synchronization"></a>Test della sincronizzazione

Dopo **ReplicationState** `2` , connettersi a ogni database o subset di database usando l'endpoint secondario `<fog-name>.secondary.database.windows.net` ed eseguire qualsiasi query sui database per garantire la connettività, la configurazione di sicurezza appropriata e la replica dei dati.

### <a name="initiate-the-move"></a>Avviare lo spostamento

1. Connettersi al server di destinazione utilizzando l'endpoint secondario `<fog-name>.secondary.database.windows.net` .
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) per impostare l'istanza gestita secondaria come primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback.
1. Verificare che il comando sia stato completato correttamente usando `nslook up <fog-name>.secondary.database.windows.net` per verificare che la voce DNS CNAME punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il CNAME non verrà aggiornato.

### <a name="remove-the-source-databases"></a>Rimuovere i database di origine

Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari.

1. Eliminare il gruppo di failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Eliminare ogni database di origine usando [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) per ogni database nel server di origine. I collegamenti per la replica geografica verranno terminati automaticamente.
1. Eliminare il server di origine utilizzando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Rimuovere l'insieme di credenziali delle chiavi, controllare i contenitori di archiviazione, l'hub eventi, l'istanza Azure Active Directory (Azure AD) e altre risorse dipendenti per interrompere la fatturazione.

## <a name="move-elastic-pools"></a>Spostare pool elastici

### <a name="verify-prerequisites"></a>Verificare i prerequisiti

1. Creare un server di destinazione per ogni server di origine.
1. Configurare il firewall con le eccezioni corrette usando [PowerShell](scripts/create-and-configure-database-powershell.md).
1. Configurare i server con gli account di accesso corretti. Se non si è l'amministratore della sottoscrizione o l'amministratore del server, collaborare con l'amministratore per assegnare le autorizzazioni necessarie. Per altre informazioni, vedere [come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md).
1. Se i database vengono crittografati con Transparent Data Encryption e usano la propria chiave di crittografia in Azure Key Vault, assicurarsi che venga effettuato il provisioning del materiale di crittografia corretto nell'area di destinazione.
1. Creare un pool elastico di destinazione per ogni pool elastico di origine, assicurandosi che il pool venga creato nello stesso livello di servizio, con lo stesso nome e le stesse dimensioni.
1. Se è abilitato un controllo a livello di database, disabilitarlo e abilitare invece il controllo a livello di server. Al termine del failover, il controllo a livello di database richiede il traffico tra aree, operazione non desiderata o possibile dopo lo spostamento.
1. Per i controlli a livello di server, verificare che:
    - Il contenitore di archiviazione, Log Analytics o hub eventi con i log di controllo esistenti viene spostato nell'area di destinazione.
    - La configurazione di controllo è configurata nel server di destinazione. Per ulteriori informazioni, vedere [controllo del database SQL](../../azure-sql/database/auditing-overview.md).
1. Se l'istanza ha un criterio di conservazione a lungo termine, i backup di LTR esistenti rimarranno associati al server corrente. Poiché il server di destinazione è diverso, sarà possibile accedere ai precedenti backup di LTR nell'area di origine tramite il server di origine, anche se il server è stato eliminato.

      > [!NOTE]
      > Questa operazione non sarà sufficiente per lo trasferimento tra il cloud sovrano e un'area pubblica. Una migrazione di questo tipo richiede lo spostamento dei backup di LTR nel server di destinazione, che attualmente non è supportata.

### <a name="prepare-to-move"></a>Preparare lo spostamento

1. Creare un [gruppo di failover](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) separato tra ogni pool elastico nel server di origine e il relativo pool elastico corrispondente nel server di destinazione.
1. Aggiungere tutti i database nel pool al gruppo di failover.

    La replica dei database aggiunti verrà avviata automaticamente. Per altre informazioni, vedere [procedure consigliate per i gruppi di failover con pool elastici](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Sebbene sia possibile creare un gruppo di failover che includa più pool elastici, è consigliabile creare un gruppo di failover separato per ogni pool. Se si dispone di un numero elevato di database in più pool elastici che è necessario spostare, è possibile eseguire i passaggi di preparazione in parallelo e quindi avviare il passaggio di spostamento in parallelo. Questo processo verrà ridimensionato meglio e sarà necessario meno tempo rispetto alla presenza di più pool elastici nello stesso gruppo di failover.

### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto di output di `Get-AzSqlDatabaseFailoverGroup` include una proprietà per **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) indica che il database è sincronizzato e che può essere sottoposta a failover in modo sicuro.
- **ReplicationState = 0** (seeding) indica che il database non è ancora stato sottoposta a seeding e un tentativo di eseguire il failover avrà esito negativo.

### <a name="test-synchronization"></a>Test della sincronizzazione

Quando **ReplicationState** è `2` , connettersi a ogni database o subset di database usando l'endpoint secondario `<fog-name>.secondary.database.windows.net` ed eseguire qualsiasi query sui database per garantire la connettività, la configurazione di sicurezza appropriata e la replica dei dati.

### <a name="initiate-the-move"></a>Avviare lo spostamento

1. Connettersi al server di destinazione utilizzando l'endpoint secondario `<fog-name>.secondary.database.windows.net` .
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) per impostare l'istanza gestita secondaria come primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback.
1. Verificare che il comando sia stato completato correttamente usando `nslook up <fog-name>.secondary.database.windows.net` per verificare che la voce DNS CNAME punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il CNAME non verrà aggiornato.

### <a name="remove-the-source-elastic-pools"></a>Rimuovere i pool elastici di origine

Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari.

1. Eliminare il gruppo di failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Eliminare ogni pool elastico di origine nel server di origine usando [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Eliminare il server di origine utilizzando [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Rimuovere l'insieme di credenziali delle chiavi, controllare i contenitori di archiviazione, l'hub eventi, Azure AD istanza e altre risorse dipendenti per interrompere la fatturazione.

## <a name="move-a-managed-instance"></a>Spostare un'istanza gestita

### <a name="verify-prerequisites"></a>Verificare i prerequisiti

1. Per ogni istanza gestita di origine, creare un'istanza di destinazione di SQL Istanza gestita della stessa dimensione nell'area di destinazione.  
1. Configurare la rete per un'istanza gestita. Per ulteriori informazioni, vedere [configurazione di rete](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Configurare il database master di destinazione con gli account di accesso corretti. Se non si è la sottoscrizione o l'amministratore di SQL Istanza gestita, collaborare con l'amministratore per assegnare le autorizzazioni necessarie.
1. Se i database vengono crittografati con Transparent Data Encryption e usano la propria chiave di crittografia in Azure Key Vault, assicurarsi che le Azure Key Vault con chiavi di crittografia identiche esistano sia nelle aree di origine che in quelle di destinazione. Per ulteriori informazioni, vedere [Transparent Data Encryption con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Se il controllo è abilitato per l'istanza gestita, verificare che:
    - Il contenitore di archiviazione o l'hub eventi con i log esistenti viene spostato nell'area di destinazione.
    - Il controllo è configurato nell'istanza di destinazione. Per altre informazioni, vedere [controllo con SQL istanza gestita](../managed-instance/auditing-configure.md).
1. Se l'istanza ha un criterio di conservazione a lungo termine, i backup di LTR esistenti rimarranno associati all'istanza corrente. Poiché l'istanza di destinazione è diversa, sarà possibile accedere ai precedenti backup di LTR nell'area di origine usando l'istanza di di origine, anche se l'istanza è stata eliminata.

  > [!NOTE]
  > Questa operazione non sarà sufficiente per lo trasferimento tra il cloud sovrano e un'area pubblica. Tale operazione richiede lo spostamento dei backup di LTR nell'istanza di destinazione, che non è attualmente supportata.

### <a name="prepare-resources"></a>Preparare le risorse

Creare un gruppo di failover tra ogni istanza gestita di origine e l'istanza di destinazione corrispondente di SQL Istanza gestita.

La replica di tutti i database in ogni istanza verrà avviata automaticamente. Per altre informazioni, vedere [gruppi di failover automatico](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Monitorare il processo di preparazione

È possibile chiamare periodicamente [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) per monitorare la replica dei database dall'origine alla destinazione. L'oggetto di output di `Get-AzSqlDatabaseFailoverGroup` include una proprietà per **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) indica che il database è sincronizzato e che può essere sottoposta a failover in modo sicuro.
- **ReplicationState = 0** (seeding) indica che non è ancora stato eseguito il seeding del database e un tentativo di eseguire il failover avrà esito negativo.

### <a name="test-synchronization"></a>Test della sincronizzazione

Quando **ReplicationState** è `2` , connettersi a ogni database o sottoinsieme di database usando l'endpoint secondario `<fog-name>.secondary.database.windows.net` ed eseguire qualsiasi query sui database per garantire la connettività, la configurazione di sicurezza appropriata e la replica dei dati.

### <a name="initiate-the-move"></a>Avviare lo spostamento

1. Connettersi all'istanza gestita di destinazione usando l'endpoint secondario `<fog-name>.secondary.database.windows.net` .
1. Usare [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) per impostare l'istanza gestita secondaria come primaria con sincronizzazione completa. Questa operazione avrà esito positivo o verrà eseguito il rollback.
1. Verificare che il comando sia stato completato correttamente usando `nslook up <fog-name>.secondary.database.windows.net` per verificare che la voce DNS CNAME punti all'indirizzo IP dell'area di destinazione. Se il comando switch ha esito negativo, il CNAME non verrà aggiornato.

### <a name="remove-the-source-managed-instances"></a>Rimuovere le istanze gestite di origine

Al termine dello spostamento, rimuovere le risorse nell'area di origine per evitare addebiti non necessari.

1. Eliminare il gruppo di failover usando [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Questa operazione eliminerà la configurazione del gruppo di failover e terminerà i collegamenti di replica geografica tra le due istanze.
1. Eliminare l'istanza gestita di origine usando [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Rimuovere eventuali risorse aggiuntive nel gruppo di risorse, ad esempio il cluster virtuale, la rete virtuale e il gruppo di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

[Gestire](manage-data-after-migrating-to-database.md) il database dopo la migrazione.
