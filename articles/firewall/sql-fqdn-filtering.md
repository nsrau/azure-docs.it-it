---
title: Configurare le regole dell'applicazione Firewall di Azure con FQDN SQLConfigure Azure Firewall application rules with SQL FQDNs
description: In questo articolo viene illustrato come configurare gli FQDN SQL nelle regole dell'applicazione Firewall di Azure.In this article, you learn how to configure SQL FQDNs in Azure Firewall application rules.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501506"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurare le regole dell'applicazione Firewall di Azure con FQDN SQLConfigure Azure Firewall application rules with SQL FQDNs

> [!IMPORTANT]
> Le regole delle applicazioni del firewall di Azure con FQDN SQL sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È ora possibile configurare le regole dell'applicazione Firewall di Azure con FQDN SQL. In questo modo è possibile limitare l'accesso dalle reti virtuali solo alle istanze di SQL Server specificate.

Con gli FQDN SQL, è possibile filtrare il traffico:With SQL FQDNs, you can filter traffic:

- Dalle reti virtuali a un database SQL di Azure o a un data warehouse SQL di Azure.From your VNets to an Azure SQL Database or an Azure SQL Data Warehouse. Ad esempio: consenti l'accesso solo a *sql-server1.database.windows.net*.
- Da istanze gestite SQL di Azure o SQL IaaS in esecuzione nelle reti virtuali.
- Da spoke-to-spoke alle istanze gestite SQL di Azure o SQL IaaS in esecuzione nelle reti virtuali.

Durante l'anteprima pubblica, il filtro FQDN SQL è supportato solo in [modalità proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se si utilizza SQL nella modalità di reindirizzamento predefinita, è possibile filtrare l'accesso utilizzando il tag del servizio SQL come parte delle regole di [rete](overview.md#network-traffic-filtering-rules).
Se si utilizzano porte non predefinite per il traffico SQL IaaS, è possibile configurare tali porte nelle regole dell'applicazione firewall.

Le regole dell'applicazione con FQDN SQL sono attualmente disponibili in tutte le aree tramite il portale di Azure, l'interfaccia della riga di comando di Azure, REST e i modelli.

## <a name="configure-using-azure-cli"></a>Configurare usando l'interfaccia della riga di comando di AzureConfigure using

1. Distribuire un firewall di [Azure usando l'interfaccia della riga di comando](deploy-cli.md)di Azure.Deploy an Azure Firewall using Azure CLI .
2. Se si filtra il traffico verso il database SQL di Azure, SQL Data Warehouse o istanza gestita SQL, verificare che la modalità di connettività SQL sia impostata su **Proxy**. Per informazioni su come passare dalla modalità di connettività SQL, vedere Impostazioni di [connettività SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)di Azure.

   > [!NOTE]
   > La modalità *proxy* SQL può comportare una maggiore latenza rispetto al *reindirizzamento.* Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando il tag del [servizio](service-tags.md) SQL nelle regole di [rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule)del firewall.

3. Configurare una regola dell'applicazione con FQDN SQL per consentire l'accesso a un server SQL:Configure an application rule with SQL FQDN to allow access to a SQL server:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurare con il Portale di Azure
1. Distribuire un firewall di [Azure usando l'interfaccia della riga di comando](deploy-cli.md)di Azure.Deploy an Azure Firewall using Azure CLI .
2. Se si filtra il traffico verso il database SQL di Azure, SQL Data Warehouse o istanza gestita SQL, verificare che la modalità di connettività SQL sia impostata su **Proxy**. Per informazioni su come passare dalla modalità di connettività SQL, vedere Impostazioni di [connettività SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)di Azure.  

   > [!NOTE]
   > La modalità *proxy* SQL può comportare una maggiore latenza rispetto al *reindirizzamento.* Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando il tag del [servizio](service-tags.md) SQL nelle regole di [rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule)del firewall.
3. Aggiungere la regola dell'applicazione con il protocollo, la porta e l'FQDN SQL appropriati, quindi selezionare **Salva**.
   ![regola dell'applicazione con FQDN SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Accedere a SQL da una macchina virtuale in una rete virtuale che filtra il traffico attraverso il firewall. 
5. Verificare che i log di [Firewall di Azure](log-analytics-samples.md) mostrino che il traffico è consentito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle modalità di reindirizzamento e proxy SQL, vedere Architettura di connettività del database SQL di [Azure.](../sql-database/sql-database-connectivity-architecture.md)