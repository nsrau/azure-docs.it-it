---
title: Architettura di riferimento aziendale per Azure DevTest Labs
description: Questo articolo fornisce indicazioni sull'architettura di riferimento per I laboratori di Azure DevTest in un'azienda.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132843"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architettura di riferimento di Azure DevTest Labs per le aziendeAzure DevTest Labs reference architecture for enterprises
Questo articolo fornisce un'architettura di riferimento che consente di distribuire una soluzione basata su Azure DevTest Labs in un'azienda. Include quanto segue:
- Connettività locale tramite Azure ExpressRoute
- Un gateway desktop remoto per accedere in remoto alle macchine virtuali
- Connettività a un archivio di elementi per gli elementi privati
- Altri servizi PaaS utilizzati nei laboratori

![Diagramma dell'architettura di riferimento](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architecture
Questi sono gli elementi chiave dell'architettura di riferimento:

- **Azure Active Directory (Azure AD):** DevTest Labs usa il servizio Azure AD per la [gestione delle identità.](../active-directory/fundamentals/active-directory-whatis.md) Considerare questi due aspetti chiave quando si concede agli utenti l'accesso a un ambiente basato su DevTest Labs:Consider these two key aspects when you give users access to an environment based on DevTest Labs:
    - **Gestione delle risorse:** fornisce l'accesso al portale di Azure per gestire le risorse (creare macchine virtuali; creare ambienti; avviare, arrestare, riavviare, eliminare e applicare elementi e così via). La gestione delle risorse viene eseguita in Azure usando il controllo degli accessi in base al ruolo. Assegnare ruoli agli utenti e impostare le autorizzazioni a livello di risorse e di accesso.
    - **Macchine virtuali (a livello di rete):** nella configurazione predefinita, le macchine virtuali usano un account amministratore locale. Se è disponibile un dominio ( Servizi di[dominio Azure AD](../active-directory-domain-services/overview.md), un dominio locale o un dominio basato su cloud), i computer possono essere aggiunti al dominio. Gli utenti possono quindi usare le identità basate su dominio per connettersi alle macchine virtuali.
- **Connettività locale:** nel diagramma dell'architettura viene usato ExpressRoute.On-premises connectivity : In our architecture diagram, [ExpressRoute](../expressroute/expressroute-introduction.md) is used. Ma è anche possibile utilizzare una [VPN da sito a sito](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Anche se ExpressRoute non è necessario per DevTest Labs, viene comunemente usato nelle aziende. ExpressRoute è necessario solo se è necessario accedere alle risorse aziendali. Gli scenari comuni sono:
    - Si dispone di dati locali che non possono essere spostati nel cloud.
    - Si preferisce aggiungere le macchine virtuali del lab al dominio locale.
    - Si desidera forzare tutto il traffico di rete in avanti e in uscita dall'ambiente cloud tramite un firewall locale per la sicurezza/conformità.
- **Gruppi**di sicurezza di rete : Un modo comune per limitare il traffico all'ambiente cloud (o all'interno dell'ambiente cloud) in base agli indirizzi IP di origine e di destinazione consiste nell'utilizzare un gruppo di sicurezza di [rete.](../virtual-network/security-overview.md) Ad esempio, si desidera consentire solo il traffico proveniente dalla rete aziendale nelle reti del lab.
- **Gateway desktop remoto:** le aziende in genere bloccano le connessioni desktop remoto in uscita nel firewall aziendale. Sono disponibili diverse opzioni per abilitare la connettività all'ambiente basato su cloud in DevTest Labs, tra cui:There are several options to enable connectivity to the cloud-based environment in DevTest Labs, including:
  - Utilizzare un [gateway di desktop remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e consentire l'indirizzo IP statico del servizio di bilanciamento del carico del gateway.
  - [Indirizzare tutto](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) il traffico RDP in ingresso tramite la connessione VPN ExpressRoute/da sito a sito. Questa funzionalità è una considerazione comune quando le aziende pianificano una distribuzione di DevTest Labs.This functionality is a common consideration when enterprises plan a DevTest Labs deployment.
- Servizi di rete (reti virtuali, subnet): la topologia di rete di Azure è un altro elemento chiave nell'architettura DevTest **Labs.Network services (virtual networks, subnets)**: The [Azure networking](../networking/networking-overview.md) topology is another key element in the DevTest Labs architecture. Controlla se le risorse del lab possono comunicare e hanno accesso a Internet e in locale. Il diagramma dell'architettura include i modi più comuni in cui i clienti usano DevTest Labs: tutti i laboratori si connettono tramite [peering](../virtual-network/virtual-network-peering-overview.md) di rete virtuale usando un [modello hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per la connessione VPN ExpressRoute/sito-sito a locale. Ma DevTest Labs usa direttamente Rete virtuale di Azure, quindi non ci sono restrizioni sulla configurazione dell'infrastruttura di rete.
- **DevTest Labs**: DevTest Labs è una parte fondamentale dell'architettura complessiva. Per ulteriori informazioni sul servizio, vedere [Informazioni su DevTest Labs](devtest-lab-overview.md).
- **Macchine virtuali e altre risorse (SaaS, PaaS, IaaS):** le macchine virtuali sono un carico di lavoro chiave supportato da DevTest Labs insieme ad altre risorse di Azure.Virtual machines and other resources (SaaS, PaaS, IaaS) : Virtual machines are a key workload that DevTest Labs supports with other Azure resources. DevTest Labs consente a un'azienda di fornire l'accesso alle risorse di Azure in modo semplice e veloce. Altre informazioni sull'accesso ad Azure per [sviluppatori](devtest-lab-developer-lab.md) e [tester](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità
Anche se DevTest Labs non dispone di quote o limiti predefiniti, le altre risorse di Azure usate nell'operazione tipica di un lab dispongono di quote a livello di [sottoscrizione.](../azure-resource-manager/management/azure-subscription-service-limits.md) Pertanto, in una distribuzione aziendale tipica, sono necessarie più sottoscrizioni di Azure per coprire una distribuzione di grandi dimensioni di DevTest Labs.So, in a typical enterprise deployment, you need multiple Azure subscriptions to cover a large deployment of DevTest Labs. Le quote che le imprese raggiungono più comunemente sono:

- Gruppi di **risorse:** nella configurazione predefinita DevTest Labs crea un gruppo di risorse per ogni nuova macchina virtuale oppure l'utente crea un ambiente usando il servizio. Le sottoscrizioni possono contenere [fino a 980 gruppi di risorse.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Questo è il limite di macchine virtuali e ambienti in una sottoscrizione. Esistono altre due configurazioni da considerare:There are two other configurations that you should consider:
    - **[Tutte le macchine virtuali passano allo stesso gruppo](resource-group-control.md)** di risorse: sebbene questa configurazione consenta di soddisfare il limite del gruppo di risorse, influisce sul limite del tipo di risorsa per ogni gruppo di risorse.
    - **Uso di indirizzi IP pubblici condivisi:** tutte le macchine virtuali della stessa dimensione e area vengono inserite nello stesso gruppo di risorse. Questa configurazione è un "terreno intermedio" tra le quote dei gruppi di risorse e le quote del tipo di risorsa per ogni gruppo di risorse, se alle macchine virtuali è consentito avere indirizzi IP pubblici.
- **Risorse per gruppo di risorse per tipo di risorsa:** il limite predefinito per le risorse per gruppo di [risorse per tipo di risorsa è 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Quando si usano tutte le macchine virtuali passare alla stessa configurazione del gruppo di *risorse,* gli utenti limitano questo limite di sottoscrizione molto prima, soprattutto se le macchine virtuali hanno molti dischi aggiuntivi.
- **Account di archiviazione:** un lab in DevTest Labs viene fornito con un account di archiviazione. La quota di Azure per il numero di account di archiviazione per area per sottoscrizione è 250.The Azure quota for number [of storage accounts per region per subscription is 250.](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) Anche il numero massimo di DevTest Labs nella stessa area è 250.The maximum number of DevTest Labs in the same region is also 250.
- **Assegnazioni**di ruolo: un'assegnazione di ruolo consente di concedere a un utente o a un'entità l'accesso a una risorsa (proprietario, risorsa, livello di autorizzazione). In Azure è previsto un [limite di 2.000 assegnazioni di ruolo per sottoscrizione.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) Per impostazione predefinita, il servizio DevTest Labs crea un gruppo di risorse per ogni macchina virtuale. Al proprietario viene concessa l'autorizzazione *di proprietario* per la macchina virtuale DevTest Labs e l'autorizzazione di *lettura* per il gruppo di risorse. In questo modo, ogni nuova macchina virtuale creata usa due assegnazioni di ruolo oltre alle assegnazioni usate quando si concede agli utenti l'autorizzazione per il lab.
- **Letture/scritture API:** esistono vari modi per automatizzare Azure e DevTest Labs, tra cui API REST, PowerShell, l'interfaccia della riga di comando di Azure e Azure SDK. Tramite l'automazione, è possibile raggiungere un altro limite per le richieste API: ogni sottoscrizione consente fino a [12.000 richieste di lettura e 1.200 richieste di scrittura all'ora.](../azure-resource-manager/management/request-limits-and-throttling.md) Tenere presente questo limite quando si automatizza DevTest Labs.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità
DevTest Labs dispone di un'ottima interfaccia utente amministrativa per l'utilizzo di un singolo lab. Ma in un'azienda, è probabile che si disponga di più sottoscrizioni di Azure e molti lab. Apportare modifiche in modo coerente a tutti i laboratori richiede script/automazione. Di seguito sono riportati alcuni esempi e procedure consigliate per la gestione di una distribuzione di DevTest Labs:Here are some examples and best management practices for a DevTest Labs deployment:

- **Modifiche alle impostazioni lab:** uno scenario comune consiste nell'aggiornare un'impostazione lab specifica in tutti i lab della distribuzione. Ad esempio, è disponibile una nuova dimensione di istanza della macchina virtuale e tutti i lab devono essere aggiornati per consentirlo. È consigliabile automatizzare queste modifiche usando script di PowerShell, l'interfaccia della riga di comando o le API REST.  
- Token di **accesso personale del repository degli artefatti:** in genere, i token di accesso personale per un repository Git scadono tra 90 giorni, un anno o due anni. Per garantire la continuità, è importante estendere il token di accesso personale o crearne uno nuovo. Utilizzare quindi l'automazione per applicare il nuovo token di accesso personale a tutti i lab.
- **Limitare le modifiche a un'impostazione di laboratorio**: Spesso, una particolare impostazione deve essere limitata (ad esempio, consentire l'uso di immagini del marketplace). È possibile usare Criteri di Azure per impedire modifiche a un tipo di risorsa. In alternativa, è possibile creare un ruolo personalizzato e concedere agli utenti tale ruolo anziché il ruolo di *proprietario* per il lab. È possibile eseguire questa operazione per la maggior parte delle impostazioni nel lab (supporto interno, annuncio lab, dimensioni delle macchine virtuali consentite e così via).
- Richiedere alle macchine virtuali di seguire una convenzione di **denominazione:** i responsabili in genere desiderano identificare facilmente le macchine virtuali che fanno parte di un ambiente di sviluppo e test basato su cloud. A tale scopo, è possibile usare Criteri di Azure.You can do this by using [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

È importante notare che DevTest Labs usa le risorse di Azure sottostanti gestite allo stesso modo: rete, dischi, calcolo e così via. Ad esempio, Criteri di Azure si applica alle macchine virtuali create in un lab. Il Centro sicurezza di Azure può creare report sulla conformità delle macchine virtuali. Il servizio Backup di Azure può inoltre fornire backup regolari per le macchine virtuali nel lab.

## <a name="security-considerations"></a>Considerazioni sulla sicurezza
Azure DevTest Labs usa le risorse esistenti in Azure (calcolo, rete e così via). Così beneficia automaticamente delle funzionalità di sicurezza che sono incorporati nella piattaforma. Ad esempio, per richiedere che le connessioni desktop remoto in ingresso abbiano origine solo dalla rete aziendale, è sufficiente aggiungere un gruppo di sicurezza di rete alla rete virtuale nel gateway desktop remoto. L'unica considerazione di sicurezza aggiuntiva è il livello di autorizzazioni concesse ai membri del team che utilizzano i lab quotidianamente. Le autorizzazioni più comuni sono [ *proprietario* e *utente*](devtest-lab-add-devtest-user.md). Per altre informazioni su questi ruoli, vedere Aggiungere proprietari e utenti in Lab di Azure DevTest.For more information about these roles, see [Add owners and users in Azure DevTest Labs.](devtest-lab-add-devtest-user.md)

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo successivo di questa serie: Aumentare le [dimensioni dell'infrastruttura di Azure DevTest Labs.See](devtest-lab-guidance-scale.md)the next article in this series: Scale up your Azure DevTest Labs infrastructure .
