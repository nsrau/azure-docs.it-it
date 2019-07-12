---
title: Configurare le regole del Firewall di Azure application con FQDN di SQL
description: In questo articolo descrive come configurare gli FQDN SQL nelle regole del Firewall di Azure dell'applicazione.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786598"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurare le regole del Firewall di Azure application con FQDN di SQL

> [!IMPORTANT]
> Regole di applicazione Firewall di Azure con FQDN di SQL è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È ora possibile configurare regole del Firewall di Azure dell'applicazione con FQDN di SQL. Ciò consente di limitare l'accesso dalle reti virtuali solo specificato SQL nelle istanze del server.

Con FQDN di SQL, è possibile filtrare il traffico:

- Dalle reti virtuali in un Database SQL di Azure o un Azure SQL Data Warehouse. Ad esempio:  Consentire solo l'accesso al *sql-server1.database.windows.net*.
- Da locale per le istanze gestite di SQL Azure o IaaS di SQL in esecuzione nelle reti virtuali.
- Dallo spoke-a-spoke per le istanze gestite di SQL Azure o IaaS di SQL in esecuzione nelle reti virtuali.

Durante l'anteprima pubblica, il filtro FQDN di SQL è supportato in [modalità proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) solo (la porta 1433). Se si usa SQL in modalità reindirizzamento predefinito, è possibile filtrare l'accesso usando il tag di servizio SQL come parte della [regole di rete](overview.md#network-traffic-filtering-rules).
Se si usano porte non predefinite per il traffico SQL IaaS, è possibile configurare queste porte nelle regole firewall di applicazione.

> [!NOTE]
> Le regole di applicazione con FQDN di SQL è attualmente disponibile in tutte le aree tramite la CLI di Azure, REST e modelli. Interfaccia utente del portale viene aggiunto alle aree in modo incrementale e sarà disponibile in tutte le aree quando viene completata l'implementazione.

## <a name="configure-using-azure-cli"></a>Configurare tramite la CLI di Azure

1. Distribuire un' [Firewall di Azure usando Azure CLI](deploy-cli.md).
2. Se si filtra il traffico a istanza gestita di SQL, SQL Data Warehouse o Database SQL di Azure, assicurarsi che la modalità di connettività SQL viene impostata su **Proxy**. Per informazioni su come impostare la modalità di connettività SQL, vedere [architettura della connettività SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* modalità può comportare latenza maggiore rispetto alle *reindirizzare*. Se si desidera continuare a usare la modalità di reindirizzamento, che è il valore predefinito per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando il codice SQL [tag del servizio](service-tags.md) firewall [regole di rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Configurare una regola di applicazioni con FQDN di SQL per consentire l'accesso a SQL server:

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurare nel portale di Azure
1. Distribuire un' [Firewall di Azure usando Azure CLI](deploy-cli.md).
2. Se si filtra il traffico a istanza gestita di SQL, SQL Data Warehouse o Database SQL di Azure, assicurarsi che la modalità di connettività SQL viene impostata su **Proxy**. Per informazioni su come impostare la modalità di connettività SQL, vedere [architettura della connettività SQL Azure](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* modalità può comportare latenza maggiore rispetto alle *reindirizzare*. Se si desidera continuare a usare la modalità di reindirizzamento, che è il valore predefinito per i client che si connettono all'interno di Azure, è possibile filtrare l'accesso usando il codice SQL [tag del servizio](service-tags.md) firewall [regole di rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Aggiungere la regola delle applicazioni con il protocollo appropriato, porta e il FQDN di SQL e quindi selezionare **salvare**.
   ![regola delle applicazioni con FQDN di SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Accesso SQL da una macchina virtuale in una rete virtuale che consente di filtrare il traffico attraverso il firewall. 
5. Verificare che [Firewall di Azure registra](log-analytics-samples.md) Mostra il traffico è consentito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul proxy SQL e le modalità di reindirizzamento, vedere [architettura della connettività del database SQL di Azure](../sql-database/sql-database-connectivity-architecture.md).