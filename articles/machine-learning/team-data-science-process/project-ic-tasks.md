---
title: Attività per un singolo collaboratore nel processo di data science per i team
description: Procedura dettagliata delle attività per un singolo collaboratore in un progetto team data science.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b64d9669c11f15de5e6bd616ff7a79f59b748363
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244284"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Attività per un singolo collaboratore nel processo di data science per i team

Questo argomento descrive le attività completate da un *singolo collaboratore* per configurare un progetto nel [processo di Data Science](overview.md) per i team (TDSP). L'obiettivo è lavorare in un ambiente collaborativo del team che standardizza la TDSP. Il TDSP è progettato per contribuire a migliorare la collaborazione e l'apprendimento del team. Per una descrizione dei ruoli del personale e delle attività associate gestite da un team data science la standardizzazione di TDSP, vedere [ruoli e attività del processo di Data Science](roles-tasks.md)per i team.

Il diagramma seguente illustra le attività che vengono completate dal progetto di singoli collaboratori (data scientist) per configurare l'ambiente del team. Per istruzioni su come eseguire un progetto data science in TDSP, vedere [esecuzione di progetti di Data Science](project-execution.md). 

![Singole attività del collaboratore](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** è il repository che il team di progetto gestisce per condividere i modelli di progetto e gli asset.
- **TeamUtilities** è il repository di utilità che il team gestisce in modo specifico per il team. 
- **GroupUtilities** è il repository che il gruppo gestisce per condividere utilità utili nell'intero gruppo. 

> [!NOTE] 
> Questo articolo usa Azure Repos e una Data Science Virtual Machine (DSVM) per configurare un ambiente TDSP, perché questo è il modo in cui implementare TDSP in Microsoft. Se il team USA altre piattaforme di hosting o sviluppo di codice, le singole attività del collaboratore sono le stesse, ma il modo per completarle può essere diverso.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che le risorse e le autorizzazioni seguenti siano state configurate dal [responsabile del gruppo](group-manager-tasks.md), dal responsabile del [Team](team-lead-tasks.md)e dal responsabile del [progetto](project-lead-tasks.md):

- **Organizzazione** DevOps di Azure per l'unità di Data Science
- Un **repository di progetto** configurato dal responsabile del progetto per condividere i modelli di progetto e gli asset
- Repository **GroupUtilities** e **TeamUtilities** impostati dal responsabile del gruppo e dal responsabile del team, se applicabile
- **Archiviazione file** di Azure configurata per le risorse condivise per il team o il progetto, se applicabile
- **Autorizzazioni** per la clonazione e il push di nuovo nel repository del progetto 

Per clonare i repository e modificare il contenuto nel computer locale o in DSVM o montare archiviazione file di Azure in DSVM, è necessario quanto segue:

- Una sottoscrizione di Azure.
- Git installato nel computer. Se si usa un DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare un DSVM, Windows o Linux DSVM creato e configurato in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/).
- Per un DSVM Windows, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) è installato nel computer. Nel file *Readme.MD* scorrere verso il basso fino alla sezione **download and install** e selezionare il **programma di installazione più recente**. Scaricare il programma di installazione con *estensione exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per un DSVM Linux, viene configurata una chiave pubblica SSH nella DSVM e aggiunta in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **create SSH Public Key** nell' [appendice Platforms and Tools](platforms-and-tools.md#appendix). 
- Informazioni sull'archiviazione file di Azure per qualsiasi archiviazione file di Azure che è necessario montare nella DSVM. 

## <a name="clone-repositories"></a>Clonare i repository

Per lavorare con i repository in locale ed effettuare il push delle modifiche al team condiviso e ai repository di progetto, è prima necessario copiare o *clonare* i repository nel computer locale. 

1. In Azure DevOps, passare alla pagina di riepilogo del progetto del team in *https: \/ @ no__t-2 @ no__t-3Server name >/\<organization name >/\<team nome >* , ad esempio **https: \//dev. Azure. com/DataScienceUnit/team**.
   
1. Selezionare **repository nel percorso di spostamento a sinistra** , quindi nella parte superiore della pagina selezionare il repository che si vuole clonare.
   
1. Nella pagina Repository selezionare **Clone** in alto a destra.
   
1. Nella finestra di dialogo **clona repository** selezionare **https** per una connessione HTTP o **SSH** per una connessione SSH, quindi copiare l'URL clone sotto **riga di comando** negli Appunti.
   
   ![Clonare il repository](./media/project-ic-tasks/clone.png)
   
1. Nel computer locale o DSVM creare le directory seguenti:
   
   - Per Windows: **C:\GitRepos**
   - Per Linux: **$Home/gitrepos**
   
1. Passare alla directory creata.
   
1. In git bash eseguire il comando `git clone <clone URL>` per ogni repository che si vuole clonare. 
   
   Ad esempio, il comando seguente consente di clonare il repository **TeamUtilities** nella directory del *Team* nel computer locale. 
   
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

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Montare archiviazione file di Azure in DSVM

Se il team o il progetto ha risorse condivise in archiviazione file di Azure, montare l'archiviazione file nel computer locale o in DSVM. Seguire le istruzioni in [montare archiviazione file di Azure nel computer locale o in DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni dettagliate degli altri ruoli e delle attività definiti dal processo di Data Science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)

