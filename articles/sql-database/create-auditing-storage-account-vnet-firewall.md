---
title: Controllo nell'account di archiviazione dietro VNet e firewall
description: Configurare il controllo per scrivere gli eventi del database in un account di archiviazione dietro la rete virtuale e il firewall
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
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Scrivere il controllo in un account di archiviazione dietro VNet e firewall

Il controllo per il [database SQL di Azure](sql-database-technical-overview.md) e l' [analisi di Azure sinapsi](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) supporta la scrittura di eventi di database in un [account di archiviazione di Azure](../storage/common/storage-account-overview.md) dietro una rete virtuale e un firewall. 

Questo articolo illustra due modi per configurare SQL Server di Azure e l'account di archiviazione di Azure per questa opzione. Il primo usa il portale di Azure, il secondo USA REST.

### <a name="background"></a>Informazioni

[Rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md) è il blocco predefinito fondamentale per la rete privata in Azure. VNet consente a diversi tipi di risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra di esse, con Internet e con le reti locali. VNet è simile a una rete tradizionale in data center, ma offre vantaggi aggiuntivi dell'infrastruttura di Azure, ad esempio scalabilità, disponibilità e isolamento.

Per altre informazioni sui concetti di VNet, le procedure consigliate e molto altro ancora, vedere [che cos'è la rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

Per altre informazioni su come creare una rete virtuale, vedere [Guida introduttiva: creare una rete virtuale usando il portale di Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per il controllo per la scrittura in un account di archiviazione dietro un VNet o un firewall, sono necessari i prerequisiti seguenti:

> [!div class="checklist"]
> * Un account di archiviazione per utilizzo generico V2. Se si dispone di un account di archiviazione BLOB o V1 per utilizzo generico, [eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2](../storage/common/storage-account-upgrade.md). Per altre informazioni, vedere [tipi di account di archiviazione](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * L'account di archiviazione deve trovarsi nella stessa sottoscrizione e nello stesso percorso del server di database SQL di Azure. 
> * Per l'account di archiviazione `Allow trusted Microsoft services to access this storage account`di Azure è necessario. Impostare questa impostazione su **firewall e reti virtuali**dell'account di archiviazione.
> * È necessario disporre `Microsoft.Authorization/roleAssignments/write` dell'autorizzazione per l'account di archiviazione selezionato. Per altre informazioni, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurare nel portale di Azure

Connettersi a [portale di Azure](https://portal.azure.com) con la sottoscrizione. Passare al gruppo di risorse e al server del database SQL di Azure.

1. Fare clic su **controllo** sotto l'intestazione sicurezza. Selezionare **on**.

2. Selezionare **Archiviazione**. Selezionare l'account di archiviazione in cui verranno salvati i log. L'account di archiviazione deve essere conforme ai requisiti elencati in [prerequisiti](#prerequisites).

3. Apri **Dettagli archiviazione** 

  > [!NOTE]
  > Se l'account di archiviazione selezionato è dietro VNet, verrà visualizzato il messaggio seguente:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se questo messaggio non viene visualizzato, l'account di archiviazione non si trova dietro un VNet.

4. Consente di selezionare il numero di giorni per il periodo di memorizzazione. Fare quindi clic su **OK**. I log antecedenti al periodo di conservazione vengono eliminati.

5. Selezionare **Salva** nelle impostazioni di controllo.

Il controllo è stato configurato correttamente per la scrittura in un account di archiviazione dietro un VNet o un firewall. 

## <a name="configure-with-rest-commands"></a>Configurare con i comandi REST

In alternativa all'uso del portale di Azure, è possibile usare i comandi REST per configurare il controllo per la scrittura di eventi di database in un account di archiviazione dietro VNet e firewall. 

Per gli script di esempio di questa sezione è necessario aggiornare lo script prima di eseguirli. Sostituire i valori seguenti negli script:

|Valore di esempio|Descrizione di esempio|
|:-----|:-----|
|`<subscriptionId>`| ID sottoscrizione di Azure|
|`<resource group>`| Resource group|
|`<sql database server>`| Nome del server di database SQL di Azure|
|`<administrator login>`| Account amministratore database SQL |
|`<complex password>`| Password complessa per l'account amministratore|

Per configurare il controllo SQL per la scrittura di eventi in un account di archiviazione dietro un VNet o un firewall:

1. Registrare il server di database SQL di Azure con Azure Active Directory (Azure AD). Usare PowerShell o l'API REST.

   **PowerShell**
   
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

2. Aprire il [portale di Azure](https://portal.azure.com). Passare all'account di archiviazione. Individuare il **controllo di accesso (IAM)** e fare clic su **Aggiungi assegnazione ruolo**. Assegnare il ruolo di **collaboratore dei dati BLOB di archiviazione** al SQL Server di Azure che ospita il database SQL di Azure registrato con Azure Active Directory (Azure ad) come nel passaggio precedente.

   > [!NOTE]
   > Solo i membri con il privilegio di proprietario possono eseguire questo passaggio. Per diversi ruoli predefiniti per le risorse di Azure, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

3. Configurare i [criteri di controllo BLOB del server SQL di Azure](/rest/api/sql/server%20auditing%20settings/createorupdate)senza specificare un *storageAccountAccessKey*:

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

- [Usare PowerShell per creare un endpoint del servizio di rete virtuale e quindi una regola della rete virtuale per il database SQL di Azure.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Regole della rete virtuale: operazioni con le API REST](/rest/api/sql/virtualnetworkrules)
- [Usare endpoint servizio e regole di rete virtuale per server di database](sql-database-vnet-service-endpoint-rule-overview.md)
