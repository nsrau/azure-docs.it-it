---
title: Configurare i firewall e le reti virtuali di Archiviazione di Azure | Microsoft Docs
description: Configurare la sicurezza di rete su più livelli per l'account di archiviazione.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: c475fc4d958044413ae7566c027c0e796f1d699a
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486394"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurare i firewall e le reti virtuali di Archiviazione di Azure

Archiviazione di Azure offre un modello di sicurezza su più livelli, che consente di proteggere gli account di archiviazione limitandoli a un set specifico di reti supportate. Quando si configurano le regole di rete, solo le applicazioni che richiedono dati dal set di reti specificato possono accedere a un account di archiviazione.

Per accedere a un account di archiviazione quando le regole di rete sono applicate, un'applicazione deve inviare una richiesta che deve essere correttamente autorizzata. Autorizzazione è supportato con le credenziali di Azure Active Directory (Azure AD) per i BLOB e code, con una chiave di accesso di account valido o con un token di firma di accesso condiviso.

> [!IMPORTANT]
> L'attivazione delle regole firewall per l'account di archiviazione blocca le richieste in ingresso per i dati per impostazione predefinita, a meno che le richieste non provengano da un servizio che opera all'interno di una rete virtuale di Azure. Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via.
>
> È possibile concedere l'accesso ai servizi di Azure eseguiti all'interno di una rete virtuale consentendo l'accesso alla subnet dell'istanza del servizio. Abilitare un numero limitato di scenari tramite il meccanismo di [eccezioni](#exceptions) descritto nella sezione seguente. Per accedere al portale di Azure, è necessario usare un computer all'interno del limite attendibile (IP o rete virtuale) configurato.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenari

Configurare gli account di archiviazione in modo da impedire l'accesso al traffico da qualsiasi rete (incluso il traffico Internet) per impostazione predefinita. Consentire quindi l'accesso al traffico da reti virtuali specifiche. Questa configurazione consente di creare un limite di rete protetto per le applicazioni. È anche possibile concedere l'accesso a intervalli di indirizzi IP della rete Internet pubblica, abilitando le connessioni da client Internet o client locali specifici.

Le regole di rete vengono applicate a tutti i protocolli di rete per l'archiviazione di Azure, tra cui REST e SMB. Per accedere ai dati con strumenti come il portale di Azure, Storage Explorer e AZCopy, è necessario configurare regole di rete esplicite.

È possibile applicare le regole di rete ad account di archiviazione esistenti oppure al momento della creazione di nuovi account di archiviazione.

Una volta configurate, le regole di rete vengono applicate a tutte le richieste. I token di firma di accesso condiviso che concedono l'accesso a un indirizzo IP specifico hanno lo scopo di limitare l'accesso del titolare del token, ma non concedono nuovi accessi oltre le regole di rete configurate.

Le regole di rete non influiscono sul traffico del disco della macchina virtuale (incluse le operazioni di montaggio e smontaggio e l'I/O del disco). L'accesso REST ai BLOB di pagine è protetto dalle regole di rete.

Gli account di archiviazione classici non supportano i firewall e le reti virtuali.

È possibile usare dischi non gestiti in account di archiviazione a cui sono applicate regole di rete per eseguire operazioni di backup e ripristino di macchine virtuali mediante la creazione di un'eccezione. Questo processo è documentato nella sezione [Eccezioni](#exceptions) di questo articolo. Le eccezioni firewall non sono applicabili ai dischi gestiti perché sono già gestiti da Azure.

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete

Per impostazione predefinita, gli account di archiviazione accettano connessioni da client di qualsiasi rete. Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> La modifica delle regole di rete può influire sulla capacità di connessione delle applicazioni al servizio Archiviazione di Azure. L'impostazione della regola di rete predefinita **Nega** blocca l'accesso a tutti i dati, a meno che non vengano applicate anche regole di rete specifiche che **concedono** l'accesso. Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete.

### <a name="managing-default-network-access-rules"></a>Gestione delle regole predefinite di accesso alla rete

Le regole predefinite di accesso alla rete per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **Reti selezionate**. Per consentire il traffico da tutte le reti, scegliere di consentire l'accesso da **Tutte le reti**.

1. Fare clic su **Salva** per applicare le modifiche.

#### <a name="powershell"></a>PowerShell

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

1. Visualizzare lo stato della regola predefinita per l'account di archiviazione.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>Interfaccia della riga di comando v2

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

1. Visualizzare lo stato della regola predefinita per l'account di archiviazione.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Concedere l'accesso da una rete virtuale

È possibile configurare gli account di archiviazione in modo da consentire l'accesso solo da reti virtuali specifiche.

Abilitare un [endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview) per Archiviazione di Azure all'interno della rete virtuale. Questo endpoint indirizza il traffico in maniera ottimale al servizio Archiviazione di Azure. Con ogni richiesta vengono anche trasmesse le identità della rete virtuale e della subnet. Gli amministratori possono quindi configurare regole di rete per l'account di archiviazione che consentono la ricezione di richieste da subnet specifiche nella rete virtuale. Per accedere ai dati, i client ai quali viene garantito l'accesso con queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione dell'account di archiviazione.

Ogni account di archiviazione supporta fino a 100 regole di rete virtuale, che possono essere combinate con [regole di rete IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Aree della rete virtuale disponibili

In generale gli endpoint di servizio funzionano tra reti virtuali e istanze del servizio incluse nella stessa area di Azure. Quando si usano endpoint di servizio con Archiviazione di Azure, questo ambito viene espanso per includere le [aree abbinate](/azure/best-practices-availability-paired-regions). Gli endpoint di servizio garantiscono la continuità durante un failover a livello di area, nonché l'accesso alle istanze di archiviazione con ridondanza geografica e accesso in lettura. Le regole di rete che concedono l'accesso da una rete virtuale a un account di archiviazione concedono anche l'accesso a qualsiasi istanza RA-GRS.

Quando si pianifica il ripristino di emergenza durante un'interruzione a livello di area, è consigliabile creare in anticipo le reti virtuali nell'area abbinata. Abilitare gli endpoint di servizio per Archiviazione di Azure, con regole di rete che concedono l'accesso da queste reti virtuali alternative. Applicare quindi le regole agli account di archiviazione con ridondanza geografica.

> [!NOTE]
> Gli endpoint di servizio non si applicano al traffico esterno all'area della rete virtuale di Microsoft Azure e alla coppia di aree designata. Le regole di rete che concedono l'accesso dalle reti virtuali agli account di archiviazione possono essere applicate solo nell'area primaria di un account di archiviazione o nell'area abbinata designata.

### <a name="required-permissions"></a>Autorizzazioni necessarie

Per applicare una regola di rete virtuale a un account di archiviazione, l'utente deve avere le autorizzazioni appropriate per le subnet aggiunte. L'autorizzazione necessaria è *Join Service to a Subnet* (Aggiungi servizio a una subnet), inclusa nel ruolo predefinito *Collaboratore Account di archiviazione*. Può anche essere aggiunta a definizioni del ruolo personalizzate.

Gli account di archiviazione e le reti virtuali alle quali è stato garantito l'accesso possono trovarsi in sottoscrizioni diverse, ma tali sottoscrizioni devono appartenere allo stesso tenant di Azure AD.

### <a name="managing-virtual-network-rules"></a>Gestione delle regole della rete virtuale

Le regole della rete virtuale per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una rete virtuale con una nuova regola di rete, in **Reti virtuali** fare clic su **Aggiungi rete virtuale esistente**, selezionare **Reti virtuali** e **Subnet**, quindi fare clic su **Aggiungi**. Per creare una nuova rete virtuale e concedere l'accesso, fare clic su **Aggiungi nuova rete virtuale**. Specificare le informazioni necessarie per creare la nuova rete virtuale e quindi fare clic su **Crea**.

    > [!NOTE]
    > Se un endpoint di servizio per Archiviazione di Azure non è stato configurato in precedenza per la rete virtuale e le subnet selezionate, è possibile configurarlo in questa operazione.

1. Per rimuovere una regola di rete virtuale o subnet, fare clic su **…** per aprire il menu di scelta rapida per la rete virtuale o la subnet, quindi fare clic su **Rimuovi**.

1. Fare clic su **Salva** per applicare le modifiche.

#### <a name="powershell"></a>PowerShell

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

1. Visualizzare l'elenco delle regole della rete virtuale.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Abilitare l'endpoint di servizio di Archiviazione di Azure in una rete virtuale e una subnet esistenti.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

#### <a name="cliv2"></a>Interfaccia della riga di comando v2

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

1. Visualizzare l'elenco delle regole della rete virtuale.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Abilitare l'endpoint di servizio di Archiviazione di Azure in una rete virtuale e una subnet esistenti.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

## <a name="grant-access-from-an-internet-ip-range"></a>Concedere l'accesso da un intervallo IP di Internet

È possibile configurare gli account di archiviazione in modo che consentano l'accesso da intervalli specifici di indirizzi IP Internet pubblici. Questa configurazione concede l'accesso ai servizi specifici basati su Internet e alle reti locali e blocca il traffico Internet generale.

Specificare gli intervalli di indirizzi Internet consentiti con la [notazione CIDR](https://tools.ietf.org/html/rfc4632) nel formato *16.17.18.0/24* o come indirizzi IP singoli, ad esempio *16.17.18.19*.

   > [!NOTE]
   > Gli intervalli di indirizzi di piccole dimensioni con dimensioni di prefisso "/31" o "/32" non sono supportati. Questi intervalli vanno configurati con le regole usate per gli indirizzi IP singoli.

Le regole di rete per gli IP sono consentite solo per gli indirizzi IP della **rete Internet pubblica**. Gli intervalli di indirizzi IP riservati per le reti private (come definito nell'[RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) non sono consentiti nelle regole IP. Le reti private includono gli indirizzi che iniziano con _10.*_, _172.16.*_ - _172.31.*_ e _192.168.*_.

   > [!NOTE]
   > Le regole della rete IP non hanno alcun effetto sulle richieste provenienti dalla stessa area di Azure dell'account di archiviazione. Usare le [regole della rete virtuale](#grant-access-from-a-virtual-network) per consentire richieste della stessa area.

Attualmente sono supportati solo gli indirizzi IPV4.

Ogni account di archiviazione supporta fino a 100 regole di rete IP, che possono essere combinate con le [regole della rete virtuale](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali

Per garantire l'accesso all'account di archiviazione dalle reti locali con una regola di rete IP è necessario identificare gli indirizzi IP esposti a Internet usati dalla rete. Per assistenza contattare l'amministratore di rete.

È possibile usare [ExpressRoute](/azure/expressroute/expressroute-introduction) per connettere la propria rete alla rete di Azure. Qui ogni circuito è configurato con due indirizzi IP pubblici. È possibile trovarli in corrispondenza dell'Edge Microsoft e usano il [peering pubblico di Azure](/azure/expressroute/expressroute-circuit-peerings) per stabilire la connessione a servizi Microsoft come Archiviazione di Azure. Per consentire la comunicazione con Archiviazione di Azure, creare regole di rete IP per gli indirizzi IP pubblici dei circuiti. Per trovare gli indirizzi IP pubblici del circuito ExpressRoute, [aprire un ticket di supporto con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure.

### <a name="managing-ip-network-rules"></a>Gestione delle regole di rete IP

Le regole di rete IP per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a un intervallo IP di Internet, immettere l'indirizzo IP o l'intervallo di indirizzi (in formato CIDR) in **Firewall** > **Intervallo di indirizzi**.

1. Per rimuovere una regola di rete IP, fare clic sull'icona del cestino accanto all'intervallo di indirizzi.

1. Fare clic su **Salva** per applicare le modifiche.

#### <a name="powershell"></a>PowerShell

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

1. Elencare le regole di rete IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

#### <a name="cliv2"></a>Interfaccia della riga di comando v2

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

1. Elencare le regole di rete IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

## <a name="exceptions"></a>Eccezioni

Le regole di rete possono abilitare una configurazione di rete protetta per la maggior parte degli scenari. Esistono tuttavia alcuni casi in cui è necessario concedere delle eccezioni per abilitare le funzionalità complete. Gli account di archiviazione possono essere configurati con eccezioni per i servizi Microsoft attendibili e per l'accesso ai dati di Analisi archiviazione.

### <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili

Alcuni servizi Microsoft che interagiscono con gli account di archiviazione vengono eseguiti da reti alle quali non è possibile concedere l'accesso tramite le regole di rete.

Per far sì che questo tipo di servizi funzioni come previsto, consentire al set di servizi Microsoft attendibili di ignorare le regole di rete. Questi servizi usano quindi l'autenticazione avanzata per accedere all'account di archiviazione.

Se si abilita l'eccezione **Consenti ai servizi Microsoft attendibili...**, ai servizi seguenti (se registrati nella sottoscrizione) viene concesso l'accesso all'account di archiviazione:

|Service|Nome provider di risorse|Scopo|
|:------|:---------------------|:------|
|Backup di Azure|Microsoft.Backup|Eseguire il backup e il ripristino di dischi non gestiti nelle macchine virtuali IAAS (non obbligatorio per i dischi gestiti). [Altre informazioni](/azure/backup/backup-introduction-to-azure-backup)|
|Azure Site Recovery|Microsoft.SiteRecovery |Configurare il ripristino di emergenza abilitando la replica delle macchine virtuali IaaS di Azure. Questa operazione è necessaria se si usa un account di archiviazione della cache abilitato per il firewall, un account di archiviazione di origine o un account di archiviazione di destinazione.  [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)|
|Azure DevTest Labs|Microsoft.DevTestLab|Creazione di immagini personalizzate e installazione di artefatti. [Altre informazioni](/azure/devtest-lab/devtest-lab-overview)|
|Azure Data Box|Microsoft.DataBox|Consente l'importazione di dati in Azure mediante Data Box. [Altre informazioni](/azure/databox/data-box-overview)|
|Griglia di eventi di Azure|Microsoft.EventGrid|Abilitare la pubblicazione di eventi di archiviazione BLOB e consentire a Griglia di eventi la pubblicazione nelle code di archiviazione. Informazioni sugli [eventi di archiviazione BLOB](/azure/event-grid/event-sources) e sulla [pubblicazione nelle code](/azure/event-grid/event-handlers).|
|Hub eventi di Azure|Microsoft.EventHub|Archiviare dati con Acquisizione di Hub eventi. [Altre informazioni](/azure/event-hubs/event-hubs-capture-overview).|
|Rete di Azure|Microsoft.Networking|Archiviare e analizzare i log di traffico di rete. [Altre informazioni](/azure/network-watcher/network-watcher-packet-capture-overview)|
|Monitoraggio di Azure|Microsoft.Insights|Eseguire la scrittura dei dati di monitoraggio in un account di archiviazione protetto [Altre informazioni](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).|
|Azure SQL Data Warehouse|Microsoft.Sql|Consente scenari di importazione ed esportazione con PolyBase. [Altre informazioni](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)|

### <a name="storage-analytics-data-access"></a>Accesso ai dati di Analisi archiviazione

In alcuni casi l'accesso per la lettura di log diagnostici e metrica viene richiesto dall'esterno dei limiti di rete. È possibile autorizzare eccezioni alle regole di rete per consentire l'accesso in lettura ai file di log, alle tabelle di metrica o a entrambi gli elementi dell'account di archiviazione. [Altre informazioni sull'uso dell'analisi archiviazione.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestione delle eccezioni

Le eccezioni alle regole di rete possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. In **Eccezioni** selezionare le eccezioni da autorizzare.

1. Fare clic su **Salva** per applicare le modifiche.

#### <a name="powershell"></a>PowerShell

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

1. Visualizzare le eccezioni alle regole di rete dell'account di archiviazione.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Configurare le eccezioni alle regole di rete dell'account di archiviazione.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Rimuovere le eccezioni alle regole di rete dell'account di archiviazione.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario la rimozione delle eccezioni non ha alcun effetto.

#### <a name="cliv2"></a>Interfaccia della riga di comando v2

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

1. Visualizzare le eccezioni alle regole di rete dell'account di archiviazione.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Configurare le eccezioni alle regole di rete dell'account di archiviazione.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Rimuovere le eccezioni alle regole di rete dell'account di archiviazione.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario la rimozione delle eccezioni non ha alcun effetto.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sugli endpoint del servizio Rete di Azure in [Endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview).

Approfondire gli argomenti relativi alla sicurezza di Archiviazione di Azure in [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md).
