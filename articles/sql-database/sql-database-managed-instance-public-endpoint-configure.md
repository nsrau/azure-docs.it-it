---
title: Configurare l'endpoint pubblico - istanza gestitaConfigure public endpoint - managed instance
description: Informazioni su come configurare un endpoint pubblico per l'istanza gestitaLearn how to configure a public endpoint for managed instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256159"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurare endpoint pubblici in Istanza gestita di database SQL di Azure

L'endpoint pubblico per [un'istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) consente l'accesso ai dati all'istanza gestita dall'esterno della [rete virtuale.](../virtual-network/virtual-networks-overview.md) È possibile accedere all'istanza gestita da servizi di Azure multi-tenant come Power BI, servizio app di Azure o una rete locale. Utilizzando l'endpoint pubblico in un'istanza gestita, non è necessario usare una VPN, che consente di evitare problemi di velocità effettiva VPN.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> - Abilitare l'endpoint pubblico per l'istanza gestita nel portale di AzureEnable public endpoint for your managed instance in the Azure portal
> - Abilitare l'endpoint pubblico per l'istanza gestita tramite PowerShellEnable public endpoint for your managed instance using PowerShell
> - Configurare il gruppo di sicurezza di rete dell'istanza gestita per consentire il traffico all'endpoint pubblico dell'istanza gestitaConfigure your managed instance network security group to allow traffic to the managed instance public endpoint
> - Ottenere la stringa di connessione dell'endpoint pubblico dell'istanza gestitaObtain the managed instance public endpoint connection string

## <a name="permissions"></a>Autorizzazioni

A causa della sensibilità dei dati in un'istanza gestita, la configurazione per abilitare l'endpoint pubblico dell'istanza gestita richiede un processo in due passaggi. Questa misura di sicurezza aderisce alla separazione dei compiti (SoD):

- L'abilitazione dell'endpoint pubblico in un'istanza gestita deve essere eseguita dall'amministratore dell'istanza gestita. L'amministratore dell'istanza gestita è disponibile nella pagina **Panoramica** della risorsa dell'istanza gestita SQL.
- Consentire il traffico utilizzando un gruppo di sicurezza di rete che deve essere eseguito da un amministratore di rete. Per ulteriori informazioni, vedere [Autorizzazioni per i gruppi](../virtual-network/manage-network-security-group.md#permissions)di sicurezza di rete .

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Abilitazione dell'endpoint pubblico per un'istanza gestita nel portale di AzureEnabling public endpoint for a managed instance in the Azure portal

1. Avviare il portale di Azure all'indirizzo<https://portal.azure.com/.>
1. Aprire il gruppo di risorse con l'istanza gestita e selezionare **l'istanza gestita SQL** in cui si vuole configurare l'endpoint pubblico.
1. Nelle impostazioni **di protezione,** selezionare la scheda **Rete virtuale.**
1. Nella pagina Configurazione rete virtuale selezionare **Abilita** e quindi l'icona **Salva** per aggiornare la configurazione.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Abilitazione di un endpoint pubblico per un'istanza gestita tramite PowerShellEnabling public endpoint for a managed instance using PowerShell

### <a name="enable-public-endpoint"></a>Abilita endpoint pubblico

Eseguire i comandi di PowerShell seguenti. Sostituire **subscription-id** con l'ID sottoscrizione. Sostituire anche **rg-name** con il gruppo di risorse per l'istanza gestita e sostituire **mi-name** con il nome dell'istanza gestita.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Disabilitare l'endpoint pubblicoDisable public endpoint

Per disabilitare l'endpoint pubblico tramite PowerShell, è necessario eseguire il comando seguente (e inoltre non dimenticare di chiudere il gruppo di sicurezza di rete per la porta in ingresso 3342 se è configurato):To disable the public endpoint using PowerShell, you would execute the following command (and also not forget to close the NSG for the inbound port 3342 if you have configured):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Consentire il traffico di endpoint pubblici nel gruppo di sicurezza di reteAllow public endpoint traffic on the network security group

1. Se la pagina di configurazione dell'istanza gestita è ancora aperta, passare alla scheda **Panoramica.** In caso contrario, tornare alla risorsa **dell'istanza gestita SQL.** Selezionare il collegamento **Rete virtuale/subnet** per passare alla pagina Configurazione rete virtuale.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selezionare la scheda **Subnet** nel riquadro di configurazione sinistro della rete virtuale e prendere nota del **gruppo SECURITY** per l'istanza gestita.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Tornare al gruppo di risorse che contiene l'istanza gestita. Verrà visualizzato il nome del gruppo di sicurezza di **rete** indicato in precedenza. Selezionare il nome da passare alla pagina di configurazione del gruppo di sicurezza di rete.

1. Selezionare la scheda Regole di **sicurezza in ingresso** e **Aggiungi** una regola con priorità più alta rispetto alla regola **di deny_all_inbound** con le impostazioni seguenti: </br> </br>

    |Impostazione  |Valore consigliato  |Descrizione  |
    |---------|---------|---------|
    |**origine**     |Qualsiasi indirizzo IP o tag di servizio         |<ul><li>Per i servizi di Azure come Power BI, selezionare il tag del servizio cloud di AzureFor Azure services like Power BI, select the Azure Cloud Service Tag</li> <li>Per il computer o la macchina virtuale di Azure, usare l'indirizzo IP NATFor your computer or Azure VM, use NAT IP address</li></ul> |
    |**Intervalli di porte di origine**     |*         |Lasciare questo valore a (qualsiasi) poiché le porte di origine vengono in genere allocate dinamicamente e, come tali, |
    |**Destinazione**     |Qualsiasi         |Lasciando la destinazione come Qualsiasi per consentire il traffico nella subnet dell'istanza gestita |
    |**Intervalli di porte di destinazione**     |3342         |Porta di destinazione dell'ambito a 3342, ovvero l'endpoint TDS pubblico dell'istanza gestitaScope destination port to 3342, which is the managed instance public TDS endpoint |
    |**Protocollo**     |TCP         |L'istanza gestita utilizza il protocollo TCP per TDS |
    |**Azione**     |Allow         |Consentire il traffico in ingresso all'istanza gestita tramite l'endpoint pubblicoAllow inbound traffic to managed instance through the public endpoint |
    |**Priority**     |1300         |Assicurarsi che la regola sia con priorità più alta rispetto alla regola **di deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > La porta 3342 viene utilizzata per le connessioni endpoint pubblici all'istanza gestita e non può essere modificata a questo punto.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Recupero della stringa di connessione dell'endpoint pubblico dell'istanza gestitaObtaining the managed instance public endpoint connection string

1. Passare alla pagina di configurazione dell'istanza gestita SQL abilitata per l'endpoint pubblico. Selezionare la scheda **Stringhe di connessione** nella configurazione Impostazioni.Select the Connection strings tab under the **Settings** configuration.
1. Si noti che il nome host dell'endpoint pubblico è nel formato <mi_name>. **public**.<dns_zone>.database.windows.net e che la porta utilizzata per la connessione è 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sull'uso dell'istanza gestita dal database SQL](sql-database-managed-instance-public-endpoint-securely.md)di Azure in modo sicuro con l'endpoint pubblico.