---
title: Controllo all'account di archiviazione dietro VNet e firewall
description: Configurare il controllo per scrivere eventi di database in un account di archiviazione dietro la rete virtuale e il firewallConfigure auditing to write database events on a storage account behind virtual network and firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387632"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Scrivere il controllo in un account di archiviazione protetto da rete virtuale e firewallWrite audit to a storage account behind VNet and firewall

Il controllo per [il database SQL di Azure](sql-database-technical-overview.md) e Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) supporta la scrittura di eventi di database in un account di Archiviazione di [Azure](../storage/common/storage-account-overview.md) dietro una rete virtuale e un firewall. 

Questo articolo illustra due modi per configurare Azure SQL Server e l'account di archiviazione di Azure per questa opzione. Il primo usa il portale di Azure, il secondo usa REST.

### <a name="background"></a>Background

Rete virtuale di Azure (VNet) è il blocco predefinito fondamentale per la rete privata in [Azure.Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) is the fundamental building block for your private network in Azure. VNet consente a diversi tipi di risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra di esse, con Internet e con le reti locali. La rete virtuale è simile a una rete tradizionale nel proprio data center, ma offre vantaggi aggiuntivi dell'infrastruttura di Azure, ad esempio scalabilità, disponibilità e isolamento.

Per altre informazioni sui concetti di VNet, sulle procedure consigliate e molto altro ancora, vedere [Che cos'è Rete virtuale](../virtual-network/virtual-networks-overview.md)di Azure.

Per altre informazioni su come creare una rete virtuale, vedere Guida introduttiva: Creare una rete virtuale usando il portale di Azure.To learn more about how to create a virtual network, see [Quickstart: Create a virtual network using the Azure portal.](../virtual-network/quick-create-portal.md)

## <a name="prerequisites"></a>Prerequisiti

Affinché il controllo scriva in un account di archiviazione dietro una rete virtuale o un firewall, sono necessari i prerequisiti seguenti:For audit to write to a storage account behind a VNet or firewall, the following prerequisites are required:

> [!div class="checklist"]
> * Un account di archiviazione v2 generico. Se si dispone di un account di archiviazione BLOB o v1 generico, [eseguire l'aggiornamento a un account](../storage/common/storage-account-upgrade.md)di archiviazione per la versione 2 generico. Per altre informazioni, vedere [Tipi di account di archiviazione](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * L'account di archiviazione deve trovarsi nella stessa sottoscrizione e nello stesso percorso del server di database SQL di Azure.The storage account must be on the same subscription and at the same location of the Azure SQL Database server. 
> * L'account di `Allow trusted Microsoft services to access this storage account`archiviazione di Azure richiede . Impostarlo su Firewall account di archiviazione **e reti virtuali**.
> * È necessario `Microsoft.Authorization/roleAssignments/write` disporre dell'autorizzazione per l'account di archiviazione selezionato. Per altre informazioni, vedere Ruoli predefiniti di Azure.For more information, see [Azure built-in roles](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurare nel portale di Azure

Connettersi al portale di [Azure](https://portal.azure.com) con la sottoscrizione. Passare al gruppo di risorse e al server di database SQL di Azure.Navigate to the resource group and Azure SQL database server.

1. Fare clic su **Controllo** sotto l'intestazione Sicurezza. Selezionare **On (Attivato).**

2. Selezionare **Archiviazione**. Selezionare l'account di archiviazione in cui verranno salvati i log. L'account di archiviazione deve essere conforme ai requisiti elencati in [Prerequisiti](#prerequisites).

3. Aprire i **dettagli di archiviazioneOpen Storage details** 

  > [!NOTE]
  > Se l'account di archiviazione selezionato è dietro la rete virtuale, verrà visualizzato il seguente messaggio:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se questo messaggio non viene visualizzato, l'account di archiviazione non si trova dietro una rete virtuale.

4. Selezionare il numero di giorni per il periodo di conservazione. Fare quindi clic su **OK**. I registri precedenti al periodo di conservazione vengono eliminati.

5. Selezionare **Salva** nelle impostazioni di controllo.

Il controllo è stato configurato correttamente per scrivere in un account di archiviazione dietro una rete virtuale o un firewall. 

## <a name="configure-with-rest-commands"></a>Configurare con i comandi REST

In alternativa all'uso del portale di Azure, è possibile usare i comandi REST per configurare il controllo per scrivere eventi di database in un account di archiviazione dietro una rete virtuale e un firewall. 

Gli script di esempio in questa sezione richiedono l'aggiornamento dello script prima di eseguirli. Sostituire i valori seguenti negli script:

|Valore di esempio|Descrizione di esempio|
|:-----|:-----|
|`<subscriptionId>`| ID sottoscrizione di Azure|
|`<resource group>`| Resource group|
|`<sql database server>`| Nome del server di database SQL di Azure|
|`<administrator login>`| Account amministratore del database SQL |
|`<complex password>`| Password complessa per l'account amministratore|

To configure SQL Audit to write events to a storage account behind a VNet or Firewall:

1. Registrare il server di database SQL di Azure con Azure Active Directory (Azure AD). Usare PowerShell o l'API REST.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**API REST**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Richiesta di esempio

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Corpo della richiesta

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Aprire il [portale di Azure](https://portal.azure.com). Passare all'account di archiviazione. Individuare **Controllo di accesso (IAM)** e fare clic su Aggiungi **assegnazione ruolo**. Assegnare il ruolo **Collaboratore dati BLOB di archiviazione** al gruppo SQL Server di Azure che ospita il database SQL di Azure registrato con Azure Active Directory (Azure AD) come nel passaggio precedente.

   > [!NOTE]
   > Solo i membri con il privilegio di proprietario possono eseguire questo passaggio. Per vari ruoli predefiniti per le risorse di Azure, vedere [Ruoli predefiniti di Azure.](../role-based-access-control/built-in-roles.md)

3. Configurare i [criteri di controllo BLOB di Azure SQL Server,](/rest/api/sql/server%20auditing%20settings/createorupdate)senza specificare *un'oggetto storageAccountAccessKey:*

   Richiesta di esempio

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Corpo della richiesta

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per creare un endpoint del servizio di rete virtuale e quindi una regola di rete virtuale per il database SQL di Azure.Use PowerShell to create a virtual network service endpoint, and then a virtual network rule for Azure SQL Database.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Regole di rete virtuale: operazioni con API RESTVirtual Network Rules: Operations with REST APIs](/rest/api/sql/virtualnetworkrules)
- [Usare endpoint servizio e regole di rete virtuale per server di database](sql-database-vnet-service-endpoint-rule-overview.md)
