---
title: Pianificazione e connessioni di rete per Azure AD Domain Services | Microsoft Docs
description: Informazioni su alcune delle considerazioni sulla progettazione della rete virtuale e sulle risorse usate per la connettività quando si esegue Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 307b1a6838c3a78c04ba6a36ffd52bd6b98aae04
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722824"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Considerazioni sulla progettazione della rete virtuale e opzioni di configurazione per Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) fornisce servizi di autenticazione e gestione ad altre applicazioni e carichi di lavoro. La connettività di rete è un componente chiave. Senza le risorse della rete virtuale configurate correttamente, le applicazioni e i carichi di lavoro non possono comunicare con e utilizzare le funzionalità fornite da Azure AD DS. Pianificare i requisiti della rete virtuale per assicurarsi che Azure AD DS possa fornire le applicazioni e i carichi di lavoro in base alle esigenze.

Questo articolo illustra le considerazioni e i requisiti di progettazione per una rete virtuale di Azure per supportare Azure AD DS.

## <a name="azure-virtual-network-design"></a>Progettazione della rete virtuale di Azure

Per garantire la connettività di rete e consentire a applicazioni e servizi di eseguire l'autenticazione in un dominio gestito di Azure AD DS, è possibile usare una rete virtuale e una subnet di Azure. Idealmente, il dominio gestito deve essere distribuito nella propria rete virtuale.

È possibile includere una subnet dell'applicazione separata nella stessa rete virtuale per ospitare la VM di gestione o i carichi di lavoro di applicazioni leggeri. Una rete virtuale separata per i carichi di lavoro di applicazioni più grandi o complesse, con peering alla rete virtuale Azure AD DS, è in genere la progettazione più appropriata.

Le altre opzioni di progettazione sono valide, purché si soddisfino i requisiti descritti nelle sezioni seguenti per la rete virtuale e la subnet.

Quando si progetta la rete virtuale per Azure AD DS, si applicano le considerazioni seguenti:

* Azure AD DS deve essere distribuito nella stessa area di Azure della rete virtuale.
    * A questo punto, è possibile distribuire un solo dominio gestito per ogni tenant Azure AD. Il dominio gestito viene distribuito in una singola area. Assicurarsi di creare o selezionare una rete virtuale in un' [area che supporta Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Prendere in considerazione la vicinanza di altre aree di Azure e delle reti virtuali che ospitano i carichi di lavoro dell'applicazione.
    * Per ridurre al minimo la latenza, lasciare le applicazioni core vicine o nella stessa area della subnet della rete virtuale per il dominio gestito. È possibile usare il peering di rete virtuale o le connessioni VPN (Virtual Private Network) tra le reti virtuali di Azure. Queste opzioni di connessione sono illustrate nella sezione seguente.
* La rete virtuale non può basarsi su servizi DNS diversi dai servizi forniti dal dominio gestito.
    * Azure AD DS fornisce il proprio servizio DNS. La rete virtuale deve essere configurata per l'uso di questi indirizzi del servizio DNS. La risoluzione dei nomi per altri spazi dei nomi può essere eseguita usando i server d'inoltri condizionali.
    * Non è possibile usare le impostazioni del server DNS personalizzato per indirizzare le query da altri server DNS, incluse le macchine virtuali. Le risorse nella rete virtuale devono usare il servizio DNS fornito dal dominio gestito.

> [!IMPORTANT]
> Non è possibile spostare Azure AD DS in una rete virtuale diversa dopo l'abilitazione del servizio.

Un dominio gestito si connette a una subnet in una rete virtuale di Azure. Progettare questa subnet per Azure AD DS con le considerazioni seguenti:

* Un dominio gestito deve essere distribuito nella propria subnet. Non usare una subnet esistente o una subnet del gateway.
* Un gruppo di sicurezza di rete viene creato durante la distribuzione di un dominio gestito. Questo gruppo di sicurezza di rete contiene le regole necessarie per la corretta comunicazione del servizio.
    * Non creare o usare un gruppo di sicurezza di rete esistente con regole personalizzate.
* Un dominio gestito richiede 3-5 indirizzi IP. Verificare che l'intervallo di indirizzi IP della subnet possa fornire questo numero di indirizzi.
    * La limitazione degli indirizzi IP disponibili può impedire al dominio gestito di gestire due controller di dominio.

