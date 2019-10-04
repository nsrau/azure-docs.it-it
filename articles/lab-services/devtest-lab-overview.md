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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561456"
---
# <a name="about-azure-devtest-labs"></a>Informazioni su Azure DevTest Labs
Azure DevTest Labs consente agli sviluppatori nel team di gestire in modo efficiente self-macchine virtuali (VM) e risorse PaaS senza tempi di attesa per le approvazioni.

DevTest Labs crea lab costituiti basi preconfigurate o modelli di Azure Resource Manager. Questi sono tutti i software che è possibile usare per creare ambienti e gli strumenti necessari. È possibile creare ambienti in pochi minuti, a differenza di ore o giorni.

Con DevTest Labs, è possibile testare le versioni più recenti delle applicazioni in questo modo le attività seguenti:

- Esegui rapidamente il provisioning di ambienti Windows e Linux tramite modelli ed elementi riutilizzabili.
- Consente di integrare facilmente la pipeline di distribuzione in lab di sviluppo e test per effettuare il provisioning di ambienti su richiesta.
- Aumentare i test eseguendo il provisioning di più agenti di test di carico e creare ambienti con provisioning già effettuati per il training e le demo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Capabilities
DevTest Labs offre le funzionalità seguenti per gli sviluppatori che lavorano con le macchine virtuali:

- Creare rapidamente macchine virtuali da meno di cinque semplici passaggi.
- Scegliere da un elenco curato di basi per VM che sono configurati, approvato e autorizzato dal responsabile del team o centrale IT.
- Creare macchine virtuali da immagini personalizzate create in precedenza con tutto il software e gli strumenti già installati. 
- Creare macchine virtuali da formule che sono essenzialmente personalizzate immagini combinate con la build più recenti del software che viene installato quando vengono create le macchine virtuali. 
- Installare gli elementi che sono le estensioni distribuite in macchine virtuali dopo si esegue il provisioning.
- Configurare l'arresto automatico e le pianificazioni per le macchine virtuali di avvio automatico.
- Richiedere una macchina virtuale creata in precedenza senza passare attraverso il processo di creazione.

DevTest Labs offre le funzionalità seguenti per gli sviluppatori che lavorano con gli ambienti PaaS:

- Usare Resource Manager per creare rapidamente ambienti PaaS tramite meno di tre semplici passaggi.
- Scegliere da un elenco curato dei modelli di Resource Manager, che sono configurati e autorizzato dal responsabile del team o centrale IT.
- Crea un gruppo di risorse vuoto (sandbox) usando un modello di Resource Manager di esplorare Azure all'interno del contesto di un lab.

DevTest Labs consente anche di centrale IT per controllare rifiuti, ottimizzare i costi delle risorse e rimanere entro i budget effettuando le operazioni seguenti:  

- Impostazione di pianificazioni di avvio automatico e l'arresto automatico in macchine virtuali.
- Impostazione di criteri per il numero di macchine virtuali che gli utenti possono creare.
- Impostazione dei criteri sulle dimensioni delle macchine virtuali e immagini della raccolta che gli utenti selezionare.
- Tenere traccia dei costi e l'impostazione di destinazioni di laboratori pratici.
- Ricevere notifiche in alto dei costi previsti per le esercitazioni pratiche e poter intraprendere le azioni necessarie.

DevTest Labs offre i seguenti vantaggi nella creazione, configurazione e la gestione di ambienti nel cloud.

## <a name="cost-control-and-governance"></a>Governance e controllo dei costi
DevTest Labs rende più semplice per controllare i costi consentendo di eseguire le attività seguenti:

- [Impostare i criteri per i laboratori](devtest-lab-get-started-with-lab-policies.md), ad esempio il numero di macchine virtuali per utente o per ogni lab. 
- Creare [i criteri per arrestare automaticamente](devtest-lab-set-lab-policy.md) e avviare le macchine virtuali.
- Tenere traccia dei costi delle risorse PaaS e le macchine virtuali costituito backup all'interno di laboratori di rimanere entro [il tuo budget](devtest-lab-configure-cost-management.md).
- Rimanere all'interno del contesto dei propri lab in modo da non lasciamo risorse esterne ad essi.

## <a name="quickly-get-to-ready-to-test"></a>Ambienti di test subito pronti
DevTest Labs consente di creare ambienti di pre-provisioning con tutto ciò che serve per sviluppare e testare applicazioni tuo team. Appena [richiedere gli ambienti](devtest-lab-add-claimable-vm.md) in cui è installato l'ultima compilazione valida dell'applicazione e l'uso di inizio. Oppure Usa i contenitori per la creazione dell'ambiente anche più veloce e più efficienti.

## <a name="create-once-use-everywhere"></a>Creazione di un'unica soluzione per scenari di diverso tipo
Acquisire e condividere PaaS [modelli di ambiente](devtest-lab-create-environment-from-arm.md) e [elementi](add-artifact-repository.md) all'interno del team o organizzazione, in controllo del codice sorgente, ovvero creare per gli sviluppatori e gli ambienti di test con facilità.

## <a name="worry-free-self-service"></a>Self-service di semplice uso
DevTest Labs consente agli sviluppatori e tester a rapidamente e facilmente [creare le macchine virtuali IaaS](devtest-lab-add-vm.md) e [risorse PaaS](devtest-lab-create-environment-from-arm.md) usando un set di risorse preconfigurate.

## <a name="use-iaas-and-paas-resources"></a>Usare le risorse IaaS e PaaS 
Gli sviluppatori possono anche attivare risorse PaaS, ad esempio i cluster Azure Service Fabric, la funzionalità App Web del servizio App di Azure e le farm di SharePoint, con i modelli di Resource Manager. Per un'introduzione a PaaS nei laboratori, usare i modelli dal [repository ambiente pubblico](devtest-lab-configure-use-public-environments.md) oppure [connettere il lab al repository Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). È anche possibile rilevare i costi su queste risorse per il rispettando il tuo budget.

## <a name="integrate-with-your-existing-toolchain"></a>Integrazione con la toolchain esistente
Usare preconfigurati plug-in o l'API per gli ambienti di sviluppo/test di effettuare il provisioning direttamente dalla voce preferita [dello strumento di integrazione continua (CI)](devtest-lab-integrate-ci-cd-vsts.md), ambiente di sviluppo (IDE) integrato o dalla pipeline di rilascio automatizzata. È anche possibile usare lo strumento da riga di comando completo.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per altre informazioni su DevTest Labs, vedere [concetti relativi a DevTest Labs](devtest-lab-concepts.md).
- Per una procedura dettagliata con istruzioni dettagliate, vedere [esercitazione: Configurare un lab con Azure DevTest Labs](tutorial-create-custom-lab.md).