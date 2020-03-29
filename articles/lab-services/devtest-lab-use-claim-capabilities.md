---
title: Usare le funzionalità di attestazione in Azure DevTest Labs. Documenti Microsoft
description: Informazioni sui diversi scenari per l'uso delle funzionalità di attestazione/annullamento dell'ontagua di Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861430"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Usare le funzionalità di attestazione in Azure DevTest LabsUse claim capabilities in Azure DevTest Labs
Il servizio Azure DevTest Labs migliora l'efficacia e l'efficienza di sviluppatori e tester. Questo articolo è incentrato sulla possibilità di attestazioni o annullamento dell'annullamento dell'onda delle macchine virtuali in Lab di Azure DevTest.This article focuses on the ability to claim or unclaim virtual machines in Azure DevTest Labs. Vengono inoltre elencati vari modi in cui questa funzionalità migliora l'esperienza utente. Prima di esaminare diversi scenari in cui questa funzionalità può essere utilizzata, esaminiamo cos'è **la rivendicazione** e come funziona.

## <a name="claimable-machines"></a>Macchine pretendebili
Una macchina richiedente è una macchina virtuale (VM) creata in un lab senza proprietario. Una volta richiesta la macchina, l'utente dispone di una gamma completa di opzioni per tale macchina virtuale. Quando un utente reclama un computer, vengono apportate alcune modifiche. La macchina virtuale viene spostata dall'elenco **Macchine virtuali rivendicabili** all'elenco Macchine virtuali personali nel portale di Azure.The VM is moved from the Claimable virtual machines list to the **My virtual machines** list in the Azure portal. 

L'utente può connettersi alla macchina virtuale, personalizzare gli elementi, riavviare, arrestare o annullare l'annullamento del computer. Esistono un paio di modi per rendere una macchina virtuale rivendicabile:There are a couple of ways to make a VM claimable:

- Creare un computer e annullare la registrazione in modo che si sposti nel pool richiedente. 
- Creare una macchina virtuale e inserire il pool condiviso usando [le impostazioni avanzate](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Esistono due casi in cui le funzionalità di attestazione/annullamento dell'attestazione possono essere utilizzate in modo efficace. Il primo caso richiede più preveggenza e pianificazione, per essere progettato ed eseguito correttamente. E, il secondo è più situazionale. Di seguito sono riportati alcuni esempi dei diversi casi.

## <a name="designed-use-of-claimable-machines"></a>Uso progettato di macchine rivendicabili

- **Sviluppo/test del software:** Consentire agli sviluppatori o ai tester di essere più produttivi avendo configurato i computer pronti e in uno stato non reclamato. La presenza di un set di macchine virtuali con configurazioni diverse, strumenti necessari e con il codice più recente consente agli utenti di richiedere una macchina virtuale e iniziare a lavorare senza dover dedicare il tempo necessario per configurare una macchina. Prima che vengano richieste le macchine virtuali, viene eseguito il provisioning delle macchine, ma viene arrestato riducendo al minimo il costo della presenza di macchine usate meno spesso. Quando sono necessarie le macchine virtuali, un utente semplicemente rivendica la macchina virtuale, che avvia la macchina. L'opzione di annullamento dell'annullamento dell'onda non è così utile in questo caso poiché la creazione di una nuova macchina virtuale è spesso più semplice ed economica.
- **Aula/Laboratori:** Fare in modo che le macchine virtuali siano preconfigurate per una classe o un lab in modo che gli studenti possano connettersi immediatamente a un computer usando il portale di Azure.Have VMs pre-configured for a class or a lab so that students can immediately connect to a machine using the Azure portal.  Quando uno studente richiede una macchina virtuale, il lab garantisce che nessuno possa richiedere la stessa macchina. L'automazione di questo processo garantisce che sia disponibile il numero richiesto di computer con l'ambiente specificato. Se gli studenti non si presentano o sono in ritardo, le macchine non reclamate possono essere tenute disponibili fino al trattempo dalla sessione con un costo minimo. L'opzione di annullamento dell'esecuzione non è altrettanto efficace in questo scenario poiché la macchina virtuale è in uno stato sconosciuto al termine dell'utente precedente.
- **Dimostrazioni:** Usare i computer per le dimostrazioni, in cui i computer del lab sono impostati con ambienti specifici. Questa funzionalità è utile quando più persone possono fornire una dimostrazione contemporaneamente o in momenti casuali, ad esempio in una conferenza. L'opzione di annullamento dell'errore può essere utile in questa situazione in quanto la demo non deve modificare lo stato del computer, consentendo agli utenti di restituire una macchina virtuale al pool rivendicabile per la dimostrazione successiva. Con il computer non rivendicato di cui viene eseguito il deprovisioning e comporta un costo minimo, le macchine virtuali possono essere lasciate in laboratorio per periodi di tempo più lunghi.
- **Lavoratori temporanei/contratti:** Consentire agli utenti di utilizzare una macchina. Quando se ne vanno, restituiscono la macchina virtuale al pool rivendicabile senza perdita di dati. Con la macchina virtuale non rivendicata, un altro utente può richiedere la macchina virtuale e continuare o rivedere la macchina per ulteriori informazioni.
- **In generale:** La possibilità di disporre di un'unica origine di configurare e distribuire automaticamente le macchine virtuali, a una cadenza specifica, è utile in molte situazioni diverse. Esistono diverse situazioni in cui la funzionalità attestazione/annullamento dell'ontatura consente agli utenti di essere più efficienti con un processo automatizzato per compilare le macchine virtuali in uno stato non rivendicato con una configurazione di set. Le configurazioni possono includere diversi sistemi operativi, lingue, dischi o [altro software (artefatti)](devtest-lab-artifact-author.md) a seconda delle esigenze. La possibilità di richiedere una macchina virtuale dal lab consente all'utente lab di ottenere un sistema configurato correttamente senza spendere il tempo o l'impegno nella configurazione del computer. Il gestore di lab potrebbe usare lo stato rivendicato delle macchine virtuali per migliorare il numero di macchine generate, pulire i computer e determinare la priorità delle configurazioni. La [factory](image-factory-create.md) di immagini è un buon esempio di processo automatizzato per la creazione di macchine virtuali e immagini per più lab. Gli script possono essere modificati per eseguire una delle seguenti situazioni con le modifiche appropriate o essere utilizzati come riferimento per la creazione di un sistema personalizzato.

## <a name="situational-use-of-claimable-machines"></a>Uso situazionale di macchine pretendebili

- Utilizzare la funzionalità di attestazione/annullamento dell'attestazione che consente agli utenti di passare il controllo delle macchine da uno all'altro e di non dover sapere in modo esplicito chi raccoglierà la macchina successivamente.
- Sviluppo, test e debug di uno scenario in cui una specifica configurazione del computer può riprodurre un bug, quindi il computer può essere reclamato consentendo a un altro sviluppatore di richiedere il computer e continuare il lavoro. Questa funzione è particolarmente utile in quanto sempre più persone lavorano da remoto in diverse aree del mondo. 
- I membri del team possono lavorare con un singolo ambiente. Ad esempio, è possibile configurare manualmente un ambiente complesso che non può essere automatizzato o creare risorse in grado di gestire solo le modifiche per un singolo input come le immagini. In passato, questo problema è stato affrontato con una macchina dedicata in funzione. La funzionalità rivendicabile è un miglioramento rispetto al processo manuale mediante il controllo di accesso utente incorporato e l'identificazione visiva, se disponibile. Quando non viene rivendicato, viene eseguito il deprovisioning della macchina virtuale per ridurre i costi.
- Disporre di un disco dati collegato a una macchina virtuale. Ogni disco fino a 1 TB di dati consente di passare un grande volume di dati senza dover copiare o duplicare i dati. La macchina virtuale viene inizialmente creata con un disco collegato con un grande volume di dati.  Qualsiasi utente potrebbe quindi richiedere la macchina e accedere ai dati. Al termine, annullare l'autorizzazione della macchina virtuale per consentire ad altri utenti di connettersi alla macchina.

Ci sono alcuni avvertimenti per l'utilizzo di macchine rivendicabili, più comunemente intorno guadagnando l'accesso alla macchina. Se il computer fa parte di un dominio, l'utente che dichiara che il computer dovrà avere già ricevuto l'accesso, in genere viene eseguito concedendo l'accesso a un gruppo che include tutti gli utenti nel lab quando viene creata la macchina virtuale. Se il computer non fa parte di un dominio, sarà necessario eseguire l'elemento **Reimposta password macchina virtuale** nel repository pubblico per aggiungere l'utente come amministratore.  Gli artefatti possono essere applicati anche dopo che la macchina è stata avviata o rivendicata.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente: [Creare e gestire macchine virtuali udibili in Azure DevTest LabsSee](devtest-lab-add-claimable-vm.md) the following article: Create and manage claimable VMs in Azure DevTest Labs
