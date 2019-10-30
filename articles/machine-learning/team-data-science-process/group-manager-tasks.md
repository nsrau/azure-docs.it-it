---
title: Attività del responsabile del gruppo in un processo di data science per i team
description: Seguire questa procedura dettagliata delle attività completate da un responsabile del gruppo in un progetto team data science.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a3d23950f5cbfaac00b03b25e3c19078c76ad0a5
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053314"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Attività del responsabile del gruppo in un processo di data science per i team

Questo articolo descrive le attività che un *responsabile del gruppo* completa per un'organizzazione Data Science. Il responsabile del gruppo gestisce l'intera unità di data science in un'organizzazione. Un'unità di data science può avere più team, ognuno dei quali sta lavorando a molti progetti di data science in verticali aziendali distinti. L'obiettivo del responsabile del gruppo è creare un ambiente di gruppo collaborativo che standardizza il [processo di Data Science](overview.md) per i team (TDSP). Per una descrizione di tutti i ruoli del personale e delle attività associate gestite da un team data science la standardizzazione di TDSP, vedere [ruoli e attività del processo di Data Science](roles-tasks.md)per i team.

Nel diagramma seguente vengono illustrate le sei attività di configurazione principali del gruppo di gestione. I responsabili del gruppo possono delegare le attività ai surrogati, ma le attività associate al ruolo non cambiano.

