---
title: Scenari comuni per l'uso di Azure DevTest Labs
description: Questo articolo fornisce gli scenari principali per l'uso di Azure DevTest Labs e due percorsi generali per iniziare a usare il servizio all'interno dell'organizzazione.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272384"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Scenari comuni per l'uso di Azure DevTest Labs
A seconda delle esigenze di un'azienda, DevTest Labs può essere configurato per soddisfare requisiti diversi.  Questo articolo illustra gli scenari comuni. Ogni scenario illustra vantaggi introdotti con DevTest Labs e le risorse da usare per implementare tali scenari.  

- Desktop degli sviluppatori
- Ambienti di test
- Sessioni di formazione, laboratori pratici e gli hackathon
- Indagini creata mediante sandbox
- Lab per le classi

## <a name="developer-desktops"></a>Desktop degli sviluppatori
Gli sviluppatori hanno spesso requisiti diversi per i computer di sviluppo per progetti diversi. Con DevTest Labs, gli sviluppatori possono avere accesso alle macchine virtuali on demand che sono configurati per soddisfare il maggior parte degli scenari. DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono distribuire computer di sviluppo comuni garantendo la coerenza tra i team.
- Gli sviluppatori possono rapidamente il provisioning dei computer di sviluppo su richiesta oppure [attestazione di un computer preconfigurato esistente](devtest-lab-add-claimable-vm.md).
- Gli sviluppatori il provisioning di risorse in modo self-service senza bisogno di autorizzazioni a livello di sottoscrizione.
- IT o gli amministratori possono [predefinire la topologia di rete](devtest-lab-configure-vnet.md) e gli sviluppatori direttamente utilizzarlo in modo semplice e intuitivo senza richiedere l'accesso speciale.
- Gli sviluppatori possono facilmente [personalizzare](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) relativi allo sviluppo dei computer in base alle esigenze.
- Gli amministratori possono controllare i costi assicurandosi che:
    - Gli sviluppatori [non è possibile ottenere più VM](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) quelle necessarie per lo sviluppo
    - [Macchine virtuali vengono arrestate](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando non in uso
    - Solo [consentendo un subset di dimensioni delle istanze di macchina virtuale](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) per i laboratori specifici
    - [Gestire gli obiettivi di costo e le notifiche](devtest-lab-configure-cost-management.md) in ogni lab.

Per altre informazioni, vedere [usare Azure DevTest Labs per gli sviluppatori](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambienti di test
La creazione e la gestione di ambienti di test in un'organizzazione può richiedere un notevole sforzo. Con DevTest Labs, ambienti di test possono essere facilmente creati, aggiornati o duplicati. Consente ai team di accedere a un ambiente completamente configurato quando è necessaria. In questo scenario DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono fornire ambienti di test comuni garantendo la coerenza tra i team.
- I tester possono provare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux usando modelli riutilizzabili.
- Gli amministratori possono connettersi i lab per DevOps di Azure per abilitare scenari DevOps
- I proprietari del lab possono controllare i costi assicurandosi che:
    - [Vengono arrestate le macchine virtuali in ambienti](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando non in uso
    - Solo [consentendo un subset di dimensioni di istanze di macchina virtuale per](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) i laboratori specifici
    - [Gestire gli obiettivi di costo e le notifiche](devtest-lab-configure-cost-management.md) in ogni lab.

Per altre informazioni, vedere [ambienti di test usare Azure DevTest Labs per macchine Virtuali e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Indagini creata mediante sandbox
Gli sviluppatori spesso esaminare le diverse tecnologie o progettazione dell'infrastruttura. Per impostazione predefinita, vengono creati tutti gli ambienti creati con DevTest Labs nel proprio gruppo di risorse. Utente DevTest Labs ottiene accesso in lettura solo a tali risorse. Tuttavia, per gli sviluppatori che necessitano di maggiore controllo, un'impostazione a livello di lab può essere aggiornata per offrire [diritti di collaboratore](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) all'origine utente DevTest Labs per tutti gli ambienti creati.  Con DevTest Labs, gli sviluppatori possono essere concessa l'autorizzazione di collaboratore automaticamente per gli ambienti che vengono create nel lab.  Questo scenario consente agli sviluppatori di aggiungere e/o modificare le risorse di Azure necessario per i propri ambienti di sviluppo o test. Il [costo per risorsa](devtest-lab-configure-cost-management.md#view-cost-by-resource) pagina consente ai proprietari di Lab di tenere traccia dei costi di ogni ambiente usato per le indagini.

Per altre informazioni, vedere [diritti di accesso Set per un gruppo di risorse di ambiente](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Gli hackathon, laboratori pratici e corsi di formazione 
Un lab in Azure DevTest Labs funge da contenitore ideale per le attività temporanei come workshop, laboratori pratici, corsi di formazione o gli hackathon.  Il servizio consente di creare un lab in cui è possibile offrire modelli personalizzati che ogni partecipante può usare per creare ambienti identici e isolati per il training. In questo scenario DevTest Labs offre i vantaggi seguenti:

- [I criteri](devtest-lab-set-lab-policy.md) assicurarsi che i partecipanti ottengono solo il numero di risorse, ad esempio le macchine virtuali, che hanno bisogno.
- Sono configurate in precedenza e create le macchine [richiesti](devtest-lab-add-claimable-vm.md) con singola operazione dal partecipante.
- Labs sono condivisi con i partecipanti accedendo [URL per il lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Date di scadenza](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) nelle macchine virtuali, assicurarsi che le macchine vengono eliminate dopo che non sono più necessari.
- È facile [Elimina un lab](devtest-lab-delete-lab-vm.md#delete-a-lab) e tutte le [le risorse correlate](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando i corsi di formazione è posizionato.

Per altre informazioni, vedere [usare Azure DevTest Labs per il training](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Modello di verifica e distribuzione con scalabilità
Dopo aver deciso di Esplora DevTest Labs, esistono due percorsi generali in avanti: Prova di Visual Studio concetto distribuzione con aumento del numero.  

Una **distribuzione ridimensionata** prevede settimane o mesi di revisione e pianificazione con la finalità di distribuire DevTest Labs a tutta l'azienda con l'impiego di centinaia o migliaia di sviluppatori.

Oggetto **modello di verifica** distribuzione si concentra su un grande sforzo da un unico team per determinare il valore dell'organizzazione. Mentre si può essere tentati a pensare a una distribuzione ridimensionata, l'approccio tende ad avere esito negativo più spesso rispetto all'opzione del modello di verifica. Pertanto, è consigliabile iniziare gradualmente con un primo team per acquisire le conoscenze, ripetere lo stesso approccio con due o tre team aggiuntivi e quindi pianificare una distribuzione ridimensionata in base alla conoscenza acquisita. Per un modello di verifica valido, si consiglia di selezionare uno o due team e identificare i relativi scenari (ambiente di sviluppo rispetto all'ambiente di test) documentare i casi d'uso correnti e distribuire DevTest Labs.

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [Concetti di Lab di sviluppo e test](devtest-lab-concepts.md)
- [Domande frequenti su DevTest Labs](devtest-lab-faq.md)

