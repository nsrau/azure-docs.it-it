---
title: Controllare l'account di archiviazione dietro rete virtuale e firewall
description: Configurare il controllo per scrivere gli eventi di database in un account di archiviazione dietro la rete virtuale e il firewall e il firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: f916fdcf632cc369d1fb7e2faefad6dddafd1e15
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677240"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Controllo per la scrittura in un account di archiviazione dietro rete virtuale e firewall
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Il controllo per [database SQL di Azure](sql-database-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) supporta la scrittura di eventi di database in un [account di archiviazione di Azure](../../storage/common/storage-account-overview.md) dietro una rete virtuale e un firewall.

Questo articolo illustra due modi per configurare il database SQL di Azure e l'account di archiviazione di Azure per questa opzione. Il primo usa il portale di Azure, il secondo usa REST.

## <a name="background"></a>Background

[Rete virtuale di Azure (VNet)](../../virtual-network/virtual-networks-overview.md) è il blocco predefinito fondamentale per la rete privata in Azure. VNet consente a diversi tipi di risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra di esse, con Internet e con le reti locali. Simile alle reti tradizionali nei data center, VNet offre anche i vantaggi dell'infrastruttura di Azure, tra cui scalabilità, disponibilità e isolamento.

Per altre informazioni sui concetti relativi a VNet, sulle procedure consigliate e molto altro, vedere [Che cos'è Rete virtuale di Azure?](../../virtual-network/virtual-networks-overview.md).

Per ulteriori informazioni su come creare una rete virtuale, vedere [Avvio rapido: Creare una rete virtuale usando il portale di Azure](../../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Perché il controllo possa scrivere in un account di archiviazione dietro una rete virtuale o un firewall, sono necessari i prerequisiti seguenti:

> [!div class="checklist"]
>
> * Un account di archiviazione per utilizzo generico v2. Se si dispone di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB, [eseguire l’aggiornamento a un account di archiviazione per utilizzo generico v2](../../storage/common/storage-account-upgrade.md). Per altre informazioni, vedere [Tipi di account di archiviazione](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * L'account di archiviazione deve trovarsi nella stessa sottoscrizione e nello stesso percorso del [server SQL logico](logical-servers.md).
> * L'account di archiviazione di Azure richiede `Allow trusted Microsoft services to access this storage account`. Effettuare questa impostazione in **Firewall e reti virtuali** dell’account di archiviazione.
> * È necessaria l’autorizzazione `Microsoft.Authorization/roleAssignments/write` per l'account di archiviazione selezionato. Per altre informazioni, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurare nel portale di Azure

Connettersi al [portale di Azure](https://portal.azure.com) con la propria sottoscrizione. Passare al gruppo di risorse e al server.

1. Fare clic su **Controllo** sotto l'intestazione Sicurezza. Selezionare **Attivato** .

2. Selezionare **Archiviazione** . Selezionare l'account di archiviazione in cui verranno salvati i log. L'account di archiviazione deve essere conforme ai requisiti elencati in [Prerequisiti](#prerequisites).

3. Aprire **Dettagli archiviazione**

  > [!NOTE]
  > Se l'account di archiviazione selezionato è dietro VNet, verrà visualizzato il messaggio seguente:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se questo messaggio non viene visualizzato, l'account di archiviazione non si trova dietro una rete virtuale.

4. Selezionare il numero di giorni per il periodo di conservazione. Fare quindi clic su **OK** . I log antecedenti al periodo di conservazione vengono eliminati.

5. Selezionare **Salva** nelle impostazioni di controllo.

Il controllo è stato configurato correttamente per la scrittura in un account di archiviazione dietro una rete virtuale o un firewall.

## <a name="configure-with-rest-commands"></a>Configurare con i comandi REST

In alternativa all'uso del portale di Azure, è possibile usare i comandi REST per configurare il controllo per la scrittura di eventi di database in un account di archiviazione dietro una rete virtuale e un firewall.

Per gli script di esempio di questa sezione, è necessario aggiornare lo script prima di eseguirlo. Sostituire i valori seguenti negli script:

|Valore di esempio|Descrizione di esempio|
|:-----|:-----|
|`<subscriptionId>`| ID sottoscrizione di Azure|
|`<resource group>`| Resource group|
|`<logical SQL server>`| Nome server|
|`<administrator login>`| Account amministratore |
|`<complex password>`| Password complessa dell'account amministratore|

Per configurare il controllo SQL per la scrittura di eventi in un account di archiviazione dietro una rete virtuale o un firewall:

1. Registrare il server con Azure Active Directory (Azure AD). Usare PowerShell o l'API REST.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**API REST**](/rest/api/sql/servers/createorupdate):

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

2. Aprire il [portale di Azure](https://portal.azure.com). Passare all'account di archiviazione. Individuare **Controllo di accesso (IAM)** e fare clic su **Aggiungi un'assegnazione di ruolo** . Assegnare il ruolo di Azure **collaboratore dati BLOB di archiviazione** al server che ospita il database registrato con Azure Active Directory (Azure ad) come nel passaggio precedente.

   > [!NOTE]
   > Solo i membri con il privilegio di proprietario possono eseguire questo passaggio. Per i vari ruoli predefiniti di Azure, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

3. Configurare i [criteri di controllo BLOB del server](/rest/api/sql/server%20auditing%20settings/createorupdate)senza specificare un *storageAccountAccessKey* :

   Richiesta di esempio

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
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

## <a name="using-azure-powershell"></a>Uso di Azure PowerShell

- [Creare o aggiornare il criterio di controllo del database (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Creare o aggiornare il criterio di controllo del server (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager

È possibile configurare il controllo per scrivere eventi di database in un account di archiviazione dietro la rete virtuale e il firewall usando [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modello, come illustrato nell'esempio seguente:

> [!IMPORTANT]
> Per usare l'account di archiviazione dietro la rete virtuale e il firewall, è necessario impostare il parametro **isStorageBehindVnet** su true

- [Distribuire un server SQL di Azure con il controllo abilitato per scrivere i log di controllo in un archivio BLOB](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> L'esempio collegato si trova in un repository pubblico esterno e viene fornito "così com'è", senza garanzia e non è supportato in alcun programma o servizio di supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi

* [Usare PowerShell per creare un endpoint servizio di rete virtuale e quindi una regola di rete virtuale per il database SQL di Azure.](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Regole di rete virtuale: Operazioni con API REST](/rest/api/sql/virtualnetworkrules)
* [Usare gli endpoint del servizio rete virtuale e le regole per i server](vnet-service-endpoint-rule-overview.md)