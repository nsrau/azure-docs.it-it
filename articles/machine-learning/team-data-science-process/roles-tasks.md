---
title: Ruoli e attività del processo di data science per i team
description: Un contorno dei componenti chiave, dei ruoli del personale e delle attività associate per un gruppo di data science.
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

Il team Data Science Process (TDSP) è un framework sviluppato da Microsoft che fornisce una metodologia strutturata per compilare in modo efficiente soluzioni di analisi predittiva e applicazioni intelligenti. In questo articolo vengono illustrati i ruoli chiave del personale e le attività associate per un team data science la standardizzazione di questo processo.

Questo articolo introduttivo include collegamenti alle esercitazioni su come configurare l'ambiente TDSP. Le esercitazioni forniscono istruzioni dettagliate per l'uso di Azure DevOps Projects, repository Azure Repos e Azure Boards.  L'obiettivo di motivazione è passare dal concetto alla modellazione e alla distribuzione.

Le esercitazioni usano Azure DevOps perché questo è l'implementazione di TDSP in Microsoft. Azure DevOps semplifica la collaborazione grazie all'integrazione della sicurezza basata sui ruoli, alla gestione e al rilevamento degli elementi di lavoro e all'hosting del codice, alla condivisione e al controllo del codice sorgente. Le esercitazioni usano anche un [Data Science Virtual Machine](https://aka.ms/dsvm) di Azure (DSVM) come desktop di analisi, che include diversi strumenti di Data Science più diffusi preconfigurati e integrati con il software Microsoft e i servizi di Azure. 

È possibile usare le esercitazioni per implementare TDSP usando altri strumenti e ambienti di hosting di codice, pianificazione agile e sviluppo, ma alcune funzionalità potrebbero non essere disponibili.

## <a name="structure-of-data-science-groups-and-teams"></a>Struttura di gruppi e team di data science

Le funzioni di Data Science nelle aziende sono spesso organizzate nella gerarchia seguente:

- Gruppo Data Science
  - Team di Data Science all'interno del gruppo

In una struttura di questo tipo sono presenti lead di gruppo e lead del team. In genere, un progetto di data science viene eseguito da un team di data science. I team di Data Science hanno lead di progetto per le attività di gestione del progetto e di governance e singoli data scientist e tecnici per eseguire le parti di data science e di progettazione dei dati del progetto. La configurazione e la governance iniziali del progetto vengono eseguite dal gruppo, dal team o dai lead del progetto.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definizione e attività per i quattro ruoli TDSP
Supponendo che l'unità di data science sia costituita da team all'interno di un gruppo, sono disponibili quattro ruoli distinti per il personale TDSP:

1. **Gestione gruppo**: gestisce l'intera unità di Data Science in un'organizzazione. Un'unità di data science può comprendere più team, ognuno dei quali lavora a più progetti di data science in segmenti verticali aziendali distinti. Un responsabile del gruppo può delegare le attività a un sostituto, ma le attività associate al ruolo non cambiano.
   
2. **Responsabile del team**: gestisce un team nell'unità di Data Science di un'azienda. Un team è costituito da più data scientist. Per una piccola unità di data science, il responsabile del gruppo e il responsabile del team possono essere la stessa persona.
   
3. **Responsabile del progetto**: gestisce le attività quotidiane dei singoli data scientist in un progetto di Data Science specifico.
   
4. **Singoli collaboratori del progetto**: data scientist, Business Analyst, data Engineers, architetti e altri utenti che eseguono un progetto Data Science.

> [!NOTE]
> A seconda della struttura e della dimensione di un'organizzazione, è possibile che una singola persona riproduca più di un ruolo o che più di una persona possa occupare un ruolo.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Attività da completare con i quattro ruoli

Il diagramma seguente illustra le attività di primo livello per ogni ruolo del processo di Data Science per i team. Questo schema e la seguente struttura più dettagliata delle attività per ogni ruolo TDSP possono aiutare a scegliere l'esercitazione necessaria in base alle proprie responsabilità.

