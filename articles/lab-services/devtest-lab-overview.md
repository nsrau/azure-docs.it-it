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
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339330"
---
# <a name="about-azure-devtest-labs"></a>Informazioni su Azure DevTest Labs
Azure DevTest Labs è un servizio che consente agli sviluppatori in un team per le macchine virtuali in modo efficiente self-service e/o alle risorse PaaS necessari per lo sviluppo, test, formazione e le demo e così via senza tempi di attesa per le approvazioni costante sugli strumenti di cui hanno bisogno. 

Il lab è costituito, già basi preconfigurate o modelli di Resource Manager con tutti gli strumenti necessari e software che gli sviluppatori possono usare per creare ambienti. Gli sviluppatori possono creare i propri ambienti in pochi minuti anziché ore o giorni. 

Uso di DevTest Labs, è possibile testare la versione più recente dell'applicazione eseguendo le attività seguenti:

- Rapidamente il provisioning di ambienti Windows e Linux usando modelli ed elementi riutilizzabili
- Integra con facilità la pipeline di distribuzione con DevTest Labs per effettuare il provisioning di ambienti on demand
- Aumentare i test eseguendo il provisioning di più agenti di test di carico e creare ambienti con provisioning già effettuati per il training e le demo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Capabilities
DevTest Labs offre le funzionalità seguenti per gli sviluppatori che lavorano con le macchine virtuali:

- Creare rapidamente una macchina virtuale da meno di cinque semplici passaggi.
- Scegliere da un elenco curato di basi per VM che sono configurati, approvato e autorizzato dal responsabile del team o centrale IT.
- Creare le macchine da immagini personalizzate create in precedenza con tutto il software e gli strumenti installati nell'immagine. 
- Creare le macchine da formule che sono essenzialmente le immagini personalizzate e una build più recente del software installata al momento della creazione della macchina virtuale.
- Installare gli elementi che sono le estensioni distribuite nella macchina virtuale dopo il provisioning.
- Configurare l'arresto automatico e auto pianificazioni di avvio nei computer di chiusura alla fine della giornata e versioni superiori e in esecuzione mattino successivo.
- Attestazione semplicemente una macchina virtuale creata in precedenza senza dover passare attraverso il processo di creazione della macchina. 

DevTest Labs offre le funzionalità seguenti per gli sviluppatori che lavorano con gli ambienti PaaS:

- Crea ambienti di gestione risorse di Azure PaaS rapidamente dopo meno di tre semplici passaggi.
- Scegliere da un elenco curato dei modelli di Resource Manager, che sono configurati, approvato e autorizzato dal responsabile del team o centrale IT.
- Crea un gruppo di risorse vuoto (Sandbox) usando un modello di Resource Manager per esplorare l'intero di Azure rimanendo comunque entro il contesto del lab.
- Lab di sviluppo e test offre i seguenti vantaggi nella creazione, configurazione e gestione di ambienti di sviluppo e test nel cloud

Oltre a un modello self-service per gli sviluppatori su un team, il servizio Abilita centrale IT per controllare rifiuti, ottimizzare i costi delle risorse e rimanere entro i budget effettuando le operazioni seguenti: 

- Arresto automatico di impostazione e automaticamente avviare pianificazioni nelle macchine virtuali.
- Impostazione dei criteri per il numero di utenti di macchine virtuali può creare.
- Impostazione dei criteri sulle dimensioni delle macchine virtuali e raccolta utenti immagini può scegliere tra.
- Tenere traccia dei costi e l'impostazione di destinazioni di lab.
- Ricevere notifiche in alto dei costi previsti per l'ambiente lab in modo che sia possibile eseguire le azioni necessarie. 

DevTest Labs offre i seguenti vantaggi nella creazione, configurazione e la gestione di ambienti nel cloud:

## <a name="cost-control-and-governance"></a>Governance e controllo dei costi
DevTest Labs rende più semplice per controllare i costi consentendo di eseguire le attività seguenti:

- Impostare i criteri per il tuo lab, ad esempio il numero di macchine virtuali (VM) per ogni utente e numero di macchine virtuali per lab. 
- Creare criteri per arrestare e avviare le macchine virtuali automaticamente.
- Consente di tenere traccia dei costi in macchine virtuali e risorse PaaS riattivate all'interno di lab in modo che possono rimanere entro il tuo budget predefiniti. 
- Consente agli sviluppatori di rimanere entro il contesto del lab in modo che non entrino piattelli tutte le risorse all'esterno di esso nel gruppo di risorse sottostanti o sottoscrizione.

## <a name="quickly-get-to-ready-to-test"></a>Ambienti di test subito pronti
Lab di sviluppo e test consente di creare ambienti di pre-provisioning con tutto ciò che è necessario per il team affinché possa iniziare lo sviluppo e il test delle applicazioni. È sufficiente richiedere gli ambienti in cui è installato l'ultima compilazione valida dell'applicazione e iniziare a usare subito. Oppure utilizzare i contenitori per creare gli ambienti in modo ancora più veloce ed efficiente.

## <a name="create-once-use-everywhere"></a>Creazione di un'unica soluzione per scenari di diverso tipo
Acquisire e condividere elementi all'interno del team o organizzazione, il tutto nel controllo del codice sorgente - e modelli di ambiente PaaS per sviluppatori di creare e testare con facilità gli ambienti.

## <a name="worry-free-self-service"></a>Self-service di semplice uso
DevTest Labs consente agli sviluppatori e tester rapidamente e facilmente crea macchine virtuali (IaaS) e risorse PaaS all'interno di un paio di clic usando un set di risorse preconfigurato per loro.

## <a name="use-iaas-and-paas-resources"></a>Usare le risorse IaaS e PaaS 
DevTest Labs consente anche agli sviluppatori di creare rapidamente le risorse di PaaS, ad esempio le app Web, i cluster di Service Fabric, le farm di SharePoint e così via. all'interno del lab usando un modello di Resource Manager. Usare i modelli di Resource Manager dal repository pubblico di ambiente o connettere il lab al repository di Git per iniziare a usare su PaaS in Lab. È anche possibile rilevare i costi su queste risorse da non superare il budget predefiniti. 

## <a name="integrate-with-your-existing-toolchain"></a>Integrazione con la toolchain esistente
Usare i plug-in già pronti oppure la nostra API per il provisioning di ambienti di sviluppo/Test direttamente dallo strumento di integrazione continua (CI), ambiente di sviluppo (IDE) integrato o dalla pipeline di rilascio automatizzata. E’ anche possibile utilizzare il nostro strumento da riga di comando completo.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- Per altre informazioni su DevTest Labs, vedere [concetti relativi a DevTest Labs](devtest-lab-concepts.md)
- Per una procedura dettagliata con istruzioni dettagliate, vedere [esercitazione: Configurare un lab con Azure DevTest Labs](tutorial-create-custom-lab.md)


