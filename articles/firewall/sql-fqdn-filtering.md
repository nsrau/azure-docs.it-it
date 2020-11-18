---
title: Configurare le regole dell'applicazione Firewall di Azure con FQDN di SQL
description: Questo articolo illustra come configurare FQDN di SQL nelle regole dell'applicazione Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: 7256f94b8e8376cf98a279d085a131a4ce84826f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658623"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurare le regole dell'applicazione Firewall di Azure con FQDN di SQL

È ora possibile configurare le regole dell'applicazione Firewall di Azure con FQDN di SQL. In questo modo è possibile limitare l'accesso dalle reti virtuali solo alle istanze di SQL Server specificate.

Con i nomi di dominio completi di SQL è possibile filtrare il traffico:

- Dalla reti virtuali a un database SQL di Azure o ad Azure sinapsi Analytics. Ad esempio: Consentire l'accesso solo a *sql-server1.database.windows.net*.
- Da locale a istanze gestite di SQL di Azure o IaaS SQL in esecuzione in reti virtuali.
- Da spoke a spoke a istanze gestite di SQL di Azure o IaaS SQL in esecuzione in reti virtuali.

Il filtro FQDN di SQL è supportato solo in modalità [proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). Se si usa SQL nella modalità di reindirizzamento predefinita, è possibile filtrare l'accesso usando il tag del servizio SQL come parte delle [regole di rete](features.md#network-traffic-filtering-rules).
Se si usano porte non predefinite per il traffico IaaS SQL, è possibile configurare tali porte nelle regole dell'applicazione firewall.

## <a name="configure-using-azure-cli"></a>Configurare con l'interfaccia della riga di comando

1. Distribuire un [firewall di Azure con l'interfaccia della riga di comando di Azure](deploy-cli.md).
2. Se si filtra il traffico verso il database SQL di Azure, Azure sinapsi Analytics o SQL Istanza gestita, assicurarsi che la modalità di connettività SQL sia impostata su **proxy**. Per informazioni su come cambiare la modalità di connettività SQL, vedere [Impostazioni di connettività per SQL di Azure](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > La modalità *proxy di SQL* può comportare una latenza maggiore rispetto al *reindirizzamento*. Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando il [tag di servizio](service-tags.md) di SQL nelle [regole di rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule) del firewall.

3. Configurare una regola dell'applicazione con il nome di dominio completo di SQL per consentire l'accesso a un server SQL:

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
1. Distribuire un [firewall di Azure con l'interfaccia della riga di comando di Azure](deploy-cli.md).
2. Se si filtra il traffico verso il database SQL di Azure, Azure sinapsi Analytics o SQL Istanza gestita, assicurarsi che la modalità di connettività SQL sia impostata su **proxy**. Per informazioni su come cambiare la modalità di connettività SQL, vedere [Impostazioni di connettività per SQL di Azure](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).  

   > [!NOTE]
   > La modalità *proxy di SQL* può comportare una latenza maggiore rispetto al *reindirizzamento*. Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando il [tag di servizio](service-tags.md) di SQL nelle [regole di rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule) del firewall.
3. Aggiungere la regola dell'applicazione con il protocollo, la porta e il nome di dominio completo di SQL appropriati, quindi selezionare **Salva**.
   ![regole dell'applicazione con FQDN di SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Accedere a SQL da una macchina virtuale in una rete virtuale che filtra il traffico attraverso il firewall. 
5. Verificare che i [log del firewall di Azure](log-analytics-samples.md) mostrino che il traffico è consentito.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle modalità di reindirizzamento e proxy SQL, vedere [architettura della connettività del database SQL di Azure](../azure-sql/database/connectivity-architecture.md).