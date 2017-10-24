---
title: 'Azure AD Domain Services: linee guida sulla rete | Microsoft Docs'
description: Considerazioni sulla rete per Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: e274e0806e99cce484f6ff03803c03bf0034dcd6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerazioni sulla rete per Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Come selezionare una rete virtuale di Azure
Le indicazioni seguenti semplificano la selezione di una rete virtuale da usare con Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Tipo di rete virtuale di Azure
* **Reti virtuali di gestione risorse**: la funzionalità Azure AD Domain Services può essere abilitata nelle reti virtuali create usando Azure Resource Manager.
* È possibile abilitare Azure AD Domain Services in una rete virtuale di Azure classica. Il supporto per le reti virtuali classiche sarà tuttavia deprecato a breve. È consigliabile usare le reti virtuali di Resource Manager per i nuovi domini gestiti creati.
* È possibile connettere altre reti virtuali alla rete virtuale in cui è abilitata la funzionalità Azure AD Domain Services. Per altre informazioni, vedere la sezione [Connettività di rete](active-directory-ds-networking.md#network-connectivity).
* **Reti virtuali a livello di area**: se si prevede di usare una rete virtuale esistente, assicurarsi che sia una rete virtuale a livello di area.

  * Con Servizi di dominio Azure AD non è possibile usare reti virtuali che usano il meccanismo dei gruppi di affinità legacy.
  * Per usare Azure AD Domain Services, [eseguire la migrazione delle reti virtuali legacy alle reti virtuali a livello di area](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

### <a name="azure-region-for-the-virtual-network"></a>Area di Azure per la rete virtuale
* Il dominio gestito di Azure AD Domain Services viene distribuito nella stessa area di Azure della rete virtuale in cui si è scelto di abilitare il servizio.
* Selezionare una rete virtuale in un'area di Azure supportata da Azure AD Domain Services.
* Per informazioni sulle aree di Azure in cui è disponibile Azure AD Domain Services, vedere la pagina relativa ai [servizi di Azure per area](https://azure.microsoft.com/regions/#services/) .

### <a name="requirements-for-the-virtual-network"></a>Requisiti per la rete virtuale
* **Prossimità ai carichi di lavoro di Azure**: selezionare la rete virtuale che ospita o ospiterà le macchine virtuali che richiedono l'accesso ad Azure AD Domain Services. È anche possibile scegliere di connettere le reti virtuali se i carichi di lavoro vengono distribuiti in una rete virtuale diversa dal dominio gestito.
* **Server DNS personalizzati/Bring-Your-Own**: assicurarsi che non siano presenti server DNS personalizzati configurati per la rete virtuale. Un esempio di server DNS personalizzato è un'istanza di DNS di Windows Server in esecuzione su una VM Windows Server distribuita nella rete virtuale. Azure AD Domain Services non si integra con i server DNS personalizzati distribuiti nella rete virtuale.
* **Domini esistenti con lo stesso nome di dominio**: assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Provare successivamente ad abilitare un dominio gestito di Azure AD Domain Services con lo stesso nome di dominio, ovvero 'contoso.com', alla rete virtuale. Si verifica un errore quando si prova ad abilitare Azure AD Domain Services. L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

> [!WARNING]
> Non è possibile spostare Domain Services in una rete virtuale diversa dopo l'abilitazione del servizio.
>
>

## <a name="network-security-groups-and-subnet-design"></a>Gruppi di sicurezza di rete e struttura della subnet
Un [gruppo di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md) contiene un elenco di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete alle istanze di VM in una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole ACL si applicano a tutte le istanze di VM in tale subnet. Il traffico verso una singola VM può essere inoltre ulteriormente limitato associando un gruppo di sicurezza di rete direttamente a tale VM.

![Struttura consigliata per la subnet](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="best-practices-for-choosing-a-subnet"></a>Procedure consigliate per la scelta di una subnet
* Distribuire Azure Active Directory Domain Services in una **subnet separata dedicata** nella rete virtuale di Azure.
* Non applicare alcun gruppo di sicurezza di rete alla subnet dedicata per il dominio gestito. Se è necessario applicare gruppi di sicurezza di rete alla subnet dedicata, verificare di **non bloccare le porte necessarie per la manutenzione e la gestione del dominio**.
* Non limitare in modo eccessivo il numero di indirizzi IP disponibili nella subnet dedicata per il dominio gestito. Un'eccessiva limitazione impedisce al servizio di rendere disponibili due controller di dominio per il dominio gestito.
* **Non abilitare Azure AD Domain Services nella subnet del gateway** della rete virtuale.

> [!WARNING]
> Quando si associa un gruppo di sicurezza di rete a una subnet in cui è abilitata la funzionalità Azure AD Domain Services, è possibile che si interferisca con la possibilità di manutenzione e gestione del dominio da parte di Microsoft. Viene inoltre ostacolata la sincronizzazione tra il tenant Azure AD e il dominio gestito. **Il Contratto di servizio non si applica alle distribuzioni in cui è stato creato un gruppo di sicurezza di rete che impedisce ad Azure Active Directory Domain Services di aggiornare e gestire il dominio.**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Porte necessarie per Azure Active Directory Domain Services
Azure Active Directory Domain Services richiede le porte seguenti per la manutenzione e la gestione del dominio. Verificare che queste porte non siano bloccate per la subnet in cui è stato abilitato il dominio gestito.

| Numero della porta | Scopo |
| --- | --- |
| 443 |Sincronizzazione con il tenant di Azure AD |
| 3389 |Gestione del dominio |
| 5986 |Gestione del dominio |
| 636 |Accesso LDAP sicuro (LDAPS) per il dominio gestito |

La porta 5986 viene usata per eseguire attività di gestione con la comunicazione remota di PowerShell nel dominio gestito. I controller di dominio del dominio gestito non sono in genere in ascolto su questa porta. Il servizio apre questa porta sui controller di dominio gestiti solo quando per il dominio gestito deve essere eseguita un'operazione di gestione o di manutenzione. Non appena completata l'operazione, il servizio arresta la porta sui controller di dominio gestiti.

La porta 3389 viene usata per le connessioni desktop remoto per il dominio gestito. Anche questa porta resta per la maggior parte del tempo disattivata nel dominio gestito. Il servizio attiva la porta solo se è necessario connettersi al dominio gestito per la risoluzione di problemi, generalmente avviata in risposta a una richiesta di servizio avviata dall'utente. Questo meccanismo non viene usato regolarmente poiché le attività di gestione e monitoraggio vengono eseguite usando la comunicazione remota di PowerShell. Questa porta viene usata solo nel raro caso che sia necessario connettersi in remoto al dominio gestito per la risoluzione dei problemi avanzata. La porta viene chiusa non appena l'operazione di risoluzione dei problemi viene completata.


### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Gruppo di sicurezza di rete (NSG) di esempio per le reti virtuali con Azure AD Domain Services
La tabella seguente illustra un gruppo di sicurezza di rete di esempio che è possibile configurare per una rete virtuale con un dominio gestito di Azure AD Domain Services. Questa regola consente il traffico sulle porte specificate per garantire che il dominio gestito rimanga aggiornato, che vengano applicate le patch e possa essere monitorato da Microsoft. La regola predefinita "DenyAll" si applica a tutto il traffico in ingresso da Internet.

Il gruppo di sicurezza di rete mostra anche come bloccare l'accesso LDAP sicuro tramite Internet. Ignorare questa regola se l'accesso LDAP sicuro al dominio gestito tramite Internet non è stato abilitato. Il gruppo di sicurezza di rete contiene alcune regole che consentono l'accesso LDAPS in ingresso sulla porta TCP 636 solo da un set specificato di indirizzi IP. La regola del gruppo di sicurezza di rete per consentire l'accesso LDAPS su Internet da indirizzi IP specificati ha una priorità superiore rispetto alla regola DenyAll del gruppo di sicurezza di rete.

![Gruppo di sicurezza di rete di esempio per proteggere l'accesso LDAPS su Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Altre informazioni** - [Creare un gruppo di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Connettività di rete
Un dominio gestito di Azure AD Domain Services può essere abilitato solo in una singola rete virtuale di Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scenari per la connessione di reti di Azure
È possibile connettere le reti virtuali di Azure per l'uso del dominio gestito negli scenari di distribuzione seguenti:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Usare il dominio gestito in più di una rete virtuale di Azure
È possibile connettere altre reti virtuali di Azure alla rete virtuale di Azure in cui è stata abilitata la funzionalità Azure AD Domain Services. Questa connessione VPN/peering reti virtuali consente di usare il dominio gestito con i carichi di lavoro distribuiti in altre reti virtuali.

![Connettività di rete virtuale classica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Usare il dominio gestito in una rete virtuale basata su Resource Manager
È possibile connettere una rete virtuale basata su Resource Manager alla rete virtuale classica in cui è abilitata la funzionalità Azure AD Domain Services. Questa connessione consente di usare il dominio gestito con i carichi di lavoro distribuiti nella rete virtuale basata su Resource Manager.

![Connettività da rete virtuale basata su Resource Manager a rete virtuale classica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opzioni per le connessioni di rete
* **Connessioni da rete virtuale a rete virtuale tramite il peering reti virtuali**: il peering reti virtuali è un meccanismo che connette due reti virtuali nella stessa area tramite la rete backbone di Azure. Una volta eseguito il peering, le due reti virtuali appaiono come una sola per qualsiasi scopo di connettività. Continuano a essere gestite come risorse separate, ma le macchine virtuali in queste reti virtuali possono comunicare direttamente tra di esse usando gli indirizzi IP privati.

    ![Connettività di rete virtuale tramite peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Altre informazioni: Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md)
    
* **Connessioni da rete virtuale a rete virtuale tramite connessioni VPN da sito a sito**: la connessione di una rete virtuale a un'altra rete virtuale è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE.

    ![Connettività di rete virtuale tramite gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Altre informazioni: connettere reti virtuali usando il gateway VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Contenuti correlati
* [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md)
* [Configurare una connessione da rete virtuale a rete virtuale per il modello di distribuzione classico](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
* [Creare un gruppo di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