Il diagramma di esempio seguente illustra una progettazione valida in cui il dominio gestito ha una propria subnet, esiste una subnet del gateway per la connettività esterna e i carichi di lavoro dell'applicazione si trovano in una subnet connessa all'interno della rete virtuale:

![Struttura consigliata per la subnet](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Connessioni alla rete virtuale Azure AD DS

Come indicato nella sezione precedente, è possibile creare un dominio gestito solo in una singola rete virtuale in Azure ed è possibile creare un solo dominio gestito per ogni Azure AD tenant. In base a questa architettura, potrebbe essere necessario connettere una o più reti virtuali che ospitano i carichi di lavoro dell'applicazione alla rete virtuale del dominio gestito.

È possibile connettere i carichi di lavoro delle applicazioni ospitati in altre reti virtuali di Azure usando uno dei metodi seguenti:

* Peering di rete virtuale
* Rete privata virtuale (VPN)

### <a name="virtual-network-peering"></a>Peering di rete virtuale

Peering di rete virtuale è un meccanismo che connette due reti virtuali nella stessa area tramite la rete backbone di Azure. Il peering di rete virtuale globale è in grado di connettere la rete virtuale tra aree di Azure. Una volta eseguito il peering, le due reti virtuali consentono alle risorse, ad esempio le macchine virtuali, di comunicare direttamente tra loro usando indirizzi IP privati. L'uso del peering di rete virtuale consente di distribuire un dominio gestito con i carichi di lavoro dell'applicazione distribuiti in altre reti virtuali.

![Connettività di rete virtuale tramite peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Per altre informazioni, vedere [Panoramica del peering di rete virtuale di Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Rete privata virtuale (VPN)

È possibile connettere una rete virtuale a un'altra rete virtuale (da VNet a VNet) nello stesso modo in cui è possibile configurare una rete virtuale in un percorso del sito locale. Entrambe le connessioni usano un gateway VPN per creare un tunnel sicuro usando IPsec/IKE. Questo modello di connessione consente di distribuire il dominio gestito in una rete virtuale di Azure e quindi di connettere percorsi locali o altri cloud.

![Connettività di rete virtuale tramite un gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Per altre informazioni sull'uso della rete privata virtuale, vedere [configurare una connessione gateway VPN da VNet a VNet usando il portale di Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Risoluzione dei nomi durante la connessione di reti virtuali

Le reti virtuali connesse alla rete virtuale del dominio gestito hanno in genere le proprie impostazioni DNS. Quando si connettono le reti virtuali, la risoluzione dei nomi per la rete virtuale di connessione non viene configurata automaticamente per risolvere i servizi forniti dal dominio gestito. La risoluzione dei nomi nelle reti virtuali di connessione deve essere configurata in modo da consentire ai carichi di lavoro dell'applicazione di individuare il dominio gestito.

È possibile abilitare la risoluzione dei nomi usando i server d'inoltri DNS condizionali nel server DNS che supporta le reti virtuali di connessione oppure usando gli stessi indirizzi IP DNS della rete virtuale del dominio gestito.

## <a name="network-resources-used-by-azure-ad-ds"></a>Risorse di rete usate da Azure AD DS

Un dominio gestito crea alcune risorse di rete durante la distribuzione. Queste risorse sono necessarie per il corretto funzionamento e la gestione del dominio gestito e non devono essere configurate manualmente.

| Risorsa di Azure                          | Descrizione |
|:----------------------------------------|:---|
| Scheda di interfaccia di rete                  | Azure AD DS ospita il dominio gestito in due controller di dominio (DCs) che vengono eseguiti in Windows Server come macchine virtuali di Azure. Ogni macchina virtuale dispone di un'interfaccia di rete virtuale che si connette alla subnet della rete virtuale. |
| Indirizzo IP pubblico standard dinamico      | Azure AD DS comunica con il servizio di sincronizzazione e gestione usando un indirizzo IP pubblico dello SKU standard. Per altre informazioni sugli indirizzi IP pubblici, vedere [tipi di indirizzi IP e metodi di allocazione in Azure](../virtual-network/public-ip-addresses.md). |
| Azure Load Balancer standard            | Azure AD DS usa un servizio di bilanciamento del carico SKU standard per Network Address Translation (NAT) e bilanciamento del carico (se usato con LDAP sicuro). Per altre informazioni sui bilanciamenti del carico di Azure, vedere [che cos'è Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Regole NAT (Network Address Translation) | Azure AD DS crea e usa tre regole NAT nel servizio di bilanciamento del carico: una regola per il traffico HTTP protetto e due regole per la comunicazione remota di PowerShell sicura. |
| Regole del servizio di bilanciamento del carico                     | Quando un dominio gestito è configurato per l'LDAP sicuro sulla porta TCP 636, vengono create tre regole e usate in un servizio di bilanciamento del carico per distribuire il traffico. |

> [!WARNING]
> Non eliminare o modificare alcuna risorsa di rete creata da Azure AD DS, ad esempio configurando manualmente il servizio di bilanciamento del carico o le regole. Se si eliminano o si modificano le risorse di rete, è possibile che si verifichi un'interruzione del servizio Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Gruppi di sicurezza di rete e porte obbligatorie

Un [gruppo di sicurezza di rete (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) contiene un elenco di regole che consentono o negano il traffico di rete al traffico in una rete virtuale di Azure. Un gruppo di sicurezza di rete viene creato quando si distribuisce un dominio gestito che contiene un set di regole che consentono al servizio di fornire funzioni di autenticazione e di gestione. Questo gruppo di sicurezza di rete predefinito è associato alla subnet della rete virtuale in cui è distribuito il dominio gestito.

Le seguenti regole del gruppo di sicurezza di rete sono necessarie affinché il dominio gestito fornisca servizi di autenticazione e gestione. Non modificare o eliminare queste regole del gruppo di sicurezza di rete per la subnet della rete virtuale in cui è distribuito il dominio gestito.

| Numero porta | Protocollo | Source (Sorgente)                             | Destination | Azione | Necessario | Scopo |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Qualsiasi         | Allow  | Sì      | Sincronizzazione con il tenant del Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Qualsiasi         | Allow  | Sì      | Gestione del dominio. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Qualsiasi         | Allow  | Sì      | Gestione del dominio. |

Viene creato un servizio di bilanciamento del carico Standard di Azure che richiede la definizione di queste regole. Questo gruppo di sicurezza di rete protegge Azure AD Domain Services ed è necessario affinché il dominio gestito funzioni correttamente. Non eliminare questo gruppo di sicurezza di rete. Il servizio di bilanciamento del carico non funziona correttamente senza di esso.

> [!WARNING]
> Non modificare manualmente le configurazioni e le risorse di rete. Quando si associa un gruppo di sicurezza di rete configurato in modo non configurato o una tabella di route definita dall'utente con la subnet in cui viene distribuito il dominio gestito, è possibile che si verifichino problemi di gestione e gestione del dominio da parte di Microsoft. Viene anche interrotta la sincronizzazione tra il tenant Azure AD e il dominio gestito.
>
> Se si usa l'accesso LDAP sicuro, è possibile aggiungere la regola della porta TCP 636 necessaria per consentire il traffico esterno, se necessario. Se si aggiunge questa regola, le regole del gruppo di sicurezza di rete non vengono inserite in uno stato non supportato. Per altre informazioni, vedere [bloccare l'accesso LDAP sicuro tramite Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Per il gruppo di sicurezza di rete esistono anche le regole predefinite per *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*e *DenyAllOutBound* . Non modificare o eliminare queste regole predefinite.
>
> Il contratto di servizio di Azure non si applica alle distribuzioni in cui sono stati applicati un gruppo di sicurezza di rete configurato in modo non corretto e/o tabelle di route definite dall'utente che impedisce a Azure AD DS di aggiornare e gestire il dominio.

### <a name="port-443---synchronization-with-azure-ad"></a>Porta 443-sincronizzazione con Azure AD

* Usato per sincronizzare il tenant di Azure AD con il dominio gestito.
* Senza l'accesso a questa porta, il dominio gestito non può essere sincronizzato con il tenant di Azure AD. Gli utenti potrebbero non essere in grado di eseguire l'accesso perché le modifiche apportate alle password non verranno sincronizzate con il dominio gestito.
* Per impostazione predefinita, l'accesso in ingresso a questa porta agli indirizzi IP è limitato tramite il tag del servizio **AzureActiveDirectoryDomainServices** .
* Non limitare l'accesso in uscita da questa porta.

### <a name="port-3389---management-using-remote-desktop"></a>Porta 3389-gestione con desktop remoto

* Utilizzato per le connessioni Desktop remoto ai controller di dominio nel dominio gestito.
* La regola del gruppo di sicurezza di rete predefinito usa il tag del servizio *CorpNetSaw* per limitare ulteriormente il traffico.
    * Questo tag di servizio consente solo alle workstation con accesso sicuro nella rete aziendale Microsoft di usare desktop remoto per il dominio gestito.
    * L'accesso è consentito solo con la motivazione aziendale, ad esempio per gli scenari di gestione o di risoluzione dei problemi.
* Questa regola può essere impostata su *Deny*e impostata su *Consenti* solo quando richiesto. La maggior parte delle attività di gestione e monitoraggio viene eseguita usando la comunicazione remota di PowerShell. Il protocollo RDP viene usato solo nel raro caso in cui Microsoft debba connettersi in remoto al dominio gestito per la risoluzione dei problemi avanzata.

> [!NOTE]
> Se si tenta di modificare questa regola del gruppo di sicurezza di rete, non è possibile selezionare manualmente il tag del servizio *CorpNetSaw* dal portale. È necessario usare Azure PowerShell o l'interfaccia della riga di comando di Azure per configurare manualmente una regola che usa il tag del servizio *CorpNetSaw* .
>
> Ad esempio, è possibile usare lo script seguente per creare una regola che consenta il protocollo RDP: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

### <a name="port-5986---management-using-powershell-remoting"></a>Porta 5986-gestione con la comunicazione remota di PowerShell

* Consente di eseguire attività di gestione tramite la comunicazione remota di PowerShell nel dominio gestito.
* Senza l'accesso a questa porta, il dominio gestito non può essere aggiornato, configurato, sottoposto a backup o monitorato.
* Per i domini gestiti che usano una rete virtuale basata su Gestione risorse, è possibile limitare l'accesso in ingresso a questa porta al tag del servizio *AzureActiveDirectoryDomainServices* .
    * Per i domini gestiti legacy che usano una rete virtuale basata su classica, è possibile limitare l'accesso in ingresso a questa porta agli indirizzi IP di origine seguenti: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*e *104.40.87.209*.

    > [!NOTE]
    > In 2017 Azure AD Domain Services è diventato disponibile per ospitare in una rete Azure Resource Manager. Da allora, siamo riusciti a creare un servizio più sicuro usando le funzionalità moderne del Azure Resource Manager. Poiché le distribuzioni di Azure Resource Manager sostituiscono completamente le distribuzioni classiche, le distribuzioni di rete virtuale di Azure AD DS classico verranno ritirate il 1 ° marzo 2023.
    >
    > Per ulteriori informazioni, vedere l' [avviso ufficiale di deprecazione](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Route definite dall'utente

Le route definite dall'utente non vengono create per impostazione predefinita e non sono necessarie per il corretto funzionamento di Azure AD DS. Se è necessario usare le tabelle di route, evitare di apportare modifiche alla route *0.0.0.0* . Le modifiche apportate a questa route interrompono Azure AD DS e il dominio gestito viene inserito in uno stato non supportato.

È anche necessario instradare il traffico in ingresso dagli indirizzi IP inclusi nei rispettivi tag del servizio di Azure alla subnet del dominio gestito. Per altre informazioni sui tag di servizio e l'indirizzo IP associato da, vedere [intervalli IP e tag di servizio di Azure-cloud pubblico](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Questi intervalli IP del Data Center di Azure possono cambiare senza preavviso. Assicurarsi di disporre di processi per la convalida degli indirizzi IP più recenti.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su alcune risorse di rete e sulle opzioni di connessione utilizzate da Azure AD DS, vedere gli articoli seguenti:

* [Peering di rete virtuale di Azure](../virtual-network/virtual-network-peering-overview.md)
* [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Gruppi di sicurezza di rete di Azure](../virtual-network/security-overview.md)