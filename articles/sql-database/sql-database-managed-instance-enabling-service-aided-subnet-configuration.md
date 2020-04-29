---
title: Abilitazione della configurazione della subnet assistita da servizi per Istanza gestita di database SQL di Azure
description: Abilitazione della configurazione della subnet assistita da servizi per Istanza gestita di database SQL di Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533267"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Abilitazione della configurazione della subnet assistita da servizi per Istanza gestita di database SQL di Azure
La configurazione della subnet assistita da servizi consente la gestione automatica della configurazione di rete per le subnet che ospitano istanze gestite. Con la configurazione della subnet assistita da servizi, l'utente mantiene il controllo completo dell'accesso ai dati (flussi del traffico TDS) mentre l'istanza gestita si assume la responsabilità di garantire un flusso ininterrotto del traffico di gestione per poter soddisfare il contratto di servizio.

I gruppi di sicurezza di rete e le regole della tabella di route configurate automaticamente sono visibili al cliente e annotate con il prefisso _Microsoft. SQL-managedInstances_UseOnly__.

La configurazione assistita da servizi viene abilitata automaticamente dopo l'attivazione della delega `Microsoft.Sql/managedInstances` della [subnet](../virtual-network/subnet-delegation-overview.md) per il provider di risorse.

> [!IMPORTANT] 
> Una volta attivata la delega della subnet, non è possibile disattivarla fino a quando non si rimuove l'ultimo cluster virtuale dalla subnet. Per ulteriori informazioni su come eliminare un cluster virtuale, vedere l' [articolo](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)seguente.

> [!NOTE] 
> Poiché la configurazione della subnet assistita dal servizio è essenziale per la gestione del contratto di servizio, a partire dal 1 ° maggio 2020, non sarà possibile distribuire istanze gestite in subnet non delegate al provider di risorse dell'istanza gestita. Il 1 ° luglio 2020 tutte le subnet contenenti istanze gestite verranno delegate automaticamente al provider di risorse dell'istanza gestita. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Abilitazione della delega della subnet per le nuove distribuzioni
Per distribuire l'istanza gestita in una subnet vuota, è necessario delegarla al provider di `Microsoft.Sql/managedInstances` risorse, come descritto nell' [articolo](../virtual-network/manage-subnet-delegation.md)seguente. _Si noti che l'articolo a cui `Microsoft.DBforPostgreSQL/serversv2` si fa riferimento usa il provider di risorse, ad esempio. In alternativa, è necessario `Microsoft.Sql/managedInstances` usare il provider di risorse._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Abilitazione della delega della subnet per le distribuzioni esistenti

Per abilitare la delega della subnet per la distribuzione dell'istanza gestita esistente, è necessario individuare la subnet della rete virtuale in cui è posizionata. 

Per informazioni, è possibile controllare `Virtual network/subnet` il `Overview` pannello del portale dell'istanza gestita.

In alternativa, è possibile eseguire i comandi di PowerShell seguenti per apprendere questo problema. Sostituire **Subscription-ID** con l'ID sottoscrizione. Sostituire anche **RG-Name** con il gruppo di risorse per l'istanza gestita e sostituire **mi-Name** con il nome dell'istanza gestita.

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

Una volta trovata la subnet dell'istanza gestita, è necessario delegarla al provider di `Microsoft.Sql/managedInstances` risorse, come descritto nell' [articolo](../virtual-network/manage-subnet-delegation.md)seguente. _Si noti che l'articolo a cui `Microsoft.DBforPostgreSQL/serversv2` si fa riferimento usa il provider di risorse, ad esempio. In alternativa, è necessario `Microsoft.Sql/managedInstances` usare il provider di risorse._


> [!IMPORTANT]
> L'abilitazione della configurazione assistita da servizi non causa il failover o l'interruzione della connettività per le istanze gestite già presenti nella subnet.
