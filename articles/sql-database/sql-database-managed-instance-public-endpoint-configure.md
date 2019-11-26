---
title: Configurare un'istanza gestita di endpoint pubblico
description: Informazioni su come configurare un endpoint pubblico per l'istanza gestita
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227991"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurare l'endpoint pubblico nell'istanza gestita di database SQL di Azure

L'endpoint pubblico per un' [istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) consente l'accesso ai dati all'istanza gestita dall'esterno della [rete virtuale](../virtual-network/virtual-networks-overview.md). È possibile accedere all'istanza gestita da servizi di Azure multi-tenant, ad esempio Power BI, app Azure servizio o una rete locale. Usando l'endpoint pubblico in un'istanza gestita, non è necessario usare una VPN, che può aiutare a evitare problemi di velocità effettiva della VPN.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> - Abilitare l'endpoint pubblico per l'istanza gestita nel portale di Azure
> - Abilitare l'endpoint pubblico per l'istanza gestita tramite PowerShell
> - Configurare il gruppo di sicurezza di rete dell'istanza gestita per consentire il traffico all'endpoint pubblico dell'istanza gestita
> - Ottenere la stringa di connessione dell'endpoint pubblico dell'istanza gestita

## <a name="permissions"></a>autorizzazioni

A causa della riservatezza dei dati presenti in un'istanza gestita, la configurazione per abilitare l'endpoint pubblico dell'istanza gestita richiede un processo in due passaggi. Questa misura di sicurezza si attiene alla separazione dei compiti (SoD):

- L'abilitazione dell'endpoint pubblico in un'istanza gestita deve essere eseguita dall'amministratore dell'istanza gestita. L'amministratore dell'istanza gestita è disponibile nella pagina **Panoramica** della risorsa istanza gestita di SQL.
- Consentire il traffico tramite un gruppo di sicurezza di rete che deve essere eseguito da un amministratore di rete. Per altre informazioni, vedere [autorizzazioni del gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Abilitazione dell'endpoint pubblico per un'istanza gestita nel portale di Azure

1. Avviare il portale di Azure in <https://portal.azure.com/.>
1. Aprire il gruppo di risorse con l'istanza gestita e selezionare l' **istanza di SQL gestita** in cui si vuole configurare l'endpoint pubblico.
1. In impostazioni **sicurezza** selezionare la scheda **rete virtuale** .
1. Nella pagina Configurazione rete virtuale selezionare **Abilita** e quindi l'icona **Salva** per aggiornare la configurazione.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Abilitazione dell'endpoint pubblico per un'istanza gestita tramite PowerShell

### <a name="enable-public-endpoint"></a>Abilita endpoint pubblico

Eseguire i comandi di PowerShell seguenti. Sostituire **Subscription-ID** con l'ID sottoscrizione. Sostituire anche **RG-Name** con il gruppo di risorse per l'istanza gestita e sostituire **mi-Name** con il nome dell'istanza gestita.

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

### <a name="disable-public-endpoint"></a>Disabilitare l'endpoint pubblico

Per disabilitare l'endpoint pubblico usando PowerShell, eseguire il comando seguente (e non dimenticare di chiudere NSG per la porta in ingresso 3342 se è stato configurato):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Consenti traffico endpoint pubblico nel gruppo di sicurezza di rete

1. Se la pagina di configurazione dell'istanza gestita è ancora aperta, passare alla scheda **Panoramica** . in caso contrario, tornare alla risorsa **istanza gestita di SQL** . Selezionare il collegamento **rete virtuale/subnet** , che consente di passare alla pagina Configurazione rete virtuale.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selezionare la scheda **subnet** nel riquadro di configurazione a sinistra della rete virtuale e prendere nota del **gruppo di sicurezza** per l'istanza gestita.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Tornare al gruppo di risorse che contiene l'istanza gestita. Verrà visualizzato il nome del **gruppo di sicurezza di rete** indicato in precedenza. Selezionare il nome da visualizzare nella pagina di configurazione del gruppo di sicurezza di rete.

1. Selezionare la scheda **regole di sicurezza in ingresso** e **aggiungere** una regola con una priorità più alta rispetto alla regola **deny_all_inbound** con le impostazioni seguenti: </br> </br>

    |Impostazione  |Valore consigliato  |DESCRIZIONE  |
    |---------|---------|---------|
    |**Origine**     |Qualsiasi indirizzo IP o tag di servizio         |<ul><li>Per i servizi di Azure come Power BI, selezionare il tag del servizio cloud di Azure</li> <li>Per il computer o la macchina virtuale di Azure, usare l'indirizzo IP NAT</li></ul> |
    |**Intervalli di porte di origine**     |*         |Lasciarlo a * (qualsiasi) perché le porte di origine vengono in genere allocate in modo dinamico e come tali, imprevedibili |
    |**Destinazione**     |Qualsiasi         |Uscita da destinazione per consentire il traffico nella subnet dell'istanza gestita |
    |**Intervalli di porte di destinazione**     |3342         |Porta di destinazione dell'ambito su 3342, ovvero l'endpoint TDS pubblico dell'istanza gestita |
    |**Protocollo**     |TCP         |Istanza gestita usa il protocollo TCP per TDS |
    |**Azione**     |Consenti         |Consentire il traffico in ingresso verso l'istanza gestita tramite l'endpoint pubblico |
    |**Priorità**     |1300         |Verificare che questa regola abbia una priorità più alta rispetto alla regola di **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > La porta 3342 viene usata per le connessioni dell'endpoint pubblico all'istanza gestita e non può essere modificata in questo momento.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Recupero della stringa di connessione dell'endpoint pubblico dell'istanza gestita

1. Passare alla pagina di configurazione dell'istanza gestita di SQL abilitata per l'endpoint pubblico. Selezionare la scheda **stringhe di connessione** nella configurazione **Impostazioni** .
1. Si noti che il nome host dell'endpoint pubblico è nel formato < mi_name >. **public**. < dns_zone >. database. Windows. NET e che la porta usata per la connessione è 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [uso dell'istanza gestita di database SQL di Azure in modo sicuro con l'endpoint pubblico](sql-database-managed-instance-public-endpoint-securely.md).