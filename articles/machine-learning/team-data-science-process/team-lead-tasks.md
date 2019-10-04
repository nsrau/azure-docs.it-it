---
title: Attività per il responsabile del team nel team del processo di data science del team
description: Una procedura dettagliata delle attività per un responsabile del team per un team del processo di Data Science per i team
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9c6d0fcc20afc613094f10e9f3fb7c917ec6fa73
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327241"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Attività per il responsabile del team per un team del processo di Data Science per i team

Questo articolo descrive le attività completate da un responsabile del *Team* per il team Data Science. L'obiettivo del responsabile del team è stabilire un ambiente collaborativo del team che standardizza il [processo di Data Science](overview.md) per i team (TDSP). Il TDSP è progettato per contribuire a migliorare la collaborazione e l'apprendimento del team. 

TDSP è una metodologia agile e iterativa data science per offrire in modo efficiente soluzioni di analisi predittiva e applicazioni intelligenti. Il processo è una distillazione delle procedure consigliate e delle strutture di Microsoft e del settore, necessarie per una corretta implementazione di data science iniziative per aiutare le aziende a realizzare pienamente i vantaggi dei propri programmi di analisi. Per una descrizione dei ruoli del personale e delle attività associate per un team di data science la standardizzazione in TDSP, vedere [ruoli e attività del processo di Data Science](roles-tasks.md)per i team.

Un responsabile del team gestisce un team costituito da diversi data scientist nell'unità data science di un'azienda. A seconda delle dimensioni e della struttura dell'unità data science, il [responsabile del gruppo](group-manager-tasks.md) e il responsabile del team possono essere la stessa persona oppure possono delegare le attività ai surrogati. Le attività da svolgere restano tuttavia le stesse. 

Il diagramma seguente illustra il flusso di lavoro per le attività che il responsabile del team ha completato per configurare un ambiente del team:

![Flusso di lavoro attività Lead Team](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Creare un **progetto team** nell'organizzazione del gruppo in Azure DevOps. 
  
1. Rinominare il repository del team predefinito in **TeamUtilities**.
  
1. Creare un nuovo repository **teamTemplate** nel progetto team. 
  
1. Importare i contenuti dei repository **GroupUtilities** e **GroupProjectTemplate** del gruppo nei repository **TeamUtilities** e **teamTemplate** . 
  
1. Configurare il **controllo di sicurezza** aggiungendo i membri del team e configurando le autorizzazioni.
  
1. Se necessario, creare risorse di analisi e dati del team:
   - Aggiungere le utilità specifiche del team al repository **TeamUtilities** . 
   - Creare **archiviazione file di Azure** per archiviare gli asset di dati che possono essere utili per l'intero team. 
   - Montare la risorsa di archiviazione file di Azure per la **Data Science Virtual Machine** del responsabile del team (DSVM) e aggiungere gli asset di dati.

Nell'esercitazione riportata di seguito vengono illustrati i passaggi dettagliati.

> [!NOTE] 
> Questo articolo usa Azure DevOps e un DSVM per configurare un ambiente del team TDSP, perché questo è il modo in cui implementare TDSP in Microsoft. Se il team USA altre piattaforme di hosting o sviluppo di codice, le attività del responsabile del team sono le stesse, ma il modo per completarle può essere diverso.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che le risorse e le autorizzazioni seguenti siano state impostate dal [responsabile del gruppo](group-manager-tasks.md):

- **Organizzazione** DevOps di Azure per l'unità dati
- Repository **GroupProjectTemplate** e **GroupUtilities** , popolati con il contenuto dei repository **ProjectTemplate** e **Utilities** del team di Microsoft TDSP
- Autorizzazioni per l'account dell'organizzazione per la creazione di progetti e repository per il team

Per essere in grado di clonare i repository e di modificarne il contenuto nel computer locale o in DSVM o configurare l'archiviazione file di Azure e montarla nella DSVM, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- Git installato nel computer. Se si usa un DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare un DSVM, Windows o Linux DSVM creato e configurato in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/).
- Per un DSVM Windows, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) è installato nel computer. Nel file *Readme.MD* scorrere verso il basso fino alla sezione **download and install** e selezionare il **programma di installazione più recente**. Scaricare il programma di installazione con *estensione exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per un DSVM Linux, viene configurata una chiave pubblica SSH nella DSVM e aggiunta in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **create SSH Public Key** nell' [appendice Platforms and Tools](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Creare un progetto team e i repository

In questa sezione vengono create le risorse seguenti nell'organizzazione DevOps di Azure del gruppo:

- Progetto **Team** in Azure DevOps
- Repository **teamTemplate**
- Repository **TeamUtilities**

