---
title: Reti virtuali
titleSuffix: Azure Cognitive Services
description: Configurare la sicurezza di rete a più livelli per le risorse di Servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371223"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configurare le reti virtuali di Servizi cognitivi di AzureConfigure Azure Cognitive Services virtual networks

Servizi cognitivi di Azure offre un modello di sicurezza a più livelli. Questo modello consente di proteggere gli account di Servizi cognitivi a un sottoinsieme specifico di reti. Quando le regole di rete sono configurate, solo le applicazioni che richiedono dati sul set di reti specificato possono accedere all'account. È possibile limitare l'accesso alle risorse con il filtro delle richieste. Consentire solo le richieste provenienti da indirizzi IP, intervalli IP o da un elenco di subnet in Reti virtuali di [Azure.](../virtual-network/virtual-networks-overview.md) Se sei interessato a questa offerta, dovrai [richiedere l'accesso](https://aka.ms/cog-svc-vnet-signup)in anteprima.

Un'applicazione che accede a una risorsa di Servizi cognitivi quando le regole di rete sono attive richiede l'autorizzazione. L'autorizzazione è supportata con le credenziali di [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) o con una chiave API valida.

> [!IMPORTANT]
> L'attivazione delle regole del firewall per l'account di Servizi cognitivi blocca le richieste di dati in ingresso per impostazione predefinita. Per consentire il traslazione delle richieste, è necessario soddisfare una delle seguenti condizioni:
> * La richiesta deve avere origine da un servizio che opera all'interno di una rete virtuale di Azure (VNet) nell'elenco di subnet consentite dell'account Servizi cognitivi di destinazione. L'endpoint nelle richieste originato dalla rete virtuale deve essere impostato come [sottodominio personalizzato](cognitive-services-custom-subdomains.md) dell'account di Servizi cognitivi.
> * Oppure la richiesta deve avere origine da un elenco consentito di indirizzi IP.
>
> Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenari

Per proteggere la risorsa Servizi cognitivi, è innanzitutto necessario configurare una regola per negare l'accesso al traffico da tutte le reti (incluso il traffico Internet) per impostazione predefinita. Quindi, è necessario configurare le regole che concedono l'accesso al traffico da reti virtuali specifiche. Questa configurazione consente di creare un limite di rete protetto per le applicazioni. È inoltre possibile configurare regole per concedere l'accesso al traffico da intervalli di indirizzi IP Internet pubblici selezionati, abilitando le connessioni da client Internet o locali specifici.

Network rules are enforced on all network protocols to Azure Cognitive Services, including REST and WebSocket. Per accedere ai dati usando strumenti come le console di test di Azure, è necessario configurare regole di rete esplicite. È possibile applicare regole di rete alle risorse di Servizi cognitivi esistenti o quando si creano nuove risorse di Servizi cognitivi. Una volta configurate, le regole di rete vengono applicate a tutte le richieste.

## <a name="supported-regions-and-service-offerings"></a>Regioni e offerte di servizi supportate

Il supporto della rete virtuale per i servizi cognitivi elencati di seguito è limitato alle aree *euAP*degli Stati Uniti centrali , *Stati Uniti centro-meridionali,* *Stati Uniti orientali*, Stati Uniti *occidentali 2*, *Nord Europa*, Sud Africa *nord*, *Europa occidentale*, *India centrale*, *Australia orientale*, Stati Uniti *occidentali*e Stati *Uniti Gov Virginia* Azure. Se l'offerta di servizi non è elencata qui, non supporta le reti virtuali.

> [!div class="checklist"]
> * [Rilevamento anomalie](./anomaly-detector/index.yml)
> * [Visione artificiale](./computer-vision/index.yml)
> * [Moderatore dei contenuti](./content-moderator/index.yml)
> * [Visione personalizzata](./custom-vision-service/index.yml)
> * [Viso](./face/index.yml)
> * [Riconoscimento modulo](./form-recognizer/index.yml)
> * [Luis](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [Analisi del testo](./text-analytics/index.yml)
> * [Creatore QnA](./qnamaker/index.yml)

Il supporto della rete virtuale per i servizi cognitivi elencati di seguito è limitato alle aree *EUAP degli Stati Uniti centrali*, Stati Uniti *centro-meridionali*, *Stati Uniti orientali*, Stati *Uniti occidentali 2*, *globali*e *Gov Virginia degli Stati Uniti.*
> [!div class="checklist"]
> * [Testo traduttore](./translator/index.yml)

## <a name="service-tags"></a>Tag di servizio
Oltre a supportare gli endpoint del servizio di rete virtuale per i servizi precedenti, Servizi cognitivi supporta anche un tag di servizio per la configurazione delle regole di rete in uscita. I servizi seguenti sono inclusi nel tag del servizio CognitiveServicesManagement.The following services are included in the CognitiveServicesManagement service tag.
> [!div class="checklist"]
> * [Rilevamento anomalie](./anomaly-detector/index.yml)
> * [Visione artificiale](./computer-vision/index.yml)
> * [Moderatore dei contenuti](./content-moderator/index.yml)
> * [Visione personalizzata](./custom-vision-service/index.yml)
> * [Viso](./face/index.yml)
> * [Riconoscimento modulo](./form-recognizer/index.yml)
> * [Luis](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [Analisi del testo](./text-analytics/index.yml)
> * [Creatore QnA](./qnamaker/index.yml)
> * [Testo traduttore](./translator/index.yml)
> * [Servizio di riconoscimento vocale](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete

Per impostazione predefinita, le risorse di Servizi cognitivi accettano connessioni dai client in qualsiasi rete. Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> Apportare modifiche alle regole di rete può influire sulla capacità delle applicazioni di connettersi a Servizi cognitivi di Azure.Making changes to network rules can impact your application' ability to connect to Azure Cognitive Services. L'impostazione della regola di rete predefinita per **la negazione** blocca tutti gli accessi ai dati, a meno che non vengano applicate anche regole di rete specifiche che **concedono** l'accesso. Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete. Se si consente l'elenco degli indirizzi IP per la rete locale, assicurarsi di aggiungere tutti i possibili indirizzi IP pubblici in uscita dalla rete locale.

### <a name="managing-default-network-access-rules"></a>Gestione delle regole predefinite di accesso alla rete

È possibile gestire le regole di accesso alla rete predefinite per le risorse di Servizi cognitivi tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.You can manage default network access rules for Cognitive Services resources through the Azure portal, PowerShell, or the Azure CLI.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

1. Passare alla risorsa Servizi cognitivi che si vuole proteggere.

1. Selezionare il menu **RESOURCE MANAGEMENT** denominato **Rete virtuale**.

   ![Opzione di rete virtuale](media/vnet/virtual-network-blade.png)

1. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **Reti selezionate**. Con l'impostazione **Reti selezionate** da sola, non accompagnate da **reti virtuali** configurate o intervalli di **indirizzi:** tutti gli accessi vengono effettivamente negati. Quando viene negato tutto l'accesso, le richieste che tentano di utilizzare la risorsa Servizi cognitivi non sono consentite. Il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure possono comunque essere usati per configurare la risorsa Servizi cognitivi.
1. Per consentire il traffico da tutte le reti, scegliere di consentire l'accesso da **Tutte le reti**.

   ![Le reti virtuali negano](media/vnet/virtual-network-deny.png)

1. Selezionare **Salva** per applicare le modifiche.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-az-ps) e [accedere](/powershell/azure/authenticate-azureps)oppure selezionare **Prova.**

1. Visualizzare lo stato della regola predefinita per la risorsa Servizi cognitivi.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

1. Installare [l'interfaccia della riga di comando](/cli/azure/install-azure-cli) di Azure e [accedere](/cli/azure/authenticate-azure-cli)oppure selezionare **Prova.**

1. Visualizzare lo stato della regola predefinita per la risorsa Servizi cognitivi.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Concedere l'accesso da una rete virtuale

È possibile configurare le risorse di Servizi cognitivi per consentire l'accesso solo da subnet specifiche. Le subnet consentite possono appartenere a una rete virtuale nella stessa sottoscrizione o in una sottoscrizione diversa, incluse le sottoscrizioni appartenenti a un tenant di Azure Active Directory diverso.

Abilitare un endpoint di [servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per Servizi cognitivi di Azure all'interno della rete virtuale. L'endpoint del servizio indirizza il traffico dalla rete virtuale tramite un percorso ottimale al servizio Servizi cognitivi di Azure.The service endpoint routes traffic from the VNet through an optimal path to the Azure Cognitive Services service. Anche le identità della subnet e della rete virtuale vengono trasmesse a ogni richiesta. Gli amministratori possono quindi configurare le regole di rete per la risorsa Servizi cognitivi che consentono la ricezione di richieste da subnet specifiche in una rete virtuale. I client a cui è stato concesso l'accesso tramite queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione della risorsa Servizi cognitivi per accedere ai dati.

Ogni risorsa di Servizi cognitivi supporta fino a 100 regole di rete virtuale, che possono essere combinate con le regole di [rete IP.](#grant-access-from-an-internet-ip-range)

### <a name="required-permissions"></a>Autorizzazioni necessarie

Per applicare una regola di rete virtuale a una risorsa di Servizi cognitivi, l'utente deve disporre delle autorizzazioni appropriate per le subnet aggiunte. L'autorizzazione richiesta è il ruolo *Collaboratore* predefinito o il ruolo *Collaboratore Servizi cognitivi.* Le autorizzazioni necessarie possono anche essere aggiunte alle definizioni di ruolo personalizzate.

La risorsa Servizi cognitivi e le reti virtuali a cui è stato concesso l'accesso possono trovarsi in sottoscrizioni diverse, incluse le sottoscrizioni che fanno parte di un tenant di Azure AD diverso.

> [!NOTE]
> La configurazione delle regole che concedono l'accesso alle subnet nelle reti virtuali che fanno parte di un tenant di Azure Active Directory diverso sono attualmente supportate solo tramite Powershell, l'interfaccia della riga di comando e le API REST. Tali regole non possono essere configurate tramite il portale di Azure, anche se possono essere visualizzate nel portale.

### <a name="managing-virtual-network-rules"></a>Gestione delle regole di rete virtuale

È possibile gestire le regole della rete virtuale per le risorse di Servizi cognitivi tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.You can manage virtual network rules for Cognitive Services resources through the Azure portal, PowerShell, or the Azure CLI.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

1. Passare alla risorsa Servizi cognitivi che si vuole proteggere.

1. Selezionare il menu **RESOURCE MANAGEMENT** denominato **Rete virtuale**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una rete virtuale con una regola di rete esistente, in **Reti virtuali**selezionare Aggiungi rete **virtuale esistente**.

   ![Aggiungere una rete virtuale esistenteAdd existing vNet](media/vnet/virtual-network-add-existing.png)

1. Selezionare le opzioni **Reti virtuali** e **Subnet,** quindi selezionare **Abilita**.

   ![Aggiungere i dettagli della rete virtuale esistenteAdd existing vNet details](media/vnet/virtual-network-add-existing-details.png)

1. Per creare una nuova rete virtuale e concederle l'accesso, selezionare **Aggiungi nuova rete virtuale**.

   ![Aggiungi nuova vNet](media/vnet/virtual-network-add-new.png)

1. Fornire le informazioni necessarie per creare la nuova rete virtuale e quindi selezionare **Crea**.

   ![Crea vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se un endpoint del servizio per Servizi cognitivi di Azure non è stato configurato in precedenza per la rete virtuale e le subnet selezionate, è possibile configurarlo come parte di questa operazione.
    >
    > Attualmente, solo le reti virtuali appartenenti allo stesso tenant di Azure Active Directory vengono visualizzate per la selezione durante la creazione della regola. Per concedere l'accesso a una subnet in una rete virtuale appartenente a un altro tenant, usare Powershell, l'interfaccia della riga di comando o le API REST.

1. Per rimuovere una regola di rete virtuale o subnet, selezionare **...** per aprire il menu di scelta rapida per la rete virtuale o la subnet e selezionare **Rimuovi**.

   ![Rimuovi vNet](media/vnet/virtual-network-remove.png)

1. Selezionare **Salva** per applicare le modifiche.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-az-ps) e [accedere](/powershell/azure/authenticate-azureps)oppure selezionare **Prova.**

1. Visualizzare l'elenco delle regole di rete virtuale.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Abilitare l'endpoint del servizio per Servizi cognitivi di Azure in una subnet e una rete virtuale esistenti.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Per aggiungere una regola di rete per una subnet in una rete virtuale appartenente a un altro tenant di Azure AD, utilizzare un parametro **VirtualNetworkResourceId** completo nel formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

1. Installare [l'interfaccia della riga di comando](/cli/azure/install-azure-cli) di Azure e [accedere](/cli/azure/authenticate-azure-cli)oppure selezionare **Prova.**

1. Visualizzare l'elenco delle regole di rete virtuale.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Abilitare l'endpoint del servizio per Servizi cognitivi di Azure in una subnet e una rete virtuale esistenti.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Per aggiungere una regola per una subnet in una rete virtuale appartenente a un altro tenant di Azure AD, usare un ID di subnet completo nel formato "/subscriptions/subscription/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > È possibile usare il parametro **di sottoscrizione** per recuperare l'ID di subnet per una rete virtuale appartenente a un altro tenant di Azure AD.

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

## <a name="grant-access-from-an-internet-ip-range"></a>Concedere l'accesso da un intervallo IP di Internet

È possibile configurare le risorse di Servizi cognitivi per consentire l'accesso da intervalli di indirizzi IP Internet pubblici specifici. Questa configurazione concede l'accesso a servizi specifici e reti locali, bloccando in modo efficace il traffico Internet generale.

Fornire intervalli di indirizzi Internet consentiti `16.17.18.0/24` utilizzando la [notazione CIDR](https://tools.ietf.org/html/rfc4632) nel modulo o come singoli indirizzi IP come `16.17.18.19`.

   > [!Tip]
   > Gli intervalli di indirizzi di piccole dimensioni con dimensioni di prefisso "/31" o "/32" non sono supportati. Questi intervalli vanno configurati con le regole usate per gli indirizzi IP singoli.

Le regole di rete per gli IP sono consentite solo per gli indirizzi IP della **rete Internet pubblica**. Gli intervalli di indirizzi IP riservati per le reti private (come definito nell'[RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) non sono consentiti nelle regole IP. Le reti private includono `10.*` `172.16.*`  -  `172.31.*`indirizzi `192.168.*`che iniziano con , , e .

   > [!NOTE]
   > Le regole di rete IP non hanno alcun effetto sulle richieste provenienti dalla stessa area di Azure della risorsa Servizi cognitivi. Usare le [regole di rete virtuale](#grant-access-from-a-virtual-network) per consentire richieste della stessa area.

Attualmente sono supportati solo gli indirizzi IPV4. Ogni risorsa di Servizi cognitivi supporta fino a 100 regole di rete IP, che possono essere combinate con le regole di [rete virtuale.](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali

Per concedere l'accesso dalle reti locali alla risorsa Servizi cognitivi con una regola di rete IP, è necessario identificare gli indirizzi IP con connessione Internet usati dalla rete. Per assistenza contattare l'amministratore di rete.

Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md) locale per il peering pubblico o il peering Microsoft, è necessario identificare gli indirizzi IP NAT. Per il peering pubblico, ogni circuito ExpressRoute per impostazione predefinita usa due indirizzi IP NAT. Ognuno viene applicato al traffico del servizio di Azure quando il traffico entra nella backbone di rete di Microsoft Azure.Each is applied to Azure service traffic when the traffic enters the Microsoft Azure network backbone. Per il peering Microsoft, gli indirizzi IP NAT utilizzati vengono forniti dal cliente o dal provider di servizi. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Vedere altre informazioni su [NAT per il peering pubblico e il peering Microsoft ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestione delle regole di rete IP

È possibile gestire le regole di rete IP per le risorse di Servizi cognitivi tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.You can manage IP network rules for Cognitive Services resources through the Azure portal, PowerShell, or the Azure CLI.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

1. Passare alla risorsa Servizi cognitivi che si vuole proteggere.

1. Selezionare il menu **RESOURCE MANAGEMENT** denominato **Rete virtuale**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a un intervallo IP Internet, immettere l'indirizzo IP o l'intervallo di indirizzi (in [formato CIDR](https://tools.ietf.org/html/rfc4632)) in**Intervallo di indirizzi**del **firewall** > . Vengono accettati solo indirizzi IP pubblici validi (non riservati).

   ![Aggiungi intervallo IP](media/vnet/virtual-network-add-ip-range.png)

1. Per rimuovere una regola di rete <span class="docon docon-delete x-hidden-focus"></span> IP, selezionare l'icona del cestino accanto all'intervallo di indirizzi.

   ![Elimina intervallo IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selezionare **Salva** per applicare le modifiche.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installare [Azure PowerShell](/powershell/azure/install-az-ps) e [accedere](/powershell/azure/authenticate-azureps)oppure selezionare **Prova.**

1. Elencare le regole di rete IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

1. Installare [l'interfaccia della riga di comando](/cli/azure/install-azure-cli) di Azure e [accedere](/cli/azure/authenticate-azure-cli)oppure selezionare **Prova.**

1. Elencare le regole di rete IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare i vari [servizi cognitivi](welcome.md) di AzureExplore the various Azure Cognitive Services
* Altre informazioni sugli endpoint del servizio di rete virtuale di AzureLearn more about [Azure Virtual Network Service Endpoints](../virtual-network/virtual-network-service-endpoints-overview.md)