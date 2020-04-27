---
title: Informazioni su Azure DevTest Labs | Microsoft Docs
description: Informazioni su come Lab di sviluppo/test può rendere più semplice la creazione, la gestione e il monitoraggio delle macchine virtuali di Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60561456"
---
# <a name="about-azure-devtest-labs"></a>Informazioni su Azure DevTest Labs
Azure DevTest Labs consente agli sviluppatori di team di gestire in modo autonomo macchine virtuali (VM) e risorse PaaS senza attendere le approvazioni.

DevTest Labs crea laboratori costituiti da basi preconfigurate o modelli Azure Resource Manager. Sono disponibili tutti gli strumenti e il software necessari per la creazione di ambienti. È possibile creare ambienti in pochi minuti, anziché ore o giorni.

Con DevTest Labs è possibile testare le versioni più recenti delle applicazioni eseguendo le attività seguenti:

- Effettuare rapidamente il provisioning di ambienti Windows e Linux tramite modelli ed elementi riutilizzabili.
- Consente di integrare facilmente la pipeline di distribuzione in lab di sviluppo e test per effettuare il provisioning di ambienti su richiesta.
- Ridimensionare il test di carico eseguendo il provisioning di più agenti di test e creare ambienti con pre-provisioning per training e demo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Capabilities
DevTest Labs fornisce le funzionalità seguenti agli sviluppatori che usano le macchine virtuali:

- Per creare rapidamente le macchine virtuali, seguire meno di cinque semplici passaggi.
- È possibile scegliere da un elenco curato di basi di VM configurate, approvate e autorizzate dal responsabile del team o dall'IT centrale.
- Creazione di macchine virtuali da immagini personalizzate create in precedenza con tutti gli strumenti e il software già installati. 
- Creare macchine virtuali da formule che sono essenzialmente immagini personalizzate combinate con le build più recenti del software installato al momento della creazione delle macchine virtuali. 
- Installare gli artefatti che sono estensioni distribuite nelle macchine virtuali dopo il provisioning.
- Impostare l'arresto automatico e le pianificazioni di avvio automatico nelle macchine virtuali.
- Richiedere una macchina virtuale creata in precedenza senza eseguire il processo di creazione.

DevTest Labs offre le funzionalità seguenti per gli sviluppatori che usano gli ambienti PaaS:

- Usare Gestione risorse per creare rapidamente ambienti PaaS seguendo meno di tre semplici passaggi.
- Scegli da un elenco curato di modelli di Gestione risorse, che sono configurati e autorizzati dal responsabile del team o dall'IT centrale.
- Creare un gruppo di risorse vuoto (sandbox) usando un modello di Gestione risorse per esplorare Azure nel contesto di un Lab.

DevTest Labs consente inoltre alle IT centrali di controllare gli sprechi, ottimizzare i costi delle risorse e rimanere entro i budget effettuando le attività seguenti:  

- Impostazione dell'arresto automatico e delle pianificazioni di avvio automatico nelle macchine virtuali.
- Impostazione dei criteri per il numero di macchine virtuali che gli utenti possono creare.
- Impostazione dei criteri nelle immagini della raccolta e delle dimensioni delle VM da cui gli utenti scelgono.
- Gestione dei costi e impostazione delle destinazioni nei Lab.
- Ricevere notifiche su costi elevati proiettati per i laboratori, in modo da poter eseguire le azioni necessarie.

DevTest Labs offre i vantaggi seguenti per la creazione, la configurazione e la gestione di ambienti nel cloud.

## <a name="cost-control-and-governance"></a>Controllo dei costi e governance
DevTest Labs consente di controllare più facilmente i costi consentendo di eseguire le attività seguenti:

- [Impostare i criteri nei Lab](devtest-lab-get-started-with-lab-policies.md), ad esempio il numero di macchine virtuali per utente o per Lab. 
- Creare [criteri per arrestare](devtest-lab-set-lab-policy.md) e avviare automaticamente le macchine virtuali.
- Tieni sotto controllo i costi per le macchine virtuali e le risorse PaaS all'interno di Lab per restare al passo con [il tuo budget](devtest-lab-configure-cost-management.md).
- È possibile rimanere nel contesto dei Lab in modo da non creare risorse esterne.

## <a name="quickly-get-to-ready-to-test"></a>Ambienti di test subito pronti
DevTest Labs consente di creare ambienti di pre-provisioning dotati di tutti gli elementi necessari al team per sviluppare e testare le applicazioni. È sufficiente [richiedere gli ambienti](devtest-lab-add-claimable-vm.md) in cui è installata l'ultima build corretta dell'applicazione e iniziare a lavorare. In alternativa, puoi usare i contenitori per creare più rapidamente un ambiente più snello.

## <a name="create-once-use-everywhere"></a>Creazione di un'unica soluzione per scenari di diverso tipo
Acquisisci e Condividi gli [artefatti](add-artifact-repository.md) e i [modelli di ambiente](devtest-lab-create-environment-from-arm.md) PaaS all'interno del team o dell'organizzazione, il tutto nel controllo del codice sorgente, per creare facilmente ambienti di sviluppo e test.

## <a name="worry-free-self-service"></a>Self-service di semplice uso
DevTest Labs consente agli sviluppatori e ai tester di creare in modo rapido e semplice [macchine virtuali IaaS](devtest-lab-add-vm.md) e [risorse PaaS](devtest-lab-create-environment-from-arm.md) usando un set di risorse preconfigurate.

## <a name="use-iaas-and-paas-resources"></a>Usare le risorse IaaS e PaaS 
Gli sviluppatori possono anche creare risorse PaaS, ad esempio cluster di Azure Service Fabric, la funzionalità app Web del servizio app Azure e le farm di SharePoint, usando modelli di Gestione risorse. Per iniziare a usare PaaS in Labs, usare i modelli del [repository dell'ambiente pubblico](devtest-lab-configure-use-public-environments.md) o [connettere il Lab al proprio repository git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). È anche possibile tenere traccia dei costi di queste risorse per mantenersi all'interno del budget.

## <a name="integrate-with-your-existing-toolchain"></a>Integra con la tua organizzazione esistente
USA plug-in predefiniti o l'API per effettuare il provisioning di ambienti di sviluppo/test direttamente dallo [strumento di integrazione continua (ci)](devtest-lab-integrate-ci-cd-vsts.md)preferito, dal Integrated Development Environment (IDE) o dalla pipeline di rilascio automatizzata. È anche possibile usare lo strumento da riga di comando completo.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per altre informazioni su DevTest Labs, vedere [concetti relativi a DevTest Labs](devtest-lab-concepts.md).
- Per una procedura dettagliata con istruzioni dettagliate, vedere [esercitazione: configurare un Lab usando Azure DevTest Labs](tutorial-create-custom-lab.md).