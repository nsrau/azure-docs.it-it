---
title: Sviluppo Agile di progetti di data science - Processo di data science per i team
description: Eseguire un progetto data science in modo sistematico, controllato dalla versione e collaborativo all'interno di un team di progetto tramite il processo di Data Science per i team.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/05/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 09c5962e62077fbecc9b327320d0bb5b88416ffa
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260676"
---
# <a name="agile-development-of-data-science-projects"></a>Sviluppo Agile di progetti di data science

Questo documento descrive come gli sviluppatori possono eseguire un progetto di data science in modo sistematico, collaborativo e con controllo della versione all'interno di un team di progetto tramite il [processo di data science per i team](overview.md) (TDSP, Team Data Science Process). TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva basate sul cloud. Per una descrizione dei ruoli e delle attività gestiti da un team data science la standardizzazione di TDSP, vedere [ruoli e attività del processo di Data Science](roles-tasks.md)per i team. 

Questo articolo comprende istruzioni su come: 

- Eseguire la *pianificazione dello sprint* per gli elementi di lavoro che coinvolgono un progetto.
- Aggiungere *elementi di lavoro* agli sprint.
- Creare e usare un *modello di elemento di lavoro derivato da agile* che è allineato in modo specifico alle fasi del ciclo di vita TDSP.

Le istruzioni seguenti illustrano i passaggi necessari per configurare un ambiente del team TDSP usando Azure Boards e Azure Repos in Azure DevOps. Le istruzioni usano Azure DevOps perché questo è l'implementazione di TDSP in Microsoft. Se il gruppo usa una piattaforma di hosting del codice diversa, le attività del responsabile del team in genere non cambiano, ma il modo per completare le attività è diverso. Ad esempio, il collegamento di un elemento di lavoro con un ramo git potrebbe non essere lo stesso con GitHub così com'è con Azure Repos.

Nella figura seguente viene illustrato un flusso di lavoro tipico per la pianificazione, la codifica e il controllo del codice sorgente per un progetto data science:

![Processo di analisi scientifica dei dati per i team](./media/agile-development/1-project-execute.png)

##  <a name='Terminology-1'></a>Tipi di elemento di lavoro

Nel Framework di pianificazione sprint TDSP sono disponibili quattro tipi di *elemento di lavoro* usati di frequente: *Funzionalità*, *storie utente*, *attività*e *bug*. Il backlog per tutti gli elementi di lavoro è a livello di progetto, non a livello di repository git. 

Di seguito sono riportate le definizioni per i tipi di elemento di lavoro:

- **Funzionalità**: Una funzionalità corrisponde a un engagement del progetto. Diverse interazioni con un client sono funzionalità diverse ed è consigliabile considerare diverse fasi di un progetto come diverse funzionalità. Se si sceglie uno schema, ad esempio  *\<clientname >\<-engagementname >* per assegnare un nome alle funzionalità, è possibile riconoscere facilmente il contesto del progetto e il coinvolgimento dei nomi stessi.
  
- **Storia utente**: Le storie utente sono elementi di lavoro necessari per completare una funzionalità end-to-end. Esempi di storie utente includono:
  - Ottenere i dati 
  - Esplorazione dei dati 
  - Genera funzionalità
  - Creare modelli
  - Rendere operativi i modelli 
  - Ripetere il training dei modelli
  
- **Attività**: Le attività sono elementi di lavoro assegnabili che devono essere eseguiti per completare una storia utente specifica. Ad esempio, le attività nella storia utente *ottengono i dati* :
  - Ottenere le credenziali SQL Server
  - Caricare i dati in SQL Data Warehouse
  
- **Bug**: I bug sono problemi nel codice o nei documenti esistenti che devono essere corretti per completare un'attività. Se i bug sono causati da elementi di lavoro mancanti, possono escalarsi per essere storie utente o attività. 

