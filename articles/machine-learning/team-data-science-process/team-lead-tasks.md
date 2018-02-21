---
title: "Attività del responsabile del team nel processo di data science per i team - Azure | Microsoft Docs"
description: "Descrizione delle attività di un responsabile del team in un progetto team di data science."
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
ms.openlocfilehash: 58e8bfeb547fbcd7f472fbed50073b1ca3176b72
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="team-lead-tasks"></a>Attività del responsabile del team

Questo argomento illustra le attività che un responsabile del team deve completare per il proprio team di data science. L'obiettivo è di stabilire un ambiente di collaborazione nel team per normalizzare il [processo di data science per i team](overview.md) (TDSP, Team Data Science Process). Il processo di data science per i team (TDSP) è una metodologia di data science agile e iterativa, mirata a offrire in modo efficiente soluzioni di analisi predittiva e applicazioni intelligenti. Il processo è studiato per migliorare la collaborazione e l'apprendimento del team. Il processo è un'ottimizzazione di strutture e procedure consigliate di Microsoft e di settore, ai fini di una corretta implementazione di iniziative di data science volte ad aiutare le aziende a realizzare appieno i vantaggi dei propri programmi di analisi. Per una descrizione dei ruoli del personale e delle rispettive attività associate che un team di data science gestisce allo scopo di normalizzare il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di data science per i team).

Un **responsabile del team** gestisce un team nell'unità di data science di un'azienda. Un team è costituito da più data scientist. Nelle unità di data science con un numero ridotto di data scientist, il **responsabile del gruppo** e il **responsabile del team** possono essere la stessa persona o possono delegare le attività a un sostituto. Le attività da svolgere restano tuttavia le stesse. Il flusso di lavoro delle attività che i responsabili dei team devono completare per impostare questo ambiente è descritto nella figura seguente:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Le attività nei blocchi 1 e 2 della figura sono necessarie se si usa Visual Studio Team Services (VSTS) come piattaforma di hosting del codice e si vuole disporre di un progetto team separato per il proprio team. Dopo che tutte le attività sono state completate, è possibile creare tutti i repository del team nel progetto team. 

Dopo che il responsabile del gruppo ha completato le numerose attività preliminari specificate nella sezione che segue, è possibile completare le cinque attività di base, alcune delle quali sono facoltative, disponibili in questa esercitazione. Queste attività corrispondono alle sezioni principali numerate di questo argomento:

1. Creare un **progetto team** nel server VSTS del gruppo e due repository del team nel progetto:
    - **Repository ProjectTemplate** 
    - **Repository TeamUtilities**
2. Effettuare il seeding del repository **ProjectTemplate** del team dal repository **GroupProjectTemplate** configurato dal responsabile del gruppo. 
3. Creare le risorse di analisi e di dati del team:
    - Aggiungere le utilità specifiche del team nel repository **TeamUtilities**. 
    - (Facoltativo) Creare una **risorsa di archiviazione file di Azure** da usare per archiviare gli asset di dati che possono essere utili a tutto il team. 
4. (Facoltativo) Montare la risorsa di archiviazione file di Azure nella **macchina virtuale data science** (DSVM) del responsabile del team e aggiungervi gli asset di dati.
5. Configurare il **controllo di sicurezza** aggiungendo i membri del team e configurandone i privilegi.

>[AZURE.NOTE] Nelle istruzioni seguenti vengono illustrati i passaggi necessari per configurare un ambiente TDSP usando VSTS. Viene specificato come eseguire queste attività con VSTS poiché questa è la modalità di implementazione del TDSP usata in Microsoft. Se si usa un'altra piattaforma di hosting del codice per il proprio gruppo, le attività che il responsabile del team deve completare di per sé non cambiano. È invece diverso il modo in cui devono essere completate.

## <a name="repositories-and-directories"></a>Repository e directory

