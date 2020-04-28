---
title: Scenari comuni per l'uso di Azure DevTest Labs
description: Questo articolo fornisce gli scenari principali per l'uso di Azure DevTest Labs e di due percorsi generali per iniziare a usare il servizio nell'organizzazione.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60773809"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Scenari comuni per l'uso di Azure DevTest Labs
A seconda delle esigenze di un'azienda, DevTest Labs può essere configurato in modo da soddisfare requisiti diversi.  Questo articolo illustra gli scenari più diffusi. Ogni scenario copre i vantaggi offerti dall'uso di DevTest Labs e delle risorse da usare per implementare tali scenari.  

- Desktop degli sviluppatori
- Ambienti di test
- Sessioni di formazione, laboratori pratici e gli hackathon
- Indagini in modalità sandbox
- Lab per le classi

## <a name="developer-desktops"></a>Desktop degli sviluppatori
Gli sviluppatori hanno spesso requisiti diversi per i computer di sviluppo per progetti diversi. Con DevTest Labs gli sviluppatori possono accedere alle macchine virtuali su richiesta configurate per adattarsi ai propri scenari più comuni. DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono fornire computer di sviluppo comuni che garantiscono la coerenza tra i team.
- Gli sviluppatori possono effettuare rapidamente il provisioning dei computer di sviluppo su richiesta o [richiedere un computer preconfigurato esistente](devtest-lab-add-claimable-vm.md).
- Gli sviluppatori possono eseguire il provisioning delle risorse in modalità self-service senza richiedere autorizzazioni a livello di sottoscrizione.
- L'IT o gli amministratori possono [predefinire la topologia di rete](devtest-lab-configure-vnet.md) e gli sviluppatori possono usarla direttamente in modo semplice e intuitivo senza richiedere alcun accesso speciale.
- Gli sviluppatori possono [personalizzare](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) facilmente i computer di sviluppo in base alle esigenze.
- Gli amministratori possono controllare i costi assicurandosi che:
    - Gli sviluppatori [non possono ottenere più macchine virtuali](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) del necessario per lo sviluppo
    - Le [macchine virtuali vengono arrestate](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando non sono in uso
    - [Consentire solo un subset di dimensioni delle istanze di VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) per i Lab specifici
    - [Gestione degli obiettivi di costo e delle notifiche](devtest-lab-configure-cost-management.md) per ogni Lab.

Per ulteriori informazioni, vedere [utilizzare Azure DevTest Labs per gli sviluppatori](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambienti di test
La creazione e la gestione di ambienti di test in un'organizzazione può richiedere un notevole sforzo. Con DevTest Labs, gli ambienti di test possono essere creati, aggiornati o duplicati in modo semplice. Consente ai team di accedere a un ambiente completamente configurato quando è necessario. In questo scenario DevTest Labs offre i vantaggi seguenti:

- Le organizzazioni possono fornire ambienti di test comuni garantendo la coerenza tra i team.
- I tester possono testare la versione più recente dell'applicazione eseguendo rapidamente il provisioning di ambienti Windows e Linux usando modelli riutilizzabili.
- Gli amministratori possono connettere il Lab ad Azure DevOps per abilitare gli scenari DevOps
- I proprietari del Lab possono controllare i costi garantendo che:
    - Le [macchine virtuali negli ambienti vengono arrestate](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando non sono in uso
    - [Consentire solo un subset di dimensioni delle istanze di VM per](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) i Lab specifici
    - [Gestione degli obiettivi di costo e delle notifiche](devtest-lab-configure-cost-management.md) per ogni Lab.

Per altre informazioni, vedere [usare Azure DevTest Labs per gli ambienti di testing di macchine virtuali e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Indagini in modalità sandbox
Gli sviluppatori spesso esaminano diverse tecnologie o progettazione dell'infrastruttura. Per impostazione predefinita, tutti gli ambienti creati con DevTest Labs vengono creati nel proprio gruppo di risorse. L'utente di DevTest Labs ottiene solo l'accesso in lettura a tali risorse. Per gli sviluppatori che hanno bisogno di un maggiore controllo, tuttavia, è possibile aggiornare un'impostazione a livello di Lab per concedere [diritti di collaboratore](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) all'utente DevTest Labs di origine per ogni ambiente creato.  Con DevTest Labs, gli sviluppatori possono ricevere automaticamente le autorizzazioni di collaboratore per gli ambienti creati nel Lab.  Questo scenario consente agli sviluppatori di aggiungere e/o modificare le risorse di Azure in base alle esigenze per gli ambienti di sviluppo o di test. La pagina [costo per risorsa](devtest-lab-configure-cost-management.md#view-cost-by-resource) consente ai proprietari del Lab di tenere traccia dei costi di ogni ambiente utilizzato per le indagini.

Per altre informazioni, vedere [impostare i diritti di accesso a un gruppo di risorse dell'ambiente](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Training, laboratori pratici e gli hackathon 
Un Lab in Azure DevTest Labs funge da contenitore ideale per attività temporanee, ad esempio laboratori, laboratori pratici, corsi di formazione o gli hackathon.  Il servizio consente di creare un lab in cui è possibile offrire modelli personalizzati che ogni partecipante può usare per creare ambienti identici e isolati per il training. In questo scenario DevTest Labs offre i vantaggi seguenti:

- I [criteri](devtest-lab-set-lab-policy.md) assicurano che i partecipanti ottengano solo il numero di risorse, ad esempio le macchine virtuali, di cui hanno bisogno.
- I computer preconfigurati e creati vengono [richiesti](devtest-lab-add-claimable-vm.md) con una singola azione del partecipante.
- I Lab sono condivisi con i partecipanti accedendo all' [URL per il Lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- Le [date di scadenza](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) sulle macchine virtuali assicurano che i computer vengano eliminati dopo che non sono più necessari.
- Al termine del training è facile [eliminare un Lab](devtest-lab-delete-lab-vm.md#delete-a-lab) e tutte le [risorse correlate](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) .

Per ulteriori informazioni, vedere [utilizzare Azure DevTest Labs per il training](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Confronto tra modello di prova e distribuzione ridimensionata
Una volta deciso di esplorare DevTest Labs, sono disponibili due percorsi generali: il modello di verifica rispetto alla distribuzione ridimensionata.  

Una **distribuzione ridimensionata** prevede settimane o mesi di revisione e pianificazione con la finalità di distribuire DevTest Labs a tutta l'azienda con l'impiego di centinaia o migliaia di sviluppatori.

Una distribuzione del modello di **prova** è incentrata su un lavoro concentrato da un singolo team per stabilire un valore aziendale. Mentre si può essere tentati a pensare a una distribuzione ridimensionata, l'approccio tende ad avere esito negativo più spesso rispetto all'opzione del modello di verifica. Pertanto, è consigliabile iniziare gradualmente con un primo team per acquisire le conoscenze, ripetere lo stesso approccio con due o tre team aggiuntivi e quindi pianificare una distribuzione ridimensionata in base alla conoscenza acquisita. Per un modello di verifica valido, si consiglia di selezionare uno o due team e identificare i relativi scenari (ambiente di sviluppo rispetto all'ambiente di test) documentare i casi d'uso correnti e distribuire DevTest Labs.

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [Concetti di Lab di sviluppo e test](devtest-lab-concepts.md)
- [Domande frequenti su DevTest Labs](devtest-lab-faq.md)

