---
title: Scenari comuni per l'uso di Azure DevTest Labs
description: Questo articolo fornisce gli scenari principali per l'uso di Azure DevTest Labs e due percorsi generali per iniziare a usare il servizio nell'organizzazione.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773809"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Scenari comuni per l'uso di Azure DevTest Labs
A seconda delle esigenze di un'azienda, DevTest Labs può essere configurato per soddisfare requisiti diversi.  In questo articolo vengono illustrati gli scenari più comuni. Ogni scenario illustra i vantaggi offerti dall'uso di DevTest Labs e le risorse da utilizzare per implementare tali scenari.  

- Desktop degli sviluppatori
- Ambienti di test
- Sessioni di formazione, laboratori pratici e hackathon
- Indagini in modalità sandbox
- Lab per le classi

## <a name="developer-desktops"></a>Desktop degli sviluppatori
Gli sviluppatori hanno spesso requisiti diversi per i computer di sviluppo per progetti diversi. Con DevTest Labs, gli sviluppatori possono avere accesso alle macchine virtuali su richiesta configurate per adattarle agli scenari più comuni. DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono fornire macchine di sviluppo comuni garantendo la coerenza tra i team.
- Gli sviluppatori possono eseguire rapidamente il provisioning delle macchine di sviluppo su richiesta o [richiedere una macchina preconfigurata esistente.](devtest-lab-add-claimable-vm.md)
- Gli sviluppatori possono eseguire il provisioning delle risorse in modalità self-service senza dover disporre di autorizzazioni a livello di sottoscrizione.
- Gli IT o gli amministratori possono [predefinire la topologia](devtest-lab-configure-vnet.md) di rete e gli sviluppatori possono utilizzarla direttamente in modo semplice e intuitivo senza richiedere alcun accesso speciale.
- Gli sviluppatori possono [personalizzare](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) facilmente i propri computer di sviluppo in base alle esigenze.
- Gli amministratori possono controllare i costi assicurandosi che:
    - Gli sviluppatori [non possono ottenere più macchine virtuali](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) di quelle necessarie per lo sviluppo
    - [Le macchine virtuali vengono arrestate](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando non sono in uso
    - Consentire solo [un sottoinsieme di dimensioni dell'istanza di VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) per i lab specifici
    - [Gestione degli obiettivi di costo e delle notifiche](devtest-lab-configure-cost-management.md) per ogni lab.

Per ulteriori informazioni, vedere [Usare Azure DevTest Labs per gli sviluppatori.](devtest-lab-developer-lab.md) 

## <a name="test-environments"></a>Ambienti di test
La creazione e la gestione di ambienti di test in un'organizzazione può richiedere un notevole sforzo. Con DevTest Labs, gli ambienti di test possono essere facilmente creati, aggiornati o duplicati. Consente ai team di accedere a un ambiente completamente configurato quando è necessario. In questo scenario DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono fornire ambienti di test comuni garantendo la coerenza tra i team.
- I tester possono testare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux utilizzando modelli riutilizzabili.
- Gli amministratori possono connettere il lab ad Azure DevOps per abilitare gli scenari DevOpsAdministrators can connect the lab to Azure DevOps to enable DevOps scenarios
- I proprietari dei laboratori possono controllare i costi assicurandosi che:
    - [Le macchine virtuali negli ambienti vengono arrestate](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando non sono in uso
    - Consentire solo [un sottoinsieme di dimensioni dell'istanza di VM per](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) i lab specifici
    - [Gestione degli obiettivi di costo e delle notifiche](devtest-lab-configure-cost-management.md) per ogni lab.

Per altre informazioni, vedere Usare i laboratori di Azure DevTest per gli ambienti di [test VM e PaaS.](devtest-lab-test-env.md)

## <a name="sandboxed-investigations"></a>Indagini in modalità sandbox
Gli sviluppatori spesso esaminano diverse tecnologie o progettazione dell'infrastruttura. Per impostazione predefinita, tutti gli ambienti creati con DevTest Labs vengono creati nel proprio gruppo di risorse. L'utente DevTest Labs ottiene solo l'accesso in lettura a tali risorse. Tuttavia, per gli sviluppatori che necessitano di un maggiore controllo, è possibile aggiornare un'impostazione a livello di laboratorio per assegnare [ai collaboratori i diritti](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) per l'utente DevTest Labs di origine per ogni ambiente creato.  Con DevTest Labs, agli sviluppatori può essere concessa automaticamente l'autorizzazione di collaboratore per gli ambienti creati nel lab.  Questo scenario consente agli sviluppatori di aggiungere e/o modificare le risorse di Azure in base alle esigenze per gli ambienti di sviluppo o test. La pagina [Costo per risorsa](devtest-lab-configure-cost-management.md#view-cost-by-resource) consente ai proprietari del lab di tenere traccia del costo di ogni ambiente utilizzato per le indagini.

Per altre informazioni, vedere Impostare i diritti di [accesso a un gruppo di risorse dell'ambiente.](https://aka.ms/dtl-sandbox)

## <a name="trainings-hands-on-labs-and-hackathons"></a>Corsi di formazione, laboratori pratici e hackathon 
Un laboratorio in Azure DevTest Labs funge da ottimo contenitore per attività temporanee come workshop, laboratori pratici, corsi di formazione o hackathon.  Il servizio consente di creare un lab in cui è possibile offrire modelli personalizzati che ogni partecipante può usare per creare ambienti identici e isolati per il training. In questo scenario DevTest Labs offre i vantaggi seguenti:

- [I criteri](devtest-lab-set-lab-policy.md) garantiscono che i tirocinanti ottengono solo il numero di risorse necessarie, ad esempio le macchine virtuali.
- Le macchine preconfigurate e create vengono [richieste](devtest-lab-add-claimable-vm.md) con un'unica azione da parte del tirocinante.
- I laboratori vengono condivisi con i tirocinanti accedendo [all'URL per il lab.](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)
- [Le date](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) di scadenza nelle macchine virtuali garantiscono che le macchine vengano eliminate dopo che non sono più necessarie.
- È facile [eliminare un lab](devtest-lab-delete-lab-vm.md#delete-a-lab) e tutte le risorse [correlate](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) al termine del training.

Per altre informazioni, vedere [Usare Azure DevTest Labs per la formazione.](devtest-lab-training-lab.md)  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Confronto tra proof of concept e distribuzione in scala
Dopo aver deciso di esplorare DevTest Labs, sono disponibili due percorsi generali: Proof of Concept e Distribuzione in scala.  

Una **distribuzione ridimensionata** prevede settimane o mesi di revisione e pianificazione con la finalità di distribuire DevTest Labs a tutta l'azienda con l'impiego di centinaia o migliaia di sviluppatori.

Una distribuzione **proof-of-concept** si concentra su uno sforzo concentrato da parte di un singolo team per stabilire il valore organizzativo. Mentre si può essere tentati a pensare a una distribuzione ridimensionata, l'approccio tende ad avere esito negativo più spesso rispetto all'opzione del modello di verifica. Pertanto, è consigliabile iniziare gradualmente con un primo team per acquisire le conoscenze, ripetere lo stesso approccio con due o tre team aggiuntivi e quindi pianificare una distribuzione ridimensionata in base alla conoscenza acquisita. Per un modello di verifica valido, si consiglia di selezionare uno o due team e identificare i relativi scenari (ambiente di sviluppo rispetto all'ambiente di test) documentare i casi d'uso correnti e distribuire DevTest Labs.

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [Concetti di Lab di sviluppo e test](devtest-lab-concepts.md)
- [Domande frequenti su DevTest Labs](devtest-lab-faq.md)

