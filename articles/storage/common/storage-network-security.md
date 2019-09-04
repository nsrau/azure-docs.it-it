---
title: Configurare i firewall e le reti virtuali di Archiviazione di Azure | Microsoft Docs
description: Configurare la sicurezza di rete su più livelli per l'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00e69d9222444e3b700fca10e3f15b4b110e0c60
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241732"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurare i firewall e le reti virtuali di Archiviazione di Azure

Archiviazione di Azure offre un modello di sicurezza su più livelli, Questo modello consente di proteggere gli account di archiviazione con un subset specifico di reti. Quando vengono configurate le regole di rete, solo le applicazioni che richiedono dati tramite il set di reti specificato possono accedere a un account di archiviazione. È possibile limitare l'accesso all'account di archiviazione alle richieste provenienti da indirizzi IP specificati, intervalli IP o da un elenco di subnet nelle reti virtuali di Azure.

Un'applicazione che accede a un account di archiviazione quando le regole di rete sono attive richiede l'autorizzazione appropriata per la richiesta. L'autorizzazione è supportata con le credenziali Azure Active Directory (Azure AD) per i BLOB e le code, con una chiave di accesso dell'account valida o con un token SAS.

> [!IMPORTANT]
> L'attivazione delle regole del firewall per l'account di archiviazione blocca le richieste in ingresso per i dati per impostazione predefinita, a meno che le richieste provengano da un servizio che opera all'interno di una rete virtuale di Azure (VNet). Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via.
>
> È possibile concedere l'accesso ai servizi di Azure che operano all'interno di una VNet consentendo il traffico dalla subnet che ospita l'istanza del servizio. È anche possibile abilitare un numero limitato di scenari tramite il meccanismo di [eccezioni](#exceptions) descritto nella sezione seguente. Per accedere ai dati dall'account di archiviazione tramite la portale di Azure, è necessario trovarsi in un computer all'interno del limite attendibile (IP o VNet) impostato.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenari

Per proteggere l'account di archiviazione, è necessario innanzitutto configurare una regola per negare l'accesso al traffico da tutte le reti, incluso il traffico Internet, per impostazione predefinita. Quindi, è necessario configurare le regole che concedono l'accesso al traffico da reti virtuali specifici. Questa configurazione consente di creare un limite di rete protetto per le applicazioni. È anche possibile configurare regole per concedere l'accesso al traffico da intervalli di indirizzi IP Internet pubblici selezionati, abilitando le connessioni da client Internet o locali specifici.

Le regole di rete vengono applicate a tutti i protocolli di rete per l'archiviazione di Azure, tra cui REST e SMB. Per accedere ai dati tramite strumenti come portale di Azure, Storage Explorer e AZCopy, è necessario configurare le regole di rete esplicite.

È possibile applicare le regole di rete ad account di archiviazione esistenti oppure al momento della creazione di nuovi account di archiviazione.

Una volta configurate, le regole di rete vengono applicate a tutte le richieste. I token di firma di accesso condiviso che concedono l'accesso a un indirizzo IP specifico hanno lo scopo di limitare l'accesso del titolare del token, ma non concedono nuovi accessi oltre le regole di rete configurate.

