---
title: Pianificazione della rete e connessioni per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su alcune delle considerazioni di progettazione della rete virtuale e le risorse usate per la connettività quando si esegue Servizi di dominio Azure Active Directory.Learn about some of the virtual network design considerations and resources used for connectivity when you run Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e610bf94dfdee4e2765e4fae4259f18a9f1036b5
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639985"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Considerazioni sulla progettazione della rete virtuale e opzioni di configurazione per Servizi di dominio Azure ADVirtual network design considerations and configuration options for Azure AD Domain Services

Poiché Servizi di dominio Azure Active Directory fornisce servizi di autenticazione e gestione ad altre applicazioni e carichi di lavoro, la connettività di rete è un componente chiave. Senza risorse di rete virtuale configurate correttamente, le applicazioni e i carichi di lavoro non possono comunicare e usare le funzionalità fornite da Servizi di dominio Active Directory di Azure.Without correctly configured virtual network resources, applications and workloads can't communicate with and use the features provided by Azure AD DS. Pianificare i requisiti della rete virtuale per assicurarsi che Azure AD DS possa soddisfare le applicazioni e i carichi di lavoro in base alle esigenze.

Questo articolo descrive considerazioni sulla progettazione e requisiti per una rete virtuale di Azure per supportare Azure AD DS.This article outlines design considerations and requirements for an Azure virtual network to support Azure AD DS.

## <a name="azure-virtual-network-design"></a>Progettazione della rete virtuale di AzureAzure virtual network design

Per fornire la connettività di rete e consentire alle applicazioni e ai servizi di eseguire l'autenticazione in Servizi di dominio Active Directory di Azure, usare una subnet e una rete virtuale di Azure.To provide network connectivity and allow applications and services to authenticate against Azure AD DS, you use an Azure virtual network and subnet. In teoria, Servizi di dominio Active Directory di Azure deve essere distribuito nella propria rete virtuale. È possibile includere una subnet dell'applicazione separata nella stessa rete virtuale per ospitare la macchina virtuale di gestione o carichi di lavoro leggeri delle applicazioni. Una rete virtuale separata per carichi di lavoro di applicazioni più grandi o complessi, sottoposta a peered alla rete virtuale di Servizi di dominio Active Directory di Azure, è in genere la progettazione più appropriata. Sono valide altre opzioni di progettazione, a condizione che siano soddisfatti i requisiti descritti nelle sezioni seguenti per la rete virtuale e la subnet.

Quando si progetta la rete virtuale per Servizi di dominio Azure AD, si applicano le considerazioni seguenti:As you design the virtual network for Azure AD DS, the following considerations apply:

* Servizi di dominio Active Directory di Azure deve essere distribuito nella stessa area di Azure della rete virtuale.
    * Al momento, è possibile distribuire un solo dominio gestito di Servizi di dominio Active Directory di Azure per ogni tenant di Azure AD. Il dominio gestito di Azure AD DS viene distribuito in un'area singola. Assicurarsi di creare o selezionare una rete virtuale in [un'area che supporta Servizi](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)di dominio Active Directory di Azure.
* Considerare la vicinanza di altre aree di Azure e delle reti virtuali che ospitano i carichi di lavoro delle applicazioni.
    * Per ridurre al minimo la latenza, mantenere le applicazioni principali vicine o nella stessa area della subnet di rete virtuale per il dominio gestito di Servizi di dominio Active Directory di Azure.To minimize latency, keep your core applications close to, or in the same region as, the virtual network subnet for your Azure AD DS managed domain. È possibile usare il peering di rete virtuale o le connessioni di rete privata virtuale (VPN) tra reti virtuali di Azure.You can use virtual network peering or virtual private network (VPN) connections between Azure virtual networks. Queste opzioni di connessione sono descritte in una sezione seguente.
* La rete virtuale non può fare affidamento su servizi DNS diversi da quelli forniti da Servizi di dominio Active Directory di Azure.The virtual network can't rely on DNS services other than those provided by Azure AD DS.
    * Servizi di dominio Active Directory di Azure fornisce il proprio servizio DNS. La rete virtuale deve essere configurata per l'utilizzo di questi indirizzi del servizio DNS. La risoluzione dei nomi per gli spazi dei nomi aggiuntivi può essere eseguita utilizzando i server d'inoltro condizionali.
    * Non è possibile utilizzare le impostazioni personalizzate del server DNS per indirizzare le query da altri server DNS, incluse le macchine virtuali. Le risorse nella rete virtuale devono usare il servizio DNS fornito da Servizi di dominio Active Directory di Azure.Resources in the virtual network must use the DNS service provided by Azure AD DS.

