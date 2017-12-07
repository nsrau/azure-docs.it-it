---
title: Sviluppo Agile di progetti di data science - Azure Machine Learning | Microsoft Docs
description: Come gli sviluppatori possono eseguire un progetto di data science in modo sistematico, collaborativo e con controllo della versione all'interno di un team di progetto tramite il processo di data science per i team.
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
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>Sviluppo Agile di progetti di data science

Questo documento descrive come gli sviluppatori possono eseguire un progetto di data science in modo sistematico, collaborativo e con controllo della versione all'interno di un team di progetto tramite il [processo di data science per i team](overview.md) (TDSP, Team Data Science Process). Il TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva, basate sul cloud. Per una descrizione dei ruoli del personale e delle rispettive attività associate che un team di data science gestisce allo scopo di normalizzare il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di data science per i team). 

Questo articolo comprende istruzioni su come: 

1. eseguire la **pianificazione dello sprint** per gli elementi di lavoro interessati da un progetto.<br> Se non si ha familiarità con la pianificazione dello sprint, è possibile trovare dettagli e informazioni generali [qui](https://en.wikipedia.org/wiki/Sprint_(software_development) "qui"). 
2. **aggiungere elementi di lavoro** agli sprint. 

> [!NOTE]
> Nel seguente set di istruzioni vengono illustrati i passaggi necessari per configurare un ambiente per i team TDSP usando Visual Studio Team Services (VSTS). I passaggi specificano come eseguire queste attività con VSTS, dal momento che questa è la modalità di implementazione di TDSP presso Microsoft.  Se si sceglie di usare VSTS, gli elementi (3) e (4) nell'elenco precedente sono vantaggi che si ottengono naturalmente. Se si usa un'altra piattaforma di hosting del codice per il proprio gruppo, le attività che il responsabile del team deve completare di per sé non cambiano. Sarà tuttavia diverso il modo in cui queste attività vengono completate. Ad esempio, l'elemento nella sezione 6, **Collegare un elemento di lavoro a un ramo Git**, potrebbe non essere semplice come è in Visual Studio Team Services.
>
>

La figura seguente illustra la pianificazione di sprint, la codifica e il flusso di lavoro di controllo del codice sorgente tipici relativi all'implementazione di un progetto di data science:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

Nel framework di pianificazione dello sprint TDSP sono disponibili quattro tipi frequentemente usati di **elementi di lavoro**: **Funzionalità**, **Storia utente**, **Attività** e **Bug**. Ogni progetto team conserva un unico backlog per tutti gli elementi di lavoro. Non è presente alcun backlog a livello di repository Git in un progetto team. Di seguito sono illustrate le relative definizioni:

- **Funzionalità**: una funzionalità corrisponde a un impegno di progetto. Diversi impegni con un client vengono considerati funzionalità diverse. Analogamente, è consigliabile considerare le diverse fasi di un progetto con un client come diverse funzionalità. Se si sceglie uno schema, ad esempio ***ClientName-EngagementName*** per denominare le funzionalità, allora è possibile riconoscere facilmente il contesto del progetto/impegno dai nomi stessi.
- **Storia**: le storie sono elementi di lavoro diversi che sono necessari per completare una funzionalità (progetto) end-to-end. Gli esempi di storie comprendono:
    - Recupero dei dati 
    - Esplorazione dei dati 
    - Generazione delle funzionalità
    - Compilazione dei modelli
    - Attività che rende operativi i modelli 
    - Ripetizione del training dei modelli
- **Attività**: le attività sono elementi di lavoro di codice o di documento assegnabili o altre attività che devono essere eseguite per completare una storia specifica. Ad esempio, le attività nella storia *Getting Data* (Acquisizione dei dati) potrebbero essere:
    -  Ottenere le credenziali di SQL Server 
    -  Caricamento dei dati in SQL Data Warehouse. 
- **Bug**: i bug fanno in genere riferimento alle correzioni necessarie per un codice o un documento esistenti che vengono eseguite durante il completamento di un'attività. Se il bug è causato rispettivamente da passaggi o attività mancanti, è possibile passarlo allo stato di storia o di attività. 

> [!NOTE]
> I concetti di funzionalità, storie, attività e bug sono presi in prestito dalla gestione del codice software e usati in data science, pur in un senso leggermente diverso da quello delle definizioni convenzionali proprie della gestione del codice software.
>
>

> [!NOTE]
> Gli esperti di dati potrebbero sentirsi più a proprio agio usando un modello Agile, che consente un allineamento specifico con le fasi del ciclo di vita TDSP. Tenendo presente ciò, è stato creato un modello di pianificazione dello sprint derivato da Agile, dove epiche, storie e così via vengono sostituite da fasi o sottofasi del ciclo di vita TDSP. Per istruzioni su come creare un modello Agile, vedere [Impostare un processo di data science Agile in Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Pianificazione dello sprint 

La pianificazione dello sprint è utile per la definizione della priorità e per l'allocazione e la pianificazione delle risorse. Molti data scientist sono impegnati su più progetti, ciascuno dei quali può richiedere mesi per essere completato. I progetti spesso proseguono a ritmi diversi. Nel server di Visual Studio Team Services è possibile creare, gestire e tenere traccia facilmente degli elementi di lavoro nel progetto team e condurre la pianificazione dello sprint per garantire che i progetti procedano come previsto. 

Seguire [questo collegamento](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) per le istruzioni dettagliate sulla pianificazione dello sprint in Visual Studio Team Services. 


## 3. <a name='AddFeature-3'></a>Aggiungere una funzionalità  

Dopo aver creato il repository di progetto in un progetto team, passare alla pagina del team **Panoramica** e fare clic su **Gestisci il lavoro**.

![2](./media/agile-development/2-sprint-team-overview.png)

Per includere una funzionalità nel backlog, fare clic su **Backlog** --> **Funzionalità** --> **Nuovo**, digitare nella funzionalità **Titolo**(in genere il nome del progetto) e quindi fare clic su **Aggiungi**.

![3](./media/agile-development/3-sprint-team-add-work.png)

Fare doppio clic sulla funzionalità creata. Compilare le descrizioni, assegnare i membri del team per questa funzionalità e impostare i parametri di pianificazione per questa funzionalità. 

È anche possibile collegare questa funzionalità al repository del progetto. Fare clic su **Aggiungi collegamento** nella sezione **Sviluppo**. Dopo aver completato la modifica della funzionalità, fare clic su **Salva e chiudi** per uscire.


## 4. <a name='AddStoryunderfeature-4'></a>Aggiungere una storia nella funzionalità 

Nella funzionalità è possibile aggiungere delle storie per descrivere i passaggi principali necessari per completare il progetto (funzionalità). Per aggiungere una nuova storia, fare clic sulla firma **+** a sinistra della funzionalità nella visualizzazione di backlog.  

![4](./media/agile-development/4-sprint-add-story.png)

È possibile modificare i dettagli della storia, ad esempio la stato, la descrizione, i commenti, la pianificazione e la priorità nella finestra popup.

![5](./media/agile-development/5-sprint-edit-story.png)

È possibile collegare la storia a un repository esistente facendo clic su **+ Aggiungi collegamento** in **Sviluppo**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Aggiungere un'attività a una storia 

Le attività sono procedure dettagliate specifiche, necessarie per completare ogni storia. Al termine di tutte le attività di una storia, anche la storia deve essere completata. 

Per aggiungere un'attività a una storia, fare clic sulla firma **+** accanto all'elemento della storia, selezionare **Attività** e quindi immettere le informazioni dettagliate di questa attività nella finestra popup.

![7](./media/agile-development/7-sprint-add-task.png)

Dopo aver creato le storie, le funzionalità e le attività, è possibile visualizzarle nelle viste **Backlog** o **Lavagna** per rilevarne lo stato.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Impostare un modello di lavoro TDSP Agile in Visual Studio Online

Questo articolo illustra come impostare un modello di Team Data Science Process Agile che usa le fasi del ciclo di vita di data science di TDSP e tiene traccia degli elementi di lavoro con Visual Studio Online (vso). La procedura riportata di seguito illustra un esempio di configurazione del modello di processo Agile specifico per data science *AgileDataScienceProcess* e mostra come creare elementi di lavoro di data science in base al modello.

### <a name="agile-data-science-process-template-setup"></a>Impostazione del modello di processo di data science Agile

1. Passare alla home page del server, **Configura** -> **Processo**.

    ![10](./media/agile-development/10-settings.png) 

2. Passare a **Tutti i processi** -> **Processi** in **Agile** e fare clic su **Crea processo ereditato**. Specificare il nome del processo "AgileDataScienceProcess" e fare clic su **Crea processo**.

    ![11](./media/agile-development/11-agileds.png)

3. Nella scheda **AgileDataScienceProcess** -> **Tipi di elemento di lavoro** disabilitare i tipi di elemento di lavoro **Epica**, **Funzionalità**, **Storia utente** e **Attività** tramite **Configura -> Disabilita**.

    ![12](./media/agile-development/12-disable.png)

4. Passare alla scheda **AgileDataScienceProcess** -> **Livelli di backlog**. Rinominare "Epiche" in "Progetti TDSP" facendo clic su **Configura** -> **Modifica/Rinomina**. Nella stessa finestra di dialogo fare clic su **+Nuovo tipo di elemento di lavoro** in "Progetto di data science" e impostare il valore di **Tipo di elemento di lavoro predefinito** su "Progetto TDSP". 

    ![13](./media/agile-development/13-rename.png)  

5. Analogamente, modificare il nome di Backlog "Funzionalità" in "Fasi TDSP" e aggiungere le informazioni seguenti per **Nuovo tipo di elemento di lavoro**:

    - Informazioni commerciali
    - Acquisizione dei dati
    - Modellazione
    - Distribuzione

6. Rinominare "Storia utente" in "Sottofasi TDSP" con il tipo di elemento di lavoro predefinito impostato sul tipo "Sottofase TDSP" appena creato.

7. Impostare "Attività" sul tipo di elemento di lavoro appena creato "Attività TDSP". 

8. Dopo questi passaggi, i livelli di backlog dovrebbero essere analoghi ai seguenti:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Creare elementi di lavoro di data science

Dopo avere creato il modello di processo di data science, è possibile creare e tenere traccia degli elementi di lavoro di data science corrispondenti al ciclo di vita TDSP.

1. Quando si crea un nuovo progetto team, selezionare "Agile\AgileDataScienceProcess" come **Processo di elemento di lavoro**:

    ![15](./media/agile-development/15-newproject.png)

2. Passare al progetto team appena creato e fare clic su **Lavoro** -> **Backlog**.

3. Rendere visibile "Progetti TDSP" facendo clic su **Consente di configurare le impostazioni del team** e selezionare "Progetti TDSP", quindi salvare.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. È ora possibile avviare la creazione di elementi di lavoro specifici per data science.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Ecco un esempio di come dovrebbero apparire gli elementi di lavoro del progetto di data science:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Passaggi successivi

[Creazione collaborativa di codice con Git](collaborative-coding-with-git.md) descrive come collaborare allo sviluppo di codice per i progetti di data science usando Git come framework di sviluppo di codice condiviso e come collegare queste attività di codifica al lavoro pianificato con il processo Agile.

Ecco altri collegamenti a risorse sui processi Agile.

- Agile process (Processo Agile) [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Agile process work item types and workflow (Flusso di lavoro e tipi di elementi di lavoro del processo Agile) [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Sono anche disponibili esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 