In questo argomento vengono usati nomi abbreviati per i repository e le directory, per permettere di seguire più facilmente le operazioni tra i repository e le directory. Questa notazione (**R** per i repository Git e **D** per le directory locali nel DSVM) viene usata nelle sezioni seguenti:

- **R1**: il repository **GroupProjectTemplate** in Git configurato dal responsabile del gruppo nel server VSTS del gruppo.
- **R3**: il repository **ProjectTemplate** del team in Git configurato dall'utente.
- **R4**: il repository **TeamUtilities** in Git configurato dall'utente.
- **D1**: la directory locale clonata da R1 e copiata in D3.
- **D3**: la directory locale clonata da R3, personalizzata e copiata nuovamente in R3.
- **D4**: la directory locale clonata da R4, personalizzata e copiata nuovamente in R4.

I nomi specificati per i repository e le directory in questa esercitazione sono finalizzati all'obiettivo di definire un progetto team separato per il proprio team all'interno di un gruppo di data science più grande. Esistono tuttavia altre opzioni che si possono scegliere in qualità di responsabile del team:

- L'intero gruppo può scegliere di creare un singolo progetto team. In questo caso, tutti i progetti di tutti i team di data science rientrano nel progetto team singolo. È possibile a tal fine designare un amministratore Git che segua queste istruzioni per creare un progetto team singolo. Questo scenario potrebbe essere valido, ad esempio, per gli scenari seguenti:
    -  un gruppo di data science di piccole dimensioni che dispone di un solo team di data science 
    -  un gruppo di data science di dimensioni più grandi con più team di data science che intende comunque ottimizzare la collaborazione tra i team con attività come la pianificazione sprint a livello di gruppo. 
- I team possono scegliere di usare modelli di progetto specifici del team o utilità specifiche del team nel progetto team singolo per l'intero gruppo. I responsabili dei team devono in questo caso creare repository di modelli di progetto team e/o repository di utilità dei team nello stesso progetto team. Assegnare a questi repository nomi secondo il formato *<TeamName\>ProjectTemplate* e *<TeamName\>Utilities*, ad esempio *TeamJohnProjectTemplate* e *TeamJohnUtilities*. 

I responsabili dei team devono, in ogni caso, indicare ai membri dei rispettivi team quali repository di modelli e di utilità adottare quando configurano e clonano i repository del progetto e delle utilità. I responsabili di progetto devono eseguire le [attività di responsabile di progetto per un team di data science](project-lead-tasks.md) per creare i repository del progetto, indipendentemente che si sia scelto di usare progetti team separati o un progetto team singolo. 


## <a name="0-prerequisites"></a>0. prerequisiti

I prerequisiti vengono soddisfatti completando le attività assegnate al responsabile del gruppo delineate in [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md). Di seguito vengono riepilogati i prerequisiti che è necessario soddisfare prima di iniziare le attività di responsabile del team: 

