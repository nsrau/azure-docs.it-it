---
title: "Ruoli e attività del processo di data science per i team - Azure | Microsoft Docs"
description: "Una struttura dei componenti chiave, ruoli del personale e attività associate per un progetto team di data science."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b502a586cdb7351d5b22c6b0db966723b8ebb7b4
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="team-data-science-process-roles-and-tasks"></a>Ruoli e attività del processo di data science per i team

Il processo di data science per i team è un framework sviluppato da Microsoft che fornisce una metodologia strutturata per creare con efficienza soluzioni di analisi predittiva e applicazioni intelligenti. Questo articolo descrive i ruoli chiave del personale e le loro attività associate gestite da un team di data science che usa questo processo come standard. 

Questa introduzione è collegata a esercitazioni che forniscono istruzioni su come configurare l'ambiente TDSP per l'intero gruppo di data science, i team di data science e i progetti. Vengono fornite istruzioni dettagliate sull'uso di Visual Studio Team Services (VSTS) nelle esercitazioni come piattaforma di hosting del codice e strumento di pianificazione agile per gestire le attività del team, controllare l'accesso e gestire i repository. 

Sarà possibile usare queste informazioni anche per implementare TDSP nel proprio strumento di hosting del codice e pianificazione. 

## <a name="structures-of-data-science-groups-and-teams"></a>Strutture dei gruppi e team di data science
Le funzioni di data science nelle aziende sono spesso organizzate nella gerarchia seguente:

1. ***Uno o più gruppi di data science***

2. ***Uno o più team di data science interni a uno o più gruppi***

In tale struttura ci sono responsabili di gruppo e team. In genere, un progetto di data science viene eseguito da un team di data science, che può essere composto da responsabili del progetto (per le attività di gestione e governance del progetto) e data scientist o tecnici (singoli collaboratori o personale tecnico) che si occuperanno delle parti di data science e ingegneria dei dati del progetto. Prima dell'esecuzione, la configurazione e la governance sono eseguite dai responsabili di gruppo, team o progetto.

## <a name="definition-of-four-tdsp-roles"></a>Definizione di quattro ruoli TDSP
Sulla base del presupposto precedente, sono stati specificati quattro ruoli distinti per il personale del team:

1. ***Responsabile del gruppo***. Il responsabile del gruppo è responsabile dell'intera unità di data science in un'azienda. Un'unità di data science può comprendere più team, ognuno dei quali lavora a più progetti di data science in segmenti verticali aziendali distinti. Un responsabile del gruppo può delegare le attività a un sostituto, ma le attività associate al ruolo non cambiano.

2. ***Responsabile del team***. Il responsabile del team gestisce un team nell'unità di data science di un'azienda. Un team è costituito da più data scientist. Per le unità di data science con un numero ridotto di data scientist, il responsabile del gruppo e il responsabile del team possono essere la stessa persona.

3. ***Responsabile di progetto***. Un responsabile di progetto gestisce le attività quotidiane dei singoli data scientist in un progetto di data science specifico.

4. ***Singolo collaboratore di progetto***. Data scientist, analista aziendale, tecnico dei dati, architetto, etc. Un singolo collaboratore di progetto esegue un progetto di data science. 


**[AZURE.NOTE]**: a seconda della struttura aziendale, un singolo utente può coprire più ruoli O viceversa più persone possono lavorare nello stesso ruolo. Questo succede spesso nelle piccole aziende o organizzazioni che hanno un numero ridotto di persone nell'organizzazione di data science.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Attività che devono essere completare dalle quattro persone

L'immagine seguente illustra le attività di primo livello del personale, per ruolo, nell'adozione e implementazione del processo di data science del team, secondo la concettualizzazione di Microsoft. 

![Panoramica di ruoli e attività](./media/roles-tasks/overview-tdsp-top-level.png)

Questo schema e la successiva descrizione dettagliata delle attività che sono assegnate a ciascun ruolo nel TDSP consentono di scegliere l'esercitazione appropriata in base alle responsabilità dell'utente nell'organizzazione.

