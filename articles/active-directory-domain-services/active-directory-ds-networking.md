---
title: 'Azure AD Domain Services: linee guida sulla rete | Documentazione Microsoft'
description: Considerazioni sulla rete per Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: maheshu
ms.openlocfilehash: 537643f582f6cc3328bd1c098de03c4f6e07c113
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerazioni sulla rete per Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Come selezionare una rete virtuale di Azure
Le indicazioni seguenti semplificano la selezione di una rete virtuale da usare con Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Tipo di rete virtuale di Azure
* **Reti virtuali di gestione risorse**: la funzionalità Azure AD Domain Services può essere abilitata nelle reti virtuali create usando Azure Resource Manager.
* Non è possibile abilitare Azure AD Domain Services in una rete virtuale di Azure classica.
* È possibile connettere altre reti virtuali alla rete virtuale in cui è abilitata la funzionalità Azure AD Domain Services. Per altre informazioni, vedere la sezione [Connettività di rete](active-directory-ds-networking.md#network-connectivity).

### <a name="azure-region-for-the-virtual-network"></a>Area di Azure per la rete virtuale
* Il dominio gestito di Azure AD Domain Services viene distribuito nella stessa area di Azure della rete virtuale in cui si è scelto di abilitare il servizio.
* Selezionare una rete virtuale in un'area di Azure supportata da Azure AD Domain Services.
* Per informazioni sulle aree di Azure in cui è disponibile Azure AD Domain Services, vedere la pagina relativa ai [servizi di Azure per area](https://azure.microsoft.com/regions/#services/) .

### <a name="requirements-for-the-virtual-network"></a>Requisiti per la rete virtuale
* **Prossimità ai carichi di lavoro di Azure**: selezionare la rete virtuale che ospita o ospiterà le macchine virtuali che richiedono l'accesso ad Azure AD Domain Services. Se i carichi di lavoro vengono distribuiti in una rete virtuale diversa dal dominio gestito, è anche possibile scegliere di connettere le reti virtuali.
* **Server DNS personalizzati/Bring-Your-Own**: assicurarsi che non siano presenti server DNS personalizzati configurati per la rete virtuale. Un esempio di server DNS personalizzato è un'istanza di DNS di Windows Server in esecuzione su una VM Windows Server distribuita nella rete virtuale. Azure AD Domain Services non si integra con i server DNS personalizzati distribuiti nella rete virtuale.
* **Domini esistenti con lo stesso nome di dominio**: assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Provare successivamente ad abilitare un dominio gestito di Azure AD Domain Services con lo stesso nome di dominio, ovvero 'contoso.com', alla rete virtuale. Si verifica un errore quando si prova ad abilitare Azure AD Domain Services. L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

> [!WARNING]
> Non è possibile spostare Domain Services in una rete virtuale diversa dopo l'abilitazione del servizio.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Linee guida per la scelta di una subnet

![Struttura consigliata per la subnet](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Distribuire Azure Active Directory Domain Services in una **subnet separata dedicata** nella rete virtuale di Azure.
* Non applicare alcun gruppo di sicurezza di rete alla subnet dedicata per il dominio gestito. Se è necessario applicare gruppi di sicurezza di rete alla subnet dedicata, verificare di **non bloccare le porte necessarie per la manutenzione e la gestione del dominio**.
* Non limitare in modo eccessivo il numero di indirizzi IP disponibili nella subnet dedicata per il dominio gestito. Un'eccessiva limitazione impedisce al servizio di rendere disponibili due controller di dominio per il dominio gestito.
* **Non abilitare Azure AD Domain Services nella subnet del gateway** della rete virtuale.

> [!WARNING]
> Quando si associa un gruppo di sicurezza di rete a una subnet in cui è abilitata la funzionalità Azure AD Domain Services, è possibile che si interferisca con la possibilità di manutenzione e gestione del dominio da parte di Microsoft. Viene inoltre ostacolata la sincronizzazione tra il tenant Azure AD e il dominio gestito. **Il Contratto di servizio non si applica alle distribuzioni in cui è stato creato un gruppo di sicurezza di rete che impedisce ad Azure Active Directory Domain Services di aggiornare e gestire il dominio.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Porte necessarie per Azure Active Directory Domain Services
Azure Active Directory Domain Services richiede le porte seguenti per la manutenzione e la gestione del dominio. Verificare che queste porte non siano bloccate per la subnet in cui è stato abilitato il dominio gestito.

| Numero della porta | Obbligatorio? | Scopo |
| --- | --- | --- |
| 443 | Mandatory |Sincronizzazione con il tenant di Azure AD |
| 5986 | Mandatory | Gestione del dominio |
| 3389 | Facoltativo | Gestione del dominio |
| 636 | Facoltativo | Accesso LDAP sicuro (LDAPS) per il dominio gestito |

**Porta 443 (sincronizzazione con Azure AD)**
* Usata per sincronizzare la directory di Azure AD con il dominio gestito.
* È obbligatorio consentire l'accesso a questa porta nel gruppo di sicurezza di rete. Senza l'accesso a questa porta, il dominio gestito non è sincronizzato con la directory di Azure AD. Gli utenti potrebbero non essere in grado di accedere, perché le modifiche alle password non vengono sincronizzate con il dominio gestito.
* È possibile limitare l'accesso in ingresso a questa porta agli indirizzi IP appartenenti all'intervallo di indirizzi IP di Azure.

**Porta 5986 (comunicazione remota di PowerShell)** 
* Usata per eseguire attività di gestione con la comunicazione remota di PowerShell nel dominio gestito.
* È obbligatorio consentire l'accesso attraverso questa porta nel gruppo di sicurezza di rete. Senza accesso a questa porta, il dominio gestito non può essere aggiornato né configurato e non è possibile eseguirne il monitoraggio o il backup.
* È possibile limitare l'accesso in ingresso a questa porta agli indirizzi IP di origine seguenti: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 
* I controller di dominio del dominio gestito non sono in genere in ascolto su questa porta. Il servizio apre questa porta sui controller di dominio gestiti solo quando per il dominio gestito deve essere eseguita un'operazione di gestione o di manutenzione. Non appena completata l'operazione, il servizio arresta la porta sui controller di dominio gestiti.

**Porta 3389 (Desktop remoto)** 
* Usata per le connessioni Desktop remoto ai controller di dominio del dominio gestito. 
* L'apertura di questa porta tramite il gruppo di sicurezza di rete è facoltativa. 
* Anche questa porta resta per la maggior parte del tempo disattivata nel dominio gestito. Questo meccanismo non viene usato regolarmente poiché le attività di gestione e monitoraggio vengono eseguite usando la comunicazione remota di PowerShell. Questa porta viene usata solo nel raro caso in cui per Microsoft sia necessario connettersi in remoto al dominio gestito dell'utente per una risoluzione dei problemi avanzata. La porta viene chiusa non appena l'operazione di risoluzione dei problemi viene completata.

**Porta 636 (LDAP sicuro)**
* Usata per abilitare o disabilitare l'accesso LDAP sicuro al dominio gestito via Internet.
* L'apertura di questa porta tramite il gruppo di sicurezza di rete è facoltativa. Aprire la porta solo se l'accesso LDAP sicuro via Internet è abilitato.
* È possibile limitare l'accesso in ingresso a questa porta agli indirizzi IP di origine da cui si prevede di connettersi tramite LDAP sicuro.


## <a name="network-security-groups"></a>Gruppi di sicurezza di rete
Un [gruppo di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md) contiene un elenco di regole dell'elenco di controllo di accesso (ACL) che consentono o rifiutano il traffico di rete alle istanze di VM in una rete virtuale. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole ACL si applicano a tutte le istanze di VM in tale subnet. Il traffico verso una singola VM può essere inoltre ulteriormente limitato associando un gruppo di sicurezza di rete direttamente a tale VM.

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
