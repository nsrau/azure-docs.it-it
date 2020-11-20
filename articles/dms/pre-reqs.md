---
title: Prerequisiti per il servizio migrazione del database di Azure
description: Informazioni sulla panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni di database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: f6438fb1c21ce248f6e1b766e7e10cc79043db9f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961584"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure

Sono necessari alcuni prerequisiti per garantire che il servizio migrazione del database di Azure venga eseguito senza problemi durante l'esecuzione delle migrazioni del database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

I prerequisiti associati all'utilizzo del Servizio Migrazione del database di Azure sono elencati nelle sezioni seguenti.

## <a name="prerequisites-common-across-migration-scenarios"></a>Prerequisiti comuni tra gli scenari di migrazione

In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:

* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Verificare che le regole del gruppo di sicurezza di rete della rete virtuale (NSG) non blocchino le porte di comunicazione seguenti 443, 53, 9354, 445, 12000. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Configurare [Windows Firewall per l'accesso al motore di database](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abilitare il protocollo TCP/IP, che viene disabilitato per impostazione predefinita durante l'installazione di SQL Server Express, seguendo le istruzioni riportate nell'articolo [Abilitare o disabilitare un protocollo di rete del server](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > La creazione di un'istanza del servizio migrazione del database di Azure richiede l'accesso alle impostazioni della rete virtuale che in genere non rientrano nello stesso gruppo di risorse. Di conseguenza, l'utente che crea un'istanza di DMS richiede l'autorizzazione a livello di sottoscrizione. Per creare i ruoli necessari, che è possibile assegnare in base alle esigenze, eseguire lo script seguente:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Prerequisiti per la migrazione di un database SQL Server a un database SQL di Azure

Oltre ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione, ci sono anche altri prerequisiti che si applicano specificatamente a uno scenario o all'altro.

Quando si usa il Servizio Migrazione del database di Azure per eseguire le migrazioni di un database SQL Server a un database SQL di Azure, oltre ai prerequisiti comuni a tutti gli scenari di migrazione, è necessario risolvere i seguenti prerequisiti aggiuntivi:

* Creare un'istanza dell'istanza del database SQL di Azure, seguendo le istruzioni riportate nell'articolo [creare un database nel database SQL di Azure nel portale di Azure](../azure-sql/database/single-database-create-quickstart.md).
* Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 o versione successiva.
* Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.
* Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Creare una [regola del firewall](../azure-sql/database/firewall-configure.md) a livello di server per il database SQL per consentire al servizio migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
* Assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano le autorizzazioni [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Verificare che le credenziali utilizzate per connettersi al database di destinazione dispongano dell'autorizzazione CONTROL DATABASE per il database di destinazione.

   > [!NOTE]
   > Per l'elenco completo dei prerequisiti richiesti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni da SQL Server al database SQL di Azure, vedere l'esercitazione [Eseguire la migrazione di SQL Server nel database SQL di Azure](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Prerequisiti per la migrazione di SQL Server ad Azure SQL Istanza gestita

* Per creare un Istanza gestita SQL, seguire le istruzioni riportate nell'articolo [creare un istanza gestita SQL di Azure nella portale di Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
* Aprire i firewall per consentire il traffico SMB sulla porta 445 per l'intervallo di indirizzi IP o di subnet del Servizio Migrazione del database di Azure.
* Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.
* Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Verificare che gli accessi usati per connettersi all'istanza di SQL Server di origine e all'istanza gestita di destinazione siano membri del ruolo sysadmin del server.
* Creare una condivisione di rete che può essere usata dal servizio Migrazione del database di Azure per il backup del database di origine.
* Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
* Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il servizio migrazione del database di Azure rappresenta le credenziali dell'utente per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
* Creare un contenitore BLOB e recuperare l'URI della firma di accesso condiviso associato usando la procedura descritta nell'articolo [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Selezionare tutte le autorizzazioni (lettura, scrittura, eliminazione, elenco) nella finestra dei criteri durante la creazione dell'URI SAS.

   > [!NOTE]
   > Per un elenco completo dei prerequisiti necessari per usare il servizio migrazione del database di Azure per eseguire le migrazioni da SQL Server a SQL Istanza gestita, vedere l'esercitazione [eseguire la migrazione di SQL Server a sql istanza gestita](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).