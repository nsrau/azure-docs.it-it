---
title: "Attività del responsabile di progetto nel processo di data science per i team - Azure | Microsoft Docs"
description: "Descrizione delle attività del responsabile di un progetto di data science in team."
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
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: fe7cf1e6167de7d0dfaa5ed75c0b7f5ec23b18cf
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="project-lead-tasks"></a>Attività del responsabile di progetto

Questa esercitazione descrive le attività che un responsabile di progetto deve completare per il team del progetto. L'obiettivo è di stabilire un ambiente di collaborazione per normalizzare il [processo di data science per il team](overview.md) (TDSP, Team Data Science Process). TDSP è un framework sviluppato da Microsoft che fornisce una sequenza strutturata di attività per eseguire in modo efficiente soluzioni di analisi predittiva, basate sul cloud. Per una descrizione dei ruoli del personale e delle relative attività associate gestiti da un team di data science, che vengono standardizzati con il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di data science per i team).

Un **responsabile di progetto** gestisce le attività quotidiane dei singoli data scientist in uno specifico progetto di data science. Il flusso di lavoro delle attività che i responsabili dei progetti devono completare per configurare questo ambiente è descritto nella figura seguente:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Questo argomento illustra attualmente le attività 1, 2 e 6 del flusso di lavoro per i responsabili dei progetti.

>[AZURE.NOTE] Nelle istruzioni seguenti vengono illustrati i passaggi necessari per configurare un ambiente per il team TDSP per un progetto usando Visual Studio Team Services (VSTS). Viene specificato come eseguire queste attività con VSTS perché questa è la modalità di implementazione di TDSP in Microsoft. Se si usa un'altra piattaforma di hosting del codice per il proprio gruppo, le attività che il responsabile del team deve completare di per sé non cambiano, ma sarà diverso il modo in cui queste attività vengono completate.


## <a name="repositories-and-directories"></a>Repository e directory

Questa esercitazione usa nomi abbreviati per i repository e le directory. Questi nomi consentono di seguire più facilmente le operazioni tra i repository e le directory. Questa notazione (R per i repository Git e D per le directory locali nella DSVM) viene usata nelle sezioni seguenti:

- **R3**: il repository **ProjectTemplate** del team in Git che il responsabile del team ha configurato.
- **R5**: il repository del progetto in Git configurato per il progetto.
- **D3**: la directory locale clonata da R3.
- **D5**: la directory locale clonata da R5.


## <a name="0-prerequisites"></a>0. Prerequisiti

I prerequisiti vengono soddisfatti completando le attività assegnate al responsabile del gruppo delineate in [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md) e al responsabile del team delineate in [Team lead tasks for a data science team](team-lead-tasks.md) (Attività del responsabile del team per un team di data science). 

Di seguito vengono riepilogati i prerequisiti che è necessario soddisfare prima di iniziare le attività del responsabile del team: 

