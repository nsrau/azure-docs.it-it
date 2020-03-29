---
title: Ruoli e attività del processo di data science per i team
description: Struttura dei componenti chiave, dei ruoli del personale e delle attività associate per un gruppo di data science.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720011"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Ruoli e attività del processo di data science per i team

Il Team Data Science Process (TDSP) è un framework sviluppato da Microsoft che fornisce una metodologia strutturata per creare in modo efficiente soluzioni di analisi predittiva e applicazioni intelligenti. In questo articolo vengono descritti i ruoli chiave del personale e le attività associate per un team di data science che standardizza su questo processo.

Questo articolo introduttivo contiene collegamenti a esercitazioni su come configurare l'ambiente TDSP. Le esercitazioni forniscono indicazioni dettagliate per l'uso di progetti DevOps di Azure, repository di Azure e schede di Azure.The tutorials provide detailed guidance for using Azure DevOps Projects, Azure Repos repositories, and Azure Boards.  L'obiettivo motivante è passare dal concetto alla modellazione e alla distribuzione.

Le esercitazioni usano DevOps di Azure perché è come implementare TDSP in Microsoft.The tutorials use Azure DevOps because that is how to implement TDSP at Microsoft. Azure DevOps facilita la collaborazione integrando la sicurezza basata sui ruoli, la gestione e il rilevamento degli elementi di lavoro e l'hosting, la condivisione e il controllo del codice. Le esercitazioni usano anche una macchina virtuale di analisi scientifica dei dati (DSVM, [Data Science Virtual Machine)](https://aka.ms/dsvm) di Azure come desktop di analisi, che include diversi strumenti di analisi scientifica dei dati preconfigurati e integrati con il software Microsoft e i servizi di Azure.The tutorials also use an Azure Data Science Virtual Machine (DSVM) as the analytics desktop, which has several popular data science tools pre-configured and integrated with Microsoft software and Azure services. 

È possibile usare le esercitazioni per implementare TDSP usando altri strumenti e ambienti di hosting del codice, pianificazione agile e sviluppo, ma alcune funzionalità potrebbero non essere disponibili.

## <a name="structure-of-data-science-groups-and-teams"></a>Struttura dei gruppi e dei team di data science

Le funzioni di data science nelle aziende sono spesso organizzate nella gerarchia seguente:Data science functions in enterprises are often organized in the following hierarchy:

- Gruppo di analisi scientifica dei datiData science group
  - Team/i di data science all'interno del gruppo

In una tale struttura, ci sono contatti di gruppo e lead di squadra. In genere, un progetto di data science viene eseguito da un team di data science. I team di data science hanno responsabili del progetto per le attività di gestione e governance dei progetti, nonché singoli data scientist e ingegneri per eseguire le parti di data science e di progettazione dei dati del progetto. La configurazione e la governance del progetto iniziali vengono eseguite dai responsabili del gruppo, del team o del progetto.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definizione e attività per i quattro ruoli TDSP
Presupponendo che l'unità di data science sia costituita da team all'interno di un gruppo, esistono quattro ruoli distinti per il personale TDSP:

1. **Group Manager**: Gestisce l'intera unità di data science in un'azienda. Un'unità di data science può comprendere più team, ognuno dei quali lavora a più progetti di data science in segmenti verticali aziendali distinti. Un responsabile del gruppo può delegare le attività a un sostituto, ma le attività associate al ruolo non cambiano.
   
2. **Team Lead**: Gestisce un team nell'unità di data science di un'azienda. Un team è costituito da più data scientist. Per una piccola unità di data science, il responsabile del gruppo e il responsabile del team potrebbero essere la stessa persona.
   
3. **Project Lead**: Gestisce le attività quotidiane dei singoli data scientist su uno specifico progetto di data science.
   
4. **Project Individual Contributors**: Data Scientists, Business Analysts, Data Engineers, Architetti e altri utenti che eseguono un progetto di data science.

> [!NOTE]
> A seconda della struttura e delle dimensioni di un'impresa, una singola persona può svolgere più di un ruolo o più di una persona può ricoprire un ruolo.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Compiti che devono essere completati dai quattro ruoli

Nel diagramma seguente vengono illustrate le attività di primo livello per ogni ruolo Del processo di analisi scientifica dei dati del team. Questo schema e la seguente struttura più dettagliata delle attività per ogni ruolo TDSP consentono di scegliere l'esercitazione necessaria in base alle proprie responsabilità.

