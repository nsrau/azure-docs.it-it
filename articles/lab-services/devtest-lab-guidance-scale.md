---
title: Aumentare l'infrastruttura di Azure DevTestLabs
description: Questo articolo include indicazioni per aumentare l'infrastruttura di Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 25a088686c739c53feadd6354baf75f3147bdc33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561490"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Aumentare l'infrastruttura di Azure DevTestLabs
Prima di implementare DevTest Labs su scala aziendale, esistono diversi punti fondamentali da decidere. La comprensione di questi punti a livello generale aiuta un'organizzazione a progettare le decisioni per il futuro. Questi punti non devono tuttavia trattenere un'organizzazione dall'avviare un modello di verifica. Di seguito sono elencate le tre aree principali che interessano la pianificazione iniziale della scalabilità verticale:

- Rete e sicurezza
- Topologia della sottoscrizione
- Ruoli e responsabilità

## <a name="networking-and-security"></a>Rete e sicurezza
La rete e la sicurezza sono i fondamenti per tutte le organizzazioni. Mentre una distribuzione a livello aziendale richiede un'analisi molto più approfondita, il numero di requisiti necessari per ottenere un modello di verifica di successo è ridotto. Alcune aree fondamentali di interesse includono:

- **Sottoscrizione di Azure**: per distribuire DevTest Labs, è necessario avere accesso a una sottoscrizione di Azure con i diritti appropriati per creare risorse. Esistono diverse modalità di accesso alle sottoscrizioni di Azure, ad esempio tramite un contratto Enterprise Agreement o una sottoscrizione con pagamento a consumo. Per altre informazioni su come ottenere una sottoscrizione di Azure, vedere [Licenze di Azure per l'azienda](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Accesso a risorse locali**: alcune organizzazioni richiedono che le proprie risorse in DevTest Labs abbiano accesso alle risorse locali. È necessario disporre di una connessione sicura tra l'ambiente locale e Azure. È pertanto importante installare/configurare una connessione VPN o ExpressRoute prima di iniziare. Per altre informazioni, vedere [Panoramica sulle reti virtuali](../virtual-network/virtual-networks-overview.md).
- **Requisiti di sicurezza aggiuntivi**: gli altri requisiti di sicurezza, ad esempio i criteri dei computer, l'accesso agli indirizzi IP pubblici, la connessione a Internet, sono scenari che potrebbe essere necessario esaminare prima di implementare un modello di verifica. 

## <a name="subscription-topology"></a>Topologia della sottoscrizione
La topologia della sottoscrizione è una considerazione di progettazione critica quando si distribuisce DevTest Labs in ambito aziendale. Non è comunque necessario consolidare tutte le decisioni fino a quando un modello di verifica non è stato completato. Nella valutazione del numero di sottoscrizioni necessarie per un'implementazione aziendale, si possono considerare due opzioni opposte: 

- Un'unica sottoscrizione per l'intera organizzazione
- Una sottoscrizione per utente

Di seguito vengono esposti i vantaggi di ogni approccio.

### <a name="one-subscription"></a>Un'unica sottoscrizione
L'approccio con un'unica sottoscrizione spesso non è gestibile in un'azienda di grandi dimensioni. La scelta di limitare il numero di sottoscrizioni offre tuttavia i benefici seguenti:

- **Previsione** dei costi per l'azienda.  La determinazione del budget diventa molto più semplice con una singola sottoscrizione perché tutte le risorse sono riunite in un unico pool. Questo approccio consente di prendere le decisioni con più facilità in merito a quando esercitare misure di controllo dei costi in una fase specifica del ciclo di fatturazione.
- **Gestibilità** di macchine virtuali, gli elementi, le formule, configurazione di rete, autorizzazioni, i criteri, e così via è più semplice poiché tutti gli aggiornamenti sono necessari solo in una sottoscrizione invece di effettuare gli aggiornamenti tra più sottoscrizioni.
- Il lavoro richiesto per le **reti** risulta enormemente semplificato in una singola sottoscrizione per le aziende per cui la connettività locale è un requisito. La connessione di reti virtuali tra le sottoscrizioni (modello hub-spoke) è necessaria con sottoscrizioni aggiuntive e richiede configurazione, gestione, spazi di indirizzi IP aggiuntivi e altro ancora.
- La **collaborazione nei team** è più facile se tutti lavorano nella stessa sottoscrizione. È ad esempio più facile riassegnare una VM a un collega, condividere le risorse con il team e così via.

### <a name="subscription-per-user"></a>Una sottoscrizione per utente
Una sottoscrizione distinta per ogni utente offre pari opportunità nello spettro delle alternative. I vantaggi di disporre di molte sottoscrizioni includono:

- Le **quote di ridimensionamento di Azure** non ostacolano l'adozione. Al momento della stesura di questo articolo, ad esempio, Azure consente 200 account di archiviazione per ogni sottoscrizione. Sono previste quote operative per la maggior parte dei servizi in Azure (molti possono essere personalizzati, altri no). In questo modello di sottoscrizione per utente è molto improbabile che venga raggiunta la maggior parte delle quote. Per altre informazioni sulle attuali quote di ridimensionamento di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- I **chargeback** a gruppi o singoli sviluppatori diventano molto più semplici e consentono alle organizzazioni di tenere conto dei costi tramite il modello corrente.
- La **proprietà e le autorizzazioni** degli ambienti DevTest Labs sono semplici. Si offre agli sviluppatori l'accesso a livello di sottoscrizione e questi diventano responsabili al 100% di tutti gli aspetti, inclusi la configurazione di rete, i criteri del lab e la gestione delle macchine virtuali.

Nell'azienda possono essere presenti alcuni limiti agli estremi dello spettro. Può pertanto essere necessario configurare le sottoscrizioni in un modo che rappresenta una via di mezzo tra questi estremi. Come procedura consigliata, un'organizzazione deve mirare a usare il minor numero di sottoscrizioni possibile tenendo presenti le funzioni che necessariamente aumentano il numero di sottoscrizioni. Come già detto, la topologia della sottoscrizione è fondamentale per una distribuzione aziendale di DevTest Labs, ma non deve ritardare un modello di verifica. L'articolo sulla [Governance](devtest-lab-guidance-governance-policy-compliance.md) include dettagli aggiuntivi su come stabilire la granularità della sottoscrizione e dei lab all'interno dell'organizzazione.

## <a name="roles-and-responsibilities"></a>Ruoli e responsabilità
Un modello di verifica di DevTest Labs prevede tre ruoli primari con responsabilità definite: proprietario della sottoscrizione, proprietario di DevTest Labs, utente di DevTest Labs e, facoltativamente, un collaboratore.

- **Proprietario della sottoscrizione**: il proprietario della sottoscrizione dispone dei diritti per amministrare una sottoscrizione di Azure, tra cui l'assegnazione di utenti, la gestione dei criteri, la creazione e la gestione della topologia di rete, la richiesta di aumenti di quota e così via. Per altre informazioni, vedere [questo articolo](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Proprietario di DevTest Labs**: il proprietario di DevTest Labs dispone dell'accesso amministrativo completo al lab. Questo ruolo è responsabile dell'aggiunta/rimozione di utenti, delle impostazioni dei costi di gestione, delle impostazioni di lab generali e di altre attività basate su macchine virtuali/artefatti. Un proprietario del lab dispone anche di tutti i diritti di un utente di DevTest Labs.
- **Utente di DevTest Labs**: l'utente di DevTest Labs può creare e usare le macchine virtuali nel lab. Dispone di alcune capacità amministrative minime sulle macchine virtuali che crea (avvio/arresto/eliminazione/configurazione delle proprie macchine virtuali). Non può gestire le macchine virtuali di altri utenti.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo successivo di questa serie: [Orchestrare l'implementazione di Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)