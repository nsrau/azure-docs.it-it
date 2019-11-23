---
title: Attività per il responsabile di un progetto nel Processo di analisi scientifica dei dati in team
description: Una procedura dettagliata delle attività per un responsabile del progetto in un team di Team Data Science process
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8a94a2ae5298bbee8bb1c9c0fa044eb3189147be
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244336"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Attività del responsabile di progetto nel processo di Data Science per i team

Questo articolo descrive le attività completate da un *responsabile del progetto* per configurare un repository per il team di progetto nel [processo di Data Science](overview.md) per i team (TDSP). TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva basate sul cloud. Il TDSP è progettato per contribuire a migliorare la collaborazione e l'apprendimento del team. Per una descrizione dei ruoli del personale e delle attività associate per un team di data science la standardizzazione in TDSP, vedere [ruoli e attività del processo di Data Science](roles-tasks.md)per i team.

Un responsabile di progetto gestisce le attività quotidiane dei singoli data scientist in un progetto di data science specifico nella TDSP. Il diagramma seguente illustra il flusso di lavoro per le attività del responsabile del progetto:

![Flusso di lavoro attività lead progetto](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Questa esercitazione illustra il passaggio 1: creare un repository di progetto e il passaggio 2: inizializzare il repository del progetto dal repository ProjectTemplate del team. 

Per il passaggio 3: creare un elemento di lavoro della funzionalità per il progetto e passaggio 4: aggiungere storie per le fasi del progetto, vedere [sviluppo agile di progetti Data Science](agile-development.md).

Per il passaggio 5: creare e personalizzare asset e condivisioni di archiviazione/analisi, se necessario, vedere [creare risorse di](team-lead-tasks.md#create-team-data-and-analytics-resources)analisi e dati del team.

Per il passaggio 6: configurare il controllo di sicurezza del repository del progetto, vedere [aggiungere i membri del team e configurare le autorizzazioni](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Questo articolo usa Azure Repos per configurare un progetto TDSP, perché questo è il modo in cui implementare TDSP in Microsoft. Se il team usa un'altra piattaforma di hosting del codice, le attività del responsabile del progetto sono le stesse, ma il modo per completarle può essere diverso.

## <a name="prerequisites"></a>prerequisiti

In questa esercitazione si presuppone che il [responsabile del gruppo](group-manager-tasks.md) e il responsabile del [Team](team-lead-tasks.md) abbiano configurato le risorse e le autorizzazioni seguenti:

- **Organizzazione** DevOps di Azure per l'unità dati
- **Progetto** team per il team di Data Science
- **Repository** del modello Team e delle utilità
- **Autorizzazioni** per l'account dell'organizzazione per la creazione e la modifica di repository per il progetto

Per clonare i repository e modificare il contenuto nel computer locale o Data Science Virtual Machine (DSVM) o configurare l'archiviazione file di Azure e montarlo nella DSVM, è necessario anche quanto segue:

- Una sottoscrizione di Azure.
- Git installato nel computer. Se si usa un DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare un DSVM, Windows o Linux DSVM creato e configurato in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/).
- Per un DSVM Windows, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) è installato nel computer. Nel file *Readme.MD* scorrere verso il basso fino alla sezione **download and install** e selezionare il **programma di installazione più recente**. Scaricare il programma di installazione con *estensione exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per un DSVM Linux, viene configurata una chiave pubblica SSH nella DSVM e aggiunta in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **create SSH Public Key** nell' [appendice Platforms and Tools](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Creare un repository di progetto nel progetto team

Per creare un **repository di progetto nel progetto team** del team:

1. Passare alla pagina di **Riepilogo** del progetto del team all'indirizzo *https:\//nome del server \<>/\<nome dell'organizzazione >/\<nome del team*>, ad esempio **https:\//dev.Azure.com/DataScienceUnit/myTeam**, e selezionare **repository** dal dispositivo di spostamento a sinistra. 
   
1. Selezionare il nome del repository nella parte superiore della pagina e quindi selezionare **nuovo repository** nell'elenco a discesa.
   
   ![Seleziona nuovo repository](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Nella finestra di dialogo **Crea un nuovo repository** assicurarsi che **git** sia selezionato in **tipo**. Immettere *DSProject1* in **nome repository**, quindi selezionare **Crea**.
   
   ![Crea repository](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Verificare che sia possibile visualizzare il nuovo repository **DSProject1** nella pagina delle impostazioni del progetto. 
   
   ![Repository del progetto nelle impostazioni del progetto](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importare il modello Team nel repository del progetto

Per popolare il repository del progetto con il contenuto del repository del modello Team:

1. Dalla pagina di **Riepilogo** del progetto del team selezionare **repository** nel percorso di spostamento a sinistra. 
   
1. Selezionare il nome del repository nella parte superiore della pagina e selezionare **DSProject1** nell'elenco a discesa.
   
1. Nella pagina **DSProject1 è vuota** selezionare Import ( **Importa**). 
   
   ![Selezionare Importa](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. Nella finestra di dialogo **Importa un repository git** selezionare **git** come **tipo di origine**e immettere l'URL del repository **teamTemplate** in **Clone URL**. L'URL è *https:\//nome del server \<>/\<nome dell'organizzazione >/\<nome del team >/_git/\<nome del repository del modello Team*>. Ad esempio: **https:\//dev.Azure.com/DataScienceUnit/myTeam/_git/teamTemplate**. 
   
1. Selezionare **Importa**. Il contenuto del repository del modello team viene importato nel repository del progetto. 
   
   ![Importa repository di modelli team](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Se è necessario personalizzare il contenuto del repository del progetto per soddisfare le esigenze specifiche del progetto, è possibile aggiungere, eliminare o modificare file e cartelle del repository. È possibile usare direttamente Azure Repos o clonare il repository nel computer locale o in DSVM, apportare modifiche ed eseguire il commit e il push degli aggiornamenti nel repository del progetto condiviso. Seguire le istruzioni in [personalizzare il contenuto dei repository del team](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni dettagliate degli altri ruoli e delle attività definiti dal processo di Data Science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività dei singoli collaboratori per un team di data science](project-ic-tasks.md)