>[AZURE.NOTE] Nelle istruzioni seguenti vengono mostrati i passaggi per configurare un ambiente TDSP e completare altre attività di data science in Visual Studio Team Services (VSTS). Si spiega come eseguire queste attività con VSTS, perché è questo lo strumento utilizzato per implementare TDSP in Microsoft. VSTS facilita la collaborazione integrando la gestione degli elementi di lavoro che tengono traccia delle attività e un servizio di hosting del codice utilizzato per condividere le utilità, organizzare le versioni e fornire la sicurezza basata sui ruoli. Per implementare le attività illustrate dal TDSP è anche possibile scegliere altre piattaforme, se si preferisce. Tuttavia, a seconda della piattaforma, alcune funzionalità di VSTS che vengono sfruttate potrebbero non essere disponibili. 
>
>Si usa anche la [macchina virtuale per data science (DSVM)](http://aka.ms/dsvm) nel cloud di Azure come desktop di analisi con diversi strumenti di data science comuni preconfigurati e integrati con vari software Microsoft e servizi di Azure. Per implementare TDSP è possibile usare la DSVM o qualsiasi altro ambiente di sviluppo. 


## <a name="group-manager-tasks"></a>Attività del responsabile del gruppo

Il responsabile del gruppo (o un amministratore di sistema TDSP designato) deve completare le attività seguenti per adottare il TDSP:

- Creare un **account di gruppo** in una piattaforma di hosting del codice (ad esempio Github, Git, Visual Studio Team Services o altro)
- Creare un **repository dei modelli di progetto** nell'account di gruppo ed eseguirne il seeding dal repository dei modelli di progetto sviluppato dal team TDSP di Microsoft. Il repository dei modelli di progetto TDSP di Microsoft fornisce una **struttura di directory standardizzata** che comprende directory per dati, codice e documenti e fornisce un set di **modelli di documento standardizzati** per guidare in modo efficiente il processo di data scienze. 
- Creare un **repository delle utilità** ed eseguirne il seeding dal repository delle utilità sviluppato dal team TDSP di Microsoft. Il repository delle utilità TDSP di Microsoft fornisce un set di utilità per migliorare l'efficienza di lavoro di un data sientist, incluse utilità per l'esplorazione interattiva, l'analisi e la creazione di report sui dati e per la modellazione di base e la relativa creazione i report.
- Configurare i **criteri di controllo di sicurezza** di questi due repository nell'account di gruppo.  

Per istruzioni dettagliate, vedere [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Attività del responsabile del team

Il responsabile del team (o un amministratore del progetto team designato) deve completare le attività seguenti per adottare il TDSP:

- Se Visual Studio Team Services è stato scelto come piattaforma di hosting del codice per il controllo della versione e la collaborazione, creare un **progetto team** nel server VSTS del gruppo. In caso contrario, è possibile ignorare questa attività.
- Creare il **repository dei modelli del progetto team** sotto il progetto team ed eseguirne il seeding dal repository dei modelli di progetto del gruppo configurato dal responsabile del gruppo o dal delegato del responsabile. 
- Creare il **repository delle utilità del team** e aggiungere le utilità specifiche del team al repository. 
- (Facoltativo) Creare una **[risorsa di archiviazione di file di Azure](https://azure.microsoft.com/services/storage/files/)** da usare per archiviare gli asset di dati che possono essere utili per l'intero team. Gli altri membri del team possono montare questo archivio file su cloud condiviso sui loro desktop di analisi.
- (Facoltativo) Montare l'archivio file di Azure sulla **macchina virtuale per data science** (DSVM) del responsabile del team e aggiungere gli asset di dati in essa.
- Configurare il **controllo di sicurezza** aggiungendo i membri del team e configurare i loro privilegi. 

Per istruzioni dettagliate, vedere [Attività del responsabile del team per un team di data science](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Attività del responsabile di progetto

Il responsabile di progetto deve completare le attività seguenti per adottare il TDSP:

- Creare un **repository di progetto** sotto il progetto team ed eseguirne il seeding dal repository dei modelli del progetto team. 
- (Facoltativo) Creare una **risorsa di archiviazione di file di Azure** da usare per archiviare gli asset di dati per il progetto. 
- (Facoltativo) Montare l'archivio file di Azure sulla **macchina virtuale per data science** (DSVM) del responsabile di progetto e aggiungere gli asset di dati del progetto in essa.
- Configurare il **controllo di sicurezza** aggiungendo i membri del progetto e configurare i loro privilegi. 

Per istruzioni dettagliate, vedere [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Attività del singolo collaboratore di progetto

Un singolo collaboratore di progetto (in genere un data scientist) deve eseguire le seguenti attività per condurre il progetto di data science usando il TDSP:

- Clonare il **repository di progetto** configurato dal responsabile di progetto. 
- (Facoltativo) Montare l'**archivio file di Azure** condiviso del team e del progetto sulla propria **macchina virtuale per data science** (DSVM).
- Eseguire il progetto. 

 
Per istruzioni dettagliate per l'onboarding in un progetto, vedere [Singoli collaboratori al progetto per un team di data science](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Esecuzione del progetto di data science
 
Seguendo il gruppo di istruzioni pertinente, data scientist, responsabile di progetto e responsabili dei team possono creare elementi di lavoro per tenere traccia di tutte le fasi necessarie per un progetto dall'inizio alla fine. Inoltre, usando Git, si promuove la collaborazione tra i data scientist e si garantisce che gli elementi generati durante l'esecuzione del progetto ricevano un adeguato controllo della versione e siano condivisi da tutti i membri del progetto.

Le istruzioni fornite per l'esecuzione del progetto sono state sviluppate sulla base del presupposto che sia gli elementi di lavoro sia i repository Git del progetto si trovino in Visual Studio Team Services. Usando Visual Studio Team Services per entrambi è possibile collegare gli elementi di lavoro con i rami Git dei repository del progetto. In questo modo è possibile tenere traccia facilmente di ciò che è stato fatto per un elemento di lavoro. 

La figura seguente illustra questo flusso di lavoro per l'esecuzione del progetto usando il TDSP.

![Esecuzione tipica di un progetto di data science](./media/roles-tasks/overview-project-execute.png)

Il flusso di lavoro include passaggi che possono essere raggruppati in tre attività:

- Pianificazione dello sprint (responsabile di progetto)
- Sviluppo di elementi in rami Git per risolvere gli elementi di lavoro (data scientist)
- Revisione del codice e unione di rami con rami master (responsabili di progetto o altri membri del team)

Per istruzioni dettagliate sul flusso di lavoro di esecuzione del progetto, vedere [Esecuzione di progetti di data science](project-execution.md).

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Attività del responsabile del team per un team di data science](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Singoli collaboratori al progetto per un team di data science](project-ic-tasks.md)
