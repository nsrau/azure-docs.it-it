---
title: Attività per il responsabile di un progetto nel Processo di analisi scientifica dei dati in team
description: Una procedura dettagliata delle attività per un responsabile di progetto in un team del processo di analisi scientifica dei dati del team
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714419"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Attività di responsabile del progetto nel processo di analisi scientifica dei dati del teamProject lead tasks in the Team Data Science Process

In questo articolo vengono descritte le attività completate da un *responsabile* di progetto per impostare un repository per il team di progetto nel [processo di analisi scientifica dei dati](overview.md) del team (TDSP). TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva basate su cloud. Il TDSP è progettato per contribuire a migliorare la collaborazione e l'apprendimento in team. Per una descrizione dei ruoli del personale e delle attività associate per un team di data science standardizzato nel TDSP, vedere Ruoli e attività del processo di analisi scientifica dei dati del [team.](roles-tasks.md)

Un responsabile di progetto gestisce le attività quotidiane dei singoli data scientist su uno specifico progetto di data science nel TDSP. Nel diagramma seguente viene illustrato il flusso di lavoro per le attività di responsabile progetto:

![Flusso di lavoro attività di progetto per lead](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Questa esercitazione illustra Passaggio 1: Creare il repository di progetto e Passaggio 2: Archivio di progetto seed dal repository ProjectTemplate del team. 

Per il passaggio 3: creare un elemento di lavoro Feature per il progetto e Passaggio 4: Aggiungere storie per le fasi del progetto, vedere [Sviluppo Agile di progetti di data science](agile-development.md).

Per il passaggio 5: creare e personalizzare gli asset di archiviazione/analisi e condividere, se necessario, vedere [Creare dati del team e risorse di analisi](team-lead-tasks.md#create-team-data-and-analytics-resources).

Per il Passaggio 6: Impostare il controllo di sicurezza dell'archivio di progetto, vedere [Aggiungere membri del team e configurare le autorizzazioni](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Questo articolo usa Azure Repos per configurare un progetto TDSP, perché è possibile implementare TDSP in Microsoft.This article uses Azure Repos to set up a TDSP project, because this is how to implement TDSP at Microsoft. Se il team utilizza un'altra piattaforma di hosting del codice, le attività di responsabile del progetto sono le stesse, ma il modo per completarle potrebbe essere diverso.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che il manager del gruppo e [il responsabile del team](team-lead-tasks.md) abbiano impostato le risorse e le autorizzazioni seguenti:This tutorial assumes that your group [manager](group-manager-tasks.md) and team lead have set up the following resources and permissions:

- Organizzazione DevOps di Azure per **l'unità** datiThe Azure DevOps organization for your data unit
- Un **progetto** di team per il team di data science
- Repository di modelli di team e **utilità**
- **Autorizzazioni** per l'account dell'organizzazione per la creazione e la modifica di repository per il progetto

Per clonare i repository e modificare il contenuto nel computer locale o nella macchina virtuale Data Science (DSVM) o configurare l'archiviazione file di Azure e montarla nella dSVM, è inoltre necessario prendere in considerazione questo elenco di controllo:To clone repositories and modify content on your local machine or Data Science Virtual Machine (DSVM) or set up Azure file storage and mount it to your DSVM, you also need to consider this checklist:

- Una sottoscrizione di Azure.
- Git installato sul computer. Se si utilizza una DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare una DSVM, Windows o Linux DSVM creato e configurato in Azure.If you want to use a DSVM, the Windows or Linux DSVM created and configured in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione](/azure/machine-learning/data-science-virtual-machine/)relativa alle macchine virtuali di data science .
- Per una DSVM di Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installato nel computer. Nel file *README.md* scorrere verso il basso fino alla sezione **Download and Install (Scarica e installa)** e selezionare il programma di installazione più **recente.** Scaricare il programma di installazione *.exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per una dSVM Linux, una chiave pubblica SSH configurata nella DSVM e aggiunta in Azure DevOps.For a Linux DSVM, an SSH public key set up on your DSVM and added in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **Creazione di chiave pubblica SSH** nell'appendice [Piattaforme e strumenti](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Creare un repository di progetto nel progetto team

Per creare un repository di progetto nel progetto **MyTeam** del team:

1. Passare alla pagina **Riepilogo** del progetto del team all'indirizzo *\//\<https: nome server>/nome\<dell'organizzazione>/nome\<del team>*, ad esempio **https:\//dev.azure.com/DataScienceUnit/MyTeam**e selezionare **Repos** nel riquadro di spostamento sinistro. 
   
1. Selezionare il nome del repository nella parte superiore della pagina e quindi selezionare **Nuovo repository** dall'elenco a discesa.
   
   ![Selezionare Nuovo repository](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Nella finestra di dialogo **Crea un nuovo repository,** assicurarsi che **Git** sia selezionato in **Tipo**. Immettere *DSProject1* in **Nome repository**, quindi selezionare **Crea**.
   
   ![Crea repository](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Verificare che sia possibile visualizzare il nuovo repository **DSProject1** nella pagina delle impostazioni del progetto. 
   
   ![Repository di progetto in Impostazioni progetto](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importare il modello di team nel repository del progetto

Per popolare il repository del progetto con il contenuto del repository dei modelli di team:

1. Nella pagina **Riepilogo** progetto del team selezionare **Repository** nel riquadro di spostamento sinistro. 
   
1. Selezionare il nome del repository nella parte superiore della pagina e selezionare **DSProject1** dall'elenco a discesa.
   
1. Nella pagina **DSProject1 è vuota** selezionare **Importa**. 
   
   ![Selezionare Importa](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. Nella finestra di dialogo **Importa repository Git** selezionare **Git** come **tipo origine**e immettere l'URL del repository **TeamTemplate** in **Clona URL**. L'URL è *\//\<https:\<nome del\<server>/ nome\<organizzazione>/ nome del team>/_git/ nome repository modello team>*. Ad esempio: **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Selezionare **Importa**. Il contenuto del repository dei modelli di team viene importato nel repository del progetto. 
   
   ![Importare il repository dei modelli di team](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Se è necessario personalizzare il contenuto del repository del progetto per soddisfare le esigenze specifiche del progetto, è possibile aggiungere, eliminare o modificare i file e le cartelle del repository. È possibile lavorare direttamente in Azure Repos oppure clonare il repository nel computer locale o in DSVM, apportare modifiche ed eseguire il commit e il push degli aggiornamenti nel repository del progetto condiviso. Seguire le istruzioni in Personalizzare il contenuto dei repository del [team.](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i collegamenti alle descrizioni dettagliate degli altri ruoli e attività definiti dal processo di analisi scientifica dei dati del team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività di collaboratore individuale per un team di data science](project-ic-tasks.md)
