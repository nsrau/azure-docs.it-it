---
title: Roaming e collaborazione in Azure Machine Learning Workbench | Microsoft Docs
description: Scopri come configurare roaming e collaborazione in Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 137608007716452ec6468f1e13f494b095a11cb0
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e collaborazione in Azure Machine Learning Workbench
Questo articolo descrive come usare Azure Machine Learning Workbench per configurare progetti per il roaming tra computer e collaborare con i membri del team. 

Quando si crea un progetto di Azure Machine Learning con un collegamento a un repository Git remoto, i metadati e gli snapshot del progetto vengono archiviati nel cloud. È possibile usare il collegamento cloud per accedere al progetto da un altro computer (roaming). È anche possibile collaborare con i membri del team concedendo loro l'accesso al progetto. 

## <a name="prerequisites"></a>prerequisiti
1. Installare l'app Machine Learning Workbench. Verificare di avere accesso a un account Sperimentazione di Azure Machine Learning. Per altre informazioni, vedere la [Guida all'installazione](quickstart-installation.md).

2. Accedere a [Visual Studio Team Services](https://www.visualstudio.com) (Team Services) e creare un repository a cui collegare il progetto. Per altre informazioni, vedere [Using a Git repo with a Machine Learning Workbench project](using-git-ml-project.md) (Uso di un repository Git con un progetto di Machine Learning Workbench).

## <a name="create-a-new-machine-learning-project"></a>Creare un nuovo progetto di Machine Learning
Aprire Machine Learning Workbench e creare un nuovo progetto (ad esempio, un progetto denominato iris). Nella casella **URL repository GIT Visualstudio.com** immettere un URL valido per un repository Git di Team Services. 

> [!IMPORTANT]
> Scegliendo il modello di progetto vuoto, il repository Git scelto potrebbe già includere un ramo master. Machine Learning clona semplicemente il ramo master in locale e aggiunge la cartella aml_config e altri file di metadati del progetto alla cartella del progetto locale. 
>
> Se si sceglie un altro modello di progetto, il repository Git *non può* avere già un ramo master, altrimenti verrà visualizzato un errore. L'alternativa consiste nell'usare il comando `az ml project create` per creare il progetto, indicando un'opzione `--force`. Questa operazione elimina i file nel ramo master originale e li sostituisce con i nuovi file nel modello scelto.

Dopo aver creato il progetto, avviare diverse esecuzioni di script presenti all'interno del progetto. Questa azione esegue il commit dello stato del progetto nel ramo della cronologia di esecuzione del repository Git. 

> [!NOTE] 
> Solo le esecuzioni dello script attivano i commit al ramo della cronologia di esecuzione. L'esecuzione della preparazione dei dati e le esecuzioni Notebook non attivano snapshot del progetto nel ramo della cronologia di esecuzione.

Se l'autenticazione Git è configurata, è anche possibile operare nel ramo principale. In alternativa, è possibile creare un nuovo ramo. 

Esempio: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Aprire Machine Learning Workbench in un secondo computer
Dopo il collegamento del repository Git Team Services al progetto, sarà possibile accedere al progetto iris da qualsiasi computer in cui è stato installato Machine Learning Workbench. 

Per accedere al progetto iris in un altro computer, è necessario eseguire l'accesso all'app con le stesse credenziali usate durante la creazione del progetto. È anche necessario aver eseguito l'accesso allo stesso account e alla stessa area di lavoro di Sperimentazione di Machine Learning. Il progetto iris si trova all'interno di un elenco in ordine alfabetico con altri progetti della stessa area di lavoro. 

### <a name="download-the-project-on-a-second-computer"></a>Scaricare il progetto in un secondo computer
Quando si apre l'area di lavoro dal secondo computer, l'icona accanto al progetto iris è diversa dall'icona della cartella tipica. L'icona di download indica che il contenuto del progetto è disponibile nel cloud e che il progetto è pronto per il download nel computer corrente. 

![Crea progetto](./media/roaming-and-collaboration/downloadable-project.png)

Selezionare il progetto iris per avviare un download. Al termine del download, il progetto sarà pronto per l'accesso nel secondo computer. 

In Windows, il progetto si trova in C:\Utenti\\<nome utente\>\Documenti\AzureML.

In macOS, il progetto si trova in /home/\<nome utente\>/Documenti/AzureML.

In una versione futura si prevede di migliorare la funzionalità in modo che consenta di selezionare una cartella di destinazione. 

> [!NOTE]
> Se una cartella nella directory di Machine Learning ha lo stesso nome del progetto, il download ha esito negativo. Per risolvere questo problema, rinominare temporaneamente la cartella esistente.


### <a name="work-on-the-downloaded-project"></a>Lavorare sul progetto scaricato 
Il progetto appena scaricato è nello stato in cui si trovava al momento dell'ultima esecuzione all'interno del progetto stesso. Ogni volta che si invia un'esecuzione, viene eseguito automaticamente il commit di uno snapshot dello stato del progetto nel ramo della cronologia di esecuzione del repository Git Team Services. Lo snapshot associato all'esecuzione più recente consente di creare un'istanza del progetto nel secondo computer. 
 

## <a name="collaboration"></a>Collaborazione
È possibile collaborare con i membri del team nei progetti collegati a un repository Git di Team Services. È possibile assegnare agli utenti le autorizzazioni per l'account, l'area di lavoro e il progetto di Sperimentazione di Machine Learning. Al momento è possibile eseguire i comandi di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure. È anche possibile usare il [portale Azure](https://portal.azure.com), Per altre informazioni, vedere [Use the Azure portal to add users](#portal) (Uso del portale di Azure per aggiungere utenti).    

### <a name="use-the-command-line-to-add-users"></a>Usare la riga di comando per aggiungere utenti
Ad esempio, Alice è il proprietario del progetto iris. Alice vuole condividere l'accesso al progetto con Bob. 

Alice seleziona il menu **File** e la voce di menu **Prompt dei comandi**. Verrà visualizzata la finestra Prompt dei comandi con il progetto iris. Alice può ora decidere il livello di accesso da assegnare a Bob. Concede le autorizzazioni eseguendo i comandi seguenti:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Dopo l'assegnazione del ruolo, direttamente o tramite ereditarietà, Bob può visualizzare il progetto nell'elenco dei progetti di Machine Learning Workbench. Bob potrebbe dover riavviare l'applicazione per visualizzare il progetto. A questo punto potrà scaricare il progetto come descritto in [Roaming](#roaming) e iniziare a collaborare con Alice. 

Per tutti gli utenti che collaborano a un progetto, viene eseguito il commit della cronologia di esecuzione nello stesso repository Git remoto. Quando Alice avvia un'esecuzione, Bob può visualizzarla nella sezione della cronologia di esecuzione del progetto nell'app Machine Learning Workbench. Per il progetto Bob può anche ripristinare lo stato di qualsiasi esecuzione, incluse le esecuzioni avviate da Alice. 

La condivisione di un repository Git remoto per il progetto consente ad Alice e a Bob di collaborare anche nel ramo principale. Se necessario per la collaborazione, possono anche creare rami personali e usare richieste pull e merge Git. 

### <a name="use-the-azure-portal-to-add-users"></a>Usare il portale di Azure per aggiungere utenti
<a name="portal"></a>

Gli account, le aree di lavoro e i progetti di Sperimentazione di Machine Learning sono risorse di Azure Resource Manager. Per assegnare ruoli, è possibile usare il collegamento **Controllo di accesso** nel [portale di Azure](https://portal.azure.com). 

Nella vista **Tutte le risorse** è possibile trovare le risorse a cui si vogliono aggiungere utenti. Selezionare il collegamento **Controllo di accesso (IAM)** e quindi **Aggiungi utenti**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Esempio di flusso di lavoro di collaborazione
Verrà ora esaminato un esempio che illustra il flusso di lavoro di collaborazione. I dipendenti di Contoso Alice e Bob vogliono collaborare su un progetto di data science usando Machine Learning Workbench. Le loro identità appartengono allo stesso tenant di Azure Active Directory (Azure AD) Contoso. Ecco i passaggi eseguiti da Alice e Bob:

1. Alice crea prima un repository Git vuoto in un progetto Team Services. Il progetto Team Services deve essere in una sottoscrizione di Azure creata nel tenant Azure AD Contoso. 

2. Alice crea un account Sperimentazione di Machine Learning, un'area di lavoro e un progetto di Machine Learning Workbench nel suo computer. Quando crea il progetto, immette l'URL di repository Git di Team Services.

3. Alice inizia a lavorare sul progetto. Crea alcuni script e li esegue alcune volte. A ogni esecuzione viene automaticamente effettuato il push come commit di uno snapshot dell'intera cartella del progetto in un ramo della cronologia di esecuzione del repository Git Team Services creato da Machine Learning Workbench.

4. Alice è soddisfatta del lavoro in corso. Vuole eseguire il commit delle modifiche nel ramo master locale ed effettua il push nel ramo master del repository Git Team Services. Con il progetto aperto, in Machine Learning Workbench, apre la finestra Prompt dei comandi e immette questi comandi:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice aggiunge Bob all'area di lavoro con il ruolo di collaboratore. Può eseguire questa operazione nel portale di Azure oppure usando il comando `az role assignment`, come illustrato in precedenza. Alice concede a Bob anche autorizzazioni di lettura/scrittura per il repository Git Team Services.

6. Bob accede a Machine Learning Workbench sul proprio computer. Può visualizzare l'area di lavoro che Alice condivide con lui. Può visualizzare il progetto iris elencato nell'area di lavoro. 

7. Bob seleziona il nome del progetto. Il progetto viene scaricato nel suo computer.
    * I file di progetto scaricati sono copie dello snapshot dell'esecuzione più recente registrata nella cronologia delle esecuzioni. Non sono il commit più recente nel ramo master.
    * La cartella di progetto locale è impostata sul ramo master con modifiche non preparate per il commit.

8. Bob può esplorare le esecuzioni avviate da Alice. Può ripristinare gli snapshot di qualsiasi esecuzione precedente.

9. Bob vuole ottenere le modifiche più recenti sottoposte a push da Alice e iniziare a lavorare in un ramo diverso. In Machine Learning Workbench Bob apre la finestra del prompt dei comandi ed esegue i comandi seguenti:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob modifica il progetto e invia nuove esecuzioni. Le modifiche vengono eseguite nel ramo bob. Le esecuzioni di Bob possono essere visualizzate anche da Alice.

11. Bob ora può eseguire il push delle sue modifiche nel repository Git remoto. Per evitare conflitti con il ramo master su cui sta lavorando Alice, Bob decide di eseguire il push del suo lavoro in un nuovo ramo remoto denominato bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Per comunicare ad Alice i dettagli del suo codice, Bob crea una richiesta pull nel repository Git remoto dal ramo bob al ramo master. Alice può ora unire la richiesta pull al ramo master.

## <a name="next-steps"></a>Passaggi successivi
- Sono disponibili altre informazioni sull'[uso di un repository Git con un progetto di Machine Learning Workbench](using-git-ml-project.md).