- Il responsabile del gruppo deve avere configurato il **server VSTS del gruppo** (o l'account di gruppo in alcune altre piattaforme di hosting del codice).
- Il responsabile del gruppo deve avere impostato il **repository GroupProjectTemplate** (R1) nell'account di gruppo nella piattaforma di hosting del codice che si intende usare.
- Si deve disporre dell'**autorizzazione** nell'account di gruppo necessaria per creare i repository per il team.
- Git deve essere installato nel computer in uso. Se si usa una macchina virtuale di data science (DSVM, Data Science Virtual Machine), Git è stato preinstallato ed è possibile iniziare. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).  
- Se si usa una **DSVM per Windows**, è necessario avere installato [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nel computer. Nel file README.md scorrere fino alla sezione **Download and Install** (Download e installazione) e fare clic su *latest installer* (programma di installazione più recente). Si passerà alla pagina del programma di installazione più recente. Scaricare da qui il file con estensione exe del programma di installazione ed eseguirlo. 
- Se si usa una **DSVM per Linux**, creare una chiave pubblica SSH nella DSVM e aggiungerla al server VSTS del gruppo. Per altre informazioni sul protocollo SSH, vedere la sezione **Creare una chiave pubblica SSH** nell'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Creare un progetto team e i repository

Completare questo passaggio se si usa VSTS come piattaforma di hosting del codice per il controllo delle versioni e la collaborazione. Questa sezione illustra come creare tre elementi nel server VSTS del gruppo:

- Progetto **MyTeam** in VSTS
- Repository **MyProjectTemplate** (**R3**) in Git
- Repository **MyTeamUtilities** (**R4**) in Git

### <a name="create-the-myteam-project"></a>Creare il progetto MyTeam

- Passare alla home page del server VSTS del gruppo nell'URL `https://<VSTS Server Name\>.visualstudio.com`. 
- Fare clic su **Nuovo** per creare un progetto team. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Verrà visualizzata una finestra Crea progetto team in cui immettere il nome del progetto che, in questo esempio, è **MyTeam**. Verificare di selezionare **Agile** come **Modello di processo** e **Git** come **Controllo della versione**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Fare clic su **Crea progetto**. In meno di un minuto verrà creato il progetto team **MyTeam**. 

- Dopo che il progetto team **MyTeam** è stato creato, fare clic sul pulsante **Passa a progetto** per passare alla home page del progetto team. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Se viene visualizzata una finestra popup **Congratulazioni**, fare clic sul pulsante **Aggiungi codice** nel riquadro rosso. In caso contrario, fare clic su **Codice** nel riquadro giallo. Questa azione apre la pagina del repository Git del progetto team. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Creare il repository MyProjectTemplate (R3) in Git

- Nella pagina del repository Git del progetto team, fare clic sulla freccia verso il basso accanto al nome del repository **MyTeam**e selezionare **Gestisci repository**.

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Nella scheda **Controllo della versione** del pannello di controllo del progetto team, fare clic su **MyTeam**, quindi selezionare **Rinomina repository**. 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Immettere un nuovo nome per il repository nella finestra **Rinomina il repository MyTeam**, in questo esempio *MyTeamProjectTemplate*. È possibile scegliere un formato del tipo *<Nome del team\>ProjectTemplate*. Fare clic su **Rinomina** per continuare.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Creare il repository MyTeamUtilities (R4) in Git

- Per creare un nuovo repository *<nome del team\>Utilities* nel progetto team, fare clic su **Nuovo repository** nella scheda **Controllo della versione** del pannello di controllo del progetto team.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- Nella finestra **Crea un nuovo repository** che viene visualizzata immettere un nome per il repository. In questo esempio gli si assegna il nome *MyTeamUtilities*, seguendo quindi la notazione **R4**. Scegliere un nome del tipo *< nome del team\>Utilities*. Verificare di selezionare **Git** in **Tipo**. Fare quindi clic su **Crea** per continuare.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Verificare che i due nuovi repository Git creati siano presenti nel progetto team **MyTeam**. Esempio: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Effettuare il seeding dei repository ProjectTemplate e TeamUtilities del team

La procedura di seeding usa le directory nella DSVM locale come siti di staging intermedi. Se occorre personalizzare i repository **ProjectTemplate** e **TeamUtilities** per soddisfare alcune esigenze specifiche del team, è necessario attendere il penultimo passaggio della procedura seguente. Di seguito è riportato un riepilogo dei passaggi usati per effettuare il seeding del contenuto dei repository **MyTeamProjectTemplate** e **MyTeamUtilities** per un team di data science. I singoli passaggi corrispondono alle sottosezioni della procedura di seeding:

- Clonare il repository del gruppo nella directory locale: team R1 - clonato in -> D1 locale
- Clonare i repository del team nelle directory locali: team R3 e R4 - clonati in -> D3 e D4 locali
- Copiare il contenuto del modello di progetto del gruppo nella cartella del team locale: D1 - contenuto copiato in -> D3
- (Facoltativo) Personalizzazione di D3 e D4 locali
- Inserire il contenuto delle directory locali nei repository del team: D3 e D4 - contenuti aggiunti in -> R3 e R4 del team


### <a name="initialize-the-team-repositories"></a>Inizializzare i repository del team

In questo passaggio, si inizializza il repository del modello di progetto team dal repository del modello di progetto del gruppo:

- Repository **MyTeamProjectTemplate** (**R3**) dal repository **GroupProjectTemplate** (**R1**)


### <a name="clone-group-repositories-into-local-directories"></a>Clonare i repository del gruppo nelle directory locali

Per iniziare questa procedura:

- Creare le directory nel computer locale:
    - Per **Windows**: **C:\GitRepos\GroupCommon** e **C:\GitRepos\MyTeam**
    - Per **Linux**: **GitRepos\GroupCommon** e **GitRepos\MyTeam** nella home directory 
- Passare alla directory **GitRepos\GroupCommon**.
- Eseguire il comando riportato di seguito, in base alle esigenze, nel sistema operativo del computer locale.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Questi comandi clonano il repository **GroupProjectTemplate** (R1) nel server VSTS del gruppo nella directory locale **GitRepos\GroupCommon** del computer locale. Dopo la clonazione, viene creata la directory di **GroupProjectTemplate** (D1) nella directory **GitRepos\GroupCommon**. In questo caso, si presuppone che il responsabile del gruppo abbia creato un progetto team **GroupCommon**e il repository **GroupProjectTemplate** nel progetto team. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Clonare i repository del team nelle directory locali

Questi comandi clonano i repository **MyTeamProjectTemplate** (R3) e **MyTeamUtilities** (R4) del progetto team **MyTeam** nel server VSTS del gruppo nelle directory **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) nella directory **GitRepos\MyTeam** del computer locale. 