![Attività del responsabile del gruppo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Configurare un' **organizzazione DevOps di Azure** per il gruppo.
2. Creare il **progetto GroupCommon** predefinito nell'organizzazione DevOps di Azure.
3. Creare il repository **GroupProjectTemplate** in Azure Repos.
4. Creare il repository **GroupUtilities** in Azure Repos.
5. Importare i contenuti dei repository **ProjectTemplate** e **Utilities** del team di Microsoft TDSP nel repository comune del gruppo.
6. Configurare l' **appartenenza** e le **autorizzazioni** per i membri del team per accedere al gruppo.

Nell'esercitazione riportata di seguito vengono illustrati i passaggi dettagliati. 

> [!NOTE] 
> Questo articolo usa Azure DevOps per configurare un ambiente di gruppo TDSP, perché questo è il modo in cui implementare TDSP in Microsoft. Se il gruppo usa altre piattaforme di hosting o sviluppo di codice, le attività del responsabile del gruppo sono le stesse, ma il modo per completarle può essere diverso.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Creare un'organizzazione e un progetto in Azure DevOps

1. Passare a [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com), selezionare **Accedi** in alto a destra e accedere al account Microsoft. 
   
   ![Effettuare l'accesso con l'account Microsoft.](./media/group-manager-tasks/signinvs.png)
   
   Se non si dispone di un account Microsoft, selezionare **Iscriviti adesso**, creare una account Microsoft e accedere con questo account. Se l'organizzazione ha una sottoscrizione di Visual Studio, accedere con le credenziali per la sottoscrizione.
   
1. Dopo aver eseguito l'accesso, nella parte superiore destra della pagina Azure DevOps selezionare **Crea nuova organizzazione**.
   
   ![Crea nuova organizzazione](./media/group-manager-tasks/create-organization.png)
   
1. Se viene richiesto di accettare le condizioni del servizio, l'informativa sulla privacy e il codice di comportamento, selezionare **continua**.
   
1. Nella finestra di dialogo di iscrizione assegnare un nome all'organizzazione DevOps di Azure e accettare l'assegnazione dell'area host oppure fare clic su elenco a discesa e selezionare un'area diversa. Selezionare quindi **Continua**. 

1. In **Crea un progetto per**iniziare immettere *GroupCommon*, quindi selezionare **Crea progetto**. 
   
   ![Crea progetto](./media/group-manager-tasks/create-project.png)

Verrà visualizzata la pagina di **Riepilogo** del progetto **GroupCommon** . L'URL della pagina è *https:\//\<nomeserver >/\<nome-organizzazione >/GroupCommon*.

![Pagina Riepilogo progetto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Configurare i repository comuni del gruppo

Azure Repos ospita i seguenti tipi di repository per il gruppo:

- **Repository comuni di gruppo**: repository di uso generico che possono essere adottati da più team all'interno di un'unità Data Science per molti progetti di Data Science. 
- **Repository del team**: repository per team specifici all'interno di un'unità Data Science. Questi repository sono specifici per le esigenze di un team e possono essere usati per più progetti all'interno del team, ma non sono sufficientemente generici da essere usati in più team all'interno di un'unità data science.
- **Repository di progetto**: repository per progetti specifici. Tali repository potrebbero non essere sufficientemente generici per più progetti all'interno di un team o per altri team in un'unità data science.

Per configurare i repository comuni del gruppo nel progetto, è necessario: 
- Rinominare il repository **GroupCommon** predefinito in **GroupProjectTemplate**
- Creare un nuovo repository **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Rinominare il repository del progetto predefinito in GroupProjectTemplate

Per rinominare il repository del progetto **GroupCommon** predefinito in **GroupProjectTemplate**:

1. Nella pagina di **Riepilogo** del progetto **GroupCommon** selezionare **repository**. Questa azione consente di eseguire il repository **GroupCommon** predefinito del progetto GroupCommon, che attualmente è vuoto.
   
1. Nella parte superiore della pagina, a discesa della freccia accanto a **GroupCommon** , selezionare **Gestisci repository**.
   
   ![Gestisci repository](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Nella pagina **Impostazioni progetto** selezionare il **...** accanto a **GroupCommon**, quindi selezionare **Rinomina repository**. 
   
   ![Seleziona... e quindi selezionare Rinomina repository](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Nella finestra di messaggio **rinominare il repository GroupCommon** immettere *GroupProjectTemplate*e quindi selezionare **Rinomina**. 
   
   ![Rinomina repository](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Creare il repository GroupUtilities

Per creare il repository **GroupUtilities** :

1. Nella pagina di **Riepilogo** del progetto **GroupCommon** selezionare **repository**. 
   
1. Nella parte superiore della pagina, a discesa della freccia accanto a **GroupProjectTemplate** , selezionare **nuovo repository**.
   
   ![Seleziona nuovo repository](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Nella finestra di dialogo **Crea un nuovo repository** selezionare **git** come **tipo**, immettere *GroupUtilities* come nome del **repository**e quindi selezionare **Crea**.
   
   ![Creare un repository GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Nella pagina **Impostazioni progetto** selezionare **repository** in repository nel percorso di spostamento a sinistra per visualizzare i due **repository di gruppo** : **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Repository di due gruppi](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importare i repository del team di Microsoft TDSP

In questa parte dell'esercitazione si importano i contenuti dei repository **ProjectTemplate** e **Utilities** gestiti dal team di Microsoft TDSP nei repository **GroupProjectTemplate** e **GroupUtilities** . 

Per importare i repository del team TDSP:

1. Dal progetto **GroupCommon** Home page selezionare **repository** nel percorso di spostamento a sinistra. Viene aperto il repository **GroupProjectTemplate** predefinito. 
   
1. Nella pagina **GroupProjectTemplate è vuota** selezionare Import ( **Importa**). 
   
   ![Selezionare Importa](./media/group-manager-tasks/import-repo.png)
   
1. Nella finestra di dialogo **Importa un repository git** selezionare **git** come **tipo di origine**e immettere *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* per l'URL del **Clone**. Quindi selezionare **Importa**. Il contenuto del repository Microsoft TDSP team ProjectTemplate viene importato nel repository GroupProjectTemplate. 
   
   ![Importa repository Microsoft TDSP team](./media/group-manager-tasks/import-repo-2.png)
   
1. Nella parte superiore **della pagina repository** , a discesa e selezionare il repository **GroupUtilities** .
   
1. Ripetere il processo di importazione per importare il contenuto del repository Microsoft TDSP Team **Utilities** , *https:\//github.com/Azure/Azure-TDSP-Utilities.git*, nel repository **GroupUtilities** . 
   
Ognuno dei due repository di gruppo contiene ora tutti i file, ad eccezione di quelli nella directory *. git* , dal repository corrispondente del team di Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalizzare il contenuto dei repository del gruppo

Per personalizzare il contenuto dei repository del gruppo in modo da soddisfare le esigenze specifiche del gruppo, è possibile farlo ora. È possibile modificare i file, modificare la struttura di directory o aggiungere i file che il gruppo ha sviluppato o che sono utili per il gruppo.

### <a name="make-changes-in-azure-repos"></a>Apportare modifiche in Azure Repos

Per personalizzare il contenuto del repository:

1. Nella pagina di **Riepilogo** del progetto **GroupCommon** selezionare **repository**. 
   
1. Nella parte superiore della pagina selezionare il repository che si desidera personalizzare.

1. Nella struttura di directory del repository passare alla cartella o al file che si desidera modificare. 
   
   - Per creare nuove cartelle o file, selezionare la freccia accanto a **nuovo**. 
     
     ![Crea nuovo file](./media/group-manager-tasks/new-file.png)
     
   - Per caricare i file, selezionare **Carica file/i**. 
     
     ![Caricare file](./media/group-manager-tasks/upload-files.png)
     
   - Per modificare i file esistenti, passare al file e quindi fare clic su **modifica**. 
     
     ![Modificare un file](./media/group-manager-tasks/edit-file.png)
     
1. Dopo aver aggiunto o modificato i file, selezionare **commit**.
   
   ![Esegui commit delle modifiche](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Apportare modifiche usando il computer locale o DSVM

Se si desidera apportare modifiche utilizzando il computer locale o DSVM ed effettuare il push delle modifiche nei repository del gruppo, verificare che siano soddisfatti i prerequisiti per l'utilizzo di git e Dsvm:

- Una sottoscrizione di Azure, se si vuole creare un DSVM.
- Git installato nel computer. Se si usa un DSVM, Git è preinstallato. In caso contrario, vedere l'[appendice Strumenti e piattaforme](platforms-and-tools.md#appendix).
- Se si vuole usare un DSVM, Windows o Linux DSVM creato e configurato in Azure. Per ulteriori informazioni e istruzioni, vedere la [documentazione Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/).
- Per un DSVM Windows, [git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) è installato nel computer. Nel file *Readme.MD* scorrere verso il basso fino alla sezione **download and install** e selezionare il **programma di installazione più recente**. Scaricare il programma di installazione con *estensione exe* dalla pagina del programma di installazione ed eseguirlo. 
- Per un DSVM Linux, viene configurata una chiave pubblica SSH nella DSVM e aggiunta in Azure DevOps. Per ulteriori informazioni e istruzioni, vedere la sezione **create SSH Public Key** nell' [appendice Platforms and Tools](platforms-and-tools.md#appendix). 

Prima di tutto, copiare o *clonare* il repository nel computer locale. 
   
1. Nella pagina di **Riepilogo** del progetto **GroupCommon** selezionare repository e nella parte superiore della **pagina selezionare il**repository che si vuole clonare.
   
1. Nella pagina Repository selezionare **Clone** in alto a destra.
   
1. Nella finestra di dialogo **clona repository** selezionare **https** per una connessione HTTP o **SSH** per una connessione SSH, quindi copiare l'URL clone sotto **riga di comando** negli Appunti.
   
   ![Clonare il repository](./media/group-manager-tasks/clone.png)
   
1. Nel computer locale creare le directory seguenti:
   
   - Per Windows: **C:\GitRepos\GroupCommon**
   - Per Linux, **$/GitRepos/GroupCommon** nella home directory 
   
1. Passare alla directory creata.
   
1. In git bash eseguire il comando `git clone <clone URL>.`
   
   Ad esempio, uno dei comandi seguenti consente di clonare il repository **GroupUtilities** nella directory *GroupCommon* nel computer locale. 
   
   **Connessione HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Connessione SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Dopo aver apportato tutte le modifiche desiderate nel clone locale del repository, è possibile effettuare il push delle modifiche nei repository comuni del gruppo condiviso. 

Eseguire i seguenti comandi di git bash dalla directory **GroupProjectTemplate** o **GroupUtilities** locale.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se è la prima volta che si esegue il commit in un repository git, potrebbe essere necessario configurare i parametri globali *User.Name* e *User. email* prima di eseguire il comando `git commit`. Eseguire questi due comandi:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se si esegue il commit in più repository git, usare lo stesso nome e lo stesso indirizzo di posta elettronica per tutti. L'uso dello stesso nome e dell'indirizzo di posta elettronica è utile quando si compilano Power BI Dashboard per tenere traccia delle attività git in più repository.

## <a name="add-group-members-and-configure-permissions"></a>Aggiungere i membri del gruppo e configurare le autorizzazioni

Per aggiungere membri al gruppo:

1. In Azure DevOps, dal progetto **GroupCommon** Home page selezionare **Impostazioni progetto** nel percorso di spostamento a sinistra. 
   
1. Dal percorso di spostamento a sinistra **Impostazioni progetto** selezionare **Team**, quindi nella pagina **Team** selezionare il **Team GroupCommon**. 
   
   ![Configurare i team](./media/group-manager-tasks/teams.png)
   
1. Nella pagina **profilo Team** selezionare **Aggiungi**.
   
   ![Aggiungi al team GroupCommon](./media/group-manager-tasks/add-to-team.png)
   
1. Nella finestra di dialogo **Aggiungi utenti e gruppi** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare **Salva modifiche**. 
   
   ![Aggiungere utenti e gruppi](./media/group-manager-tasks/add-users.png)
   

Per configurare le autorizzazioni per i membri:

1. Nella finestra di spostamento a sinistra **Impostazioni progetto** selezionare **autorizzazioni**. 
   
1. Nella pagina **autorizzazioni** selezionare il gruppo al quale si desidera aggiungere membri. 
   
1. Nella pagina del gruppo selezionare **membri**, quindi selezionare **Aggiungi**. 
   
1. Nella finestra popup **invita membri** cercare e selezionare i membri da aggiungere al gruppo, quindi selezionare **Salva**. 
   
   ![Concedere le autorizzazioni ai membri](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Passaggi successivi

Ecco i collegamenti alle descrizioni dettagliate degli altri ruoli e attività del processo di analisi scientifica dei dati per i team:

- [Team Lead tasks for a data science team (Attività del responsabile del team per un team di data science)](team-lead-tasks.md)
- [Attività del responsabile di progetto per un team di data science](project-lead-tasks.md)
- [Attività dei singoli collaboratori del progetto per un team di data science](project-ic-tasks.md)