I data scientist possono essere più comodi usando un modello agile che sostituisce funzionalità, storie utente e attività con fasi del ciclo di vita TDSP e sottofasi. Per creare un modello derivato da agile che è allineato in modo specifico con le fasi del ciclo di vita TDSP, vedere [usare un modello di lavoro TDSP agile](#set-up-agile-dsp-6).

> [!NOTE]
> TDSP prende in prestito i concetti di funzionalità, storie utente, attività e bug da gestione codice software (SCM). I concetti di TDSP potrebbero differire leggermente dalle definizioni SCM convenzionali.

## <a name='SprintPlanning-2'></a>Pianifica Sprint

Molti data scientist sono coinvolti in più progetti, che possono richiedere mesi per il completamento e continuare a ritmi diversi. La pianificazione dello sprint è utile per la definizione della priorità e per l'allocazione e la pianificazione delle risorse. In Azure Boards, è possibile creare, gestire e tenere traccia facilmente degli elementi di lavoro per i progetti e condurre la pianificazione dello sprint per garantire che i progetti vengano spostati in avanti come previsto.

Per altre informazioni sulla pianificazione dello sprint, vedere gli [Sprint Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Per altre informazioni sulla pianificazione dello sprint in Azure Boards, vedere [assegnare elementi backlog a uno sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name='AddFeature-3'></a>Aggiungere una funzionalità al backlog 

Dopo aver creato il repository del codice del progetto e del progetto, è possibile aggiungere una funzionalità al backlog per rappresentare il lavoro per il progetto.

1. Dalla pagina del progetto, selezionare **lavagne** > dei**backlog** nel pannello di navigazione sinistro. 
   
1. Nella scheda **backlog** , se il tipo di elemento di lavoro nella barra superiore è **storie**, elenco a discesa e selezionare **funzionalità**. Quindi selezionare **nuovo elemento di lavoro.**
   
   ![Seleziona nuovo elemento di lavoro](./media/agile-development/2-sprint-team-overview.png)
   
1. Immettere un titolo per la funzionalità, in genere il nome del progetto, quindi selezionare **Aggiungi alla parte superiore**. 
   
   ![Immettere un titolo e selezionare Aggiungi all'inizio](./media/agile-development/3-sprint-team-add-work.png)
   
1. Dall'elenco **backlog** selezionare e aprire la nuova funzionalità. Inserire la descrizione, assegnare un membro del team e impostare i parametri di pianificazione. 
   
   È anche possibile collegare la funzionalità al repository del codice Azure Repos del progetto selezionando **Aggiungi collegamento** nella sezione **sviluppo** . 
   
   Al termine della modifica della funzionalità, selezionare **salva & Chiudi**.
   
   ![Modificare la funzionalità e selezionare Salva & Chiudi](./media/agile-development/3a-add-link-repo.png)

## <a name='AddStoryunderfeature-4'></a>Aggiungere una storia utente alla funzionalità 

Con la funzionalità è possibile aggiungere storie utente per descrivere i passaggi principali necessari per completare il progetto. 

Per aggiungere una nuova storia utente a una funzionalità:

1. Nella scheda **backlog** selezionare **+** a sinistra della funzionalità. 
   
   ![Aggiungere una nuova storia utente nella funzionalità](./media/agile-development/4-sprint-add-story.png)
   
1. Assegnare alla storia utente un titolo e modificare i dettagli, ad esempio assegnazione, stato, descrizione, commenti, pianificazione e priorità. 
   
   È anche possibile collegare la storia utente a un ramo del repository del codice Azure Repos del progetto selezionando **Aggiungi collegamento** nella sezione **sviluppo** . Selezionare il repository e il ramo a cui si vuole collegare l'elemento di lavoro, quindi fare clic su **OK**.
   
   ![Aggiungi collegamento](./media/agile-development/5-sprint-edit-story.png)
   
1. Al termine della modifica della storia utente, selezionare **salva & Chiudi**. 

## <a name='AddTaskunderstory-5'></a>Aggiungere un'attività a una storia utente 

Le attività sono passaggi dettagliati specifici necessari per completare ogni storia utente. Una volta completate tutte le attività di una storia utente, la storia utente deve essere completata. 

Per aggiungere un'attività a una storia utente, selezionare la **+** accanto all'elemento della storia utente e selezionare **attività**. Immettere il titolo e altre informazioni nell'attività.

![Aggiungere un'attività a una storia utente](./media/agile-development/7-sprint-add-task.png)

Dopo aver creato le funzionalità, le storie utente e le attività, è possibile visualizzarle nelle visualizzazioni **backlog** o **lavagne** per tenere traccia del relativo stato.

![Visualizzazione backlog](./media/agile-development/8-sprint-backlog-view.png)

![Visualizzazione lavagne](./media/agile-development/8a-sprint-board-view.png)

## <a name='set-up-agile-dsp-6'></a>Usare un modello di lavoro Agile TDSP

I data scientist possono essere più comodi usando un modello agile che sostituisce funzionalità, storie utente e attività con fasi del ciclo di vita TDSP e sottofasi. In Azure Boards, è possibile creare un modello derivato da agile che usa fasi del ciclo di vita TDSP per creare e tenere traccia degli elementi di lavoro. I passaggi seguenti illustrano la configurazione di un modello di processo Agile specifico per data science e la creazione di data science elementi di lavoro in base al modello.

### <a name="set-up-an-agile-data-science-process-template"></a>Configurare un modello di processo di Data Science agile

1. Dalla pagina principale dell'organizzazione di Azure DevOps selezionare **Impostazioni organizzazione** dal dispositivo di spostamento a sinistra. 
   
1. Nel **Pannello**di navigazione a sinistra **Impostazioni organizzazione** selezionare **elabora**. 
   
1. Nel riquadro **tutti i processi** selezionare il **...** accanto a **agile**, quindi selezionare **Crea processo ereditato**.
   
   ![Creazione di un processo ereditato da agile](./media/agile-development/10-settings.png) 
   
1. Nella finestra di dialogo **Crea processo ereditato da agile** immettere il nome *AgileDataScienceProcess*e selezionare **Crea processo**.
   
   ![Crea processo AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. In **tutti i processi**selezionare il nuovo **AgileDataScienceProcess**. 
   
1. Nella scheda **tipi di elemento di lavoro** disabilitare **Epic**, **feature**, **storia utente**e **attività** selezionando il **...** accanto a ogni elemento e selezionando **Disabilita**. 
   
   ![Disabilitare i tipi di elemento di lavoro](./media/agile-development/12-disable.png)
   
1. In **tutti i processi**selezionare la scheda **livelli di backlog** . In **portfolio backlog**selezionare il **...** accanto a **Epic (disabilitato)** e quindi selezionare **modifica/Rinomina**. 
   
1. Nella finestra di dialogo **modifica livello backlog** :
   1. In **nome**sostituire **Epic** con i *progetti TDSP*. 
   1. In **tipi di elemento di lavoro in questo livello di backlog**selezionare **nuovo tipo di elemento di lavoro**, immettere *progetto TDSP*e selezionare **Aggiungi**. 
   1. In **tipo di elemento di lavoro predefinito**, elenco a discesa e selezionare **progetto TDSP**. 
   1. Selezionare **Salva**.
   
   ![Imposta livello di backlog portfolio](./media/agile-development/13-rename.png)  
   
1. Seguire gli stessi passaggi per rinominare le **funzionalità** in *fasi TDSP*e aggiungere i nuovi tipi di elemento di lavoro seguenti:
   
   - *Informazioni commerciali*
   - *Acquisizione dei dati*
   - *Modellazione*
   - *Distribuzione*
   
1. In **backlog requisito**rinominare le **storie** in sottofasi *TDSP*, aggiungere il nuovo tipo di elemento di lavoro *TDSP sottofase*e impostare il tipo di elemento di lavoro predefinito su **sottofase TDSP**.
   
1. In **backlog iterazione**aggiungere un nuovo tipo di elemento di lavoro *attività TDSP*e impostarlo come tipo di elemento di lavoro predefinito. 
   
Dopo aver completato i passaggi, i livelli di backlog dovrebbero avere un aspetto simile al seguente:
   
 ![Livelli di backlog del modello TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Creare elementi di lavoro del processo di Data Science agile

È possibile usare il modello di processo data science per creare progetti TDSP e tenere traccia degli elementi di lavoro corrispondenti alle fasi del ciclo di vita TDSP.

1. Dalla pagina principale dell'organizzazione di Azure DevOps selezionare **nuovo progetto**. 
   
1. Nella finestra di dialogo **Crea nuovo progetto** assegnare un nome al progetto e quindi selezionare **Avanzate**. 
   
1. In **processo elemento di lavoro**, elenco a discesa e selezionare **AgileDataScienceProcess**, quindi selezionare **Crea**.
   
   ![Creare un progetto di TDSP](./media/agile-development/15-newproject.png)
   
1. Nel progetto appena creato selezionare **lavagne** > di**backlog** nel pannello di spostamento a sinistra.
   
1. Per rendere visibili i progetti TDSP, selezionare l'icona **Configura impostazioni team** . Nella schermata **Impostazioni** selezionare la casella di controllo **progetti TDSP** , quindi selezionare **Salva e Chiudi**.
   
   ![Casella di controllo Seleziona progetti TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Per creare un progetto TDSP specifico per data science, selezionare **progetti TDSP** nella barra superiore, quindi selezionare **nuovo elemento di lavoro**. 
   
1. Nella finestra popup assegnare un nome all'elemento di lavoro del progetto TDSP e selezionare **Aggiungi alla parte superiore**.
   
   ![Crea elemento di lavoro del progetto data science](./media/agile-development/17-dsworkitems0.png)
   
1. Per aggiungere un elemento di lavoro nel progetto TDSP, selezionare il **+** accanto al progetto, quindi selezionare il tipo di elemento di lavoro da creare. 
   
   ![Seleziona data science tipo di elemento di lavoro](./media/agile-development/17-dsworkitems1.png)
   
1. Inserire i dettagli nel nuovo elemento di lavoro e selezionare **salva & Chiudi**.
   
1. Continuare a selezionare i **+** simboli accanto agli elementi di lavoro per aggiungere nuove fasi, sottofasi e attività del TDSP. 
   
Di seguito è riportato un esempio di come gli elementi di lavoro del progetto di data science devono essere visualizzati nella visualizzazione **backlog** :

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Passaggi successivi

La [codifica collaborativa con git](collaborative-coding-with-git.md) descrive come eseguire lo sviluppo di codice collaborativo per i progetti Data Science usando git come Framework di sviluppo di codice condiviso e come collegare queste attività di codifica al lavoro pianificato con il processo Agile.

[Procedure dettagliate di esempio](walkthroughs.md) elenca le procedure dettagliate di scenari specifici, con collegamenti e descrizioni delle anteprime. Negli scenari collegati viene illustrato come combinare strumenti e servizi cloud e locali in flussi di lavoro o pipeline per creare applicazioni intelligenti.
  
Risorse aggiuntive sui processi Agile:

- [Processo Agile](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Tipi di elemento di lavoro e flusso di lavoro del processo Agile](/azure/devops/boards/work-items/guidance/agile-process-workflow)