Le regole di rete non influiscono sul traffico del disco della macchina virtuale (incluse le operazioni di montaggio e smontaggio e l'I/O del disco). L'accesso REST ai BLOB di pagine è protetto dalle regole di rete.

Gli account di archiviazione classici non supportano i firewall e le reti virtuali.

È possibile usare dischi non gestiti in account di archiviazione a cui sono applicate regole di rete per eseguire operazioni di backup e ripristino di macchine virtuali mediante la creazione di un'eccezione. Questo processo è documentato nella sezione [Eccezioni](#exceptions) di questo articolo. Le eccezioni firewall non sono applicabili ai dischi gestiti perché sono già gestiti da Azure.

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete

Per impostazione predefinita, gli account di archiviazione accettano connessioni da client di qualsiasi rete. Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> La modifica delle regole di rete può influire sulla capacità di connessione delle applicazioni al servizio Archiviazione di Azure. L'impostazione della regola di rete predefinita su **Nega** blocca tutti gli accessi ai dati, a meno che non vengano applicate anche le specifiche regole di rete che **concedono** l'accesso. Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete.

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

È possibile configurare gli account di archiviazione per consentire l'accesso solo da subnet specifiche. Le subnet consentite possono appartenere a un VNet nella stessa sottoscrizione o a quelle in una sottoscrizione diversa, incluse le sottoscrizioni appartenenti a un tenant di Azure Active Directory diverso.

Abilitare un [endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview) per Archiviazione di Azure all'interno della rete virtuale. L'endpoint di servizio instrada il traffico da VNet tramite un percorso ottimale al servizio di archiviazione di Azure. Le identità della subnet e della rete virtuale vengono trasmesse anche con ogni richiesta. Gli amministratori possono quindi configurare le regole di rete per l'account di archiviazione che consentono la ricezione di richieste da subnet specifiche in una VNet. Per accedere ai dati, i client ai quali viene garantito l'accesso con queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione dell'account di archiviazione.

Ogni account di archiviazione supporta fino a 100 regole di rete virtuale, che possono essere combinate con [regole di rete IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Aree della rete virtuale disponibili

In generale gli endpoint di servizio funzionano tra reti virtuali e istanze del servizio incluse nella stessa area di Azure. Quando si usano endpoint di servizio con Archiviazione di Azure, questo ambito viene espanso per includere le [aree abbinate](/azure/best-practices-availability-paired-regions). Gli endpoint di servizio garantiscono la continuità durante un failover a livello di area, nonché l'accesso alle istanze di archiviazione con ridondanza geografica e accesso in lettura. Le regole di rete che concedono l'accesso da una rete virtuale a un account di archiviazione concedono anche l'accesso a qualsiasi istanza RA-GRS.

Quando si pianifica il ripristino di emergenza durante un'interruzione a livello di area, è consigliabile creare in anticipo le reti virtuali nell'area abbinata. Abilitare gli endpoint di servizio per Archiviazione di Azure, con regole di rete che concedono l'accesso da queste reti virtuali alternative. Applicare quindi le regole agli account di archiviazione con ridondanza geografica.

> [!NOTE]
> Gli endpoint di servizio non si applicano al traffico esterno all'area della rete virtuale di Microsoft Azure e alla coppia di aree designata. Le regole di rete che concedono l'accesso dalle reti virtuali agli account di archiviazione possono essere applicate solo nell'area primaria di un account di archiviazione o nell'area abbinata designata.

### <a name="required-permissions"></a>Autorizzazioni necessarie

Per applicare una regola di rete virtuale a un account di archiviazione, l'utente deve avere le autorizzazioni appropriate per le subnet aggiunte. L'autorizzazione necessaria è *Join Service to a Subnet* (Aggiungi servizio a una subnet), inclusa nel ruolo predefinito *Collaboratore Account di archiviazione*. Può anche essere aggiunta a definizioni del ruolo personalizzate.

L'account di archiviazione e le reti virtuali concesse possono trovarsi in sottoscrizioni diverse, incluse le sottoscrizioni che fanno parte di un tenant di Azure AD diverso.

> [!NOTE]
> La configurazione delle regole che concedono l'accesso alle subnet nelle reti virtuali che fanno parte di un tenant di Azure Active Directory diverso è attualmente supportata solo tramite PowerShell, l'interfaccia della riga di comando e le API REST. Queste regole non possono essere configurate tramite la portale di Azure, anche se possono essere visualizzate nel portale.

### <a name="managing-virtual-network-rules"></a>Gestione delle regole di rete virtuale

Le regole di rete virtuale per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una rete virtuale con una nuova regola di rete, in **Reti virtuali** fare clic su **Aggiungi rete virtuale esistente**, selezionare **Reti virtuali** e **Subnet**, quindi fare clic su **Aggiungi**. Per creare una nuova rete virtuale e concedere l'accesso, fare clic su **Aggiungi nuova rete virtuale**. Specificare le informazioni necessarie per creare la nuova rete virtuale e quindi fare clic su **Crea**.

    > [!NOTE]
    > Se un endpoint di servizio per Archiviazione di Azure non è stato configurato in precedenza per la rete virtuale e le subnet selezionate, è possibile configurarlo in questa operazione.
    >
    > Attualmente, durante la creazione della regola vengono visualizzate solo le reti virtuali appartenenti allo stesso tenant Azure Active Directory. Per concedere l'accesso a una subnet in una rete virtuale appartenente a un altro tenant, usare PowerShell, l'interfaccia della riga di comando o le API REST.

1. Per rimuovere una regola di rete virtuale o subnet, fare clic su **…** per aprire il menu di scelta rapida per la rete virtuale o la subnet, quindi fare clic su **Rimuovi**.

1. Fare clic su **Salva** per applicare le modifiche.

#### <a name="powershell"></a>PowerShell

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).

1. Visualizzare l'elenco delle regole di rete virtuale.

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

    > [!TIP]
    > Per aggiungere una regola di rete per una subnet in una VNet che appartiene a un altro tenant Azure AD, usare un parametro **VirtualNetworkResourceId** completo nel formato "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name".

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

#### <a name="cliv2"></a>Interfaccia della riga di comando v2

1. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [accedere](/cli/azure/authenticate-azure-cli).

1. Visualizzare l'elenco delle regole di rete virtuale.

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

    > [!TIP]
    > Per aggiungere una regola per una subnet in una VNet che appartiene a un altro tenant Azure AD, usare un ID di subnet completo nel formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > È possibile usare il parametro **Subscription** per recuperare l'ID subnet per un VNet che appartiene a un altro tenant Azure ad.

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

Le regole di rete per gli IP sono consentite solo per gli indirizzi IP della **rete Internet pubblica**. Gli intervalli di indirizzi IP riservati per le reti private (come definito nell'[RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) non sono consentiti nelle regole IP. Le reti private includono gli indirizzi che iniziano con _10.*_ , _172.16.*_  - _172.31.*_ e _192.168.*_ .

   > [!NOTE]
   > Le regole della rete IP non hanno alcun effetto sulle richieste provenienti dalla stessa area di Azure dell'account di archiviazione. Usare le [regole di rete virtuale](#grant-access-from-a-virtual-network) per consentire richieste della stessa area.

Attualmente sono supportati solo gli indirizzi IPV4.

Ogni account di archiviazione supporta fino a 100 regole di rete IP, che possono essere combinate con le [regole di rete virtuale](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali

Per garantire l'accesso all'account di archiviazione dalle reti locali con una regola di rete IP è necessario identificare gli indirizzi IP esposti a Internet usati dalla rete. Per assistenza contattare l'amministratore di rete.

Se si usa [ExpressRoute](/azure/expressroute/expressroute-introduction) dall'ambiente locale, per il peering pubblico o per il peering Microsoft, sarà necessario identificare gli indirizzi IP NAT usati. Per il peering pubblico, ogni circuito ExpressRoute usa per impostazione predefinita due indirizzi IP NAT applicati al traffico del servizio di Azure quando il traffico entra nel backbone della rete di Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT usati vengono forniti dal cliente o dal provider del servizio. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto in ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Vedere altre informazioni su [NAT per il peering pubblico e il peering Microsoft ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

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

Affinché alcuni servizi funzionino come previsto, è necessario consentire a un sottoinsieme di servizi Microsoft attendibili di ignorare le regole di rete. Questi servizi usano quindi l'autenticazione avanzata per accedere all'account di archiviazione.

Se si abilita l'eccezione **Consenti ai servizi Microsoft attendibili...** , ai servizi seguenti (se registrati nella sottoscrizione) viene concesso l'accesso all'account di archiviazione:

| Service                  | Nome provider di risorse     | Scopo                                                                                                                                                                                                                                                                                                                      |
|:-------------------------|:---------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Backup di Azure             | Microsoft.RecoveryServices | Eseguire il backup e il ripristino di dischi non gestiti nelle macchine virtuali IAAS (non obbligatorio per i dischi gestiti). [Altre informazioni](/azure/backup/backup-introduction-to-azure-backup)                                                                                                                                                     |
| Azure Data Box           | Microsoft.DataBox          | Consente l'importazione di dati in Azure usando Data Box. [Altre informazioni](/azure/databox/data-box-overview)                                                                                                                                                                                                                              |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Creazione di immagini personalizzate e installazione di artefatti. [Altre informazioni](/azure/devtest-lab/devtest-lab-overview)                                                                                                                                                                                                                      |
| Griglia di eventi di Azure         | Microsoft.EventGrid        | Abilitare la pubblicazione di eventi di archiviazione BLOB e consentire a Griglia di eventi la pubblicazione nelle code di archiviazione. Informazioni sugli [eventi di archiviazione BLOB](/azure/event-grid/event-sources) e sulla [pubblicazione nelle code](/azure/event-grid/event-handlers).                                                                                                     |
| Hub eventi di Azure         | Microsoft.EventHub         | Archiviare dati con Acquisizione di Hub eventi. [Altre informazioni](/azure/event-hubs/event-hubs-capture-overview).                                                                                                                                                                                                                           |
| Sincronizzazione file di Azure          | Microsoft.StorageSync      | Consente di trasformare il file server locale in una cache per le condivisioni file di Azure. Consentire la sincronizzazione multisito, il ripristino di emergenza rapido e il backup sul cloud. [Altre informazioni](../files/storage-sync-files-planning.md)                                                                                                       |
| HDInsight di Azure          | Microsoft.HDInsight        | Eseguire il provisioning del contenuto iniziale del file system predefinito per un nuovo cluster HDInsight. [Altre informazioni](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)                                                                                                                                    |
| Servizio Azure Machine Learning | Microsoft.MachineLearningServices | Le aree di lavoro autorizzate Azure Machine Learning scrivono l'output dell'esperimento, i modelli e i log nell'archivio BLOB. [Altre informazioni](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace)                                                               
| Monitoraggio di Azure            | Microsoft.Insights         | Eseguire la scrittura dei dati di monitoraggio in un account di archiviazione protetto [Altre informazioni](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security).                                                                                                                                                                        |
| Rete di Azure         | Microsoft.Network          | Archiviare e analizzare i log di traffico di rete. [Altre informazioni](/azure/network-watcher/network-watcher-packet-capture-overview)                                                                                                                                                                                                        |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Configurare il ripristino di emergenza abilitando la replica delle macchine virtuali IaaS di Azure. Questa operazione è necessaria se si usa un account di archiviazione della cache abilitato per il firewall, un account di archiviazione di origine o un account di archiviazione di destinazione.  [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) |
| Azure SQL Data Warehouse | Microsoft.Sql              | Consente di importare ed esportare scenari da istanze di database SQL specifiche usando la polibase. [Altre informazioni](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)                                                                                                                                                 |
| Analisi di flusso di Azure   | Microsoft.StreamAnalytics  | Consente la scrittura di dati da un processo di streaming nell'archivio BLOB. Si noti che questa funzionalità è attualmente in anteprima. [Altre informazioni](../../stream-analytics/blob-output-managed-identity.md)                                                                                                                                        |

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
