<properties
	pageTitle="Azure AD Domain Services: linee guida sulla rete | Microsoft Azure"
	description="Considerazioni sulla rete per Azure Active Directory Domain Services"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>  

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>  

# Considerazioni sulla rete per Azure AD Domain Services

## Come selezionare una rete virtuale di Azure
Le indicazioni seguenti semplificano la selezione di una rete virtuale da usare con Azure AD Domain Services.

### Tipo di rete virtuale di Azure

- È possibile abilitare Azure AD Domain Services in una rete virtuale di Azure classica.

- La funzionalità Azure AD Domain Services **non può essere abilitata nelle reti virtuali create usando Azure Resource Manager**.

- È possibile connettere una rete virtuale basata su Resource Manager a una rete virtuale classica in cui è abilitata la funzionalità Azure AD Domain Services. È quindi possibile usare successivamente Azure AD Domain Services nella rete virtuale basata su Resource Manager.

- **Reti virtuali a livello di area**: se si prevede di usare una rete virtuale esistente, assicurarsi che sia una rete virtuale a livello di area.

    - Con Servizi di dominio Azure AD non è possibile usare reti virtuali che usano il meccanismo dei gruppi di affinità legacy.

	- Per usare Azure AD Domain Services, [eseguire la migrazione delle reti virtuali legacy alle reti virtuali a livello di area](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### Area di Azure per la rete virtuale

- Il dominio gestito di Azure AD Domain Services viene distribuito nella stessa area di Azure della rete virtuale in cui si è scelto di abilitare il servizio.

- Selezionare una rete virtuale in un'area di Azure supportata da Azure AD Domain Services.

- Per informazioni sulle aree di Azure in cui è disponibile Azure AD Domain Services, vedere la pagina relativa ai [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).


### Requisiti per la rete virtuale

- **Prossimità ai carichi di lavoro di Azure**: selezionare la rete virtuale che ospita o ospiterà le macchine virtuali che richiedono l'accesso ad Azure AD Domain Services.

- **Server DNS personalizzati/Bring-Your-Own**: assicurarsi che non siano presenti server DNS personalizzati configurati per la rete virtuale.

- **Domini esistenti con lo stesso nome di dominio**: assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Provare successivamente ad abilitare un dominio gestito di Azure AD Domain Services con lo stesso nome di dominio, ovvero 'contoso.com', alla rete virtuale. Si verifica un errore quando si prova ad abilitare Azure AD Domain Services. L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

> [AZURE.WARNING] Non è possibile spostare Domain Services in una rete virtuale diversa dopo l'abilitazione del servizio.


## Gruppi di sicurezza di rete e struttura della subnet
Un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) contiene un elenco di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete alle istanze VM in una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole ACL si applicano a tutte le istanze di VM in tale subnet. Il traffico verso una singola VM può essere inoltre ulteriormente limitato associando un gruppo di sicurezza di rete direttamente a tale VM.

> [AZURE.NOTE] **Distribuire Azure AD Domain Services in una subnet separata dedicata nella rete virtuale di Azure. Non applicare alcun gruppo di sicurezza di rete alla subnet dedicata. Non abilitare Azure AD Domain Services nella subnet del gateway della rete virtuale.**

![Struttura consigliata per la subnet](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)  

> [AZURE.WARNING] Quando si associa un gruppo di sicurezza di rete a una subnet in cui è abilitata la funzionalità Azure AD Domain Services, è possibile che si interferisca con la possibilità di manutenzione e gestione del dominio da parte di Microsoft. Viene inoltre ostacolata la sincronizzazione tra il tenant Azure AD e il dominio gestito. **Il Contratto di servizio non si applica alle distribuzioni in cui un gruppo di sicurezza di rete è stato applicato alla subnet in cui è abilitata la funzionalità Azure AD Domain Services.**


## Connettività di rete
Un dominio gestito di Azure AD Domain Services può essere abilitato solo in una singola rete virtuale classica di Azure. Le reti virtuali create usando Azure Resource Manager non sono supportate.

### Scenari per la connessione di reti di Azure
È possibile connettere le reti virtuali di Azure per l'uso del dominio gestito negli scenari di distribuzione seguenti:

#### Usare il dominio gestito in più di una rete virtuale di Azure classica
È possibile connettere altre reti virtuali di Azure classiche alla rete virtuale di Azure classica in cui è stata abilitata la funzionalità Azure AD Domain Services. Questa connessione consente di usare il dominio gestito con i carichi di lavoro distribuiti in altre reti virtuali.

![Connettività di rete virtuale classica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)  

#### Usare il dominio gestito in una rete virtuale basata su Resource Manager
È possibile connettere una rete virtuale basata su Resource Manager alla rete virtuale classica in cui è abilitata la funzionalità Azure AD Domain Services. Questa connessione consente di usare il dominio gestito con i carichi di lavoro distribuiti nella rete virtuale basata su Resource Manager.

![Connettività da rete virtuale basata su Resource Manager a rete virtuale classica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)  


### Opzioni per le connessioni di rete

- **Connessioni da rete virtuale a rete virtuale tramite connessioni VPN da sito a sito**: la connessione di una rete virtuale a un'altra rete virtuale è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE.

	![Connettività di rete virtuale tramite gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)  

    [Altre informazioni: connettere reti virtuali usando il gateway VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Connessioni da rete virtuale a rete virtuale tramite il peering reti virtuali**: il peering reti virtuali è un meccanismo che connette due reti virtuali nella stessa area tramite la rete backbone di Azure. Una volta eseguito il peering, le due reti virtuali appaiono come una sola per qualsiasi scopo di connettività. Continuano a essere gestite come risorse separate, ma le macchine virtuali in queste reti virtuali possono comunicare direttamente tra di esse usando gli indirizzi IP privati.

    ![Connettività di rete virtuale tramite peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)  

	[Altre informazioni: Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md)



<br>  

## Contenuti correlati

- [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md)

- [Configurare una connessione da rete virtuale a rete virtuale per il modello di distribuzione classico](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)

<!---HONumber=AcomDC_0921_2016-->