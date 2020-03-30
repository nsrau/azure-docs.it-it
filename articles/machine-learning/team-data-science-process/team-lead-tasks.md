---
title: Attività per il responsabile del team nel team del processo di data science del team
description: Una procedura dettagliata delle attività per un responsabile del team in un team del processo di analisi scientifica dei dati del team
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864282"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Attività per il team responsabile in un team del processo di analisi scientifica dei dati del team

In questo articolo vengono descritte le attività completate da un *responsabile* del team per il team di data science. L'obiettivo del responsabile del team è quello di creare un ambiente di team collaborativo che standardizza il processo di analisi scientifica [dei dati](overview.md) del team (TDSP). Il TDSP è progettato per contribuire a migliorare la collaborazione e l'apprendimento in team. 

Il TDSP è una metodologia di data science agile e iterativa per fornire in modo efficiente soluzioni di analisi predittiva e applicazioni intelligenti. Il processo distilla le procedure consigliate e le strutture di Microsoft e del settore.  L'obiettivo è la riuscita implementazione di iniziative di data science e la piena realizzazione dei vantaggi dei loro programmi di analisi. Per una descrizione dei ruoli del personale e delle attività associate per un team di data science standardizzato nel TDSP, vedere Ruoli e attività del processo di analisi scientifica dei dati del [team.](roles-tasks.md)

Un responsabile di un team gestisce un team composto da diversi data scientist nell'unità di data science di un'azienda. A seconda delle dimensioni e della struttura dell'unità di data science, il [responsabile](group-manager-tasks.md) del gruppo e il responsabile del team potrebbero essere la stessa persona oppure potrebbero delegare le attività ai surrogati. Le attività da svolgere restano tuttavia le stesse. 

Nel diagramma seguente viene illustrato il flusso di lavoro per le attività completate dal responsabile del team per configurare un ambiente team:

![Flusso di lavoro delle attività di lead team](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Creare un progetto team nell'organizzazione del gruppo in DevOps di Azure.Create a **team project** in the group's organization in Azure DevOps. 
  
1. Rinominare il repository del team predefinito in **TeamUtilities**.
  
1. Creare un nuovo repository **TeamTemplate** nel progetto team. 
  
1. Importare il contenuto dei repository **GroupUtilities** e **GroupProjectTemplate** del gruppo nei repository **TeamUtilities** e **TeamTemplate.** 
  
1. Impostare il controllo di **sicurezza** aggiungendo membri del team e configurando le relative autorizzazioni.
  
1. Se necessario, crea i dati del team e le risorse di analisi:
   - Aggiungere utilità specifiche del team al repository **TeamUtilities.** 
   - Creare **l'archiviazione** di file di Azure per archiviare gli asset di dati che possono essere utili per l'intero team. 
   - Montare l'archiviazione di file di Azure nella macchina virtuale Data Science (DSVM) del responsabile del team e aggiungervi asset di dati. **Data Science Virtual Machine**

L'esercitazione seguente illustra i passaggi in dettaglio.

> [!NOTE] 
> Questo articolo usa DevOps di Azure e una macchina dSVM per configurare un ambiente di team TDSP, perché è possibile implementare TDSP in Microsoft.This article uses Azure DevOps and a DSVM to set up a TDSP team environment, because that is how to implement TDSP at Microsoft. Se il team utilizza altre piattaforme di hosting o sviluppo del codice, le attività di team lead sono le stesse, ma il modo per completarle potrebbe essere diverso.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che le risorse e le autorizzazioni seguenti siano state impostate dal gestore del [gruppo:](group-manager-tasks.md)

- Organizzazione DevOps di Azure per **l'unità** datiThe Azure DevOps organization for your data unit
- Repository **GroupProjectTemplate** e **GroupUtilities,** popolati con il contenuto dei repository **ProjectTemplate** e **Utilities** del team Microsoft TDSP
- Autorizzazioni per l'account dell'organizzazione per la creazione di progetti e repository per il team

Per poter clonare i repository e modificarne il contenuto nel computer locale o in DSVM oppure configurare l'archiviazione file di Azure e montarlo nella DSVM, è necessario quanto segue:To be able to clone repositories and modify their content on your local machine or DSVM, or set up Azure file storage and mount it to your DSVM, you need the following:

- Una sottoscrizione di Azure.
- Git installato sul computer. Se si utilizza una DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare una DSVM, Windows o Linux DSVM creato e configurato in Azure.If you want to use a DSVM, the Windows or Linux DSVM created and configured in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione](/azure/machine-learning/data-science-virtual-machine/)relativa alle macchine virtuali di data science .
- Per una DSVM di Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installato nel computer. Nel file *README.md* scorrere verso il basso fino alla sezione **Download and Install (Scarica e installa)** e selezionare il programma di installazione più **recente.** Scaricare il programma di installazione *.exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per una dSVM Linux, una chiave pubblica SSH configurata nella DSVM e aggiunta in Azure DevOps.For a Linux DSVM, an SSH public key set up on your DSVM and added in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **Creazione di chiave pubblica SSH** nell'appendice [Piattaforme e strumenti](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Creare un progetto team e i repository