- Passare alla directory **GitRepos\MyTeam**.
- Eseguire i comandi riportati di seguito, in base alle esigenze, nel sistema operativo del computer locale. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Dopo la clonazione, le due directory **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) vengono create nella directory **GitRepos\MyTeam**. Si presuppone qui che i repository del modello del progetto team e delle utilità siano stati denominati **MyTeamProjectTemplate** e **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Copiare il contenuto del modello di progetto del gruppo nella directory del modello di progetto team locale

Per copiare il contenuto della cartella **GroupProjectTemplate** (D1) locale nella cartella **MyTeamProjectTemplate** (D3) locale, eseguire uno degli script della shell seguenti: 

####<a name="from-the-powershell-command-line-for-windows"></a>Dalla riga di comando di PowerShell per Windows        

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

####<a name="from-the-linux-shell-for-the-linux-dsvm"></a>Dalla shell di Linux per **DSVM per Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Gli script escludono i contenuti della directory .git. Gli script richiedono di inserire i **percorsi completi** alla directory di origine D1 e alla directory di destinazione D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Personalizzare il modello di progetto team o le utilità team (facoltativo)

Se necessario, personalizzare **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) in questa fase del processo di impostazione. 

- Se si intende personalizzare il contenuto di D3 per soddisfare specifiche esigenze del team, è possibile modificare i documenti di modello o la struttura di directory.

- Se il proprio team ha sviluppato alcune utilità che si vuole condividere con tutto il team, copiare e incollare le utilità nella directory D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Inserire il contenuto della directory locale nei repository del team

Per aggiungere il contenuto delle directory locali (facoltativamente personalizzate) D3 e D4 nei repository R3 e R4 del team, eseguire i Git bash seguenti da una console di Windows PowerShell o dalla shell di Linux. Eseguire i comandi dalla directory **GitRepos\MyTeam\MyTeamProjectTemplate**.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

L'esecuzione di questo script determina la sincronizzazione praticamente istantanea dei file nel repository MyTeamProjectTemplate del server VSTS del gruppo.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Eseguire ora lo stesso set di quattro comandi git dalla directory **GitRepos\MyTeam\MyTeamUtilities**. 

