---
title: Sviluppo Agile di progetti di data science - Processo di data science per i team
description: Eseguire un progetto di data science in modo sistematico, controllato dalla versione e collaborativo all'interno di un team di progetto utilizzando il processo di analisi scientifica dei dati del team.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722102"
---
# <a name="agile-development-of-data-science-projects"></a>Sviluppo Agile di progetti di data science

Questo documento descrive come gli sviluppatori possono eseguire un progetto di data science in modo sistematico, collaborativo e con controllo della versione all'interno di un team di progetto tramite il [processo di data science per i team](overview.md) (TDSP, Team Data Science Process). TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva basate su cloud. Per una descrizione dei ruoli e delle attività gestiti da un team di data science che standardizza il TDSP, vedere [Team Data Science Process roles and tasks](roles-tasks.md). 

Questo articolo comprende istruzioni su come: 

- Eseguire *la pianificazione dello sprint* per gli elementi di lavoro coinvolti in un progetto.
- Aggiungere elementi di *lavoro* agli sprint.
- Creare e utilizzare un modello di elemento di *lavoro derivato dall'agile in* linea in modo specifico con le fasi del ciclo di vita TDSP.

The following instructions outline the steps needed to set up a TDSP team environment using Azure Boards and Azure Repos in Azure DevOps. Le istruzioni usano DevOps di Azure perché è come implementare TDSP in Microsoft.The instructions use Azure DevOps because that is how to implement TDSP at Microsoft. Se il gruppo utilizza una piattaforma di hosting del codice diversa, le attività di gestione del team in genere non cambiano, ma il modo per completare le attività è diverso. Ad esempio, il collegamento di un elemento di lavoro con un ramo Git potrebbe non essere lo stesso con GitHub come in Azure Repos.For example, linking a work item with a Git branch might not be the same with GitHub as it is with Azure Repos.

Nella figura seguente viene illustrato un tipico flusso di lavoro di pianificazione dello sprint, codifica e controllo del codice sorgente per un progetto di data science:The following figure illustrates a typical sprint planning, coding, and source-control workflow for a data science project:

![Processo di analisi scientifica dei dati per i team](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Tipi di elementi di lavoro

Nel framework di pianificazione dello sprint TDSP sono disponibili quattro tipi di elementi di *lavoro* utilizzati di frequente: *Funzionalità*, *Storie utente*, *Attività*e *Bug*. Il backlog per tutti gli elementi di lavoro è a livello di progetto, non a livello di repository Git. 

Di seguito sono riportate le definizioni per i tipi di elemento di lavoro:

- **Funzionalità**: Una funzionalità corrisponde a un impegno di progetto. Impegni diversi con un cliente sono caratteristiche diverse ed è meglio considerare le diverse fasi di un progetto come funzionalità diverse. Se si sceglie uno schema, ad * \<esempio ClientName>-\<EngagementName>* per assegnare un nome alle funzionalità, è possibile riconoscere facilmente il contesto del progetto e l'impegno dai nomi stessi.
  
- **Storia utente**: Le storie utente sono elementi di lavoro necessari per completare una funzionalità end-to-end. Esempi di storie utente includono:
  - Recuperare i dati 
  - Esplorare i dati 
  - Generare funzionalità
  - Creare modelli
  - Rendere operativi i modelli 
  - Ripetere il training dei modelli
  
- **Attività:** le attività sono elementi di lavoro assegnabili che devono essere eseguiti per completare una User Story specifica. Ad esempio, le attività nella User story *Scarica dati* potrebbero essere:
  - Ottenere le credenziali di SQL ServerGet SQL Server credentials
  - Caricare dati in SQL Data Warehouse
  
- **Bug:** i bug sono problemi nel codice o nei documenti esistenti che devono essere corretti per completare un'attività. Se i bug sono causati da elementi di lavoro mancanti, è possibile che possano essere storie utente o attività. 

Gli scienziati dei dati possono sentirsi più a proprio agio utilizzando un modello agile che sostituisce le funzionalità, le storie utente e le attività con le fasi del ciclo di vita e i sottofasi TDSP. Per creare un modello derivato dall'agile in linea con le fasi del ciclo di vita TDSP, vedere Utilizzare un modello di [lavoro TDSP agile.](#set-up-agile-dsp-6)

> [!NOTE]
> TDSP prende in prestito i concetti di funzionalità, storie utente, attività e bug dalla gestione del codice software (SCM). I concetti TDSP potrebbero differire leggermente dalle definizioni SCM convenzionali.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Pianificare gli sprint

Molti data scientist sono impegnati in più progetti, che possono richiedere mesi per essere completati e procedere a ritmi diversi. La pianificazione dello sprint è utile per la definizione della priorità e per l'allocazione e la pianificazione delle risorse. Nelle bacheche di Azure è possibile creare, gestire e tenere traccia facilmente degli elementi di lavoro per i progetti ed eseguire la pianificazione dello sprint per garantire che i progetti procedano come previsto.

Per ulteriori informazioni sulla pianificazione dello sprint, vedere [Scrum sprints](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Per altre informazioni sulla pianificazione dello sprint nelle bacheche di Azure, vedere [Assegnare elementi di backlog a uno sprint.](/azure/devops/boards/sprints/assign-work-sprint) 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Aggiungere una funzionalità al backlog 

Dopo aver creato il repository del codice del progetto e del progetto, è possibile aggiungere una funzionalità al backlog per rappresentare il lavoro per il progetto.

1. Dalla pagina del progetto, seleziona**Backlog** **delle bacheche** > nel riquadro di spostamento sinistro. 
   
1. Nella scheda **Backlog,** se il tipo di elemento di lavoro nella barra superiore è **Storie**, è elenco a discesa e selezionare **Funzionalità**. Selezionare quindi **Nuovo elemento di lavoro.**
   
   ![Seleziona nuovo elemento di lavoro](./media/agile-development/2-sprint-team-overview.png)
   
1. Immettere un titolo per la funzionalità, in genere il nome del progetto, quindi selezionare **Aggiungi all'inizio**. 
   
   ![Inserisci un titolo e seleziona Aggiungi all'inizio](./media/agile-development/3-sprint-team-add-work.png)
   
1. Nell'elenco **Backlog** selezionare e aprire la nuova funzionalità. Compilare la descrizione, assegnare un membro del team e impostare i parametri di pianificazione. 
   
   È anche possibile collegare la funzionalità al repository di codice di Azure Repos del progetto selezionando Aggiungi collegamento nella sezione Sviluppo.You can also link the Feature to the project's Azure Repos code repository by selecting **Add link** under the **Development** section. 
   
   Dopo aver modificato la feature, selezionare **Salva & Chiudi**.
   
   ![Modifica feature e seleziona Salva & Chiudi](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Aggiunta di una User story alla funzionalità 

Sotto la funzionalità, è possibile aggiungere Storie utente per descrivere i passaggi principali necessari per completare il progetto. 

Per aggiungere una nuova User story a una feature:

1. Nella scheda **Backlog** selezionare la **+** casella a sinistra della caratteristica. 
   
   ![Aggiungere una nuova User story sotto la funzionalità](./media/agile-development/4-sprint-add-story.png)
   
1. Assegnare un titolo alla User Story e modificare dettagli quali assegnazione, stato, descrizione, commenti, pianificazione e priorità. 
   
   È anche possibile collegare la User Story a un ramo del repository di codice di Azure Repos del progetto selezionando Aggiungi collegamento nella sezione Sviluppo.You can also link the User Story to a branch of the project's Azure Repos code repository by selecting **Add link** under the **Development** section. Selezionare il repository e il ramo a cui si desidera collegare l'elemento di lavoro, quindi scegliere **OK**.
   
   ![Aggiungi collegamento](./media/agile-development/5-sprint-edit-story.png)
   
1. Al termine della modifica della User story, selezionare **Salva & Chiudi**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Aggiunta di un'attività a una User story 

Le attività sono passaggi dettagliati specifici necessari per completare ogni User Story. Una volta completate tutte le attività di una User story, anche la User story deve essere completata. 

Per aggiungere un'attività a una **+** User story, selezionate la casella accanto all'elemento User story e selezionate **Attività**. Inserisci il titolo e altre informazioni nell'attività.

![Aggiunta di un'attività a una User story](./media/agile-development/7-sprint-add-task.png)

Dopo aver creato Feature, Storie utente e Attività, è possibile visualizzarle nelle **visualizzazioni Backlog** o **Bacheche** per tenere traccia del relativo stato.

![Visualizzazione Backlog](./media/agile-development/8-sprint-backlog-view.png)

![Visualizzazione Bacheche](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Utilizzare un modello di lavoro TDSP agile

Gli scienziati dei dati possono sentirsi più a proprio agio utilizzando un modello agile che sostituisce le funzionalità, le storie utente e le attività con le fasi del ciclo di vita e i sottofasi TDSP. Nelle bacheche di Azure è possibile creare un modello derivato dall'agile che usa le fasi del ciclo di vita TDSP per creare e tenere traccia degli elementi di lavoro. I passaggi seguenti illustrano l'impostazione di un modello di processo Agile specifico per l'analisi scientifica dei dati e la creazione di elementi di lavoro di data science basati sul modello.

### <a name="set-up-an-agile-data-science-process-template"></a>Impostare un modello di processo di analisi scientifica dei dati AgileSet up an Agile Data Science Process template

1. Nella pagina principale dell'organizzazione DevOps di Azure selezionare **Impostazioni organizzazione** nel riquadro di spostamento sinistro. 
   
1. Nel riquadro di spostamento sinistro **Impostazioni organizzazione,** in **Bacheche**, selezionare **Elabora**. 
   
1. Nel riquadro **Tutti i processi** selezionare il **pulsante ...** accanto a **Agile**, quindi selezionare Crea **processo ereditato**.
   
   ![Creare un processo ereditato da AgileCreate inherited process from Agile](./media/agile-development/10-settings.png) 
   
1. Nella finestra di dialogo **Crea processo ereditato da Agile** immettere il nome *AgileDataScienceProcess*e selezionare **Crea processo**.
   
   ![Creare un processo AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. In **Tutti i processi**selezionare il nuovo **AgileDataScienceProcess**. 
   
1. Nella scheda **Tipi di elemento** di lavoro disabilitare **Epic**, **Feature**, **User Story**e **Task** selezionando il **...** accanto a ogni elemento e quindi **Disabilita**. 
   
   ![Disabilitare i tipi di elemento di lavoro](./media/agile-development/12-disable.png)
   
1. In **Tutti i processi**selezionare la scheda Livelli **backlog.** In **Backlog portfolio**selezionare il **pulsante ...** accanto a **Epico (disabilitato)** e quindi **Modifica/Rinomina**. 
   
1. Nella finestra di dialogo **Modifica livello di backlog:In** the Edit backlog level dialog box:
   1. In **Nome**sostituire **Epic** con *Progetti TDSP*. 
   1. In Tipi di elemento di lavoro a questo livello di **backlog**selezionare **Nuovo tipo di elemento**di lavoro , immettere *Progetto TDSP*, quindi **Aggiungi**. 
   1. In Tipo di **elemento di lavoro predefinito**, selezionare E selezionare Progetto **TDSP**. 
   1. Selezionare **Salva**.
   
   ![Impostare il livello di backlog del portfolio](./media/agile-development/13-rename.png)  
   
1. Seguire la stessa procedura per rinominare **le entità geografiche** in *Fasi TDSP*e aggiungere i nuovi tipi di elemento di lavoro seguenti:
   
   - *Comprensione aziendale*
   - *Acquisizione dei dati*
   - *Modellazione*
   - *Distribuzione*
   
1. In **Backlog dei**requisiti , rinominare **le storie** in *sottostature TDSP*, aggiungere il nuovo tipo di elemento di lavoro *TDSP Substage*e impostare il tipo di elemento di lavoro predefinito su **TDSP Substage**.
   
1. In **Backlog iterazione**aggiungere un nuovo tipo di elemento di lavoro *TDSP Task*e impostarlo come tipo di elemento di lavoro predefinito. 
   
Dopo aver completato i passaggi, i livelli di backlog dovrebbero essere simili al seguente:After you complete the steps, the backlog levels should look like this:
   
 ![Livelli di backlog del modello TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Creare elementi di lavoro Processo di analisi scientifica dei dati AgileCreate Agile Data Science Process work items

È possibile utilizzare il modello di processo di data science per creare progetti TDSP e tenere traccia degli elementi di lavoro che corrispondono alle fasi del ciclo di vita TDSP.

1. Dalla pagina principale dell'organizzazione DevOps di Azure selezionare **Nuovo progetto**. 
   
1. Nella finestra di dialogo **Crea nuovo progetto** assegnare un nome al progetto e quindi selezionare **Avanzate**. 
   
1. In **Processo elemento**di lavoro , selezionare e selezionare **AgileDataScienceProcess**, quindi scegliere **Crea**.
   
   ![Creare un progetto di TDSP](./media/agile-development/15-newproject.png)
   
1. Nel progetto appena creato, selezionare **Schede** > **Backlog** nel riquadro di spostamento sinistro.
   
1. Per rendere visibili i progetti TDSP, selezionare l'icona **Configura impostazioni team.** Nella schermata **Impostazioni** selezionare la casella di controllo **Progetti TDSP** e quindi selezionare **Salva e chiudi**.
   
   ![Casella di controllo Seleziona progetti TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Per creare un progetto TDSP specifico per l'analisi scientifica dei dati, selezionare **Progetti TDSP** nella barra superiore e quindi **Nuovo elemento**di lavoro . 
   
1. Nella finestra popup assegnare un nome all'elemento di lavoro Progetto TDSP e selezionare **Aggiungi all'inizio**.
   
   ![Creare un elemento di lavoro del progetto di data scienceCreate data science project work item](./media/agile-development/17-dsworkitems0.png)
   
1. Per aggiungere un elemento di lavoro nel **+** progetto TDSP, selezionare il accanto al progetto e quindi selezionare il tipo di elemento di lavoro da creare. 
   
   ![Selezionare il tipo di elemento di lavoro Di data scienceSelect data science work item type](./media/agile-development/17-dsworkitems1.png)
   
1. Compilare i dettagli del nuovo elemento di lavoro e selezionare **Salva & Chiudi**.
   
1. Continuare a **+** selezionare i simboli accanto agli elementi di lavoro per aggiungere nuove fasi, sottostature e attività TDSP. 
   
Di seguito è riportato un esempio di come gli elementi di lavoro del progetto di data science devono essere visualizzati nella visualizzazione **Backlog:Here** is an example of how the data science project work items should appear in Backlog view:

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Passaggi successivi

[La codifica collaborativa con Git](collaborative-coding-with-git.md) descrive come eseguire lo sviluppo di codice collaborativo per i progetti di data science usando Git come framework di sviluppo di codice condiviso e come collegare queste attività di codifica al lavoro pianificato con il processo agile.

[Procedure dettagliate](walkthroughs.md) di esempio elencano le procedure dettagliate di scenari specifici, con collegamenti e descrizioni delle anteprime. Gli scenari collegati illustrano come combinare strumenti e servizi cloud e locali in flussi di lavoro o pipeline per creare applicazioni intelligenti.
  
Risorse aggiuntive sui processi agile:Additional resources on agile processes:

- [Processo agile](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Tipi di elementi di lavoro e flusso di lavoro del processo Agile](/azure/devops/boards/work-items/guidance/agile-process-workflow)

