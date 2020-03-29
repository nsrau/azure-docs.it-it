---
title: Attività per un singolo collaboratore nel processo di data science per i team
description: Procedura dettagliata delle attività per un singolo collaboratore in un progetto team di data science.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721252"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Attività per un singolo collaboratore nel processo di data science per i team

In questo argomento vengono descritte le attività completate da un *singolo collaboratore* per impostare un progetto nel [processo di analisi scientifica dei dati](overview.md) del team (TDSP). L'obiettivo è lavorare in un ambiente di team collaborativo che standardizza il TDSP. Il TDSP è progettato per contribuire a migliorare la collaborazione e l'apprendimento in team. Per una descrizione dei ruoli del personale e delle attività associate gestite da un team di data science che standardizza il TDSP, vedere [Team Data Science Process roles and tasks](roles-tasks.md).

Il diagramma seguente mostra le attività che proiettano singoli collaboratori (scienziati dei dati) completano per configurare l'ambiente del team. Per istruzioni su come eseguire un progetto di analisi scientifica dei dati nell'ambito del TDSP, vedere [Esecuzione di progetti di data science](project-execution.md). 

![Attività dei singoli collaboratori](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** è il repository che il team di progetto gestisce per condividere risorse e modelli di progetto.
- **TeamUtilities** è il repository di utilità che il team gestisce specificamente per il tuo team. 
- **GroupUtilities** è il repository che il gruppo gestisce per condividere utilità utili nell'intero gruppo. 

> [!NOTE] 
> Questo articolo usa Azure Repos e una macchina virtuale di data science (DSVM) per configurare un ambiente TDSP, perché è possibile implementare TDSP in Microsoft.This article uses Azure Repos and a Data Science Virtual Machine (DSVM) to set up a TDSP environment, because that is how to implement TDSP at Microsoft. Se il team utilizza altre piattaforme di hosting o sviluppo del codice, le singole attività dei collaboratori sono le stesse, ma il modo per completarle potrebbe essere diverso.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che le risorse e le autorizzazioni seguenti siano state impostate dal [responsabile](group-manager-tasks.md)del gruppo, dal [responsabile del team](team-lead-tasks.md)e dal [responsabile del progetto:](project-lead-tasks.md)

- Organizzazione DevOps di Azure per **l'unità** di data scienceThe Azure DevOps organization for your data science unit
- Un **repository di progetto** impostato dal lead del progetto per condividere i modelli di progetto e le risorse
- Repository **GroupUtilities** e **TeamUtilities** impostati dal responsabile del gruppo e dal responsabile del team, se applicabile
- **Archiviazione file** di Azure configurata per le risorse condivise per il team o il progetto, se applicabile
- **Autorizzazioni** per clonare ed eseguire il push nel repository del progetto 

Per clonare i repository e modificare il contenuto nel computer locale o DSVM o montare l'archiviazione di file di Azure nella DSVM, è necessario considerare questo elenco di controllo:To clone repositories and modify content on your local machine or DSVM, or mount Azure file storage to your DSVM, you need to consider this checklist:

- Una sottoscrizione di Azure.
- Git installato sul computer. Se si utilizza una DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare una DSVM, Windows o Linux DSVM creato e configurato in Azure.If you want to use a DSVM, the Windows or Linux DSVM created and configured in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione](/azure/machine-learning/data-science-virtual-machine/)relativa alle macchine virtuali di data science .
- Per una DSVM di Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installato nel computer. Nel file *README.md* scorrere verso il basso fino alla sezione **Download and Install (Scarica e installa)** e selezionare il programma di installazione più **recente.** Scaricare il programma di installazione *.exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per una dSVM Linux, una chiave pubblica SSH configurata nella DSVM e aggiunta in Azure DevOps.For a Linux DSVM, an SSH public key set up on your DSVM and added in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **Creazione di chiave pubblica SSH** nell'appendice [Piattaforme e strumenti](platforms-and-tools.md#appendix). 
- Informazioni sull'archiviazione file di Azure per qualsiasi archiviazione di file di Azure da montare nella DSVM. 

## <a name="clone-repositories"></a>Clonare repository

Per utilizzare i repository in locale ed eseguire il push delle modifiche nei repository condivisi del team e del progetto, copiare o *clonare* i repository nel computer locale. 

1. In DevOps di Azure passare alla pagina Riepilogo progetto del team all'indirizzo *https:\//\<nome server>/\<nome organizzazione>/\<nome team>*, ad esempio **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Selezionare **Repos** nel riquadro di spostamento sinistro e nella parte superiore della pagina selezionare il repository che si desidera clonare.
   
1. Nella pagina del repository, selezionare **Clona** in alto a destra.
   
1. Nella finestra di dialogo **Clona repository,** selezionare **HTTPS** per una connessione HTTP o **SSH** per una connessione SSH e copiare l'URL del clone in Riga di **comando** negli Appunti.
   
   ![Clonare il repository](./media/project-ic-tasks/clone.png)
   
1. Nel computer locale o DSVM, creare le seguenti directory:
   
   - Per Windows: **C:**
   - Per Linux: **$home/GitRepos**
   
1. Passare alla directory creata.
   
1. In Git Bash, `git clone <clone URL>` eseguire il comando per ogni repository che si desidera clonare. 
   
   Ad esempio, il comando seguente clona il repository **TeamUtilities** nella directory *MyTeam* del computer locale. 
   
   **Connessione HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Connessione SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Verificare che sia possibile visualizzare le cartelle per i repository clonati nella directory del progetto locale.
   
   ![Tre cartelle del repository locale](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Montare l'archiviazione file di Azure in DSVMMount Azure file storage to your DSVM

Se il team o il progetto dispone di risorse condivise nell'archivio file di Azure, montare l'archiviazione file nel computer locale o dSVM. Seguire le istruzioni in [Montare l'archiviazione file di Azure nel computer locale o DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i collegamenti alle descrizioni dettagliate degli altri ruoli e attività definiti dal processo di analisi scientifica dei dati del team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)