![Panoramica di ruoli e attività](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Attività del responsabile del gruppo

Group Manager o un amministratore di sistema TDSP designato completa le seguenti attività per adottare il TDSP:

- Crea **un'organizzazione** DevOps di Azure e un progetto di gruppo all'interno dell'organizzazione. 
- Crea un repository di modelli di **progetto** nel progetto di gruppo DevOps di Azure ed esegue il seeding dal repository dei modelli di progetto sviluppato dal team di Microsoft TDSP. L'archivio dei modelli di progetto Microsoft TDSP fornisce:
  - Struttura di **directory standardizzata**, incluse le directory per dati, codice e documenti.
  - Un set di modelli di **documento standardizzati** per guidare un processo di analisi scientifica dei dati efficiente.
- Crea un repository di **utilità**e ne esegue il seeding dal repository di utilità sviluppato dal team di Microsoft TDSP. L'archivio utilità TDSP di Microsoft fornisce una serie di utilità utili per rendere più efficiente il lavoro di un data scientist. Il repository delle utilità Microsoft include utilità per l'esplorazione interattiva dei dati, l'analisi, la creazione di report e la modellazione e la creazione di report di base.
- Imposta i **criteri di controllo** di sicurezza per l'account dell'organizzazione.

Per istruzioni dettagliate, vedere Attività di Group Manager per un team di [data science.](group-manager-tasks.md)

## <a name="team-lead-tasks"></a>Attività del responsabile del team

Il responsabile del team o un amministratore di progetto designato completa le attività seguenti per adottare il TDSP:

- Crea un **progetto** team nell'organizzazione DevOps di Azure del gruppo.
- Crea il repository dei modelli di progetto nel progetto ed esegue il seeding dal repository dei modelli di progetto del gruppo impostato dal responsabile del **gruppo** o dal delegato.
- Crea il **repository dell'utilità del team,** ne esegue il seeding dal repository dell'utilità di gruppo e aggiunge utilità specifiche del team al repository.
- Facoltativamente, crea [l'archiviazione dei file](https://azure.microsoft.com/services/storage/files/) di Azure per archiviare asset di dati utili per il team. Gli altri membri del team possono montare questo archivio file su cloud condiviso sui loro desktop di analisi.
- Facoltativamente, monta l'archiviazione dei file di Azure nella **DSVM** del team e vi aggiunge gli asset di dati del team.
- Imposta il controllo di **sicurezza** aggiungendo membri del team e configurando ne sono le autorizzazioni.

Per istruzioni dettagliate, vedere [Attività di responsabile team per un team](team-lead-tasks.md)di data science.


## <a name="project-lead-tasks"></a>Attività del responsabile di progetto

Il responsabile del progetto completa le seguenti attività per adottare il TDSP:

- Crea un repository di **progetto** nel progetto team ed esegue il seeding dal repository dei modelli di progetto.
- Facoltativamente, crea **l'archiviazione dei file** di Azure per archiviare gli asset di dati del progetto.
- Facoltativamente, monta l'archiviazione dei file di Azure nella **dSVM** e vi aggiunge gli asset di dati del progetto.
- Imposta il controllo di **sicurezza** aggiungendo membri del progetto e configurandone le autorizzazioni.

Per istruzioni dettagliate, vedere Attività di project lead per un team di [data science.](project-lead-tasks.md)

## <a name="project-individual-contributor-tasks"></a>Attività del singolo collaboratore di progetto

Il collaboratore individuale del progetto, in genere un data scientist, esegue le attività seguenti utilizzando il TDSP:

- Clona il repository del **progetto** impostato dal responsabile del progetto.
- Facoltativamente, il team condiviso e il progetto **Archiviazione file di Azure** nella macchina virtuale di data **science** (DSVM).
- Esegue il progetto.

Per istruzioni dettagliate sull'onboarding di un progetto, vedere [Attività Collaboratore Individuale di progetto per un team](project-ic-tasks.md)di data science.

## <a name="data-science-project-execution-workflow"></a>Flusso di lavoro di esecuzione del progetto di data scienceData science project execution workflow

Seguendo le esercitazioni, gli data scientist, i responsabili del progetto e i responsabili del team pertinenti è possibile creare elementi di lavoro per tenere traccia di tutte le attività e le fasi del progetto dall'inizio alla fine. L'uso di Azure Repos promuove la collaborazione tra i data scientist e garantisce che gli elementi generati durante l'esecuzione del progetto siano controllati dalla versione e condivisi da tutti i membri del progetto. DevOps di Azure consente di collegare gli elementi di lavoro di Azure Boards ai rami del repository di Azure Repos e di tenere facilmente traccia delle operazioni eseguite per un elemento di lavoro.

Nella figura seguente viene descritto il flusso di lavoro TDSP per l'esecuzione del progetto:

![Flusso di lavoro tipico del progetto di data science](./media/roles-tasks/overview-project-execute.png)

I passaggi del flusso di lavoro possono essere raggruppati in tre attività:

- Pianificazione dello sprint di condotta dei lead di progetto
- Gli scienziati dei `git` dati sviluppano artefatti sui rami per indirizzare gli elementi di lavoroData Scientists develop artifacts on branches to address work items
- I responsabili del progetto o altri membri del team fanno revisioni del codice e uniscono rami di lavoro al ramo master

Per istruzioni dettagliate sul flusso di lavoro di esecuzione dei progetti, vedere [Sviluppo Agile di progetti di data science](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repository dei modelli di progetto TDSP

Utilizzare [l'archivio](https://github.com/Azure/Azure-TDSP-ProjectTemplate) dei modelli di progetto del team Microsoft TDSP per supportare l'esecuzione e la collaborazione efficienti del progetto. Il repository offre una struttura di directory standardizzata e modelli di documento che è possibile utilizzare per i propri progetti TDSP.

## <a name="next-steps"></a>Passaggi successivi

Per descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team, vedere:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Attività di Project Individual Contributor per un team di data scienceProject Individual Contributor tasks for a data science team](project-ic-tasks.md)