> [AZURE.NOTE]Se è la prima volta che si esegue il commit in un repository Git, è necessario configurare i parametri globali *user.name* e *user.email* prima di eseguire il comando `git commit`. Eseguire questi due comandi:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se si esegue il commit in più repository Git, usare lo stesso nome e lo stesso indirizzo e-mail per eseguire il commit in ognuno. L'uso dello stesso nome e dello stesso indirizzo e-mail si dimostra utile in un secondo momento, quando si compilano i dashboard di PowerBI per tenere traccia delle attività Git in più repository.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Creare risorse di analisi e di dati del team (facoltativo)

La condivisione delle risorse di analisi e di dati con l'intero team offre vantaggi in termini di prestazioni e costi: i membri del team possono svolgere i progetti nelle risorse condivise, risparmiare sui budget e collaborare in modo più efficiente. Questa sezione include le istruzioni per creare una risorsa di archiviazione file di Azure. La sezione successiva include le istruzioni per montare la risorsa di archiviazione file di Azure nel computer locale. Per altre informazioni sulla condivisione di altre risorse, ad esempio macchine virtuali data science di Azure, cluster Spark di Azure HDInsight, vedere [Strumenti e piattaforme](platforms-and-tools.md). Questo argomento include materiale sussidiario di data science sulla selezione di risorse adeguate alle proprie esigenze, nonché collegamenti a pagine di prodotto e altre esercitazioni pertinenti e utili pubblicati da Microsoft.

>[AZURE.NOTE] Per evitare la trasmissione di dati tra data center, che potrebbe essere lenta e costosa, verificare che il gruppo di risorse, l'account di archiviazione e la VM di Azure (ad esempio DSVM) si trovino nello stesso data center di Azure. 

Eseguire gli script seguenti per creare la risorsa di archiviazione file di Azure per il team. La risorsa di archiviazione file di Azure per il team può essere usata per archiviare gli asset di dati che sono utili a tutto il team. Gli script richiedono che vengano immesse le informazioni sulla sottoscrizione e sull'account di Azure. È necessario quindi averle pronte per l'inserimento. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Creare la risorsa di archiviazione file di Azure con PowerShell da Windows

Nella riga di comando di PowerShell eseguire questo comando:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Al prompt accedere con l'account Microsoft Azure:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Selezionare la sottoscrizione di Azure da usare:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Selezionare l'account di archiviazione da usare o crearne uno nuovo nella sottoscrizione selezionata:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Immettere il nome della risorsa di archiviazione file di Azure da creare. Sono accettati solo caratteri minuscoli e numeri:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Per facilitare il montaggio e la condivisione di questa risorsa di archiviazione dopo averla creata, salvare le informazioni sulla risorsa di archiviazione file di Azure in un file di testo e prendere nota del relativo percorso. Questo file è necessario in particolare per montare la risorsa di archiviazione file di Azure nelle macchine virtuali di Azure nella sezione successiva. 

È consigliabile archiviare il file di testo nel repository ProjectTemplate del team. Si consiglia di inserirlo nella directory **Docs\DataDictionaries**, in modo che tutti i progetti del team possano avere accesso a questo asset di dati. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Creare una risorsa di archiviazione file di Azure con uno script di Linux

Eseguire questo script dalla shell di Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Accedere al proprio account Microsoft Azure attenendosi alle istruzioni in questa schermata:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Selezionare la sottoscrizione di Azure che si vuole usare:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Selezionare l'account di archiviazione da usare o crearne uno nuovo nella sottoscrizione selezionata:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Immettere il nome della risorsa di archiviazione file di Azure da creare. Sono accettati solo caratteri minuscoli e numeri:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Per facilitare l'accesso a questa risorsa di archiviazione dopo averla creata, salvare le informazioni sulla risorsa in un file di testo e prendere nota del relativo percorso. Questo file è necessario in particolare per montare la risorsa di archiviazione file di Azure nelle macchine virtuali di Azure nella sezione successiva.

