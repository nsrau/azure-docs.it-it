---
title: "Attività del responsabile del gruppo in un processo di data science per i team - Azure | Microsoft Docs"
description: "Descrizione delle attività di un responsabile del gruppo in un progetto di data science in team."
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
ms.date: 09/04/2017
ms.author: bradsev;
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cd73aed14f672351b72e09d682909a47c63b9026
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---


# <a name="group-manager-tasks"></a>Attività del responsabile del gruppo

Questo argomento illustra le attività che un responsabile del gruppo deve completare per la propria organizzazione di data science. L'obiettivo è stabilire un ambiente di collaborazione per il gruppo, che viene standardizzato con il [processo di data science per i team](overview.md) (TDSP, Team Data Science Process). Per una descrizione dei ruoli del personale e delle relative attività associate gestiti da un team di data science, che vengono standardizzati con il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di data science per i team).

Il **responsabile del gruppo** è il responsabile dell'intera unità di data science in un'azienda. Un'unità di data science può comprendere più team, ognuno dei quali lavora a più progetti di data science in segmenti verticali aziendali distinti. Un responsabile del gruppo può delegare le attività a un sostituto, ma le attività associate al ruolo sono le stesse. Le attività principali sono sei, come illustrato nel diagramma seguente:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Nelle istruzioni seguenti vengono illustrati i passaggi necessari per configurare un ambiente per il gruppo TDSP usando VSTS. Viene specificato come eseguire queste attività con VSTS perché questa è la modalità di implementazione di TDSP in Microsoft. Se per il proprio gruppo viene usata un'altra piattaforma di hosting di codice, le attività che devono essere completate dal responsabile del gruppo in genere non cambiano, ma sarà diverso il modo in cui queste attività vengono completate.

1. Configurare il **server Visual Studio Team Services (VSTS)** per il gruppo.
2. Creare un **progetto team di gruppo** nel server Visual Studio Team Services (per gli utenti VSTS)
3. Creare il repository **GroupProjectTemplate**
4. Creare il repository **GroupUtilities**
5. Fornire ai repository **GroupProjectTemplate** e **GroupUtilities** per il server VSTS il contenuto dei repository TDSP.
6. Configurare i **controlli di sicurezza** per consentire ai membri del team di accedere ai repository GroupProjectTemplate e GroupUtilities.

Ognuno di questi passaggi è descritto in dettaglio, ma prima è necessario acquisire familiarità con le abbreviazioni e illustrare i prerequisiti per usare i repository.

### <a name="abbreviations-for-repositories-and-directories"></a>Abbreviazioni per i repository e le directory

Questa esercitazione usa nomi abbreviati per i repository e le directory. Queste definizioni consentono di seguire più facilmente le operazioni tra i repository e le directory. Nelle sezioni seguenti viene usata questa notazione:

- **G1**: repository di modelli di progetto sviluppato e gestito dal team TDSP di Microsoft.
- **G2**: repository di utilità sviluppato e gestito dal team TDSP di Microsoft.
- **R1**: repository GroupProjectTemplate in Git configurato nel server del gruppo VSTS.
- **R2**: repository GroupUtilities in Git configurato nel server del gruppo VSTS.
- **LG1** e **LG2**: directory locali nel computer in cui si clonano rispettivamente G1 e G2.
- **LR1** e **LR2**: directory locali nel computer in cui si clonano rispettivamente R1 e R2.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Prerequisiti per la clonazione dei repository e per l'archiviazione e l'estrazione del codice
 
- Git deve essere installato nel computer. Se si usa una macchina virtuale di data science (DSVM, Data Science Virtual Machine), Git è stato preinstallato ed è possibile iniziare. In caso contrario, vedere l'[appendice in Platforms and tools](platforms-and-tools.md#appendix) (Piattaforme e strumenti).  
- Se si usa una **DSVM Windows**, è necessario che [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) sia installato nel computer. Nel file README.md scorrere verso il basso fino alla sezione **Download and Install** (Download e installazione) e fare clic su *latest installer* (programma di installazione più recente). Si passerà alla pagina del programma di installazione più recente. Scaricare da qui il programma di installazione EXE ed eseguirlo. 
- Se si usa una **DSVM Linux**, creare una chiave pubblica SSH nella DSVM e aggiungerla al server VSTS del gruppo. Per altre informazioni su SSH, vedere la sezione **Create SSH public key** (Creare una chiave pubblica SSH) nell'[appendice in Platforms and tools](platforms-and-tools.md#appendix) (Piattaforme e strumenti). 


