---
title: Architettura di riferimento aziendale per Azure DevTest Labs
description: Questo articolo fornisce indicazioni sull'architettura di riferimento per Azure DevTest Labs in un'organizzazione.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e0791fb6c4de3da8108ffbd296c681f993c6b6cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367751"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architettura di riferimento Azure DevTest Labs per le aziende
Questo articolo fornisce un'architettura di riferimento per facilitare la distribuzione di una soluzione basata su Azure DevTest Labs in un'organizzazione. Include quanto segue:
- Connettività locale tramite Azure ExpressRoute
- Un Gateway Desktop remoto per accedere in remoto alle macchine virtuali
- Connettività a un repository di artefatti per gli artefatti privati
- Altri servizi PaaS usati nei Lab

![Diagramma dell'architettura di riferimento](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architettura
Questi sono gli elementi chiave dell'architettura di riferimento:

- **Azure Active Directory (Azure ad)**: DevTest Labs usa il [servizio Azure ad per la gestione delle identità](../active-directory/fundamentals/active-directory-whatis.md). Considerare questi due aspetti chiave quando si concede agli utenti l'accesso a un ambiente basato su DevTest Labs:
    - **Gestione delle risorse**: consente di accedere al portale di Azure per gestire le risorse (creare macchine virtuali, creare ambienti, avviare, arrestare, riavviare, eliminare e applicare elementi e così via). La gestione delle risorse viene eseguita in Azure usando il controllo degli accessi in base al ruolo (RBAC). Assegnare i ruoli agli utenti e impostare le autorizzazioni a livello di risorsa e di accesso.
    - **Macchine virtuali (a livello di rete)**: nella configurazione predefinita, le macchine virtuali usano un account amministratore locale. Se è disponibile un dominio ([Azure ad Domain Services](../active-directory-domain-services/overview.md), un dominio locale o un dominio basato su cloud), i computer possono essere aggiunti al dominio. Gli utenti possono quindi usare le proprie identità basate su dominio per connettersi alle macchine virtuali.
- **Connettività locale**: nel diagramma dell'architettura viene usato [ExpressRoute](../expressroute/expressroute-introduction.md) . È anche possibile usare una [VPN da sito a sito](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Sebbene ExpressRoute non sia necessario per DevTest Labs, viene comunemente usato nelle aziende. ExpressRoute è necessario solo se è necessario l'accesso alle risorse aziendali. Scenari comuni:
    - Sono presenti dati locali che non possono essere spostati nel cloud.
    - Si preferisce aggiungere le macchine virtuali del Lab al dominio locale.
    - Si vuole forzare tutto il traffico di rete all'interno e all'esterno dell'ambiente cloud tramite un firewall locale per la sicurezza e la conformità.
- **Gruppi di sicurezza di rete**: un modo comune per limitare il traffico all'ambiente cloud (o all'interno dell'ambiente cloud) in base agli indirizzi IP di origine e di destinazione consiste nell'usare un [gruppo di sicurezza di rete](../virtual-network/security-overview.md). Ad esempio, si desidera consentire solo il traffico proveniente dalla rete aziendale nelle reti del Lab.
- **Gateway Desktop remoto**: le aziende in genere bloccano le connessioni Desktop remoto in uscita nel firewall aziendale. Sono disponibili diverse opzioni per abilitare la connettività all'ambiente basato su cloud in DevTest Labs, tra cui:
  - Usare un [Gateway Desktop remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e consentire l'indirizzo IP statico del servizio di bilanciamento del carico del gateway.
  - [Indirizzare tutto il traffico RDP in ingresso](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) attraverso la connessione VPN da sito a sito o ExpressRoute. Questa funzionalità è una considerazione comune quando le aziende pianificano una distribuzione di DevTest Labs.
- **Servizi di rete (reti virtuali, subnet)**: la topologia di [rete di Azure](../networking/networking-overview.md) è un altro elemento chiave dell'architettura di DevTest Labs. Controlla se le risorse del Lab possono comunicare e avere accesso a Internet e in locale. Il diagramma dell'architettura include i modi più comuni in cui i clienti usano DevTest Labs: tutti i Lab si connettono tramite il [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md) usando un [modello hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) alla connessione VPN da sito a sito ExpressRoute a locale. Tuttavia, DevTest Labs USA direttamente la rete virtuale di Azure, pertanto non esistono restrizioni per la configurazione dell'infrastruttura di rete.
- **DevTest Labs**: DevTest Labs è una parte fondamentale dell'architettura complessiva. Per altre informazioni sul servizio, vedere [informazioni su DevTest Labs](devtest-lab-overview.md).
- **Macchine virtuali e altre risorse (SaaS, PaaS, IaaS)**: le macchine virtuali sono un carico di lavoro fondamentale supportato da DevTest Labs insieme ad altre risorse di Azure. DevTest Labs rende più semplice e veloce per un'azienda fornire l'accesso alle risorse di Azure (incluse le macchine virtuali e altre risorse di Azure). Scopri di più sull'accesso ad Azure per [sviluppatori](devtest-lab-developer-lab.md) e [tester](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità
Sebbene DevTest Labs non disponga di quote o limiti predefiniti, altre risorse di Azure usate nell'operazione tipica di un Lab hanno [quote a livello di sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md). In una tipica distribuzione aziendale sono quindi necessarie più sottoscrizioni di Azure per coprire un'ampia distribuzione di DevTest Labs. Le quote che le aziende raggiungono più di frequente sono:

- **Gruppi di risorse**: nella configurazione predefinita, DevTest Labs crea un gruppo di risorse per ogni nuova macchina virtuale oppure l'utente crea un ambiente tramite il servizio. Le sottoscrizioni possono contenere [fino a 980 gruppi di risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Questo è il limite di macchine virtuali e ambienti in una sottoscrizione. È necessario prendere in considerazione altre due configurazioni:
    - **[Tutte le macchine virtuali vengono indirizzate allo stesso gruppo di risorse](resource-group-control.md)**: anche se questa configurazione consente di soddisfare il limite del gruppo di risorse, influiscono sul limite di tipo risorsa per gruppo di risorse.
    - **Uso di indirizzi IP pubblici condivisi**: tutte le macchine virtuali della stessa dimensione e area vengono inserite nello stesso gruppo di risorse. Questa configurazione è un "terreno intermedio" tra le quote del gruppo di risorse e le quote di tipo risorsa per gruppo di risorse, se le macchine virtuali possono avere indirizzi IP pubblici.
- **Risorse per gruppo**di risorse per tipo di risorsa: il limite predefinito per [le risorse per ogni gruppo di risorse per ogni tipo di risorsa è 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Quando si usa *tutte le VM passare alla stessa configurazione del gruppo di risorse* , gli utenti hanno raggiunto questo limite di sottoscrizione molto prima, soprattutto se le macchine virtuali hanno molti dischi aggiuntivi.
- **Account di archiviazione**: un Lab in DevTest Labs viene associato a un account di archiviazione. La quota di Azure per il [numero di account di archiviazione per area per sottoscrizione è 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Anche il numero massimo di Lab DevTest nella stessa area è 250.
- **Assegnazioni di ruolo**: un'assegnazione di ruolo è il modo in cui si concede a un utente o a un'entità l'accesso a una risorsa (proprietario, risorsa, livello di autorizzazione). In Azure esiste un [limite di 2.000 assegnazioni di ruolo per ogni sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits). Per impostazione predefinita, il servizio DevTest Labs crea un gruppo di risorse per ogni macchina virtuale. Al proprietario è concessa l'autorizzazione *owner* per la macchina virtuale DevTest Labs e l'autorizzazione *Reader* per il gruppo di risorse. In questo modo, ogni nuova VM creata usa due assegnazioni di ruolo oltre alle assegnazioni usate quando si concede agli utenti l'autorizzazione per il Lab.
- **Letture/scritture API**: sono disponibili diversi modi per automatizzare Azure e DevTest Labs, incluse le API REST, PowerShell, l'interfaccia della riga di comando di Azure e Azure SDK. Tramite l'automazione è possibile che si sia raggiunto un altro limite per le richieste API: ogni sottoscrizione consente fino a [12.000 richieste di lettura e 1.200 richieste di scrittura all'ora](../azure-resource-manager/management/request-limits-and-throttling.md). Tenere presente questo limite quando si automatizzano DevTest Labs.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità
DevTest Labs offre un'ottima interfaccia utente amministrativa per l'uso di un singolo Lab. Tuttavia, in un'azienda, probabilmente si hanno più sottoscrizioni di Azure e molti Lab. Per apportarvi modifiche coerenti a tutti i Lab è necessario creare script/automazione. Di seguito sono riportati alcuni esempi e procedure consigliate per la gestione di una distribuzione di DevTest Labs:

- **Modifiche alle impostazioni Lab**: uno scenario comune consiste nell'aggiornare un'impostazione lab specifica in tutti i Lab della distribuzione. Ad esempio, è disponibile una nuova dimensione di istanza di macchina virtuale e tutti i lab devono essere aggiornati per consentirne l'uso. È consigliabile automatizzare queste modifiche usando gli script di PowerShell, l'interfaccia della riga di comando o le API REST.  
- **Token di accesso personale del repository di artefatti**: in genere, i token di accesso personali per un repository git scadono tra 90 giorni, un anno o due anni. Per garantire la continuità, è importante estendere il token di accesso personale o crearne uno nuovo. Usare quindi l'automazione per applicare il nuovo token di accesso personale a tutti i Lab.
- **Limitare le modifiche a un'impostazione del Lab**: spesso è necessario limitare un'impostazione specifica (ad esempio, consentire l'uso di immagini del Marketplace). È possibile usare i criteri di Azure per impedire modifiche a un tipo di risorsa. In alternativa, è possibile creare un ruolo personalizzato e concedere agli utenti il ruolo anziché il ruolo di *proprietario* per il Lab. Questa operazione può essere eseguita per la maggior parte delle impostazioni nel Lab (supporto interno, annuncio Lab, dimensioni di macchine virtuali consentite e così via).
- **Richiedi alle VM di seguire una convenzione di denominazione**: i manager vogliono in genere identificare facilmente le VM che fanno parte di un ambiente di sviluppo e test basato sul cloud. Questa operazione può essere eseguita usando [criteri di Azure](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

È importante notare che DevTest Labs USA le risorse di Azure sottostanti gestite allo stesso modo: rete, dischi, calcolo e così via. Ad esempio, criteri di Azure si applica alle macchine virtuali create in un Lab. Il Centro sicurezza di Azure può creare report sulla conformità della macchina virtuale. E il servizio backup di Azure è in grado di fornire backup regolari per le macchine virtuali nel Lab.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Azure DevTest Labs USA le risorse esistenti in Azure (calcolo, rete e così via). Quindi, sfrutta automaticamente i vantaggi delle funzionalità di sicurezza integrate nella piattaforma. Ad esempio, per richiedere che le connessioni Desktop remoto in ingresso vengano originate solo dalla rete aziendale, è sufficiente aggiungere un gruppo di sicurezza di rete alla rete virtuale sul Gateway Desktop remoto. L'unica considerazione aggiuntiva sulla sicurezza è il livello di autorizzazioni concesse ai membri del team che utilizzano i Lab quotidianamente. Le autorizzazioni più comuni sono [ *owner* e *User*](devtest-lab-add-devtest-user.md). Per ulteriori informazioni su questi ruoli, vedere [aggiungere proprietari e utenti in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere il prossimo articolo di questa serie: [scalare verticalmente l'infrastruttura Azure DevTest Labs](devtest-lab-guidance-scale.md).
