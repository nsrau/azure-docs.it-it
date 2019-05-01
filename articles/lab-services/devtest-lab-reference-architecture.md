---
title: Architettura di riferimento Enterprise per Azure DevTest Labs
description: Questo articolo fornisce indicazioni sull'architettura di riferimento per Azure DevTest Labs in un'organizzazione.
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
ms.openlocfilehash: 73a3d426e9040525b0c631db273e59c49a6a9eb0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705875"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architettura di riferimento di Azure DevTest Labs per le aziende
Questo articolo illustra l'architettura di riferimento per la distribuzione di una soluzione basata su Azure DevTest Labs in un'organizzazione. Include quanto segue:
- Connettività locale tramite Azure ExpressRoute
- Un gateway desktop remoto per accedere in remoto alle macchine virtuali
- Connettività a un repository di elementi per gli artefatti privati
- Altri servizi PaaS che vengono usati nei lab

![Diagramma dell'architettura di riferimento](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architettura
Questi sono gli elementi principali dell'architettura di riferimento:

- **Azure Active Directory (Azure AD)**: DevTest Labs Usa il [servizio di Azure AD per la gestione delle identità](../active-directory/fundamentals/active-directory-whatis.md). Quando si concede agli utenti l'accesso a un ambiente basato su DevTest Labs, tenere presente questi due aspetti principali:
    - **Gestione delle risorse**: Fornisce l'accesso al portale di Azure per gestire le risorse (creazione di macchine virtuali; creare ambienti; avviare, arrestare, riavviare, eliminare e applica artefatti; e così via). Gestione delle risorse viene eseguita in Azure usando il controllo di accesso basato sui ruoli (RBAC). Assegnare ruoli agli utenti e impostare le autorizzazioni a livello di accesso e risorse.
    - **Le macchine virtuali (a livello di rete)**: Nella configurazione predefinita, le macchine virtuali usare un account amministratore locale. Se è disponibile un dominio ([Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), un dominio locale o un dominio e basato sul cloud), le macchine possono essere aggiunte al dominio. Gli utenti possono quindi usare le proprie identità basati su dominio per la connessione alle macchine virtuali.
- **Connettività locale**: Nel nostro Diagramma architettura [ExpressRoute](../expressroute/expressroute-introduction.md) viene usato. Ma è anche possibile usare una [site-to-site VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Anche se ExpressRoute non è necessaria per DevTest Labs, viene usato comunemente nelle aziende. ExpressRoute è obbligatorio solo se è necessario l'accesso alle risorse aziendali. Scenari comuni sono:
    - Sono presenti dati in locale che non possono essere spostati nel cloud.
    - Si vuole aggiungere le macchine virtuali del lab al dominio locale.
    - Si vuole forzare tutto il traffico di rete da e verso l'ambiente cloud attraverso un firewall locale per la sicurezza o conformità.
- **Gruppi di sicurezza di rete**: Un modo comune per limitare il traffico all'ambiente cloud (o all'interno dell'ambiente cloud) basato sull'origine e gli indirizzi IP di destinazione consiste nell'usare un [gruppo di sicurezza di rete](../virtual-network/security-overview.md). Ad esempio, si vuole consentire solo il traffico proveniente dalla rete aziendale in reti dell'ambiente di test.
- **Gateway desktop remoto**: Le aziende in genere si bloccano le connessioni desktop remoto in uscita nel firewall aziendale. Sono disponibili diverse opzioni per abilitare la connettività all'ambiente basato su cloud in DevTest Labs, tra cui:
  - Usare un [gateway desktop remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e il bilanciamento del carico elenco elementi consentiti l'indirizzo IP statico del gateway.
  - [Indirizzare tutto il traffico RDP in ingresso](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) tramite la connessione VPN ExpressRoute/site-to-site. Questa funzionalità è un fattore comune quando le aziende piano una distribuzione con DevTest Labs.
- **Servizi (reti virtuali, subnet) di rete**: Il [la rete di Azure](../networking/networking-overview.md) topologia è un altro elemento chiave nell'architettura di DevTest Labs. Controlla se le risorse da lab possono comunicare e avere accesso a internet e in locale. Il diagramma dell'architettura include i modi più comuni che i clienti usano DevTest Labs: Tutti i laboratori si connettono tramite [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) con un [modello hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) alla connessione ExpressRoute/sito a sito VPN da sito locale. Ma la rete virtuale di Azure DevTest Labs Usa direttamente, in modo che non siano presenti restrizioni di come è configurato l'infrastruttura di rete.
- **DevTest Labs**:  DevTest Labs è un elemento fondamentale dell'architettura complessiva. Per altre informazioni sul servizio, vedere [su DevTest Labs](devtest-lab-overview.md).
- **Le macchine virtuali e altre risorse (SaaS, PaaS, IaaS)**:  Le macchine virtuali sono un carico di lavoro principale DevTest Labs supporta con altre risorse di Azure. DevTest Labs consente facile e veloce per un'azienda fornire l'accesso alle risorse di Azure (incluse macchine virtuali e altre risorse di Azure). Altre informazioni sull'accesso ad Azure per [sviluppatori](devtest-lab-developer-lab.md) e [tester](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità
Sebbene DevTest Labs non dispone di quote predefinite o i limiti, si dispongono di altre risorse di Azure che vengono usate nell'operazione tipica di un lab [le quote a livello di sottoscrizione](../azure-subscription-service-limits.md). Pertanto, in una distribuzione aziendale tipica, è necessario più sottoscrizioni di Azure per coprire una distribuzione di grandi dimensioni di DevTest Labs. Le quote di aziende di raggiungere più di frequente sono:

- **Gruppi di risorse**: Nella configurazione predefinita, DevTest Labs consente di creare un gruppo di risorse per ogni nuova macchina virtuale o l'utente crea un ambiente con il servizio. Le sottoscrizioni possono contenere [980 fino a gruppi di risorse](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Pertanto, questo è il limite di ambienti in una sottoscrizione e le macchine virtuali. Esistono due altre configurazioni che è opportuno considerare:
    - **[Tutte le macchine virtuali passare allo stesso gruppo di risorse](resource-group-control.md)**: Anche se questa configurazione consente di rispettare il limite del gruppo di risorse, riguarda il limite di risorse-tipo-per-resource-group.
    - **Utilizzare condivisi gli indirizzi IP pubblici**: Tutte le macchine virtuali della stessa dimensione e area passare nello stesso gruppo di risorse. Questa configurazione è "intermedie" tra le quote di gruppo di risorse e le quote di risorse-tipo-per-resource-group, se le macchine virtuali possono avere indirizzi IP pubblici.
- **Gruppo di risorse per ogni risorsa per ogni tipo di risorsa**: Il limite predefinito per [risorse per ogni gruppo di risorse per ogni tipo di risorsa è 800](../azure-subscription-service-limits.md#resource-group-limits).  Quando si usa la *tutte le macchine virtuali passare al gruppo di risorse stesso* configurazione, gli utenti hit questa sottoscrizione limitare molto presto, soprattutto se le macchine virtuali presenti molti dischi aggiuntivi.
- **Account di archiviazione**: Viene fornito un lab in DevTest Labs con un account di archiviazione. La quota di Azure per [numero di account di archiviazione per area per sottoscrizione è 250](../azure-subscription-service-limits.md#storage-limits). Inoltre, il numero massimo di DevTest Labs nella stessa area è 250.
- **Le assegnazioni di ruolo**: Un'assegnazione di ruolo è come si concede un accesso utente o entità a una risorsa (proprietario, resource, livello di autorizzazione). In Azure, è presente una [limite di 2.000 assegnazioni di ruolo per ogni sottoscrizione](../azure-subscription-service-limits.md#role-based-access-control-limits). Per impostazione predefinita, il servizio DevTest Labs consente di creare un gruppo di risorse per ogni macchina virtuale. Il proprietario viene concesso *proprietario* l'autorizzazione per la macchina virtuale di DevTest Labs e *lettore* dell'autorizzazione per il gruppo di risorse. In questo modo, ogni nuova macchina virtuale creata usa due assegnazioni di ruolo oltre le assegnazioni vengono usate quando si concede agli utenti l'autorizzazione per il lab.
- **Letture/scritture API**: Esistono vari metodi di automazione di Azure e DevTest Labs, incluse le API REST, PowerShell, CLI di Azure e Azure SDK. Tramite l'automazione, si potrebbe raggiunge un altro limite di richieste dell'API: Ogni sottoscrizione supporta fino a [12.000 leggere le richieste e 1.200 richieste all'ora di scrittura](../azure-resource-manager/resource-manager-request-limits.md). Tenere presente questo limite quando si automatizza DevTest Labs.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità
DevTest Labs ha un'interfaccia utente con privilegi amministrativi ideale per l'uso di un singolo lab. Ma in un'organizzazione, hai probabilmente più sottoscrizioni di Azure e di molti laboratori. Apportare modifiche in modo coerente per tutti i propri lab richiede la creazione di script/automazione. Ecco alcuni esempi e procedure consigliate di gestione per una distribuzione con DevTest Labs:

- **Le modifiche alle impostazioni di lab**: Uno scenario comune consiste nell'aggiornare un'impostazione di lab specifici tra tutti i lab nella distribuzione. Ad esempio, è disponibile una nuova dimensione di istanza di macchina virtuale e tutti i lab devono essere aggiornati per consentirne il. È consigliabile automatizzare queste modifiche utilizzando gli script di PowerShell, CLI o le API REST.  
- **Token di accesso personale di artefatto del repository**:  In genere, i token di accesso personale per un repository Git scadano entro 90 giorni, un anno o due anni. Per garantire continuità aziendale, è importante estendere il token di accesso personale o crearne uno nuovo. Quindi usare automazione per applicare il nuovo token di accesso personale per tutti i lab.
- **Limitare le modifiche a un ambiente di prova**: Spesso, una particolare impostazione deve essere limitata (ad esempio consentendo l'utilizzo di immagini del marketplace). È possibile usare criteri di Azure per impedire modifiche ad un tipo di risorsa. Oppure è possibile creare un ruolo personalizzato e concedere agli utenti di tale ruolo anziché il *proprietario* ruolo per l'ambiente lab. È possibile eseguire questa operazione per la maggior parte delle impostazioni nell'ambiente di laboratorio (supporto interno, annuncio lab, consentite dimensioni delle macchine Virtuali e così via).
- **Richiedere macchine virtuali da seguire una convenzione di denominazione**: I responsabili, in genere è utile identificare facilmente le macchine virtuali che fanno parte di un ambiente di testing e sviluppo basato sul cloud. È possibile farlo usando [criteri di Azure](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

È importante notare che DevTest Labs Usa risorse di Azure sottostante che sono gestite allo stesso modo: rete, dischi, calcolo e così via. Ad esempio, criteri di Azure si applica alle macchine virtuali che vengono create in un lab. Centro sicurezza di Azure possono segnalare la conformità della macchina virtuale. E il servizio Backup di Azure offre backup regolari per le macchine virtuali nel lab.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Azure DevTest Labs Usa le risorse esistenti in Azure (calcolo, rete e così via). Quindi, automaticamente trae vantaggio dalla funzionalità di sicurezza che sono integrate nella piattaforma. Ad esempio, per richiedere le connessioni desktop remoto in ingresso provenienti solo dalla rete aziendale, è sufficiente aggiungere un gruppo di sicurezza di rete nella rete virtuale nel gateway desktop remoto. Le considerazioni sulla protezione solo altri è il livello di autorizzazioni concesse ai membri del team che utilizzano i laboratori su base giornaliera. Le autorizzazioni più comuni sono [ *proprietario* e *utente*](devtest-lab-add-devtest-user.md). Per altre informazioni su questi ruoli, vedere [aggiungere proprietari e utenti in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo successivo di questa serie: [Aumentare le prestazioni dell'infrastruttura di Azure DevTest Labs](devtest-lab-guidance-scale.md).
