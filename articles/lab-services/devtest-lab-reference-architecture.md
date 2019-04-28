---
title: Architettura di riferimento per Azure DevTest Labs per un'azienda
description: Questo articolo fornisce un'indicazioni sull'architettura di riferimento per Azure DevTest Labs in un'organizzazione.
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
ms.openlocfilehash: bcb154f7cffb92ef23fc2606e1f604bb12f8d1a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561532"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure DevTest Labs: architettura di riferimento per un'azienda
Questo articolo fornisce un'architettura di riferimento per la distribuzione di una soluzione basata su Azure DevTest Labs in un'organizzazione. Include la connettività locale tramite Expressroute, gateway desktop remoto alla firma in modalità remota alle macchine virtuali, la connettività con un repository di elementi per gli elementi privati e altri servizi PaaS usate in un lab.

![Architettura di riferimento](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architettura
Gli elementi principali nell'architettura di riferimento sono:

- **Azure Active Directory (AAD)**: Azure DevTest Labs Usa il [servizio di Azure Active Directory per la gestione delle identità](../active-directory/fundamentals/active-directory-whatis.md). Esistono due aspetti da considerare in che fornisce accesso a un ambiente basato su DevTest Labs per gli utenti:
    - **Gestione delle risorse**:  Fornisce l'accesso al portale di Azure per gestire le risorse (creazione di macchine virtuali, creare ambienti, avvio/arresto/riavvio/delete/applyartifacts e così via). Questa operazione viene eseguita in Azure usando il controllo degli accessi basato su Roble (RBAC) e applicando un'assegnazione di ruolo per l'utente, risorse di impostazione e le autorizzazioni a livello di accesso.
    - **Le macchine virtuali (a livello di rete)**:  Nella configurazione predefinita, le macchine virtuali usare un account amministratore locale.  Se è disponibile un dominio ([servizi di dominio AAD](../active-directory-domain-services/active-directory-ds-overview.md), un dominio locale o un dominio e basato sul cloud), le macchine possono essere aggiunte al dominio. Una volta inserito, gli utenti potrebbero usare le proprie identità basati su dominio per connettersi alle macchine virtuali.
- **Connettività locale**: Nel diagramma dell'architettura riportato sopra, [Express Route](../expressroute/expressroute-introduction.md) i usato, ma è anche possibile usare un [VPN Site-to-Site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Anche se non è obbligatorio per DevTest Labs, è solitamente nelle aziende. È necessario solo se c'è un motivo per la connessione alle risorse aziendali. Cause più comuni sono: 
    - Dati non possono essere spostati nel cloud ancora in locale
    - Preferenza per aggiungere le macchine virtuali del lab al dominio locale
    - Forzare tutto il traffico di rete da e verso l'ambiente cloud attraverso un firewall locale per motivi di sicurezza/conformità
- **Gruppi di sicurezza di rete**: Un modo comune per limitare il traffico all'ambiente cloud (o all'interno dell'ambiente cloud) basato sull'origine e gli indirizzi IP di destinazione consiste nell'usare un [gruppo di sicurezza di rete](../virtual-network/security-overview.md). Ad esempio, consentendo solo il traffico di rete nelle reti del lab di origine dalla rete aziendale.
- **Gateway desktop remoto**:  Le aziende in genere si bloccano le connessioni desktop remoto in uscita nel firewall aziendale. Per abilitare la connettività all'ambiente basato su cloud in DevTest Labs, esistono diverse opzioni, ad esempio usando un [gateway desktop remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (elenco elementi consentiti l'indirizzo IP statico per il bilanciamento del carico di gateway) o [indirizza tutto in ingresso Il traffico RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) tramite una connessione Express Route/Site-to-Site VPN. È un fattore comune quando si pianifica una distribuzione di DevTest Labs nell'organizzazione.
- **La rete di Azure (reti virtuali, subnet)**:  Il [la rete di Azure](../networking/networking-overview.md) topologia è un altro elemento essenziale per l'architettura complessiva di DevTest Labs. In questo modo le risorse dal lab per comunicare (oppure No), l'accesso ai servizi locali (o No) e accedere a internet (oppure No). Il diagramma dell'architettura include il maggior parte dei casi i clienti usano DevTest Labs (connessi tramite tutti i laboratori [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) usando un [modello hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) per la connessione Express Route/Site-to-Site VPN in locale), ma poiché DevTest Labs Usa le reti Azure direttamente non vi sono restrizioni su come configurare l'infrastruttura di rete.
- **DevTest Labs**:  DevTest Labs è un elemento fondamentale dell'architettura complessiva. Per altre informazioni sul servizio, vedere [su DevTest Labs](devtest-lab-overview.md).
- **Le macchine virtuali e altre risorse (SaaS, PaaS, IaaS)**:  Uno dei carichi di lavoro principali supportati da DevTest Labs sono macchine virtuali e altre risorse di Azure.  DevTest Labs consente facile e veloce per un'azienda di concedere l'accesso alle risorse di Azure (incluse macchine virtuali e altre risorse di Azure).  Altre informazioni sull'accesso ad Azure per [sviluppatori](devtest-lab-developer-lab.md) e [tester](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità
Anche se DevTest Labs non include le quote predefinite o i limiti, altre risorse di Azure vengono usate nella tipica si dispongono di operazioni di un lab [le quote a livello di sottoscrizione](../azure-subscription-service-limits.md). Di conseguenza, in una distribuzione aziendale tipica, è necessario disporre di più sottoscrizioni di Azure per coprire una distribuzione di grandi dimensioni di DevTest Labs. Le quote di più comunemente raggiunte per le aziende sono:

- **Gruppi di risorse**:  Nella configurazione predefinita, DevTest Labs consente di creare un gruppo di risorse per ogni nuova macchina virtuale o un ambiente che consente di creare un utente usando il servizio. Le sottoscrizioni possono contenere una [massimo dei gruppi di risorse 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), in modo che questo limite è il limite massimo di macchine virtuali e ambienti in una sottoscrizione. Esistono due altre configurazioni che è necessario considerare:
    - **[Tutte le macchine virtuali passare allo stesso gruppo di risorse](resource-group-control.md)**:  Anche se si consente il limite di gruppo di risorse, influisce sul limite di gruppo di risorse per il tipo di risorsa.
    - **Utilizzare condivisi gli indirizzi IP pubblici**:  Tutte le macchine virtuali delle stesse dimensioni e nella stessa area passare nello stesso gruppo di risorse. Se le macchine virtuali possono avere indirizzi IP pubblici è 'intermedie' tra le quote di gruppo di risorse e il tipo di risorsa per le quote di gruppo di risorse. 
- **Gruppo di risorse per ogni risorsa per ogni tipo di risorsa**: Il limite predefinito per [risorse per ogni gruppo di risorse per ogni tipo di risorsa è 800](../azure-subscription-service-limits.md#resource-group-limits).  Quando tutte le VM usando la stessa configurazione gruppo di risorse, gli utenti raggiungerà il limite della sottoscrizione molto più rapidamente, soprattutto se le macchine virtuali presenti molti dischi aggiuntivi.
- **Account di archiviazione**: Viene fornito un lab in DevTest Labs con un account di archiviazione e la quota di Azure per [numero di account di archiviazione per area per sottoscrizione è 250](../azure-subscription-service-limits.md#storage-limits). Significa che il limite superiore per il numero di DevTest Labs nella stessa area è inoltre 250.
- **Le assegnazioni di ruolo**: Un'assegnazione di ruolo viene illustrato come concedere un accesso utente o entità a una risorsa (proprietario, resource, livello di autorizzazione). In Azure, esiste un [limite di 2000 assegnazioni di ruolo per ogni sottoscrizione](../azure-subscription-service-limits.md#role-based-access-control-limits). Il servizio DevTest Labs (nella configurazione predefinita) consente di creare un gruppo di risorse per ogni macchina virtuale e il proprietario verrà concessa il **proprietario** l'autorizzazione per la macchina virtuale di DevTest Labs e **lettore** dell'autorizzazione per il gruppo di risorse.  In questo modo, ogni nuova macchina virtuale creata usa due assegnazioni di ruolo aggiuntive per le assegnazioni di ruolo create quando concedono agli utenti le autorizzazioni per lab.
- **Letture/scritture API**: Esistono diversi modi (API REST, PowerShell, CLI, Azure SDK e così via) per l'automazione di Azure e DevTest Labs e tramite l'automazione è possibile raggiungere un altro limite per le richieste API. Ogni sottoscrizione supporta fino a [12000 leggere le richieste e 1200 richieste all'ora di scrittura](../azure-resource-manager/resource-manager-request-limits.md).  È un limite da tenere presenti durante l'automazione DevTest Labs.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità
DevTest Labs ha un'interfaccia utente con privilegi amministrativi ideale quando si lavora con un singolo lab. In un'azienda, esistono probabilmente più sottoscrizioni di Azure e di molti laboratori. Significa che apportano modifiche a tutti i lab in modo coerente richiede la creazione di script/automazione.  Ecco alcuni esempi e i modi migliori per gestire la distribuzione di DevTest Labs:

- **Le modifiche alle impostazioni di lab**: Uno scenario comune consiste nell'aggiornare un'impostazione di lab specifici tra tutti i lab nella distribuzione. Ad esempio, è disponibile una nuova dimensione di istanza di macchina virtuale e tutti i lab devono essere aggiornati per consentirne il.  È consigliabile automatizzare queste modifiche utilizzando gli script di Azure PowerShell, CLI Azure o le API REST.  
- **Token di accesso personale di artefatto del repository**:  In genere, i token di accesso personale per un repository Git di scadono (90 giorni, 1 anno, 2 anni). Per garantire continuità aziendale, è importante estendere il token di accesso personale o crearne uno nuovo e utilizzare automazione per applicare il nuovo token di accesso personale a tutti i lab.
- **Limitare le modifiche apportate a un ambiente di prova**:  È spesso il caso in cui è presente un'impostazione specifica (ad esempio, che consente Marketplace di immagini da utilizzare) che deve essere limitata. Può essere eseguita tramite criteri di Azure (impedire la modifica di quel tipo di risorsa) o la creazione di un ruolo personalizzato e la concessione di tale ruolo invece di 'proprietario' per il lab. Può essere eseguita per la maggior parte delle impostazioni nell'ambiente di laboratorio (supporto interno, annuncio Lab, le dimensioni delle VM consentite e così via)
- **Richiedere macchine virtuali seguono una convenzione di denominazione**: È una richiesta comune per identificare facilmente le macchine virtuali che fanno parte dell'ambiente di testing e sviluppo basato su cloud. È possibile farlo [con i criteri di Azure](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

È importante notare che DevTest Labs Usa le risorse sottostanti in Azure (rete, dischi, calcolo, così via) che sono gestite allo stesso modo in Azure.  Ad esempio, criteri di Azure si applica a macchine virtuali create in un lab. Il Centro sicurezza di Azure può segnalare la conformità della macchina virtuale. Il servizio Backup di Azure può fornire backup regolari per le macchine virtuali nel lab e così via. 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Azure DevTest Labs Usa le risorse esistenti in Azure (calcolo, rete e così via) e quindi automaticamente trae vantaggio dalla tutte le funzionalità di sicurezza incorporate nella piattaforma. Ad esempio, per proteggere le connessioni desktop remoto in ingresso provenienti solo dalla rete aziendale, è semplice quanto l'aggiunta di un gruppo di sicurezza di rete nella rete virtuale nel gateway desktop remoto. La considerazione solo aggiuntiva di sicurezza di Azure DevTest Labs è il livello di autorizzazioni specificato per i membri del team che utilizzeranno i laboratori su base quotidiana.  Sono le autorizzazioni comuni fornite ["Proprietario" e "Utente DevTest Labs"](devtest-lab-add-devtest-user.md). Per altre informazioni su questi ruoli, vedere [aggiungere proprietari e utenti in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Fasi successive
Vedere l'articolo successivo di questa serie: [Aumentare le prestazioni dell'infrastruttura di Azure DevTest Labs](devtest-lab-guidance-scale.md)
