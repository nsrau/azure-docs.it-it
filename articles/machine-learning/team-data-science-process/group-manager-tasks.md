---
title: Attività del responsabile del gruppo in un processo di data science per i team
description: Seguire questa procedura dettagliata delle attività completate da un manager di gruppo in un progetto team di data science.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721354"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Attività del responsabile del gruppo in un processo di data science per i team

In questo articolo vengono descritte le attività completate da un manager di *gruppo* per un'organizzazione di data science. Il manager del gruppo gestisce l'intera unità di data science in un'azienda. Un'unità di data science può avere diversi team, ognuno dei quali sta lavorando su molti progetti di data science in verticali aziendali distinti. L'obiettivo del responsabile del gruppo è quello di creare un ambiente di gruppo collaborativo che standardizza il processo di analisi scientifica [dei dati](overview.md) del team (TDSP). Per una descrizione di tutti i ruoli del personale e delle attività associate gestite da un team di data science che standardizza nel TDSP, vedere [Team Data Science Process roles and tasks](roles-tasks.md).

Nel diagramma seguente vengono illustrate le sei principali attività di impostazione del gestore di gruppo. I responsabili di gruppo possono delegare le proprie attività ai surrogati, ma le attività associate al ruolo non cambiano.

![Attività del gestore di gruppo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Configurare **un'organizzazione DevOps** di Azure per il gruppo.
2. Creare il progetto GroupCommon predefinito nell'organizzazione DevOps di Azure.Create the default **GroupCommon project** in the Azure DevOps organization.
3. Creare il repository GroupProjectTemplate in Azure Repos.Create the **GroupProjectTemplate** repository in Azure Repos.
4. Creare il repository GroupUtilities in Azure Repos.Create the **GroupUtilities** repository in Azure Repos.
5. Importare il contenuto dei repository **ProjectTemplate** e **Utilities** del team di Microsoft TDSP nei repository comuni del gruppo.
6. Impostare **l'appartenenza** e **le autorizzazioni** per l'accesso al gruppo ai membri del team.

L'esercitazione seguente illustra i passaggi in dettaglio. 

> [!NOTE] 
> Questo articolo usa DevOps di Azure per configurare un ambiente di gruppo TDSP, perché è possibile implementare TDSP in Microsoft.This article uses Azure DevOps to set up a TDSP group environment, because that is how to implement TDSP at Microsoft. Se il gruppo utilizza altre piattaforme di hosting o sviluppo di codice, le attività del gestore di gruppo sono le stesse, ma il modo per completarle potrebbe essere diverso.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Creare un'organizzazione e un progetto in Azure DevOpsCreate an organization and project in Azure DevOps

1. Vai a [visualstudio.microsoft.com,](https://visualstudio.microsoft.com)seleziona **Accedi** in alto a destra e accedi al tuo account Microsoft. 
   
   ![Effettuare l'accesso con l'account Microsoft.](./media/group-manager-tasks/signinvs.png)
   
   Se non si dispone di un account Microsoft, selezionare **Iscriviti ora**, crea un account Microsoft e accedi utilizzando questo account. Se l'organizzazione dispone di una sottoscrizione di Visual Studio, accedere con le credenziali per tale sottoscrizione.
   
1. Dopo aver effettuato l'accesso, in alto a destra nella pagina DevOps di Azure selezionare **Crea nuova organizzazione**.
   
   ![Creare una nuova organizzazione](./media/group-manager-tasks/create-organization.png)
   
1. Se viene richiesto di accettare i Termini di servizio, l'Informativa sulla privacy e il Codice di condotta, selezionare **Continua**.
   
1. Nella finestra di dialogo di iscrizione assegnare un nome all'organizzazione DevOps di Azure e accettare l'assegnazione dell'area host oppure eseguire l'elenco a discesa e selezionare un'area diversa. Quindi selezionare **Continua**. 

1. In **Crea un progetto da avviare**immettere *GroupCommon*, quindi selezionare **Crea progetto**. 
   
   ![Crea progetto](./media/group-manager-tasks/create-project.png)

Verrà visualizzata la pagina **Riepilogo** progetto **comune.** L'URL della pagina è *\//\<https: nomeserver>/\<nome-organizzazione>/GruppoComune*.

![Pagina di riepilogo del progetto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Configurare i repository comuni di gruppo

I repository di Azure ospitano i tipi di repository seguenti per il gruppo:Azure Repos hosts the following types of repositories for your group:

- **Archivi comuni**di gruppo : archivi generici che più team all'interno di un'unità di data science possono adottare per molti progetti di data science. 
- **Repository di**team : Repository per team specifici all'interno di un'unità di data science. Questi repository sono specifici per le esigenze di un team e possono essere utilizzati per più progetti all'interno di tale team, ma non sono sufficientemente generali da essere utilizzati in più team all'interno di un'unità di data science.
- **Archivi**di progetto : Repository per progetti specifici. Tali repository potrebbero non essere sufficientemente generali per più progetti all'interno di un team o per altri team in un'unità di data science.

Per impostare i repository comuni di gruppo nel progetto, è necessario: 
- Rinominare il repository **GroupCommon** predefinito in **GroupProjectTemplate**
- Creare un nuovo repository **GroupUtilitiesCreate** a new GroupUtilities repository

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Rinominare il repository di progetto predefinito in GroupProjectTemplate

Per rinominare l'archivio di progetto **GroupCommon** predefinito in **GroupProjectTemplate**:

1. Nella pagina **Riepilogo** progetto comune di **gruppo** selezionare **Repos**. Questa azione consente di passare al repository **GroupCommon** predefinito del progetto GroupCommon, attualmente vuoto.
   
1. Nella parte superiore della pagina, fare clic sulla freccia accanto a **GroupCommon** e selezionare **Manage repositories**.
   
   ![Gestire i repository](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Nella pagina **Impostazioni progetto** selezionare il **...** accanto a **GroupCommon**, quindi scegliere **Rinomina repository**. 
   
   ![Selezionare... e quindi selezionare Rinomina repository](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Nella finestra popup **Rinomina repository GroupCommon** immettere *GroupProjectTemplate*, quindi scegliere **Rinomina**. 
   
   ![Rinomina repository](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Creare il repository GroupUtilities

Per creare il repository **GroupUtilities:**

1. Nella pagina **Riepilogo** progetto comune di **gruppo** selezionare **Repos**. 
   
1. Nella parte superiore della pagina, fare clic sulla freccia accanto a **GroupProjectTemplate** e selezionare **Nuovo repository**.
   
   ![Selezionare Nuovo repository](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Nella finestra di dialogo **Crea un nuovo repository** selezionare **Git** come **Tipo**, immettere *GroupUtilities* come **Nome repository**, quindi selezionare **Crea**.
   
   ![Crea repository GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Nella pagina **Impostazioni progetto** selezionare **Repository** in **Repository** nel riquadro di spostamento sinistro per visualizzare i due repository di gruppo: **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Due repository di gruppo](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importare i repository del team Microsoft TDSP

In questa parte dell'esercitazione si importa il contenuto dei repository **ProjectTemplate** e **Utilities** gestiti dal team di Microsoft TDSP nei repository **GroupProjectTemplate** e **GroupUtilities.** 

Per importare i repository del team TDSP:

1. Nella home page del progetto **GroupCommon** selezionare **Repos** nel riquadro di spostamento sinistro. Viene aperto il repository **GroupProjectTemplate** predefinito. 
   
1. Nella pagina **GroupProjectTemplate è vuota** selezionare **Importa**. 
   
   ![Selezionare Importa](./media/group-manager-tasks/import-repo.png)
   
1. Nella finestra di dialogo **Importa repository Git** selezionare **Git** come tipo **di origine**e immettere *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* per **l'URL di clonazione**. Quindi selezionare **Importa**. Il contenuto del repository ProjectTemplate del team Microsoft TDSP viene importato nel repository GroupProjectTemplate. 
   
   ![Importare l'archivio del team Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. Nella parte superiore della pagina **Repository,** menu 'A tendalto e selezionare il repository **GroupUtilities.**
   
1. Ripetere il processo di importazione per importare il contenuto del repository Microsoft TDSP team **Utilities,** *\/https: /github.com/Azure/Azure-TDSP-Utilities.git*, nel repository **GroupUtilities.** 
   
Ognuno dei due repository di gruppo ora contiene tutti i file, ad eccezione di quelli nella directory *.git,* dal repository corrispondente del team Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalizzare il contenuto dei repository di gruppo

Se si desidera personalizzare il contenuto dei repository di gruppo per soddisfare le esigenze specifiche del gruppo, è possibile farlo ora. È possibile modificare i file, modificare la struttura di directory o aggiungere file sviluppati dal gruppo o utili per il gruppo.

### <a name="make-changes-in-azure-repos"></a>Apportare modifiche in Archiviazione repository

Per personalizzare il contenuto del repository:

1. Nella pagina **Riepilogo** progetto comune di **gruppo** selezionare **Repos**. 
   
1. Nella parte superiore della pagina selezionare il repository che si desidera personalizzare.

1. Nella struttura di directory dei repository, passare alla cartella o al file che si desidera modificare. 
   
   - Per creare nuove cartelle o file, selezionare la freccia accanto a **Nuovo**. 
     
     ![Crea nuovo file](./media/group-manager-tasks/new-file.png)
     
   - Per caricare i file, selezionare **Carica file.** 
     
     ![Caricare file](./media/group-manager-tasks/upload-files.png)
     
   - Per modificare i file esistenti, individuare il file e quindi selezionare **Modifica**. 
     
     ![Modificare un file](./media/group-manager-tasks/edit-file.png)
     
1. Dopo aver aggiunto o modificato i file, selezionare **Commit**.
   
   ![Eseguire il commit delle modifiche](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Apportare modifiche utilizzando il computer locale o DSVM

Se si desidera apportare modifiche utilizzando il computer locale o DSVM ed eseguire il push delle modifiche nei repository di gruppo, assicurarsi di disporre dei prerequisiti per l'utilizzo di Git e DSVM:

- Una sottoscrizione di Azure, se si vuole creare una dSVM.
- Git installato sul computer. Se si utilizza una DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare una DSVM, Windows o Linux DSVM creato e configurato in Azure.If you want to use a DSVM, the Windows or Linux DSVM created and configured in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione](/azure/machine-learning/data-science-virtual-machine/)relativa alle macchine virtuali di data science .
- Per una DSVM di Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installato nel computer. Nel file *README.md* scorrere verso il basso fino alla sezione **Download and Install (Scarica e installa)** e selezionare il programma di installazione più **recente.** Scaricare il programma di installazione *.exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per una dSVM Linux, una chiave pubblica SSH configurata nella DSVM e aggiunta in Azure DevOps.For a Linux DSVM, an SSH public key set up on your DSVM and added in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **Creazione di chiave pubblica SSH** nell'appendice [Piattaforme e strumenti](platforms-and-tools.md#appendix). 

Innanzitutto, copiare o *clonare* il repository nel computer locale. 
   
1. Nella pagina **Riepilogo** progetto **GroupCommon** selezionare **Repos**e nella parte superiore della pagina selezionare il repository da clonare.
   
1. Nella pagina del repository, selezionare **Clona** in alto a destra.
   
1. Nella finestra di dialogo **Clona repository,** selezionare **HTTPS** per una connessione HTTP o **SSH** per una connessione SSH e copiare l'URL del clone in Riga di **comando** negli Appunti.
   
   ![Clonare il repository](./media/group-manager-tasks/clone.png)
   
1. Nel computer locale creare le directory seguenti:
   
   - Per Windows: **C:**
   - Per Linux, **nella** tua home directory/ 
   
1. Passare alla directory creata.
   
1. In Git Bash, eseguire il comando`git clone <clone URL>.`
   
   Ad esempio, uno dei seguenti comandi clona il repository **GroupUtilities** nella directory *GroupCommon* sul computer locale. 
   
   **Connessione HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Connessione SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Dopo aver apportato le modifiche desiderate nel clone locale del repository, è possibile eseguire il push delle modifiche nei repository comuni del gruppo condiviso. 

Eseguire i comandi Git Bash seguenti dalla directory **GroupProjectTemplate** o **GroupUtilities** locale.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se è la prima volta che si esegue il commit in un repository Git, `git commit` potrebbe essere necessario configurare i parametri globali *user.name* e *user.email* prima di eseguire il comando. Eseguire questi due comandi:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se si esegue il commit in diversi repository Git, utilizzare lo stesso nome e indirizzo di posta elettronica per tutti. L'uso dello stesso nome e indirizzo di posta elettronica è utile quando si creano dashboard di Power BI per tenere traccia delle attività Git in più repository.

## <a name="add-group-members-and-configure-permissions"></a>Aggiungere membri del gruppo e configurare le autorizzazioni

Per aggiungere membri al gruppo:

1. In Azure DevOps, dalla home page del progetto **GroupCommon,** selezionare **Impostazioni progetto** nel riquadro di spostamento sinistro. 
   
1. Nel riquadro di spostamento sinistro **Impostazioni progetto** selezionare **Team**, quindi nella pagina **Team** selezionare il **gruppoGruppo comune Team**. 
   
   ![Configurare i team](./media/group-manager-tasks/teams.png)
   
1. Nella pagina **Profilo team** selezionare **Aggiungi**.
   
   ![Aggiungi al gruppo comune](./media/group-manager-tasks/add-to-team.png)
   
1. Nella finestra di dialogo **Aggiungi utenti e gruppi** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare Salva **modifiche**. 
   
   ![Aggiungere utenti e gruppi](./media/group-manager-tasks/add-users.png)
   

Per configurare le autorizzazioni per i membri:

1. Nel riquadro di spostamento sinistro **Impostazioni progetto** selezionare **Autorizzazioni**. 
   
1. Nella pagina **Autorizzazioni** selezionare il gruppo a cui si desidera aggiungere membri. 
   
1. Nella pagina del gruppo selezionare **Membri**, quindi **Aggiungi**. 
   
1. Nel popup **Invita membri** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare **Salva**. 
   
   ![Concedere autorizzazioni ai membri](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i collegamenti alle descrizioni dettagliate degli altri ruoli e attività nel processo di analisi scientifica dei dati del team:

- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Attività di Project Individual Contributor per un team di data scienceProject Individual Contributor tasks for a data science team](project-ic-tasks.md)