![Panoramica di ruoli e attività](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Attività del responsabile del gruppo

Il responsabile del gruppo o un amministratore di sistema TDSP designato completa le attività seguenti per adottare il TDSP:

- Consente di creare un' **organizzazione** DevOps di Azure e un progetto di gruppo all'interno dell'organizzazione. 
- Consente di creare un **repository di modelli di progetto** nel progetto di gruppo Azure DevOps e di deselezionarlo dal repository dei modelli di progetto sviluppato dal team Microsoft TDSP. Il repository di modelli di progetto Microsoft TDSP fornisce:
  - **Struttura di directory standardizzata**, incluse le directory per i dati, il codice e i documenti.
  - Un set di **modelli di documento standardizzati** per guidare un processo di Data Science efficiente.
- Consente di creare un **repository di utilità**e di inizializzarlo dal repository di utilità sviluppato dal team TDSP di Microsoft. Il repository di utilità TDSP di Microsoft fornisce un set di utilità utili per rendere più efficiente il lavoro di un data scientist. Il repository Microsoft Utility include utilità per l'esplorazione interattiva dei dati, l'analisi, la creazione di report e la modellazione e la creazione di report di base.
- Imposta i **criteri di controllo di sicurezza** per l'account dell'organizzazione.

Per istruzioni dettagliate, vedere [attività del responsabile del gruppo per un team di Data Science](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Attività del responsabile del team

Il responsabile del team o un amministratore di progetto designato completa le attività seguenti per adottare il TDSP:

- Consente di creare un **progetto** team nell'organizzazione DevOps di Azure del gruppo.
- Crea il **repository del modello di progetto** nel progetto e ne effettua il seeding dal repository dei modelli di progetto di gruppo impostato dal gestore del gruppo o dal delegato.
- Consente di creare il **repository di utilità team**, di eseguire il seeding dal repository di utilità del gruppo e di aggiungere al repository utilità specifiche del team.
- Crea facoltativamente [archiviazione file di Azure](https://azure.microsoft.com/services/storage/files/) per archiviare gli asset di dati utili per il team. Gli altri membri del team possono montare questo archivio file su cloud condiviso sui loro desktop di analisi.
- Facoltativamente, monta l'archiviazione file di Azure nel **DSVM** del team e aggiunge gli asset di dati del team.
- Imposta il **controllo di sicurezza** aggiungendo i membri del team e configurando le autorizzazioni.

Per istruzioni dettagliate, vedere [attività del responsabile del team per un team di Data Science](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Attività del responsabile di progetto

Il responsabile del progetto completa le attività seguenti per adottare il TDSP:

- Consente di creare un **repository del progetto** nel progetto team e di inizializzarlo dal repository del modello di progetto.
- Crea facoltativamente **archiviazione file di Azure** per archiviare gli asset di dati del progetto.
- Facoltativamente, monta la risorsa di archiviazione file di Azure in **DSVM** e aggiunge gli asset di dati del progetto.
- Imposta il **controllo di sicurezza** aggiungendo i membri del progetto e configurando le relative autorizzazioni.

Per istruzioni dettagliate, vedere [attività del responsabile di progetto per un team di Data Science](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Attività del singolo collaboratore di progetto

Il progetto singolo collaboratore, in genere un data scientist, esegue le attività seguenti usando TDSP:

- Clona il **repository di progetto** configurato dal responsabile del progetto.
- Facoltativamente, consente di montare il team condiviso e il progetto di **archiviazione file di Azure** nel relativo **Data Science Virtual Machine** (DSVM).
- Esegue il progetto.

Per istruzioni dettagliate sul caricamento di un progetto, vedere [attività dei singoli collaboratori del progetto per un team di Data Science](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Flusso di lavoro di esecuzione del progetto di Data Science

Seguendo le esercitazioni pertinenti, i data scientist, i lead del progetto e i lead del team possono creare elementi di lavoro per tenere traccia di tutte le attività e le fasi per il progetto dall'inizio alla fine. L'uso di Azure Repos promuove la collaborazione tra i data scientist e garantisce che gli artefatti generati durante l'esecuzione del progetto siano controllati dalla versione e condivisi da tutti i membri del progetto. Azure DevOps consente di collegare gli elementi di lavoro Azure Boards ai rami del repository Azure Repos e di tenere traccia facilmente delle operazioni eseguite per un elemento di lavoro.

La figura seguente illustra il flusso di lavoro TDSP per l'esecuzione del progetto:

![Flusso di lavoro tipico del progetto data science](./media/roles-tasks/overview-project-execute.png)

I passaggi del flusso di lavoro possono essere raggruppati in tre attività:

- I lead di progetto eseguono la pianificazione dello sprint
- I data scientist sviluppano gli `git` artefatti nei rami per indirizzare gli elementi di lavoro
- I lead del progetto o altri membri del team eseguono revisioni del codice e uniscono rami di lavoro al ramo master

Per istruzioni dettagliate sul flusso di lavoro di esecuzione del progetto, vedere [sviluppo agile di progetti Data Science](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repository di modelli di progetto TDSP

Usare il repository di modelli di [progetto](https://github.com/Azure/Azure-TDSP-ProjectTemplate) del team di Microsoft TDSP per supportare l'esecuzione e la collaborazione efficaci del progetto. Il repository offre una struttura di directory e modelli di documenti standardizzati che è possibile usare per i progetti TDSP.

## <a name="next-steps"></a>Passaggi successivi

Per descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team, vedere:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Attività dei singoli collaboratori del progetto per un team di data science](project-ic-tasks.md)