In questa sezione vengono create le risorse seguenti nell'organizzazione DevOps di Azure del gruppo:In this section, you create the following resources in your group's Azure DevOps organization:

- Il progetto MyTeam in Azure DevOpsThe **MyTeam** project in Azure DevOps
- Il repository **TeamTemplate**
- Il repository **TeamUtilities**

I nomi specificati per i repository e le directory in questa esercitazione presuppongono che si desideri stabilire un progetto separato per il proprio team all'interno dell'organizzazione di data science più ampia. Tuttavia, l'intero gruppo può scegliere di lavorare in un singolo progetto creato dal responsabile del gruppo o dall'amministratore dell'organizzazione. Quindi, tutti i team di data science creano repository nell'ambito di questo singolo progetto. Questo scenario potrebbe essere valido per:This scenario might be valid for:
- Un piccolo gruppo di data science che non dispone di più team di data science. 
- Gruppo di data science più ampio con più team di data science che tuttavia desidera ottimizzare la collaborazione tra team con attività quali la pianificazione dello sprint a livello di gruppo. 

Se i team scelgono di disporre di repository specifici del team in un singolo progetto di gruppo, i responsabili del team devono creare i repository con nomi quali * \<NomeTeam>Template* e * \<NomeTeam>Utilities*. Ad esempio: *TeamATemplate* e *TeamAUtilities*. 

In ogni caso, i lead del team devono informare i repository di modelli e utilità da configurare e clonare. I responsabili del progetto devono seguire le [attività di responsabile](project-lead-tasks.md) del progetto per un team di data science per creare repository di progetto, in progetti separati o in un singolo progetto. 

### <a name="create-the-myteam-project"></a>Creare il progetto MyTeam

Per creare un progetto separato per il team:

1. Nel Web browser passare alla home page dell'organizzazione Azure DevOps del gruppo all'URL *https:\//\<nome server>/nome\<organizzazione>* e selezionare Nuovo **progetto**. 
   
   ![Selezionare Nuovo progetto](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Nella finestra di dialogo **Crea progetto** immettere il nome del team, ad esempio *Team ,* in **Nome progetto**, quindi selezionare **Avanzate**. 
   
1. In **Controllo della versione**selezionare **Git**e in Processo elemento di **lavoro**selezionare **Agile**. Quindi selezionare **Crea**. 
   
   ![Crea progetto](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Viene visualizzata la pagina **Riepilogo** progetto team, con l'URL della pagina *https:\//\<nome server>/nome\<organizzazione>/nome\<team>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Rinominare il repository predefinito MyTeam in TeamUtilities

1. Nella pagina **Riepilogo** progetto **MyTeam,** in **Quale servizio si desidera iniziare?**, selezionare **Repos**. 
   
   ![Seleziona Repository](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Nella pagina Repository **MyTeam** selezionare il repository **MyTeam** nella parte superiore della pagina e quindi selezionare **Gestisci repository** dall'elenco a discesa. 
   
   ![Selezionare Gestisci repository](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Nella pagina **Impostazioni progetto** selezionare il **...** accanto al repository **MyTeam,** quindi scegliere **Rinomina repository**. 
   
   ![Selezionare Rinomina repository](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Nella finestra popup **Rinomina repository MyTeam** immettere *TeamUtilities*, quindi selezionare **Rinomina**. 

### <a name="create-the-teamtemplate-repository"></a>Creare il repository TeamTemplate

1. Nella pagina **Impostazioni progetto** selezionare **Nuovo repository.** 
   
   ![Selezionare Nuovo repository](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   In alternativa, selezionare **Repos** nella barra di spostamento sinistra della pagina **Riepilogo** progetto **MyTeam,** selezionare un repository nella parte superiore della pagina e quindi selezionare **Nuovo repository** dall'elenco a discesa.
   
1. Nella finestra di dialogo **Crea un nuovo repository,** assicurarsi che **Git** sia selezionato in **Tipo**. Immettere *TeamTemplate* in **Nome repository**, quindi selezionare **Crea**.
   
   ![Crea repository](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Verificare che nella pagina delle impostazioni del progetto sia possibile visualizzare i due repository **TeamUtilities** e **TeamTemplate.** 
   
   ![Due repository di squadra](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importare il contenuto dei repository comuni del gruppo

Per popolare i repository del team con il contenuto dei repository comuni di gruppo impostati dal responsabile del gruppo:

1. Nella home page del progetto **MyTeam** selezionare **Repos** nel riquadro di spostamento sinistro. Se viene visualizzato un messaggio che indica che il modello **MyTeam** non viene trovato, selezionare il collegamento in **Altrimenti, passare al repository TeamTemplate predefinito.** 
   
   Verrà aperto il repository **TeamTemplate** predefinito. 
   
1. Nella pagina **TeamTemplate è vuota** selezionare **Importa**. 
   
   ![Selezionare Importa](./media/team-lead-tasks/import-repo.png)
   
1. Nella finestra di dialogo **Importa repository Git** selezionare **Git** come tipo **di origine**e immettere l'URL per il repository dei modelli comuni del gruppo in **Clona URL**. L'URL è *\//\<https:\<nome del server\<>/ nome dell'organizzazione>/_git/ nome del repository>*. Ad esempio: *\/https: /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selezionare **Importa**. Il contenuto del repository dei modelli di gruppo viene importato nel repository dei modelli di team. 
   
   ![Importa repository di modelli comuni di gruppo](./media/team-lead-tasks/import-repo-2.png)
   
1. Nella parte superiore della pagina **Repository** del progetto, menu a discesa e selezionare il repository **TeamUtilities.**
   
1. Ripetere il processo di importazione per importare il contenuto del repository delle utilità comuni del gruppo, ad esempio *GroupUtilities*, nel repository **TeamUtilities.** 
   
Ognuno dei repository di due team ora contiene i file dal repository comune del gruppo corrispondente. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Personalizzare il contenuto dei repository del team

Se si desidera personalizzare il contenuto dei repository del team per soddisfare le esigenze specifiche del team, è possibile farlo ora. È possibile modificare i file, modificare la struttura della directory o aggiungere file e cartelle.

Per modificare, caricare o creare file o cartelle direttamente in DevOps di Azure:To modify, upload, or create files or folders directly in Azure DevOps:

1. Nella pagina **Riepilogo** progetto **MyTeam** selezionare **Repository**. 
   
1. Nella parte superiore della pagina selezionare il repository che si desidera personalizzare.

1. Nella struttura di directory dei repository, passare alla cartella o al file che si desidera modificare. 
   
   - Per creare nuove cartelle o file, selezionare la freccia accanto a **Nuovo**. 
     
     ![Crea nuovo file](./media/team-lead-tasks/new-file.png)
     
   - Per caricare i file, selezionare **Carica file.** 
     
     ![Caricare file](./media/team-lead-tasks/upload-files.png)
     
   - Per modificare i file esistenti, individuare il file e quindi selezionare **Modifica**. 
     
     ![Modificare un file](./media/team-lead-tasks/edit-file.png)
     
1. Dopo aver aggiunto o modificato i file, selezionare **Commit**.
   
   ![Eseguire il commit delle modifiche](./media/team-lead-tasks/commit.png)

Per utilizzare i repository nel computer locale o DSVM, copiare o *clonare* i repository nel computer locale, quindi eseguire il commit e il push delle modifiche nei repository del team condiviso, 

Per clonare i repository:

1. Nella pagina **Riepilogo** progetto **MyTeam** selezionare **Repository**e nella parte superiore della pagina selezionare il repository che si desidera clonare.
   
1. Nella pagina del repository, selezionare **Clona** in alto a destra.
   
1. Nella finestra di dialogo **Clona repository,** in **Riga di comando,** selezionare **HTTPS** per una connessione HTTP o **SSH** per una connessione SSH e copiare l'URL del clone negli Appunti.
   
   ![Copia URL clone](./media/team-lead-tasks/clone.png)
   
1. Nel computer locale creare le directory seguenti:
   
   - Per Windows: **C:**
   - Per Linux, **$home/GitRepos/MyTeam** 
   
1. Passare alla directory creata.
   
1. In Git Bash, `git clone <clone URL>`eseguite \<il comando , dove URL clone> è l'URL copiato dalla finestra di dialogo **Clona.**
   
   Ad esempio, utilizzare uno dei seguenti comandi per clonare il repository **TeamUtilities** nella directory *MyTeam* sul computer locale. 
   
   **Connessione HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Connessione SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Dopo aver apportato le modifiche desiderate nel clone locale del repository, eseguire il commit e il push delle modifiche nei repository del team condiviso. 

Eseguire i comandi Git Bash riportati di seguito dalla directory locale **GitRepos, MyTeam** o TeamTemplate o **GitRepos.**

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

## <a name="add-team-members-and-configure-permissions"></a>Aggiungere membri del team e configurare le autorizzazioni

Per aggiungere membri al team:

1. In Azure DevOps, dalla home page del progetto **MyTeam,** selezionare **Impostazioni progetto** nel riquadro di spostamento sinistro. 
   
1. Nel riquadro di spostamento sinistro **Impostazioni progetto** selezionare **Team**, quindi nella pagina **Team** selezionare team **personale**. 
   
   ![Configurare i team](./media/team-lead-tasks/teams.png)
   
1. Nella pagina **Profilo team** selezionare **Aggiungi**.
   
   ![Aggiungi a MyTeam](./media/team-lead-tasks/add-to-team.png)
   
1. Nella finestra di dialogo **Aggiungi utenti e gruppi** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare Salva **modifiche**. 
   
   ![Aggiungere utenti e gruppi](./media/team-lead-tasks/add-users.png)
   

Per configurare le autorizzazioni per i membri del team:

1. Nel riquadro di spostamento sinistro **Impostazioni progetto** selezionare **Autorizzazioni**. 
   
1. Nella pagina **Autorizzazioni** selezionare il gruppo a cui si desidera aggiungere membri. 
   
1. Nella pagina del gruppo selezionare **Membri**, quindi **Aggiungi**. 
   
1. Nel popup **Invita membri** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare **Salva**. 
   
   ![Concedere autorizzazioni ai membri](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Creare dati del team e risorse di analisiCreate team data and analytics resources

Questo passaggio è facoltativo, ma la condivisione di dati e risorse di analisi con l'intero team offre vantaggi in termini di prestazioni e costi. I membri del team possono eseguire i propri progetti sulle risorse condivise, risparmiare sui budget e collaborare in modo più efficiente. È possibile creare l'archiviazione file di Azure e montarla nella DSVM per condividerla con i membri del team. 

Per informazioni sulla condivisione di altre risorse con il team, ad esempio i cluster Spark di Azure HDInsight, vedere [Piattaforme e strumenti.](platforms-and-tools.md) In questo argomento vengono fornite indicazioni dal punto di vista dell'analisi scientifica dei dati sulla selezione delle risorse appropriate in base alle proprie esigenze e collegamenti a pagine di prodotto e altre esercitazioni pertinenti e utili.

>[!NOTE]
> Per evitare di trasmettere dati tra data center, che potrebbero essere lenti e costosi, assicurarsi che il gruppo di risorse di Azure, l'account di archiviazione e DSVM siano tutti ospitati nella stessa area di Azure.To avoid transmitting data across data centers, which might be slow and costly, make sure that your Azure resource group, storage account, and DSVM are all hosted in the same Azure region. 

### <a name="create-azure-file-storage"></a>Creare l'archiviazione dei file di AzureCreate Azure file storage

1. Eseguire lo script seguente per creare l'archiviazione dei file di Azure per gli asset di dati utili per l'intero team. Lo script richiede le informazioni sulla sottoscrizione di Azure, quindi è possibile immettere. 

   - In un computer Windows eseguire lo script dal prompt dei comandi di PowerShell:On a Windows machine, run the script from the PowerShell command prompt:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - In un computer Linux, eseguire lo script dalla shell Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Quando richiesto, accedere all'account Microsoft Azure e selezionare la sottoscrizione che si vuole usare.
   
1. Selezionare l'account di archiviazione da usare o crearne uno nuovo nella sottoscrizione selezionata. È possibile usare caratteri minuscoli, numeri e trattini per il nome di archiviazione file di Azure.You can use lowercase characters, numbers, and hyphens for the Azure file storage name.
   
1. Per facilitare il montaggio e la condivisione dell'archiviazione, premere INVIO o immettere *Y* per salvare le informazioni di archiviazione dei file di Azure in un file di testo nella directory corrente. È possibile archiviare questo file di testo nel repository **TeamTemplate,** idealmente in **Docs, DataDictionaries**, in modo che tutti i progetti del team possano accedervi. Sono inoltre necessarie le informazioni sui file per montare l'archiviazione file di Azure nella macchina DSVM di Azure nella sezione successiva. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Montare l'archiviazione file di Azure nel computer locale o DSVM

1. Per montare l'archiviazione file di Azure nel computer locale o dSVM, usare lo script seguente.
   
   - In un computer Windows eseguire lo script dal prompt dei comandi di PowerShell:On a Windows machine, run the script from the PowerShell command prompt:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - In un computer Linux, eseguire lo script dalla shell Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Premere INVIO o immettere *Y* per continuare, se è stato salvato un file di informazioni sull'archiviazione file di Azure nel passaggio precedente. Immettere il percorso completo e il nome del file creato. 
   
   Se non si dispone di un file di informazioni sull'archiviazione file di Azure, immettere n e seguire le istruzioni per immettere la sottoscrizione, l'account di archiviazione di Azure e le informazioni di archiviazione file di Azure.If you don't have an Azure file storage information file, enter *n*, and follow the instructions to enter your subscription, Azure storage account, and Azure file storage information.
   
1. Immettere il nome di un'unità locale o TDSP su cui montare la condivisione file. Nella schermata viene visualizzato un elenco dei nomi di unità esistenti. Specificare un nome di unità che non esiste già.
   
1. Verificare che la nuova unità e la nuova risorsa di archiviazione siano montate correttamente nel computer.

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i collegamenti alle descrizioni dettagliate degli altri ruoli e attività definiti dal processo di analisi scientifica dei dati del team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Attività di Project Individual Contributor per un team di data scienceProject Individual Contributor tasks for a data science team](project-ic-tasks.md)
