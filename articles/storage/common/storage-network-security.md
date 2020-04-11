---
title: Configurare i firewall e le reti virtuali di Archiviazione di Azure | Microsoft Docs
description: Configurare la sicurezza di rete su più livelli per l'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113513"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Configurare i firewall e le reti virtuali di Archiviazione di Azure

Archiviazione di Azure offre un modello di sicurezza su più livelli, Questo modello consente di proteggere e controllare il livello di accesso agli account di archiviazione richiesto dalle applicazioni e dagli ambienti aziendali, in base al tipo e al sottoinsieme di reti utilizzate. Quando vengono configurate le regole di rete, solo le applicazioni che richiedono dati sul set di reti specificato possono accedere a un account di archiviazione. È possibile limitare l'accesso all'account di archiviazione alle richieste provenienti da indirizzi IP, intervalli IP o da un elenco di subnet in una rete virtuale di Azure.You can limit access to your storage account to requests originating from specified IP addresses, IP ranges or from a list of subnets in an Azure Virtual Network (VNet).

Gli account di archiviazione dispongono di un endpoint pubblico accessibile tramite Internet.Storage accounts have a public endpoint that is accessible through the internet. È anche possibile creare [endpoint privati per l'account di archiviazione,](storage-private-endpoints.md)che assegna un indirizzo IP privato dalla rete virtuale all'account di archiviazione e protegge tutto il traffico tra la rete virtuale e l'account di archiviazione tramite un collegamento privato. Il firewall di archiviazione di Azure fornisce l'accesso al controllo di accesso per l'endpoint pubblico dell'account di archiviazione. È anche possibile usare il firewall per bloccare tutti gli accessi tramite l'endpoint pubblico quando si usano endpoint privati. La configurazione del firewall di archiviazione consente inoltre a servizi della piattaforma Azure attendibili di accedere all'account di archiviazione in modo sicuro.

Un'applicazione che accede a un account di archiviazione quando le regole di rete sono attive richiede comunque l'autorizzazione appropriata per la richiesta. L'autorizzazione è supportata con le credenziali di Azure Active Directory (Azure AD) per BLOB e code, con una chiave di accesso all'account valida o con un token di firma di accesso condiviso.

> [!IMPORTANT]
> L'attivazione delle regole del firewall per l'account di archiviazione blocca le richieste in ingresso per i dati per impostazione predefinita, a meno che non provengano da un servizio che opera all'interno di una rete virtuale di Azure o da indirizzi IP pubblici consentiti. Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via.
>
> È possibile concedere l'accesso ai servizi di Azure che operano dall'interno di una rete virtuale consentendo il traffico dalla subnet che ospita l'istanza del servizio. È inoltre possibile abilitare un numero limitato di scenari tramite il meccanismo [eccezioni](#exceptions) descritto di seguito. Per accedere ai dati dall'account di archiviazione tramite il portale di Azure, è necessario trovarsi in un computer all'interno del limite attendibile (IP o rete virtuale) configurato.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenari

Per proteggere l'account di archiviazione, è innanzitutto necessario configurare una regola per negare l'accesso al traffico da tutte le reti (incluso il traffico Internet) nell'endpoint pubblico, per impostazione predefinita. Quindi, è necessario configurare le regole che concedono l'accesso al traffico da reti virtuali specifiche. È inoltre possibile configurare regole per concedere l'accesso al traffico da intervalli di indirizzi IP Internet pubblici selezionati, abilitando le connessioni da client Internet o locali specifici. Questa configurazione consente di creare un limite di rete protetto per le applicazioni.

È possibile combinare le regole del firewall che consentono l'accesso da reti virtuali specifiche e da intervalli di indirizzi IP pubblici nello stesso account di archiviazione. Le regole del firewall di archiviazione possono essere applicate agli account di archiviazione esistenti o durante la creazione di nuovi account di archiviazione.

Le regole del firewall di archiviazione si applicano all'endpoint pubblico di un account di archiviazione. Non sono necessarie regole di accesso al firewall per consentire il traffico per gli endpoint privati di un account di archiviazione. Il processo di approvazione della creazione di un endpoint privato concede l'accesso implicito al traffico dalla subnet che ospita l'endpoint privato.

Le regole di rete vengono applicate a tutti i protocolli di rete per l'archiviazione di Azure, tra cui REST e SMB. Per accedere ai dati usando strumenti come il portale di Azure, Storage Explorer e la copia di Archiviazione, è necessario configurare regole di rete esplicite.

Una volta configurate, le regole di rete vengono applicate a tutte le richieste. I token di firma di accesso condiviso che concedono l'accesso a un indirizzo IP specifico hanno lo scopo di limitare l'accesso del titolare del token, ma non concedono nuovi accessi oltre le regole di rete configurate.

Le regole di rete non influiscono sul traffico del disco della macchina virtuale (incluse le operazioni di montaggio e smontaggio e l'I/O del disco). L'accesso REST ai BLOB di pagine è protetto dalle regole di rete.

Gli account di archiviazione classici non supportano i firewall e le reti virtuali.

È possibile usare dischi non gestiti in account di archiviazione a cui sono applicate regole di rete per eseguire operazioni di backup e ripristino di macchine virtuali mediante la creazione di un'eccezione. Questo processo è documentato nella sezione [Eccezioni](#exceptions) di questo articolo. Le eccezioni firewall non sono applicabili ai dischi gestiti perché sono già gestiti da Azure.

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete

Per impostazione predefinita, gli account di archiviazione accettano connessioni da client di qualsiasi rete. Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> La modifica delle regole di rete può influire sulla capacità di connessione delle applicazioni al servizio Archiviazione di Azure. L'impostazione della regola di rete predefinita per **la negazione** blocca tutti gli accessi ai dati, a meno che non vengano applicate anche regole di rete specifiche che **concedono** l'accesso. Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete.

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

È possibile configurare gli account di archiviazione per consentire l'accesso solo da subnet specifiche. Le subnet consentite possono appartenere a una rete virtuale nella stessa sottoscrizione o a quelle in una sottoscrizione diversa, incluse le sottoscrizioni appartenenti a un tenant di Azure Active Directory diverso.

Abilitare un [endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview) per Archiviazione di Azure all'interno della rete virtuale. L'endpoint del servizio instrada il traffico dalla rete virtuale tramite un percorso ottimale al servizio Archiviazione di Azure.The service endpoint routes traffic from the VNet through an optimal path to the Azure Storage service. Anche le identità della subnet e della rete virtuale vengono trasmesse a ogni richiesta. Gli amministratori possono quindi configurare le regole di rete per l'account di archiviazione che consentono la ricezione di richieste da subnet specifiche in una rete virtuale. Per accedere ai dati, i client ai quali viene garantito l'accesso con queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione dell'account di archiviazione.

Ogni account di archiviazione supporta fino a 100 regole di rete virtuale, che possono essere combinate con [regole di rete IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Aree della rete virtuale disponibili

In generale gli endpoint di servizio funzionano tra reti virtuali e istanze del servizio incluse nella stessa area di Azure. Quando si usano endpoint di servizio con Archiviazione di Azure, questo ambito viene espanso per includere le [aree abbinate](/azure/best-practices-availability-paired-regions). Gli endpoint di servizio garantiscono la continuità durante un failover a livello di area, nonché l'accesso alle istanze di archiviazione con ridondanza geografica e accesso in lettura. Le regole di rete che concedono l'accesso da una rete virtuale a un account di archiviazione concedono anche l'accesso a qualsiasi istanza RA-GRS.

Quando si pianifica il ripristino di emergenza durante un'interruzione a livello di area, è consigliabile creare in anticipo le reti virtuali nell'area abbinata. Abilitare gli endpoint di servizio per Archiviazione di Azure, con regole di rete che concedono l'accesso da queste reti virtuali alternative. Applicare quindi le regole agli account di archiviazione con ridondanza geografica.

> [!NOTE]
> Gli endpoint di servizio non si applicano al traffico esterno all'area della rete virtuale di Microsoft Azure e alla coppia di aree designata. Le regole di rete che concedono l'accesso dalle reti virtuali agli account di archiviazione possono essere applicate solo nell'area primaria di un account di archiviazione o nell'area abbinata designata.

### <a name="required-permissions"></a>Autorizzazioni necessarie

Per applicare una regola di rete virtuale a un account di archiviazione, l'utente deve avere le autorizzazioni appropriate per le subnet aggiunte. L'autorizzazione necessaria è *Join Service to a Subnet* (Aggiungi servizio a una subnet), inclusa nel ruolo predefinito *Collaboratore Account di archiviazione*. Può anche essere aggiunta a definizioni del ruolo personalizzate.

L'account di archiviazione e le reti virtuali a cui è stato concesso l'accesso possono trovarsi in sottoscrizioni diverse, incluse le sottoscrizioni che fanno parte di un tenant di Azure AD diverso.

> [!NOTE]
> La configurazione delle regole che concedono l'accesso alle subnet nelle reti virtuali che fanno parte di un tenant di Azure Active Directory diverso sono attualmente supportate solo tramite Powershell, l'interfaccia della riga di comando e le API REST. Tali regole non possono essere configurate tramite il portale di Azure, anche se possono essere visualizzate nel portale.

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
    > Attualmente, solo le reti virtuali appartenenti allo stesso tenant di Azure Active Directory vengono visualizzate per la selezione durante la creazione della regola. Per concedere l'accesso a una subnet in una rete virtuale appartenente a un altro tenant, usare Powershell, l'interfaccia della riga di comando o le API REST.

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
    > Per aggiungere una regola di rete per una subnet in una rete virtuale appartenente a un altro tenant di Azure AD, utilizzare un parametro **VirtualNetworkResourceId** completo nel formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

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
    > Per aggiungere una regola per una subnet in una rete virtuale appartenente a un altro tenant\<di\>Azure AD,\<usare un\>ID di subnet completo nel\<formato "/subscriptions/ subscription-ID /resourceGroups/ resourceGroup-Name /providers/Microsoft.Network/virtualNetworks/ vNet-name\>/subnets/\<nome-subnet\>".
    >
    > È possibile usare il parametro **di sottoscrizione** per recuperare l'ID di subnet per una rete virtuale appartenente a un altro tenant di Azure AD.

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
   > Le regole della rete IP non hanno alcun effetto sulle richieste provenienti dalla stessa area di Azure dell'account di archiviazione. Usare le [regole di rete virtuale](#grant-access-from-a-virtual-network) per consentire richieste della stessa area.

  > [!NOTE]
  > I servizi distribuiti nella stessa area dell'account di archiviazione usano indirizzi IP privati di Azure per la comunicazione. Pertanto, non è possibile limitare l'accesso a specifici servizi di Azure in base all'intervallo di indirizzi IP in uscita pubblico.

Solo gli indirizzi IPV4 sono supportati per la configurazione delle regole del firewall di archiviazione.

Ogni account di archiviazione supporta fino a 100 regole di rete IP.

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali

Per garantire l'accesso all'account di archiviazione dalle reti locali con una regola di rete IP è necessario identificare gli indirizzi IP esposti a Internet usati dalla rete. Per assistenza contattare l'amministratore di rete.

Se si usa [ExpressRoute](/azure/expressroute/expressroute-introduction) dall'ambiente locale, per il peering pubblico o per il peering Microsoft, sarà necessario identificare gli indirizzi IP NAT usati. Per il peering pubblico, ogni circuito ExpressRoute usa per impostazione predefinita due indirizzi IP NAT applicati al traffico del servizio di Azure quando il traffico entra nel backbone della rete di Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT utilizzati sono forniti dal cliente o sono forniti dal provider di servizi. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Vedere altre informazioni su [NAT per il peering pubblico e il peering Microsoft ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestione delle regole di rete IP

Le regole di rete IP per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a un intervallo DI indirizzi IP Internet, immettere l'indirizzo IP o l'intervallo di indirizzi (in formato CIDR) in**Intervallo di indirizzi** **del firewall** > .

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

Le regole di rete consentono di creare un ambiente sicuro per le connessioni tra le applicazioni e i dati per la maggior parte degli scenari. Tuttavia, alcune applicazioni dipendono da servizi di Azure che non possono essere isolati in modo univoco tramite la rete virtuale o le regole di indirizzo IP. Tuttavia, tali servizi devono essere concessi all'archiviazione per abilitare la funzionalità completa dell'applicazione. In tali situazioni, è possibile utilizzare l'impostazione ***Consenti servizi Microsoft attendibili...*** per consentire a tali servizi di accedere ai dati, ai log o all'analisi.

### <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili

Alcuni servizi Microsoft operano da reti che non possono essere incluse nelle regole di rete. È possibile concedere a un sottoinsieme di tali servizi Microsoft attendibili l'accesso all'account di archiviazione, mantenendo le regole di rete per altre app. Questi servizi attendibili utilizzeranno quindi l'autenticazione avanzata per connettersi all'account di archiviazione in modo sicuro. Sono stati abilitate due modalità di accesso attendibile per i servizi Microsoft.We've enabled two modes of trusted access for Microsoft services.

- Le risorse di alcuni servizi, **una volta registrate nella sottoscrizione,** possono accedere **all'account** di archiviazione nella stessa sottoscrizione per operazioni selezionate, ad esempio la scrittura di log o il backup.
- Alle risorse di alcuni servizi può essere concesso l'accesso esplicito all'account di archiviazione **assegnando un ruolo Controllo degli accessi in** base al ruolo all'identità gestita assegnata dal sistema.


Quando si abilita l'impostazione **Consenti servizi Microsoft attendibili...** alle risorse dei servizi seguenti registrati nella stessa sottoscrizione dell'account di archiviazione viene concesso l'accesso per un set limitato di operazioni come descritto:

| Service                  | Nome provider di risorse     | Operazioni consentite                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Backup di Azure             | Microsoft.RecoveryServices | Eseguire il backup e il ripristino di dischi non gestiti nelle macchine virtuali IAAS (non obbligatorio per i dischi gestiti). [Altre informazioni](/azure/backup/backup-introduction-to-azure-backup) |
| Azure Data Box           | Microsoft.DataBox          | Consente l'importazione di dati in Azure tramite Data Box. [Altre informazioni](/azure/databox/data-box-overview) |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Creazione di immagini personalizzate e installazione di artefatti. [Altre informazioni](/azure/devtest-lab/devtest-lab-overview) |
| Griglia di eventi di Azure         | Microsoft.EventGrid        | Abilitare la pubblicazione di eventi di archiviazione BLOB e consentire a Griglia di eventi la pubblicazione nelle code di archiviazione. Informazioni sugli [eventi di archiviazione BLOB](/azure/event-grid/event-sources) e sulla [pubblicazione nelle code](/azure/event-grid/event-handlers). |
| Hub eventi di Azure         | Microsoft.EventHub         | Archiviare dati con Acquisizione di Hub eventi. [Ulteriori informazioni](/azure/event-hubs/event-hubs-capture-overview). |
| Sincronizzazione file di Azure          | Microsoft.StorageSync      | Consente di trasformare il file server locale in una cache per le condivisioni file di Azure.Enables you to transform your on-prem file server to a cache for Azure File shares. Consentire la sincronizzazione multisito, il ripristino rapido e il backup sul lato cloud. [Altre informazioni](../files/storage-sync-files-planning.md) |
| HDInsight di Azure          | Microsoft.HDInsight        | Effettuare il provisioning del contenuto iniziale del file system predefinito per un nuovo cluster HDInsight.Provision the initial contents of the default file system for a new HDInsight cluster. [Altre informazioni](/azure/hdinsight/hdinsight-hadoop-use-blob-storage) |
| Esportazione importazione AzureAzure Import Export      | Microsoft.ImportExport     | Consente l'importazione di dati in Azure e l'esportazione di dati da Azure usando il servizio di importazione/esportazione. [Altre informazioni](/azure/storage/common/storage-import-export-service)  |
| Monitoraggio di Azure            | Microsoft.Insights         | Consente la scrittura dei dati di monitoraggio in un account di archiviazione protetto, inclusi i log di diagnostica delle risorse, i log di accesso e di controllo di Azure Active Directory e i log di Microsoft Intune. [Altre informazioni](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) |
| Rete di Azure         | Microsoft.Network          | Archiviare e analizzare i log di traffico di rete. [Altre informazioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Abilitare la replica per il ripristino di emergenza delle macchine virtuali IaaS di Azure quando si usano account di archiviazione di tipo cache, di origine o di destinazione abilitati per il firewall.  [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) |

L'impostazione **Consenti servizi Microsoft attendibili...** consente inoltre a una particolare istanza dei servizi seguenti di accedere all'account di archiviazione, se si assegna in modo esplicito [un ruolo RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) [all'identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/overview.md) per l'istanza della risorsa. In questo caso, l'ambito di accesso per l'istanza corrisponde al ruolo RBAC assegnato all'identità gestita.

| Service                        | Nome provider di risorse                 | Scopo            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Ricerca cognitiva di Azure         | Microsoft.Search/searchServices        | Consente ai servizi di ricerca cognitiva di accedere agli account di archiviazione per l'indicizzazione, l'elaborazione e l'esecuzione di query. |
| Attività di Registro Azure Container | Microsoft.ContainerRegistry/registries | Le attività ACR possono accedere agli account di archiviazione durante la creazione di immagini del contenitore. |
| Data factory di Azure             | Microsoft.DataFactory/factories        | Consente l'accesso agli account di archiviazione tramite il runtime ADF. |
| Condivisione dati di Azure               | Microsoft.DataShare/accounts           | Consente l'accesso agli account di archiviazione tramite la condivisione dati. |
| App per la logica di Azure               | Microsoft.Logic/workflows              | Consente alle app per la logica di accedere agli account di archiviazione. [Altre informazioni](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity) |
| Servizio Azure Machine Learning | Microsoft.MachineLearningServices      | Le aree di lavoro autorizzate di Azure Machine Learning scrivono output dell'esperimento, modelli e log nell'archiviazione BLOB e leggono i dati. [Altre informazioni](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace) | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Consente l'importazione e l'esportazione di dati da istanze di database SQL specifiche tramite PolyBase. [Altre informazioni](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) |
| Analisi di flusso di Azure         | Microsoft.StreamAnalytics             | Consente la scrittura dei dati di un processo di streaming nell'archivio BLOB. Questa funzionalità è attualmente in anteprima. [Altre informazioni](/azure/stream-analytics/blob-output-managed-identity) |
| Azure Synapse Analytics        | Microsoft.Synapse/aree di lavoro          | Consente l'accesso ai dati in Archiviazione di Azure da Synapse Analytics.Enables access to data in Azure Storage from Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Accesso ai dati di Analisi archiviazione

In alcuni casi l'accesso per la lettura di log diagnostici e metrica viene richiesto dall'esterno dei limiti di rete. Quando si configurano i servizi attendibili l'accesso all'account di archiviazione, è possibile consentire l'accesso in lettura per i file di log, le tabelle delle metriche o entrambi. [Altre informazioni sull'uso dell'analisi archiviazione.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestione delle eccezioni

Le eccezioni alle regole di rete possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure v2.

#### <a name="azure-portal"></a>Portale di Azure

1. Passare all'account di archiviazione che si vuole proteggere.

1. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. In **Eccezioni**selezionare le eccezioni che si desidera concedere.

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

Altre informazioni sugli endpoint del servizio di rete di Azure in Endpoint del servizio.Learn more about Azure Network service endpoints in [Service endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview).

Approfondire la sicurezza di Archiviazione di Azure nella guida alla sicurezza di Archiviazione di Azure.Getg deeper into Azure Storage security guide in [Azure Storage security guide](../blobs/security-recommendations.md).