È consigliabile archiviare il file di testo nel repository ProjectTemplate del team. Si consiglia di inserirlo nella directory **Docs\DataDictionaries**, in modo che tutti i progetti del team possano avere accesso a questo asset di dati. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Montare la risorsa di archiviazione file di Azure (facoltativo)

La risorsa di archiviazione file di Azure creata può essere montata nel computer locale usando uno degli script di PowerShell o Linux seguenti.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Montare la risorsa di archiviazione file di Azure con PowerShell da Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Viene chiesto prima di tutto di eseguire l'accesso, se non si è già connessi. 

Premere **INVIO** o fare clic su **s** per continuare quando viene chiesto se si dispone di un file di informazioni sulla risorsa di archiviazione file di Azure. Immettere quindi il ***percorso e il nome completi** del file creato nel passaggio precedente. Le informazioni per montare una risorsa di archiviazione file di Azure vengono lette direttamente da questo file. Si può quindi procedere al passaggio successivo.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Se non si dispone di un file con le informazioni sulla risorsa di archiviazione file di Azure, alla fine di questa sezione è disponibile la procedura per immettere le informazioni dalla tastiera.

Viene chiesto a questo punto di immettere il nome dell'unità da aggiungere alla macchina virtuale. Viene visualizzato un elenco di nomi di unità esistenti. È consigliabile immettere un nome di unità che non esiste già in questo elenco.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Verificare che una nuova unità F sia stata montata nel computer in uso.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Come immettere manualmente le informazioni nella risorsa di archiviazione file di Azure:** se non si dispone di un file di testo con le informazioni sulla risorsa di archiviazione file di Azure, è possibile seguire le istruzioni visualizzate nella schermata seguente per immettere le necessarie informazioni sulla sottoscrizione, l'account di archiviazione e la risorsa di archiviazione file di Azure:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Digitare il nome della sottoscrizione di Azure, selezionare l'account di archiviazione in cui viene creata la risorsa di archiviazione file di Azure e immettere il nome della risorsa di archiviazione file di Azure:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Montare una risorsa di archiviazione file di Azure con uno script di Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Viene chiesto prima di tutto di eseguire l'accesso, se non si è già connessi. 

Premere **INVIO** o fare clic su **s** per continuare quando viene chiesto se si dispone di un file di informazioni sulla risorsa di archiviazione file di Azure. Immettere quindi il ***percorso e il nome completi** del file creato nel passaggio precedente. Le informazioni per montare una risorsa di archiviazione file di Azure vengono lette direttamente da questo file. Si può quindi procedere al passaggio successivo.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Viene chiesto a questo punto di immettere il nome dell'unità da aggiungere alla macchina virtuale. Viene visualizzato un elenco di nomi di unità esistenti. È consigliabile immettere un nome di unità che non esiste già in questo elenco.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Verificare che una nuova unità F sia stata montata nel computer in uso.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Come immettere manualmente le informazioni nella risorsa di archiviazione file di Azure:** se non si dispone di un file di testo con le informazioni sulla risorsa di archiviazione file di Azure, è possibile seguire le istruzioni visualizzate nella schermata seguente per immettere le necessarie informazioni sulla sottoscrizione, l'account di archiviazione e la risorsa di archiviazione file di Azure:

- Immettere **n**.
- Selezionare l'indice del nome della sottoscrizione in cui è stata creata la risorsa di archiviazione file di Azure nel passaggio precedente:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Selezionare l'account di archiviazione nella sottoscrizione e immettere il nome della risorsa di archiviazione file di Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Immettere il nome dell'unità da aggiungere al computer. È consigliabile che questo nome sia diverso da quelli già esistenti:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Configurare i criteri di controllo di sicurezza 

Nella home page del server VSTS del gruppo fare clic sull'**icona a forma di ingranaggio** accanto al nome utente nell'angolo in alto a destra, quindi selezionare la scheda **Protezione**, in cui è possibile aggiungere membri al team con varie autorizzazioni.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Singoli collaboratori al progetto per un team di data science](project-ic-tasks.md)