> [!IMPORTANT]
> Non è possibile spostare Azure AD DS in una rete virtuale diversa dopo aver abilitato il servizio.

Un dominio gestito di Servizi di dominio Active Directory di Azure si connette a una subnet in una rete virtuale di Azure.An Azure AD DS managed domain connects to a subnet in an Azure virtual network. Progettare questa subnet per Servizi di dominio Active Directory di Azure con le considerazioni seguenti:Design this subnet for Azure AD DS with the following considerations:

* Servizi di dominio Active Directory di Azure deve essere distribuito nella propria subnet. Non usare una subnet esistente o una subnet gateway.
* Un gruppo di sicurezza di rete viene creato durante la distribuzione di un dominio gestito di Servizi di dominio Active Directory di Azure.A network security group is created during the deployment of an Azure AD DS managed domain. Questo gruppo di sicurezza di rete contiene le regole necessarie per la corretta comunicazione del servizio.
    * Non creare o usare un gruppo di sicurezza di rete esistente con regole personalizzate.
* Azure AD DS richiede 3-5 indirizzi IP. Assicurarsi che l'intervallo di indirizzi IP della subnet possa fornire questo numero di indirizzi.
    * Limitare gli indirizzi IP disponibili può impedire a Servizi di dominio Azure AD di gestire due controller di dominio.

Il diagramma di esempio seguente descrive una progettazione valida in cui Servizi di dominio Active Directory di Azure ha una propria subnet, è presente una subnet gateway per la connettività esterna e i carichi di lavoro delle applicazioni si trovano in una subnet connessa all'interno della rete virtuale:The following example diagram outlines a valid design where Azure AD DS has its own subnet, there's a gateway subnet for external connectivity, and application workloads are in a connected subnet within the virtual network:

![Struttura consigliata per la subnet](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Connessioni alla rete virtuale di Servizi di dominio Active Directory di AzureConnections to the Azure AD DS virtual network

Come indicato nella sezione precedente, è possibile creare un dominio gestito di Servizi di dominio Azure AD solo in una singola rete virtuale in Azure e creare un solo dominio gestito per tenant di Azure AD. In base a questa architettura, potrebbe essere necessario connettere una o più reti virtuali che ospitano i carichi di lavoro delle applicazioni alla rete virtuale di Azure AD DS.Based on this architecture, you may need to connect one or more virtual networks that host your application workloads to your Azure AD DS virtual network.

È possibile connettere i carichi di lavoro delle applicazioni ospitati in altre reti virtuali di Azure usando uno dei metodi seguenti:You can connect application workloads hosted in other Azure virtual networks using one of the following methods:

* Peering di rete virtuale
* Rete privata virtuale (VPN)

### <a name="virtual-network-peering"></a>Peering di rete virtuale

Peering di rete virtuale è un meccanismo che connette due reti virtuali nella stessa area tramite la rete backbone di Azure. Il peering globale della rete virtuale può connettere la rete virtuale tra aree di Azure.Global virtual network peering can connect virtual network across Azure regions. Dopo il peered, le due reti virtuali consentono alle risorse, ad esempio le macchine virtuali, di comunicare tra loro direttamente usando indirizzi IP privati. L'uso del peering di rete virtuale consente di distribuire un dominio gestito di Azure AD DS con i carichi di lavoro dell'applicazione distribuiti in altre reti virtuali.

![Connettività di rete virtuale tramite peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Per altre informazioni, vedere Panoramica del peering della rete virtuale di Azure.For more information, see [Azure virtual network peering overview](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Rete privata virtuale (VPN)

È possibile connettere una rete virtuale a un'altra rete virtuale (da VNet a VNet) nello stesso modo in cui è possibile configurare una rete virtuale in un percorso di sito locale. Entrambe le connessioni utilizzano un gateway VPN per creare un tunnel sicuro utilizzando IPsec/IKE. Questo modello di connessione consente di distribuire Servizi di dominio Active Directory di Azure in una rete virtuale di Azure e quindi di connettersi a percorsi locali o altri cloud.

![Connettività di rete virtuale tramite un gateway VPNVirtual network connectivity using a VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Per altre informazioni sull'uso della rete privata virtuale, vedere [Configurare una connessione al gateway VPN da rete a VNet tramite il portale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)di Azure .

## <a name="name-resolution-when-connecting-virtual-networks"></a>Risoluzione dei nomi durante la connessione di reti virtualiName resolution when connecting virtual networks

Le reti virtuali connesse alla rete virtuale di Servizi di dominio Azure AD hanno in genere impostazioni DNS personalizzate. Quando si connettono reti virtuali, non viene configurata automaticamente la risoluzione dei nomi per la rete virtuale di connessione per risolvere i servizi forniti dal dominio gestito di Servizi di dominio Active Directory di Azure.When you connect virtual networks, it doesn't automatically configure name resolution for the connecting virtual network to resolve services provided by the Azure AD DS managed domain. La risoluzione dei nomi nelle reti virtuali di connessione deve essere configurata per consentire ai carichi di lavoro delle applicazioni di individuare Servizi di dominio Azure AD.

È possibile abilitare la risoluzione dei nomi usando i server d'inoltro DNS condizionali nel server DNS che supportano le reti virtuali di connessione o usando gli stessi indirizzi IP DNS della rete virtuale del servizio di dominio di Azure AD.

## <a name="network-resources-used-by-azure-ad-ds"></a>Risorse di rete usate da Servizi di dominio Active Directory di AzureNetwork resources used by Azure AD DS

Un dominio gestito di Servizi di dominio Active Directory di Azure crea alcune risorse di rete durante la distribuzione. Queste risorse sono necessarie per il corretto funzionamento e la gestione del dominio gestito di Servizi di dominio Active Directory di Azure e non devono essere configurate manualmente.

| Risorsa di Azure                          | Descrizione |
|:----------------------------------------|:---|
| Scheda di interfaccia di rete                  | Azure AD DS hosts the managed domain on two domain controllers (DCs) that run on Windows Server as Azure VMs. Ogni macchina virtuale ha un'interfaccia di rete virtuale che si connette alla subnet della rete virtuale. |
| Indirizzo IP pubblico standard dinamico      | Servizi di dominio Active Directory di Azure comunica con il servizio di sincronizzazione e gestione usando un indirizzo IP pubblico SKU standard. Per altre informazioni sugli indirizzi IP pubblici, vedere Tipi di indirizzi IP e metodi di allocazione in Azure.For more information about public IP addresses, see [IP address types and allocation methods in Azure.](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| Servizio di bilanciamento del carico standard di AzureAzure standard load balancer            | Servizi di dominio Active Directory di Azure usa un servizio di bilanciamento del carico SKU standard per NAT (Network Address Translation) e bilanciamento del carico (se usato con LDAP sicuro). Per altre informazioni sui servizi di bilanciamento del carico di Azure, vedere [Che cos'è Azure Load Balancer.](../load-balancer/load-balancer-overview.md) |
| Regole NAT (Network Address Translation) | Servizi di dominio Active Directory di Azure crea e usa tre regole NAT nel servizio di bilanciamento del carico: una regola per il traffico HTTP sicuro e due regole per la comunicazione remota di PowerShell sicura. |
| Regole del servizio di bilanciamento del carico                     | Quando un dominio gestito di Servizi di dominio Active Directory di Azure è configurato per LDAP protetto sulla porta TCP 636, vengono create e usate tre regole in un servizio di bilanciamento del carico per distribuire il traffico. |

> [!WARNING]
> Non eliminare o modificare alcuna risorsa di rete creata da Servizi di dominio Active Directory di Azure, ad esempio la configurazione manuale del servizio di bilanciamento del carico o delle regole. Se si elimina o si modifica una delle risorse di rete, potrebbe verificarsi un'interruzione del servizio Servizi di dominio Active Directory di Azure.If you delete or modify any of the network resources, an Azure AD DS service outage may occur.

## <a name="network-security-groups-and-required-ports"></a>Gruppi di sicurezza di rete e porte necessarie

Un gruppo di sicurezza di rete contiene un elenco di regole che consentono o negano il traffico di rete al traffico in una rete virtuale di Azure.A network [security group (NSG)](../virtual-network/virtual-networks-nsg.md) contains a list of rules that allow or deny network traffic to traffic in an Azure virtual network. Un gruppo di sicurezza di rete viene creato quando si distribuisce Azure AD DS che contiene un set di regole che consentono al servizio di fornire funzioni di autenticazione e gestione. Questo gruppo di sicurezza di rete predefinito è associato alla subnet di rete virtuale in cui viene distribuito il dominio gestito di Servizi di dominio Active Directory di Azure.This default network security group is associated with the virtual network subnet your Azure AD DS managed domain is deployed into.

Le regole del gruppo di sicurezza di rete seguenti sono necessarie per Servizi di dominio Azure AD per fornire servizi di autenticazione e gestione. Non modificare o eliminare queste regole del gruppo di sicurezza di rete per la subnet della rete virtuale in cui è distribuito il dominio gestito di Servizi di dominio Active Directory di Azure.Don't edit or delete these network security group rules for the virtual network subnet your Azure AD DS managed domain is deployed into.

| Numero della porta | Protocollo | Source (Sorgente)                             | Destination | Azione | Obbligatoria | Scopo |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Qualsiasi         | Allow  | Sì      | Sincronizzazione con il tenant di Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Qualsiasi         | Allow  | Sì      | Gestione del tuo dominio. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Qualsiasi         | Allow  | Sì      | Gestione del tuo dominio. |

> [!WARNING]
> Non modificare manualmente queste risorse e configurazioni di rete. Quando si associa un gruppo di sicurezza di rete non configurato correttamente o una tabella di route definita dall'utente alla subnet in cui viene distribuito Servizi di dominio Active Directory di Azure, è possibile interrompere la capacità di Microsoft di gestire il dominio. Anche la sincronizzazione tra il tenant di Azure AD e il dominio gestito di Servizi di dominio Active Directory di Azure viene interrotta.
>
> Se si utilizza LDAP sicuro, è possibile aggiungere la regola 636 della porta TCP richiesta per consentire il traffico esterno, se necessario. L'aggiunta di questa regola non inserisce le regole del gruppo di sicurezza di rete in uno stato non supportato. Per ulteriori informazioni, consulta [Bloccare l'accesso LDAP sicuro su Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Esistono regole predefinite per *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*e *DenyAllOutBound* anche per il gruppo di sicurezza di rete. Non modificare o eliminare queste regole predefinite.
>
> Il livello di servizio di Azure non si applica alle distribuzioni in cui è stato applicato un gruppo di sicurezza di rete configurato in modo non corretto e/o tabelle di route definite dall'utente che impediscono ad Azure AD DS di aggiornare e gestire il dominio.

### <a name="port-443---synchronization-with-azure-ad"></a>Porta 443 - sincronizzazione con Azure ADPort 443 - synchronization with Azure AD

* Utilizzato per sincronizzare il tenant di Azure AD con il dominio gestito di Servizi di dominio Active Directory di Azure.
* Senza accesso a questa porta, il dominio gestito di Azure AD DS non può eseguire la sincronizzazione con il tenant di Azure AD. Gli utenti potrebbero non essere in grado di accedere poiché le modifiche alle password non vengono sincronizzate con il dominio gestito di Servizi di dominio Active Directory di Azure.Users may not be able to sign in as changes to their passwords wouldn't be synchronized to your Azure AD DS managed domain.
* L'accesso in ingresso a questa porta agli indirizzi IP è limitato per impostazione predefinita usando il tag del servizio **AzureActiveDirectoryDomainServices.Inbound access** to this port to IP addresses is restricted by default using the AzureActiveDirectoryDomainServices service tag.
* Non limitare l'accesso in uscita da questa porta.

### <a name="port-3389---management-using-remote-desktop"></a>Porta 3389 - gestione tramite desktop remoto

* Utilizzato per le connessioni desktop remoto ai controller di dominio nel dominio gestito di Servizi di dominio Active Directory di Azure.Used for remote desktop connections to domain controllers in your Azure AD DS managed domain.
* La regola del gruppo di sicurezza di rete predefinito utilizza il tag del servizio *CorpNetSaw* per limitare ulteriormente il traffico.
    * Questo tag di servizio consente solo alle workstation di accesso sicuro nella rete aziendale Microsoft di usare desktop remoto per il dominio gestito di Servizi di dominio Active Directory di Azure.This service tag permits only secure access workstations on the Microsoft corporate network to use remote desktop to the Azure AD DS managed domain.
    * L'accesso è consentito solo con motivazione aziendale, ad esempio per scenari di gestione o risoluzione dei problemi.
* Questa regola può essere impostata su *Nega*e su *Consenti* solo quando necessario. La maggior parte delle attività di gestione e monitoraggio viene eseguita tramite la comunicazione remota di PowerShell.Most management and monitoring tasks are performed using PowerShell remoting. RDP viene utilizzato solo nel raro caso in cui Microsoft debba connettersi in remoto al dominio gestito per la risoluzione avanzata dei problemi.

> [!NOTE]
> Non è possibile selezionare manualmente il tag del servizio *CorpNetSaw* dal portale se si tenta di modificare questa regola del gruppo di sicurezza di rete. È necessario usare Azure PowerShell o l'interfaccia della riga di comando di Azure per configurare manualmente una regola che usa il tag del servizio *CorpNetSaw.You* must use Azure PowerShell or the Azure CLI per configure manually a rule that uses the CorpNetSaw service tag.

### <a name="port-5986---management-using-powershell-remoting"></a>Porta 5986 - gestione tramite la comunicazione remota di PowerShellPort 5986 - management using PowerShell remoting

* Usato per eseguire attività di gestione usando la comunicazione remota di PowerShell nel dominio gestito di Servizi di dominio Active Directory di Azure.Used to perform management tasks using PowerShell remoting in your Azure AD DS managed domain.
* Senza l'accesso a questa porta, il dominio gestito di Azure AD DS non può essere aggiornato, configurato, sottoposto a backup o monitorato.
* Per i domini gestiti di Servizi di dominio Active Directory di Azure che usano una rete virtuale basata su Resource Manager, è possibile limitare l'accesso in ingresso a questa porta al tag del servizio *AzureActiveDirectoryDomainServices.For* Azure AD DS managed domains that use a Resource Manager-based virtual network, you can restrict inbound access to this port to the AzureActiveDirectoryDomainServices service tag.
    * Per i domini gestiti legacy di Servizi di dominio Active Directory di Azure che usano una rete virtuale classica, è possibile limitare l'accesso in ingresso a questa porta ai seguenti indirizzi IP di origine: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*e *104.40.87.209*.

    > [!NOTE]
    > Nel 2017, Servizi di dominio Azure AD è diventato disponibile per l'hosting in una rete di Azure Resource Manager.In 2017, Azure AD Domain Services became available to host in an Azure Resource Manager network. Da allora, siamo stati in grado di creare un servizio più sicuro usando le funzionalità moderne di Azure Resource Manager.From then, we are been able to build a more secure service using the Azure Resource Manager's modern capabilities. Poiché le distribuzioni di Azure Resource Manager sostituiscono completamente le distribuzioni classiche, le distribuzioni di reti virtuali classiche di Servizi di dominio Active Directory di Azure verranno ritirate il 1 marzo 2023.Because Azure Resource Manager deployments fully replace classic deployments, Azure AD DS classic virtual network deployments will be retired on March 1, 2023.
    >
    > Per ulteriori informazioni, consultare [l'avviso ufficiale di deprecazione](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Route definite dall'utente

Le route definite dall'utente non vengono create per impostazione predefinita e non sono necessarie per il corretto funzionamento di Servizi di dominio Active Directory di Azure.User-defined routes aren't created by default, and aren't needed for Azure AD DS to work correctly. Se è necessario utilizzare le tabelle di route, evitare di apportare modifiche alla route *0.0.0.0.* Le modifiche a questa route interrompono Servizi di dominio Azure AD e mettono il dominio gestito in uno stato non supportato.

È inoltre necessario instradare il traffico in ingresso dagli indirizzi IP inclusi nei rispettivi tag del servizio di Azure alla subnet di Servizi di dominio Azure AD. Per altre informazioni sui tag di servizio e sul relativo indirizzo IP, vedere [Intervalli IP di Azure e tag di](https://www.microsoft.com/en-us/download/details.aspx?id=56519)servizio - Cloud pubblico.

> [!CAUTION]
> Questi intervalli IP del data center di Azure possono cambiare senza preavviso. Assicurarsi di disporre di processi per convalidare gli indirizzi IP più recenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su alcune delle risorse di rete e sulle opzioni di connessione usate da Servizi di dominio Active Directory di Azure, vedere gli articoli seguenti:For more information about some of the network resources and connection options used by Azure AD DS, see the following articles:

* [Peering della rete virtuale di AzureAzure virtual network peering](../virtual-network/virtual-network-peering-overview.md)
* [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Gruppi di sicurezza di rete di Azure](../virtual-network/security-overview.md)
