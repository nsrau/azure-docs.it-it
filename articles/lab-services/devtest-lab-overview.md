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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561456"
---
# <a name="about-azure-devtest-labs"></a>Informazioni su Azure DevTest Labs
Azure DevTest Labs consente agli sviluppatori dei team di gestire in modo efficiente le macchine virtuali (VM) e le risorse PaaS senza attendere l'approvazione.

DevTest Labs crea lab costituiti da basi preconfigurate o modelli di Azure Resource Manager.DevTest Labs creates labs consisting consisting pre-configured bases or Azure Resource Manager templates. Questi hanno tutti gli strumenti e software necessari che è possibile utilizzare per creare ambienti. È possibile creare ambienti in pochi minuti, anziché ore o giorni.

Utilizzando DevTest Labs, è possibile testare le versioni più recenti delle applicazioni eseguendo le attività seguenti:

- Esegui rapidamente il provisioning di ambienti Windows e Linux utilizzando modelli ed elementi riutilizzabili.
- Consente di integrare facilmente la pipeline di distribuzione in lab di sviluppo e test per effettuare il provisioning di ambienti su richiesta.
- Scalare i test di carico eseguendo il provisioning di più agenti di test e creare ambienti pre-provisioning per training e demo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Capabilities
DevTest Labs provides the following capabilities to developers working with VMs:

- Creare rapidamente macchine virtuali seguendo meno di cinque semplici passaggi.
- Scegliere da un elenco a cura di basi di VM configurate, approvate e autorizzate dal responsabile del team o dall'IT centrale.
- Creare macchine virtuali da immagini personalizzate create in precedenismi con tutti i software e gli strumenti già installati. 
- Creare macchine virtuali da formule essenzialmente personalizzate combinate con le build più recenti del software installato quando vengono create le macchine virtuali. 
- Installare gli elementi che sono estensioni distribuite nelle macchine virtuali dopo il provisioning.
- Impostare le pianificazioni di arresto automatico e avvio automatico nelle macchine virtuali.
- Rivendicare una macchina virtuale creata in precedena senza passare attraverso il processo di creazione.

DevTest Labs offre le funzionalità seguenti agli sviluppatori che lavorano con gli ambienti PaaS:

- Usare Resource Manager per creare rapidamente ambienti PaaS seguendo meno di tre semplici passaggi.
- Scegliere da un elenco a cura dei modelli di Resource Manager, configurati e autorizzati dal responsabile del team o dall'IT centrale.
- Eseguire l'innalzarsi di un gruppo di risorse vuoto (sandbox) usando un modello di Resource Manager per esplorare Azure nel contesto di un lab.

DevTest Labs consente inoltre all'IT centrale di controllare gli sprechi, ottimizzare i costi sulle risorse e rimanere entro i budget eseguendo le attività seguenti:  

- Impostazione delle pianificazioni di arresto automatico e avvio automatico nelle macchine virtuali.
- Impostazione di criteri sul numero di macchine virtuali che gli utenti possono creare.
- Impostazione dei criteri sulle dimensioni delle macchine virtuali e sulle immagini della raccolta tra cui gli utenti possono scegliere.
- Monitoraggio dei costi e fissazione di obiettivi nei laboratori.
- Ricevere notifiche sui costi previsti elevati per i laboratori in modo da poter intraprendere le azioni necessarie.

DevTest Labs offre i vantaggi seguenti nella creazione, configurazione e gestione di ambienti nel cloud.

## <a name="cost-control-and-governance"></a>Controllo e governance dei costi
DevTest Labs semplifica il controllo dei costi consentendo di eseguire le attività seguenti:DevTest Labs makes it easier to control costs by allowing you to do the following tasks:

- [Impostare i criteri nei lab,](devtest-lab-get-started-with-lab-policies.md)ad esempio il numero di macchine virtuali per utente o per lab. 
- Creare [criteri per arrestare](devtest-lab-set-lab-policy.md) e avviare automaticamente le macchine virtuali.
- Tenere traccia dei costi nelle macchine virtuali e nelle risorse PaaS scorporature all'interno dei laboratori per rimanere all'interno [del budget.](devtest-lab-configure-cost-management.md)
- Rimani nel contesto dei tuoi laboratori in modo da non far girare le risorse al di fuori di esse.

## <a name="quickly-get-to-ready-to-test"></a>Ambienti di test subito pronti
DevTest Labs consente di creare ambienti pre-provisioning dotati di tutto ciò di cui il team ha bisogno per sviluppare e testare le applicazioni. È sufficiente [richiedere gli ambienti](devtest-lab-add-claimable-vm.md) in cui è installata l'ultima build valida dell'applicazione e iniziare a lavorare. Oppure usa i contenitori per una creazione di ambienti ancora più veloci e snelli.

## <a name="create-once-use-everywhere"></a>Creazione di un'unica soluzione per scenari di diverso tipo
Acquisisci e condividi [i modelli](devtest-lab-create-environment-from-arm.md) di ambiente PaaS e gli [elementi](add-artifact-repository.md) all'interno del tuo team o organizzazione, tutto nel controllo del codice sorgente, per creare facilmente ambienti di sviluppo e test.

## <a name="worry-free-self-service"></a>Self-service di semplice uso
DevTest Labs consente agli sviluppatori e ai tester di creare rapidamente e facilmente [macchine virtuali IaaS](devtest-lab-add-vm.md) e [risorse PaaS](devtest-lab-create-environment-from-arm.md) usando un set di risorse preconfigurate.

## <a name="use-iaas-and-paas-resources"></a>Utilizzare le risorse IaaS e PaaS 
Gli sviluppatori possono anche creare alla volta le risorse PaaS, ad esempio i cluster di Azure Service Fabric, la funzionalità App Web del servizio app di Azure e le farm di SharePoint usando i modelli di Resource Manager.Developers can also spin up PaaS resources, such as Azure Service Fabric clusters, the Web Apps feature of Azure App Service, and SharePoint farms, by using Resource Manager templates. Per iniziare a utilizzare PaaS nei laboratori, utilizzare i modelli dal [repository dell'ambiente pubblico](devtest-lab-configure-use-public-environments.md) o [connettere il lab al proprio repository Git.](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories) È inoltre possibile tenere traccia dei costi di queste risorse per rimanere all'interno del budget.

## <a name="integrate-with-your-existing-toolchain"></a>Integrazione con la toolchain esistente
Usare i plug-in predefiniti o l'API per eseguire il provisioning di ambienti di sviluppo/test direttamente dallo [strumento di integrazione continua (CI)](devtest-lab-integrate-ci-cd-vsts.md)preferito, dall'ambiente di sviluppo integrato (IDE) o dalla pipeline di rilascio automatica. È inoltre possibile utilizzare lo strumento della riga di comando completo.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- Per altre informazioni su DevTest Labs, vedere Concetti di [DevTest Labs](devtest-lab-concepts.md).
- Per una procedura dettagliata con istruzioni dettagliate, vedere [Esercitazione: Configurare un lab tramite Azure DevTest Labs.](tutorial-create-custom-lab.md)