I nomi specificati per i repository e le directory in questa esercitazione presuppongono che si desideri definire un progetto separato per il proprio team all'interno di un'organizzazione data science più grande. Tuttavia, l'intero gruppo può scegliere di lavorare con un unico progetto creato dal responsabile del gruppo o dall'amministratore dell'organizzazione. Quindi, tutti i team di data science creano repository in questo unico progetto. Questo scenario potrebbe essere valido per:
- Un piccolo gruppo di data science che non dispone di più team data science. 
- Un gruppo di data science più grande con più team data science che vogliono comunque ottimizzare la collaborazione tra i team con attività quali la pianificazione dello sprint a livello di gruppo. 

Se i team scelgono di disporre dei repository specifici del team in un singolo progetto di gruppo, i lead del team devono creare i repository con nomi quali  *\<TeamName > template* e  *\<TeamName > Utilities*. Ad esempio: *TeamATemplate* e *TeamAUtilities*. 

In ogni caso, i lead del team devono consentire ai membri del team di comprendere quali sono i repository del modello e delle utilità da configurare e clonare. I lead del progetto devono seguire le [attività del responsabile del progetto per un team di Data Science](project-lead-tasks.md) per creare repository di progetto, sia in progetti distinti che in un singolo progetto. 

### <a name="create-the-myteam-project"></a>Creare il progetto MyTeam

Per creare un progetto separato per il team:

1. Nel Web browser passare al gruppo Azure DevOps Organization Home page all'URL *https:\//\<nome server >/\<nome organizzazione >* e selezionare **nuovo progetto**. 
   
   ![Seleziona nuovo progetto](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Nella finestra di dialogo **Crea progetto** immettere il nome del team, ad esempio *Team*, in **nome progetto**, quindi selezionare **Avanzate**. 
   
1. In **controllo della versione**selezionare **git**e in **processo elemento di lavoro**selezionare **agile**. Selezionare quindi **Crea**. 
   
   ![Crea progetto](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Verrà visualizzata la pagina **Riepilogo** progetto team con URL pagina *https:\/\</nome server >/\<nome organizzazione >/\<nome Team >* .

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Rinominare il repository predefinito del team in TeamUtilities

1. Nella pagina **Riepilogo** progetto **Team** , in **quale servizio si desidera iniziare?** , selezionare **repository**. 
   
   ![Seleziona repository](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Nella pagina del repository del **Team** selezionare il repository di **Team** nella parte superiore della pagina e quindi selezionare **Gestisci repository** dall'elenco a discesa. 
   
   ![Selezionare Gestisci repository](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Nella pagina **Impostazioni progetto** selezionare il **...** accanto al repository di **Team** e quindi selezionare **Rinomina repository**. 
   
   ![Seleziona Rinomina repository](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Nel popup **Rinomina il repository del team** immettere *TeamUtilities*, quindi selezionare **Rinomina**. 

### <a name="create-the-teamtemplate-repository"></a>Creare il repository TeamTemplate

1. Nella pagina **Impostazioni progetto** selezionare **nuovo repository.** 
   
   ![Seleziona nuovo repository](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   In alternativa, **selezionare repository dal menu** di spostamento a sinistra della pagina **Riepilogo** progetto **Team** , selezionare un repository nella parte superiore della pagina e quindi selezionare **nuovo repository** nell'elenco a discesa.
   
1. Nella finestra di dialogo **Crea un nuovo repository** assicurarsi che **git** sia selezionato in **tipo**. Immettere *teamTemplate* in **nome repository**, quindi selezionare **Crea**.
   
   ![Crea repository](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Verificare che sia possibile visualizzare i due repository **TeamUtilities** e **teamTemplate** nella pagina delle impostazioni del progetto. 
   
   ![Repository di due team](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importa il contenuto dei repository comuni del gruppo

Per popolare i repository del team con il contenuto dei repository comuni del gruppo impostati dal responsabile del gruppo:

1. Dal progetto **team** Home page selezionare **repository** nel percorso di spostamento a sinistra. Se viene visualizzato un messaggio che indica che il modello non è stato trovato, **selezionare il collegamento** in **caso contrario, passare al repository teamTemplate predefinito.** 
   
   Viene aperto il repository **teamTemplate** predefinito. 
   
1. Nella pagina **teamTemplate è vuota** selezionare Import ( **Importa**). 
   
   ![Selezionare Importa](./media/team-lead-tasks/import-repo.png)
   
1. Nella finestra di dialogo **Importa un repository git** selezionare **git** come **tipo di origine**e immettere l'URL per il repository del modello comune di gruppo in **URL clone**. L'URL è *https: \/ @ no__t-2 @ no__t-3Server nome >/\<organization nome >/_git/\<repository nome >* . Ad esempio: *https: \//dev. Azure. com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selezionare **Importa**. Il contenuto del repository del modello di gruppo viene importato nel repository del modello Team. 
   
   ![Importa il repository del modello comune del gruppo](./media/team-lead-tasks/import-repo-2.png)
   
1. Nella parte superiore **della pagina dei repository del** progetto, fare clic sull'elenco a discesa e selezionare il repository **TeamUtilities** .
   
1. Ripetere il processo di importazione per importare il contenuto del repository delle utilità comuni del gruppo, ad esempio *GroupUtilities*, nel repository **TeamUtilities** . 
   
Ognuno dei due repository del team contiene ora i file del repository comune del gruppo corrispondente. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Personalizzare il contenuto dei repository del team

Per personalizzare il contenuto dei repository del team in modo da soddisfare le esigenze specifiche del team, è possibile farlo ora. È possibile modificare i file, modificare la struttura di directory o aggiungere file e cartelle.

Per modificare, caricare o creare file o cartelle direttamente in Azure DevOps:

1. Nella pagina **Riepilogo** progetto di **Team** selezionare **repository**. 
   
1. Nella parte superiore della pagina selezionare il repository che si desidera personalizzare.

1. Nella struttura di directory del repository passare alla cartella o al file che si desidera modificare. 
   
   - Per creare nuove cartelle o file, selezionare la freccia accanto a **nuovo**. 
     
     ![Crea nuovo file](./media/team-lead-tasks/new-file.png)
     
   - Per caricare i file, selezionare **Carica file/i**. 
     
     ![Carica file](./media/team-lead-tasks/upload-files.png)
     
   - Per modificare i file esistenti, passare al file e quindi fare clic su **modifica**. 
     
     ![Modificare un file](./media/team-lead-tasks/edit-file.png)
     
1. Dopo aver aggiunto o modificato i file, selezionare **commit**.
   
   ![Esegui commit delle modifiche](./media/team-lead-tasks/commit.png)

Per lavorare con i repository nel computer locale o in DSVM, è prima necessario copiare o *clonare* i repository nel computer locale, quindi eseguire il commit e il push delle modifiche nei repository del team condivisi, 

Per clonare i repository:

1. Nella pagina **Riepilogo** progetto **Team** selezionare repository e nella parte superiore della **pagina selezionare il**repository che si desidera clonare.
   
1. Nella pagina Repository selezionare **Clone** in alto a destra.
   
1. Nella finestra di dialogo **clona repository** , in **riga di comando**, selezionare **https** per una connessione HTTP o **SSH** per una connessione SSH, quindi copiare l'URL del clone negli Appunti.
   
   ![Copia URL clone](./media/team-lead-tasks/clone.png)
   
1. Nel computer locale creare le directory seguenti:
   
   - Per Windows: **C:\GitRepos\MyTeam**
   - Per Linux, **$Home/gitrepos/myTeam** 
   
1. Passare alla directory creata.
   
1. In git bash eseguire il comando `git clone <clone URL>`, dove \<clone URL > è l'URL copiato dalla finestra di dialogo **Clone** .
   
   Ad esempio, usare uno dei comandi seguenti per clonare il repository **TeamUtilities** nella directory di *Team* nel computer locale. 
   
   **Connessione HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Connessione SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Dopo aver apportato tutte le modifiche desiderate nel clone locale del repository, eseguire il commit e il push delle modifiche nei repository del team condiviso. 

Eseguire i seguenti comandi di git bash dalla directory **GitRepos\MyTeam\TeamTemplate** o **GitRepos\MyTeam\TeamUtilities** locale.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se è la prima volta che si esegue il commit in un repository git, potrebbe essere necessario configurare i parametri globali *User.Name* e *User. email* prima di `git commit` eseguire il comando. Eseguire questi due comandi:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se si esegue il commit in più repository git, usare lo stesso nome e lo stesso indirizzo di posta elettronica per tutti. L'uso dello stesso nome e dell'indirizzo di posta elettronica è utile quando si compilano Power BI Dashboard per tenere traccia delle attività git in più repository.

## <a name="add-team-members-and-configure-permissions"></a>Aggiungere i membri del team e configurare le autorizzazioni

Per aggiungere membri al team:

1. In Azure DevOps, dal progetto **team** Home page selezionare **Impostazioni progetto** nel percorso di spostamento a sinistra. 
   
1. Dal percorso di spostamento a sinistra **Impostazioni progetto** selezionare **Teams**, quindi nella pagina **Teams** selezionare il **Team**del team. 
   
   ![Configurare i team](./media/team-lead-tasks/teams.png)
   
1. Nella pagina **profilo Team** selezionare **Aggiungi**.
   
   ![Aggiungi al team del team](./media/team-lead-tasks/add-to-team.png)
   
1. Nella finestra di dialogo **Aggiungi utenti e gruppi** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare **Salva modifiche**. 
   
   ![Aggiungi utenti e gruppi](./media/team-lead-tasks/add-users.png)
   

Per configurare le autorizzazioni per i membri del team:

1. Nella finestra di spostamento a sinistra **Impostazioni progetto** selezionare **autorizzazioni**. 
   
1. Nella pagina **autorizzazioni** selezionare il gruppo al quale si desidera aggiungere membri. 
   
1. Nella pagina del gruppo selezionare **membri**, quindi selezionare **Aggiungi**. 
   
1. Nella finestra popup **invita membri** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare **Salva**. 
   
   ![Concedere le autorizzazioni ai membri](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Creare risorse di analisi e di dati del team

Questo passaggio è facoltativo, ma la condivisione di dati e risorse di analisi con l'intero team offre vantaggi in termini di prestazioni e costi. I membri del team possono eseguire i progetti sulle risorse condivise, risparmiare sui budget e collaborare in modo più efficiente. È possibile creare l'archiviazione file di Azure e montarla nella DSVM per condividerla con i membri del team. 

Per informazioni sulla condivisione di altre risorse con il team, ad esempio Azure HDInsight Spark cluster, vedere [piattaforme e strumenti](platforms-and-tools.md). In questo argomento vengono fornite indicazioni da una data science prospettiva sulla selezione di risorse appropriate per le proprie esigenze e collegamenti a pagine di prodotto e altre esercitazioni rilevanti e utili.

>[!NOTE]
> Per evitare di trasmettere i dati nei data center, che potrebbero essere lenti e costosi, assicurarsi che il gruppo di risorse di Azure, l'account di archiviazione e DSVM siano tutti ospitati nella stessa area di Azure. 

### <a name="create-azure-file-storage"></a>Creazione di archiviazione file di Azure

1. Eseguire lo script seguente per creare l'archiviazione file di Azure per gli asset di dati utili per l'intero team. Lo script richiede le informazioni sulla sottoscrizione di Azure, quindi è possibile immetterle. 

   - In un computer Windows, eseguire lo script dal prompt dei comandi di PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - In un computer Linux, eseguire lo script dalla shell di Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Accedere all'account di Microsoft Azure quando richiesto, quindi selezionare la sottoscrizione che si vuole usare.
   
1. Selezionare l'account di archiviazione da usare o crearne uno nuovo nella sottoscrizione selezionata. Per il nome di archiviazione file di Azure è possibile usare caratteri minuscoli, numeri e trattini.
   
1. Per facilitare il montaggio e la condivisione dello spazio di archiviazione, premere INVIO o immettere *Y* per salvare le informazioni di archiviazione file di Azure in un file di testo nella directory corrente. È possibile archiviare il file di testo nel repository **teamTemplate** , idealmente in **Docs\DataDictionaries**, in modo che tutti i progetti del team possano accedervi. Sono necessarie anche le informazioni sui file per montare l'archiviazione file di Azure nella DSVM di Azure nella sezione successiva. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Montare archiviazione file di Azure nel computer locale o DSVM

1. Per montare l'archiviazione file di Azure nel computer locale o in DSVM, usare lo script seguente.
   
   - In un computer Windows, eseguire lo script dal prompt dei comandi di PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - In un computer Linux, eseguire lo script dalla shell di Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Premere INVIO o immettere *Y* per continuare se è stato salvato un file di informazioni di archiviazione file di Azure nel passaggio precedente. Immettere il percorso completo e il nome del file creato. 
   
   Se non si dispone di un file di informazioni di archiviazione file di Azure, immettere *n*e seguire le istruzioni per immettere le informazioni relative a sottoscrizione, account di archiviazione di Azure e archiviazione file di Azure.
   
1. Immettere il nome di un'unità locale o TDSP in cui montare la condivisione file. Nella schermata viene visualizzato un elenco di nomi di unità esistenti. Specificare un nome di unità che non esista già.
   
1. Verificare che la nuova unità e l'archiviazione siano montate correttamente nel computer.

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni dettagliate degli altri ruoli e delle attività definiti dal processo di Data Science per i team:

- [Attività del responsabile del gruppo per un team di data science](group-manager-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Attività dei singoli collaboratori del progetto per un team di data science](project-ic-tasks.md)