## <a name="1-create-account-on-vsts-server"></a>1. Creare un account nel server VSTS

Il server VSTS ospita i repository seguenti:

- **Repository comuni del gruppo**: repository per utilizzo generico che possono essere adottati da più team di un gruppo per più progetti di data science, ad esempio i repository *GroupProjectTemplate* e *GroupUtilities*.
- **Repository dei team**: repository per team specifici di un gruppo. Questi repository sono specifici delle esigenze di un team e possono essere adottati da più progetti eseguiti da tale team, ma non sono abbastanza generici da essere utili a più team di un gruppo di data science. 
- **Repository dei progetti**: repository disponibili per progetti specifici. Tali repository potrebbero non essere abbastanza generici da essere utili a più progetti eseguiti da un team e a più team di un gruppo di data science.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Configurazione dell'accesso del server VSTS all'account Microsoft
    
Andare a [Visual Studio Online](https://www.visualstudio.com/), fare clic su **Accedi** nell'angolo in alto a destra e accedere all'account Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Se non si ha un account Microsoft, fare clic su **Iscriversi adesso** per creare un account Microsoft e quindi accedere usando questo account. 

Se l'organizzazione ha una sottoscrizione di Visual Studio/MSDN, fare clic sulla casella verde **Accedi con il tuo account aziendale o dell'istituto di istruzione** e accedere con le credenziali associate a questa sottoscrizione. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Dopo l'accesso, fare clic su **Crea nuovo account** nell'angolo in alto a destra, come illustrato nella figura seguente:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Inserire le informazioni per il server VSTS che si vuole creare nella procedura guidata **Crea l'account** usando i valori seguenti: 

- **URL server**: sostituire *mysamplegroup* con il proprio *nome del server*. L'URL del server sarà: *https://\<nomeserver\>.visualstudio.com*. 
- **Gestisci il codice con:** selezionare **_Git_**.
- **Nome progetto:** immettere *GroupCommon*. 
- **Organizza il lavoro con:** scegliere *Agile*.
- **I progetti verranno ospitati in:** scegliere una località geografica. In questo esempio si sceglie *Stati Uniti centro-meridionali*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Se viene visualizzata la finestra popup seguente dopo avere fatto clic su **Crea nuovo account**, è necessario fare clic su **Modifica dettagli** per visualizzare i dettagli di tutti i campi.

![5](./media/group-manager-tasks/create-account-2.png)


Fare clic su **Continue**. 

## <a name="2-groupcommon-team-project"></a>2. Progetto team GroupCommon

Dopo la creazione del server VSTS viene aperta la pagina **GroupCommon** (*https://\<nomeserver\>.visualstudio.com/GroupCommon*).
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Creare il repository GroupUtilities (R2)

Per creare il repository **GroupUtilities** (R2) nel server VSTS:

- Per aprire la procedura guidata **Crea un nuovo repository** fare clic su **Nuovo repository** nella scheda **Controllo della versione** del progetto team. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Selezionare *Git* come **Tipo**, immettere *GroupUtilities* come **Nome** e quindi fare clic su **Crea**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Verranno ora visualizzati due repository Git **GroupProjectTemplate** e **GroupUtilities** nella colonna a sinistra della pagina **Controllo della versione**: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Creare il repository GroupProjectTemplate (R1)

La configurazione dei repository per il server del gruppo VSTS prevede due attività:

- Rinominare il repository **GroupCommon** predefinito come ***GroupProjectTemplate***.
- Creare il repository **GroupUtilities** nel server VSTS nel progetto team **GroupCommon**. 

Le istruzioni per la prima attività sono contenute in questa sezione dopo le note sulle convenzioni di denominazione o su repository e directory. Le istruzioni per la seconda attività sono contenute nella sezione seguente del passaggio 4.

### <a name="rename-the-default-groupcommon-repository"></a>Rinominare il repository GroupCommon predefinito

Per rinominare il repository **GroupCommon** predefinito come *GroupProjectTemplate* (**R1** in questa esercitazione):
    
- Fare clic su **Collabora al codice** nella pagina del progetto team **GroupCommon**. Verrà aperta la pagina del repository Git predefinito per il progetto team **GroupCommon**. Questo repository Git attualmente è vuoto. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Fare clic su **GroupCommon** nell'angolo in alto a sinistra (evidenziato con una casella rossa nella figura seguente) nella pagina del repository Git di **GroupCommon** e selezionare **Gestisci repository** (evidenziato con una casella verde nella figura seguente). Questa procedura visualizza il **PANNELLO DI CONTROLLO**. 
- Selezionare la scheda **Controllo della versione** del progetto team. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Fare clic su **...** a destra del repository **GroupCommon** nel pannello a sinistra e selezionare **Rinomina repository**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- Nella procedura guidata **Rinomina il repository GroupCommon** visualizzata immettere *GroupProjectTemplate* nella casella **Nome repository** e quindi fare clic su **Rinomina**. 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Fornire contenuto ai repository R1 e R2 nel server VSTS

In questa fase della procedura si fornisce contenuto ai repository *GroupProjectTemplate* (R1) e *GroupUtilities* (R2) configurati nella sezione precedente. A questi repository vengono forniti i repository ***ProjectTemplate*** (**G1**) e ***Utilities*** (**G2**) gestiti da Microsoft per il processo di data science per i team. Quando questo seeding viene completato:

- Il repository R1 avrà lo stesso set di directory e modelli di documenti di G1.
- Il repository R2 conterrà il set di utilità di data science sviluppate da Microsoft.

La procedura di seeding usa le directory nella DSVM locale come siti di staging intermedi. Di seguito sono elencati i passaggi seguiti in questa sezione:

- G1 e G2: clonati in -> LG1 e LG2
- R1 e R2: clonati in -> LR1 e LR2
- LG1 e LG2: file copiati in -> LR1 e LR2
- (Facoltativo) personalizzazione di LR1 e LR2
- LR1 e LR2: contenuti aggiunti a -> R1 e R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Clonare i repository G1 e G2 nella DSVM locale

In questo passaggio si clonano il repository (G1) e le utilità (G2) di ProjectTemplate del processo di data science per i team (TDSP) dai repository GitHub TDSP alle cartelle nella DSVM locale come LG1 e LG2:

- Creare una directory che verrà usata come directory radice per ospitare tutti i cloni dei repository. 
    -  Nella DSVM Windows creare una directory *C:\GitRepos\TDSPCommon*. 
    -  Nella DSVM Linux creare una directory *GitRepos\TDSPCommon* nella home directory. 

- Usare il set di comandi seguente dalla directory *GitRepos\TDSPCommon*.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Gli script hanno dato i risultati seguenti, in cui vengono usati i nomi dei repository abbreviati: 
    - G1: clonato in -> LG1
    - G2: clonato in -> LG2
- Al termine della clonazione, verranno visualizzate due directory, _ProjectTemplate_ e _Utilities_, nella directory **GitRepos\TDSPCommon**. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Clonare i repository R1 e R2 nella DSVM locale

In questo passaggio si clonano il repository GroupProjectTemplate (R1) e il repository GroupUtilities (R2) nelle directory locali (rispettivamente LR1 e LR2) in **GitRepos\GroupCommon** nella DSVM.

- Per ottenere gli URL dei repository R1 e R2, andare alla home page di **GroupCommon** in VSTS, il cui URL in genere è *https://\<nome del server VSTS\>.visualstudio.com/GroupCommon*. 
- Fare clic su **CODICE**. 
- Scegliere i repository **GroupProjectTemplate** e **GroupUtilities**. Copiare e salvare ogni URL (HTTPS per Windows, SSH per Linux) dall'elemento **URL clonazione** per usarli negli script seguenti:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Passare alla directory **GitRepos\GroupCommon** sulla DSVM Windows o Linux ed eseguire uno dei set di comandi seguenti per clonare R1 e R2 in tale directory.
        
Ecco gli script Windows e Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Verranno visualizzati messaggi di avviso indicanti che LR1 e LR2 sono vuote.    

- Gli script hanno dato i risultati seguenti, in cui vengono usati i nomi dei repository abbreviati: 
    - R1: clonato in -> LR1
    - R2: clonato in -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Fornire contenuto a GroupProjectTemplate (LR1) e GroupUtilities (LR2)

Nel computer locale copiare ora il contenuto delle directory ProjectTemplate e Utilities (tranne i metadati nelle directory con estensione git) sotto GitRepos\TDSPCommon nelle directory GroupProjectTemplate e GroupUtilities sotto **GitRepos\GroupCommon**. Ecco le due attività da completare in questo passaggio:

- Copiare i file contenuti in GitRepos\TDSPCommon\ProjectTemplate (**LG1**) in GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Copiare i file contenuti in GitRepos\TDSPCommon\Utilities (**LG2** in GitRepos\GroupCommon\Utilities (**LR2**). 

Per completare queste due attività, eseguire gli script seguenti nella console di PowerShell (Windows) o nella console script della shell (Linux). Viene chiesto di immettere i percorsi completi in LG1, LR1, LG2 e LR2. I percorsi immessi vengono convalidati. Se si immette una directory che non esiste, viene chiesto di immetterla nuovamente. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

È ora possibile osservare che i file nelle directory LG1 e LG2 (tranne i file nella directory con estensione git) sono stati rispettivamente copiati in LR1 e LR2.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Si osserva ora che i file nelle due cartelle (tranne i file nella directory con estensione git) vengono copiati rispettivamente in GroupProjectTemplate e in GroupUtilities.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Gli script hanno dato i risultati seguenti, in cui vengono usati i nomi dei repository abbreviati:
    - LG1: file copiati in -> LR1
    - LG2: file copiati in -> LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opzione per personalizzare i contenuti di LR1 e LR2
    
Questa fase della procedura è ideale per personalizzare i contenuti di LR1 e LR2 per soddisfare le esigenze specifiche del gruppo. È possibile modificare i documenti modello, cambiare la struttura di directory e aggiungere utilità esistenti sviluppate dal gruppo o utili per l'intero gruppo. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Aggiungere i contenuti in LR1 e LR2 a R1 e R2 nel server del gruppo

È ora necessario aggiungere i contenuti in LR1 e LR2 ai repository R1 e R2. Ecco i comandi di Git Bash che è possibile eseguire in Windows PowerShell o in Linux. 

Eseguire questi comandi dalla directory GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

L'opzione -m consente di impostare un messaggio per il commit Git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

È possibile osservare che nel repository GroupProjectTemplate sul server VSTS del gruppo i file vengono sincronizzati istantaneamente.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Passare infine alla directory **GitRepos\GroupCommon\GroupUtilities** ed eseguire lo stesso set di comandi di Git Bash:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Se è la prima volta che si esegue il commit in un repository Git, è necessario configurare i parametri globali *user.name* e *user.email* prima di eseguire il comando `git commit`. Eseguire questi due comandi:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Se si esegue il commit in più repository Git, usare lo stesso nome e indirizzo di posta elettronica quando si esegue il commit in ognuno. L'uso dello stesso nome e indirizzo di posta elettronica si dimostra utile successivamente quando si compilano i dashboard di PowerBI per tenere traccia delle attività Git in più repository.


- Gli script hanno dato i risultati seguenti, in cui vengono usati i nomi dei repository abbreviati:
    - LR1: contenuti aggiunti a -> R1
    - LR2: contenuti aggiunti a -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Aggiungere membri del gruppo al server del gruppo

Dalla home page del server VSTS del gruppo fare clic sull'**icona a forma di ingranaggio** accanto al nome utente nell'angolo in alto a destra, quindi selezionare la scheda **Sicurezza**, in cui è possibile aggiungere membri al gruppo con diverse autorizzazioni.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni più dettagliate dei ruoli e delle attività definite dal processo di data science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Singoli collaboratori al progetto per un team di data science](project-ic-tasks.md)
