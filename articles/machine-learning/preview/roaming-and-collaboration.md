---
title: Roaming e collaborazione in Azure Machine Learning Workbench | Microsoft Docs
description: Elenco di problemi noti e guida alla risoluzione
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e collaborazione in Azure Machine Learning Workbench
Questo documento illustra in dettaglio in che modo Azure Machine Learning Workbench consente il roaming dei progetti tra computer diversi e la collaborazione tra i diversi membri del team. 

Quando si crea un progetto di Azure Machine Learning con un collegamento a un repository Git remoto, i metadati e gli snapshot del progetto vengono archiviati nel cloud. Il collegamento cloud consente di accedere al progetto da un altro computer (roaming). È anche possibile concedere l'accesso ai colleghi, consentendo quindi a questi di collaborare al progetto. 

## <a name="prerequisites"></a>Prerequisiti
Prima di tutto, installare Azure Machine Learning Workbench con accesso a un account di Sperimentazione. Per altri dettagli, seguire le istruzioni della [guida all'installazione](quickstart-installation.md).

Accedere quindi a [Visual Studio Team System](https://www.visualstudio.com) e creare un repository a cui collegare il progetto. Per informazioni dettagliate su Git, fare riferimento all'articolo [Using Git Repo with an Azure Machine Learning Workbench Project](using-git-ml-project.md) (Uso del repository Git con un progetto di Azure Machine Learning Workbench).

## <a name="create-a-new-azure-machine-learning-project"></a>Creare un nuovo progetto di Azure Machine Learning
Avviare Azure Machine Learning Workbench e creare un nuovo progetto (ad esempio, _iris_). Nella casella di testo **URL del repository GIT di Visualstudio.com** inserire l'URL di un repository Git VSTS valido. 

> [!IMPORTANT]
> La scelta del modello di progetto vuoto è adatta se il repository Git scelto dispone già di un ramo _master_. Azure ML duplica semplicemente il ramo _master_ in locale e aggiunge la cartella `aml_config` e altri file di metadati del progetto alla cartella del progetto locale. Ma se si sceglie un altro modello di progetto, il repository Git non deve avere già un ramo _master_, altrimenti verrà visualizzato un errore. L'alternativa consiste nell'usare lo strumento della riga di comando `az ml project create` per creare il progetto e indicare un commutatore `--force`. Questa operazione consente di eliminare i file nel ramo master originale e di sostituirli con i nuovi file nel modello scelto.

Dopo aver creato il progetto, avviare diverse esecuzioni di script presenti all'interno del progetto. Questa azione esegue il commit dello stato del progetto nel ramo della cronologia di esecuzione del repository Git. 

> [!NOTE] 
> Solo le esecuzioni dello script attivano i commit al ramo della cronologia di esecuzione. L'esecuzione della preparazione dei dati o le esecuzioni Notebook non attivano snapshot del progetto al ramo della cronologia di esecuzione.

Se l'autenticazione Git è configurata, è anche possibile operare in modo esplicito nel ramo principale o creare un nuovo ramo. 

Ad esempio: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Aprire Azure Machine Learning Workbench in un secondo computer
Dopo il collegamento del repository Git VSTS al progetto, è possibile accedere al progetto _iris_ da qualsiasi computer in cui sia stata effettuata l'installazione di Azure Machine Learning Workbench. 

Per accedere al progetto iris in un altro computer, è necessario eseguire l'accesso all'app con le stesse credenziali usate durante la creazione del progetto. È anche necessario passare allo stesso account e alla stessa area di lavoro di Sperimentazione. Il progetto _iris_ si trova all'interno di un elenco in ordine alfabetico con altri progetti della stessa area di lavoro. 

### <a name="download-project-on-second-machine"></a>Scaricare il progetto nel secondo computer
Quando si apre l'area di lavoro dal secondo computer, l'icona accanto al progetto _iris_ è diversa dall'icona della cartella tipica. L'icona di download indica che il contenuto del progetto è disponibile nel cloud e deve essere scaricato nel computer corrente. 

![crea progetto](./media/roaming-and-collaboration/downloadable-project.png)

Se si fa clic sul progetto _iris_, inizia un'operazione di download. Poco dopo, al termine del download, il progetto è pronto per l'accesso nel secondo computer. 

In Windows corrisponde a `C:\Users\<username>\Documents\AzureML`

In macOS è disponibile qui: `/home/<username>/Documents/AzureML`

In una versione futura si prevede di migliorare la funzionalità in modo che consenta di selezionare la cartella di destinazione. 

> [!NOTE]
> Se una cartella nella directory di Azure ML ha lo stesso nome del progetto, il download ha esito negativo. Per il momento, per ovviare a questo problema è necessario rinominare la cartella esistente.


### <a name="work-on-the-downloaded-project"></a>Lavorare sul progetto scaricato 
Il progetto appena scaricato è nello stato in cui si trovava al momento dell'ultima esecuzione all'interno del progetto stesso. Ogni volta che si invia un'esecuzione, viene eseguito automaticamente il commit di uno snapshot dello stato del progetto nel ramo della cronologia di esecuzione del repository Git VSTS. Quando si crea l'istanza del progetto nel secondo computer si usa lo snapshot associato all'esecuzione più recente. 
 

## <a name="collaboration"></a>Collaborazione
Per i progetti collegati a un repository Git VSTS è possibile collaborare con gli altri membri del team assegnando agli utenti le autorizzazioni opportune per l'account, l'area di lavoro e il progetto di Sperimentazione. A questo punto è possibile eseguire i comandi di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure. È anche possibile usare il [portale di Azure](https://portal.azure.com). Vedere la [sezione seguente](#portal).    

### <a name="using-command-line-to-add-users"></a>Aggiungere utenti tramite la riga di comando
Si supponga, ad esempio, che Alice sia proprietaria del progetto _Iris_ e che voglia condividere l'accesso con Bob. 

Alice fa clic sul menu **File** e seleziona la voce di menu **Prompt dei comandi** per avviare il prompt dei comandi configurato per il progetto _iris_. Alice può quindi stabilire il livello accesso da concedere a Bob tramite i comandi seguenti.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

Dopo l'assegnazione del ruolo, direttamente o tramite ereditarietà, Bob può visualizzare il progetto nell'elenco progetti di Workbench. Per visualizzare il progetto può essere necessario riavviare l'applicazione. Bob può quindi scaricare il progetto come descritto nella sezione [Roaming](#roaming) e collaborare con Alice. 

Per tutti gli utenti che collaborano a un progetto, viene eseguito il commit della cronologia di esecuzione nello stesso repository Git remoto. Quando Alice effettua un'esecuzione, quindi, Bob può visualizzarla nella sezione della cronologia di esecuzione del progetto nell'app Workbench. Per il progetto Bob può anche ripristinare lo stato di qualsiasi esecuzione, incluse le esecuzioni avviate da Alice. 

La condivisione di un repository Git remoto per il progetto consente ad Alice e a Bob di collaborare anche nel ramo principale. Se necessario per la collaborazione, possono anche creare rami personali e usare richieste pull e merge. 

### <a name="using-azure-portal-to-add-users"></a>Aggiungere utenti tramite il portale di Azure
<a name="portal"></a>

Gli account, le aree di lavoro e i progetti di Sperimentazione di Azure Machine Learning sono risorse di Azure Resource Manager. Per assegnare ruoli, è possibile usare il collegamento Controllo di accesso nel [portale di Azure](https://portal.azure.com). 

Nella vista Tutte le risorse è possibile trovare le risorse a cui si vogliono aggiungere utenti. Fare clic sul collegamento Controllo di accesso (IAM) all'interno della pagina. Aggiungi utenti 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Esempio di flusso di lavoro di collaborazione
Verrà ora esaminato un esempio che illustra il flusso di collaborazione. I dipendenti di Contoso Alice e Bob vogliono collaborare su un progetto di data science usando Azure Machine Learning Workbench. Le loro identità appartengono allo stesso tenant di Azure AD Contoso.

1. Alice crea prima un repository Git Vuoto in un progetto VSTS. Questo progetto VSTS deve essere disponibile in una sottoscrizione Azure creata nel tenant AAD Contoso. 

2. Alice crea un account di sperimentazione di Azure ML, un'area di lavoro e un progetto di Azure ML Workbench nel suo computer. Quando crea il progetto, fornisce l'URL dell'archivio Git.

3. Alice inizia a lavorare sul progetto. Crea alcuni script e li esegue alcune volte. A ogni esecuzione, viene eseguito automaticamente il commit di uno snapshot dello stato del progetto nel ramo della cronologia di esecuzione del repository Git VSTS.

4. Alice è soddisfatta del lavoro in corso. Vuole eseguire il commit delle modifiche nel ramo _master_ locale ed effettua il push nel ramo _master_ del repository VSTS Git. Per farlo, con il progetto aperto, avvia la finestra del prompt dei comandi da Azure Machine Learning Workbench ed esegue i comandi seguenti:
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. Alice poi aggiunge Bob all'area di lavoro con il ruolo di collaboratore. Lo fa dal portale di Azure o usando il comando `az role assignment` precedentemente illustrato. Garantisce inoltre a Bob l'accesso in lettura e scrittura al repository Git VSTS.

6. Bob ora accede ad Azure Machine Learning Workbench dal suo computer. Può visualizzare l'area di lavoro che Alice ha condiviso con lui e il progetto elencato nell'area di lavoro. 

7. Bob fa clic sul nome del progetto per scaricare il progetto sul suo computer.
    
    a. I file di progetto scaricati sono cloni dello snapshot dell'esecuzione più recente presente nella cronologia delle esecuzioni. Non sono il commit più recente nel ramo master.
    
    b. La cartella di progetto locale è impostata sul ramo _master_ con modifiche non preparate per il commit.

8. Bob può visualizzare le esecuzioni di Alice e ripristinare snapshot di qualsiasi esecuzione precedente.

9. Bob vuole ottenere le modifiche più recente inviate da Alice e iniziare a lavorare su un altro ramo quindi apre la finestra del prompt dei comandi da Azure Machine Learning Workbench ed esegue i comandi seguenti:

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob ora modifica il progetto e invia le nuove esecuzioni. Le modifiche vengono eseguite nel ramo _bob_. E Alice può visualizzare le esecuzioni di Bob.

11. Bob ora può eseguire il push delle sue modifiche nel repository Git remoto. Per evitare conflitti con il ramo _master_ su cui sta lavorando Alice, decide di eseguire il push del suo lavoro in un nuovo ramo remoto denominato _bob_.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bob può condividere con Alice i dettagli del suo codice e crea una richiesta pull al repository Git remoto dal ramo _bob_ al ramo _master_. Alice può quindi unire la richiesta pull al ramo _master_.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di Git con Azure Machine Learning Workbench, vedere [Using Git repository with an Azure Machine Learning Workbench project](using-git-ml-project.md) (Uso del repositoy Git con un progetto Azure Machine Learning Workbench)