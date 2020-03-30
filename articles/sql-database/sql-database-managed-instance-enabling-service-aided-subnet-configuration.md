---
title: Abilitazione della configurazione della subnet assistita dal servizio per l'istanza gestita del database SQL di AzureEnabling service-aided subnet configuration for Azure SQL Database Managed Instance
description: Abilitazione della configurazione della subnet assistita dal servizio per l'istanza gestita del database SQL di AzureEnabling service-aided subnet configuration for Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533267"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Abilitazione della configurazione della subnet assistita dal servizio per l'istanza gestita del database SQL di AzureEnabling service-aided subnet configuration for Azure SQL Database Managed Instance
La configurazione delle subnet assistite dai servizi fornisce la gestione automatica della configurazione di rete per le subnet che ospitano le istanze gestite. Con la configurazione di subnet assistita dal servizio, l'utente rimane in pieno controllo dell'accesso ai dati (flussi di traffico TDS) mentre l'istanza gestita si assume la responsabilità di garantire un flusso ininterrotto del traffico di gestione per soddisfare il contratto di servizio.

I gruppi di sicurezza di rete e le regole della tabella di route configurati automaticamente sono visibili al cliente e annotati con prefisso _Microsoft.Sql-managedInstances_UseOnly__.

La configurazione assistita dal servizio viene abilitata `Microsoft.Sql/managedInstances` automaticamente dopo l'attivazione della delega della [subnet](../virtual-network/subnet-delegation-overview.md) per il provider di risorse.

> [!IMPORTANT] 
> Una volta attivata la delega della subnet, non è possibile disattivarla finché non si rimuove l'ultimo cluster virtuale dalla subnet. Per ulteriori informazioni su come eliminare il cluster virtuale, vedere il seguente [articolo.](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)

> [!NOTE] 
> Poiché la configurazione di subnet assistita da servizi è una funzionalità essenziale per la gestione del contratto di servizio, a partire dal 1 maggio 2020, non sarà possibile distribuire istanze gestite in subnet che non sono delegate al provider di risorse dell'istanza gestita. Il 1 luglio 2020 tutte le subnet contenenti istanze gestite verranno delegate automaticamente al provider di risorse dell'istanza gestita. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Abilitazione della delega delle subnet per le nuove distribuzioniEnabling subnet-delegation for new deployments
Per distribuire l'istanza gestita in una `Microsoft.Sql/managedInstances` subnet vuota, è necessario dedelirla al provider di risorse come descritto nell'articolo seguente. [article](../virtual-network/manage-subnet-delegation.md) _Si noti che `Microsoft.DBforPostgreSQL/serversv2` l'articolo di riferimento utilizza il provider di risorse, ad esempio. Sarà invece necessario `Microsoft.Sql/managedInstances` usare il provider di risorse._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Abilitazione della delega delle subnet per le distribuzioni esistentiEnabling subnet-delegation for existing deployments

Per abilitare la delega della subnet per la distribuzione dell'istanza gestita esistente, è necessario individuare la subnet della rete virtuale in cui è posizionata. 

Per informazioni, è `Virtual network/subnet` possibile `Overview` controllare il pannello del portale dell'istanza gestita.

In alternativa, è possibile eseguire i comandi di PowerShell seguenti per ottenere questo problema. Sostituire **subscription-id** con l'ID sottoscrizione. Sostituire anche **rg-name** con il gruppo di risorse per l'istanza gestita e sostituire **mi-name** con il nome dell'istanza gestita.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Dopo aver individuato la subnet dell'istanza gestita, è necessario dedelirla al `Microsoft.Sql/managedInstances` provider di risorse come descritto [nell'articolo](../virtual-network/manage-subnet-delegation.md)seguente. _Si noti che `Microsoft.DBforPostgreSQL/serversv2` l'articolo di riferimento utilizza il provider di risorse, ad esempio. Sarà invece necessario `Microsoft.Sql/managedInstances` usare il provider di risorse._


> [!IMPORTANT]
> L'abilitazione della configurazione assistita da servizi non causa failover o interruzioni nella connettività per le istanze gestite già presenti nella subnet.