- Il responsabile del gruppo deve avere configurato il **server VSTS del gruppo** (o l'account di gruppo in alcune altre piattaforme di hosting del codice).
- Il responsabile del team deve avere configurato il **repository TeamProjectTemplate** (R3) nell'account di gruppo nella piattaforma di hosting del codice che si intende usare.
- Si deve avere l'**autorizzazione** da parte del responsabile del gruppo necessaria per creare i repository nell'account di gruppo per il team.
- Git deve essere installato nel computer. Se si usa una macchina virtuale di data science (DSVM, Data Science Virtual Machine), Git è stato preinstallato ed è possibile iniziare. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).  
- Se si usa una **DSVM per Windows**, è necessario avere installato [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nel computer. Nel file README.md scorrere fino alla sezione **Download and Install** (Download e installazione) e fare clic su *latest installer* (programma di installazione più recente). Si passerà alla pagina del programma di installazione più recente. Scaricare da qui il file con estensione exe del programma di installazione ed eseguirlo. 
- Se si usa una **DSVM per Linux**, creare una chiave pubblica SSH nella DSVM e aggiungerla al server VSTS del gruppo. Per altre informazioni sul protocollo SSH, vedere la sezione **Creare una chiave pubblica SSH** nell'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Creare un repository di progetto (R5)

- Accedere al server VSTS del gruppo all'indirizzo *https://\<Nome del server VSTS\>.visualstudio.com*. 
- In **Progetti e team recenti** fare clic su **Sfoglia**. Viene visualizzata una finestra contenente l'elenco di tutti i progetti team presenti sul server VSTS. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Fare clic sul nome del progetto team in cui si vuole creare il repository del progetto. In questo esempio fare clic su **MyTeam**. 
- Fare quindi clic su **Esplora** per passare alla home page del progetto team **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Fare clic su **Collabora al codice** per passare alla home page Git del progetto team.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Fare clic sulla freccia verso il basso nell'angolo in alto a sinistra e selezionare **+ Nuovo repository**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Nella finestra **Crea un nuovo repository** immettere un nome per il repository Git del progetto. Assicurarsi di selezionare **Git** come tipo del repository. In questo esempio viene usato il nome *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Per creare il repository Git del progetto ***DSProject1***, fare clic su **Crea**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Fornire il contenuto al repository del progetto DSProject1

Questa attività consiste nel fornire contenuto al repository del progetto **DSProject1** (R5) dal repository di modelli di progetto team (R3). La procedura di seeding usa le directory D3 e D5 nella DSVM locale come siti di staging intermedi. In sintesi, il percorso di seeding è: R3 -> D3 -> D5 -> R5.

Se occorre personalizzare il repository del progetto **DSProject1** per soddisfare alcune esigenze specifiche del progetto, è necessario attendere il penultimo passaggio della procedura seguente. Ecco un riepilogo dei passaggi necessari per fornire il contenuto del repository del progetto **DSProject1**. I singoli passaggi corrispondono alle sottosezioni della procedura di seeding:

- Clonare il repository di modelli di progetto team nella directory locale: R3 del team clonato in -> D3 locale.
- Clonare il repository DSProject1 in una directory locale: R5 del team clonato in -> D5 locale.
- Copiare il contenuto del modello di progetto team nel clone locale del repository DSProject1: contenuti di D3 copiati in -> D5.
- (Facoltativo) Personalizzazione di D5 locale.
- Eseguire il push del contenuto di DSProject1 nei repository del team: contenuti di D5 aggiunti a -> R5 del team.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Clonare il repository di modelli di progetto team (R3) in una directory (D3) sul computer locale.

Nel computer locale creare una directory:

- *C:\GitRepos\MyTeamCommon* per Windows 
- *$home/GitRepos/MyTeamCommon* per Linux

Passare a tale directory. Eseguire quindi il comando seguente per clonare il repository di modelli di progetto team nel computer locale. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Se si usa VSTS come piattaforma di hosting del codice, in genere l'*URL HTTPS del repository di modelli di progetto team* è:

 ***https://\<Nome del server VSTS\>.visualstudio.com/\<Nome del progetto team\>/_git/\<Nome del repository di modelli di progetto team\>***. 

In questo esempio:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Se si usa VSTS come piattaforma di hosting del codice, in genere l'*URL SSH del repository di modelli di progetto team* è:

***ssh://\<Nome del server VSTS\>@\<Nome del server VSTS\>.visualstudio.com:22/\<Nome del progetto team>/_git/\<Nome del repository di modelli di progetto team\>.*** 

In questo esempio:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Clonare il repository DSProject1 (R5) in una directory (D5) sul computer locale

Passare alla directory **GitRepos** ed eseguire il comando seguente per clonare il repository del progetto nel computer locale. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Se si usa VSTS come piattaforma di hosting del codice, in genere l'_URL HTTPS del repository del progetto_ è ***https://\<Nome del server VSTS\>.visualstudio.com/\<Nome del progetto team>/_git/<Nome del repository del progetto\>***. In questo esempio l'URL è ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Se si usa VSTS come piattaforma di hosting del codice, in genere l'_URL SSH del repository del progetto_ è _ssh://<Nome del server VSTS\>@<Nome del server VSTS\>.visualstudio.com:22/<Your Team Project Name>/\_git/<Nome del repository del progetto\>. In questo esempio l'URL è ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Copiare i contenuti di D3 in D5 

Nel computer locale è ora necessario copiare il contenuto di _D3_ in _D5_, tranne i metadati Git nella directory con estensione git. Gli script seguenti eseguiranno il processo. Assicurarsi di digitare i percorsi completi e corretti delle directory. La cartella di origine è quella del team (_D3_), mentre la cartella di destinazione è quella del progetto (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

È ora possibile osservare che nella cartella _DSProject1_ tutti i file (tranne quello con estensione git) vengono copiati da _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

È ora possibile osservare che nella cartella _DSProject1_ tutti i file (tranne i metadati in quello con estensione git) vengono copiati da _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Personalizzare D5 se necessario (facoltativo)

Se per il progetto sono necessarie alcune directory o documenti specifici, diversi da quelli ottenuti dal modello di progetto team (copiato nella directory D5 nel passaggio precedente), è possibile personalizzare il contenuto di D5 ora. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Aggiungere i contenuti di DSProject1 in D5 a R5 sul server VSTS del gruppo

È ora necessario eseguire il push dei contenuti di **_DSProject1_** nel repository _R5_ nel progetto team del server VSTS del gruppo. 


- Passare alla directory **D5**. 
- Usare i comandi Git seguenti per aggiungere i contenuti di **D5** in **R5**. I comandi sono gli stessi per i sistemi Windows e Linux. 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- Eseguire il commit e il push della modifica. 

>[AZURE.NOTE] Se è la prima volta che si esegue il commit in un repository Git, è necessario configurare i parametri globali *user.name* e *user.email* prima di eseguire il comando `git commit`. Eseguire questi due comandi:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se si esegue il commit in più repository Git, usare lo stesso nome e indirizzo di posta elettronica in tutti. L'uso dello stesso nome e dello stesso indirizzo e-mail si dimostra utile in un secondo momento, quando si compilano i dashboard di PowerBI per tenere traccia delle attività Git in più repository.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Creare e montare una risorsa di archiviazione file di Azure come risorsa del progetto (facoltativo)

Per creare una risorsa di archiviazione file di Azure per condividere i dati, ad esempio i dati non elaborati del progetto o le funzionalità generate per il progetto, in modo che tutti i membri del progetto abbiano accesso agli stessi set di dati da più DSVM, seguire le istruzioni delle sezioni 3 e 4 di [Team Lead tasks for a data science team](team-lead-tasks.md) (Attività del responsabile del team per un team di data science). 


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Singoli collaboratori al progetto per un team di data science](project-ic-tasks.md)