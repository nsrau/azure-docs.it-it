---
title: Configurare endpoint pubblici - Database SQL di Azure gestiti istanza | Microsoft Docs
description: Informazioni su come configurare un endpoint pubblico per l'istanza gestita
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65465213"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurare endpoint pubblici in istanza gestita di Database SQL di Azure

Endpoint pubblico per un [istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) offre accesso ai dati a istanza gestita all'esterno di [rete virtuale](../virtual-network/virtual-networks-overview.md). Si è in grado di accedere all'istanza gestita da servizi di Azure multi-tenant, ad esempio Power BI, servizio App di Azure o una rete locale. Tramite l'endpoint pubblico in un'istanza gestita, non devi usare una VPN, che consentono di evitare problemi di velocità effettiva della VPN.

L'articolo spiega come:

> [!div class="checklist"]
> - Abilitare un endpoint pubblico per l'istanza gestita nel portale di Azure
> - Abilitare un endpoint pubblico per l'istanza gestita usando PowerShell
> - Configurare il gruppo di sicurezza di rete di istanza gestita per consentire il traffico all'endpoint pubblico di istanza gestita
> - Ottenere la stringa di connessione dell'endpoint pubblico di istanza gestita

## <a name="permissions"></a>Autorizzazioni

A causa della riservatezza dei dati che si trova in un'istanza gestita, la configurazione per abilitare endpoint pubblico con istanza gestita richiede un processo in due passaggi. Questa misura di sicurezza è conforme alla separazione dei compiti:

- Abilitazione dell'endpoint pubblico in un'istanza gestita deve essere eseguita dall'amministratore istanza gestita. L'amministratore dell'istanza gestita sono disponibili nella **Panoramica** risorsa di istanza gestita di pagina di SQL.
- Consentire il traffico usando un gruppo di sicurezza di rete che deve essere eseguita da un amministratore di rete. Per altre informazioni, vedere [autorizzazioni del gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Abilitazione dell'endpoint pubblico per un'istanza gestita nel portale di Azure

1. Avviare il portale di Azure all'indirizzo <https://portal.azure.com/.>
1. Aprire il gruppo di risorse con istanza gestita e selezionare il **istanza gestita di SQL** che si desidera configurare endpoint pubblici in.
1. Nel **sicurezza** impostazioni, selezionare la **rete virtuale** scheda.
1. Nella pagina Configurazione rete virtuale, selezionare **abilitare** e quindi la **salvare** icona per aggiornare la configurazione.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Abilitazione dell'endpoint pubblico per un'istanza gestita usando PowerShell

### <a name="enable-public-endpoint"></a>Abilitare l'endpoint pubblico

Eseguire i comandi di PowerShell seguenti. Sostituire **id-sottoscrizione** con l'ID sottoscrizione. Sostituire anche **rg-name** con il gruppo di risorse per l'istanza gestita e sostituisci **nome-istanza gestita** con il nome dell'istanza gestita.

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

Per disabilitare l'endpoint pubblico con PowerShell, si eseguirà il comando seguente (e anche non dimenticare di chiudere il gruppo di sicurezza per la porta in ingresso 3342 se è stato configurato):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Consentire il traffico di endpoint pubblica nel gruppo di sicurezza di rete

1. Se si dispone di pagina di configurazione dell'istanza gestita ancora aperto, passare al **Panoramica** scheda. In caso contrario, tornare indietro per la **istanza gestita di SQL** risorsa. Selezionare il **rete virtuale/subnet** collegamento, che consente di accedere alla pagina di configurazione di rete virtuale.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Selezionare il **subnet** scheda nel riquadro sinistro di configurazione della rete virtuale e assicurarsi di annotare il **gruppo di sicurezza** per l'istanza gestita.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Tornare al gruppo di risorse che contiene istanza gestita. Dovrebbero vedere le **gruppo di sicurezza di rete** nome indicato in precedenza. Selezionare il nome per passare alla pagina di configurazione gruppo di sicurezza di rete.

1. Selezionare il **regole di sicurezza in ingresso** scheda, e **Add** una regola con priorità più alta rispetto il **deny_all_inbound** regola con le impostazioni seguenti: </br> </br>

    |Impostazione  |Valore consigliato  |Descrizione  |
    |---------|---------|---------|
    |**Origine**     |Qualsiasi indirizzo IP o un tag di servizio         |<ul><li>Per i servizi di Azure, come Power BI, selezionare il Tag di servizio Cloud di Azure</li> <li>Per il computer o macchina virtuale di Azure, usare l'indirizzo IP NAT</li></ul> |
    |**Intervalli di porte di origine**     |*         |Lasciare questa opzione per * (qualsiasi) come le porte di origine sono in genere imprevedibili allocata in modo dinamico e come tale, |
    |**Destinazione**     |Qualsiasi         |Lasciando destinazione as Any per consentire il traffico nella subnet dell'istanza gestita |
    |**Intervalli di porte di destinazione**     |3342         |Porta di destinazione di ambito per 3342, ovvero l'endpoint TDS pubblico istanza gestita |
    |**Protocollo**     |TCP         |Protocollo TCP utilizza istanza gestita TDS |
    |**Azione**     |CONSENTI         |Consentire il traffico in ingresso all'istanza gestita tramite l'endpoint pubblico |
    |**Priorità**     |1300         |Assicurarsi che questa regola è prioritario rispetto a quelli di **deny_all_inbound** regola |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Porta 3342 viene usata per endpoint pubblico con le connessioni a istanza gestita e non possono essere modificate a questo punto.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Come ottenere la stringa di connessione dell'endpoint pubblico di istanza gestita

1. Passare alla pagina Configurazione istanza gestita di SQL che è stata abilitata per endpoint pubblico. Selezionare il **stringhe di connessione** scheda sotto il **impostazioni** configurazione.
1. Notare che include il nome host dell'endpoint pubblico nel formato < mi_name >. **pubblica**. < dns_zone >. database.windows.net e che la porta usata per la connessione sia 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri [istanza in modo sicuro con endpoint pubblico con il Database SQL di Azure gestita](sql-database-managed-instance-public-endpoint-securely.md).