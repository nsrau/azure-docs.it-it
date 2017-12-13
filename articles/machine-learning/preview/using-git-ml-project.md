---
title: Usare un repository Git con un progetto di Azure Machine Learning Workbench | Microsoft Docs
description: Questo articolo spiega come usare un repository Git in combinazione con un progetto di Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: f4f1112fe68bdb2a26f68b3da08fe97f9d22d3b7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Usare un repository Git con un progetto di Machine Learning Workbench
Questo documento illustra come Azure Machine Learning Workbench usa Git per consentire il controllo della versione e garantire la riproducibilità nell'esperimento di data science. Viene inoltre spiegato come associare il progetto con un repository Git su cloud.

Machine Learning Workbench è progettato per l'integrazione con Git. Quando si crea un nuovo progetto, la cartella del progetto viene automaticamente inizializzata per Git come repository Git locale. Viene creato anche un secondo repository Git locale nascosto, con un ramo denominato AzureMLHistory/\<GUID progetto\> che tiene traccia delle modifiche alla cartella del progetto per ogni esecuzione. 

L'associazione del progetto di Azure Machine Learning con un repository Git abilita il controllo della versione automatico, localmente e in modalità remota. Il repository Git è ospitato in Visual Studio Team Services (Team Services). L'associazione del progetto di Machine Learning con un repository Git consente a chiunque abbia accesso al repository remoto di scaricare il codice sorgente più recente in un altro computer (roaming).  

> [!NOTE]
> Team Services ha un proprio elenco di controllo di accesso (ACL), indipendente dal servizio Sperimentazione di Azure Machine Learning. L'accesso utente può variare tra un repository Git e un'area di lavoro o un progetto di Machine Learning e può richiedere di essere gestito. 
> 
> Se si vuole assegnare a un membro del team l'accesso a livello di codice al progetto di Machine Learning oppure condividere semplicemente l'area di lavoro, è necessario concedere all'utente le autorizzazioni appropriate per accedere al repository Git di Team Services. 

Per gestire il controllo della versione con Git, è possibile usare il ramo master o creare altri rami nel repository. È anche possibile usare il repository Git locale ed eseguire il push al repository Git remoto, se ne è stato eseguito il provisioning.

Questo diagramma illustrata la relazione tra un repository Git di Team Services e un progetto di Machine Learning:

![Azure Machine Learning e Git](media/using-git-ml-project/aml_git.png)

Per iniziare a usare un repository Git remoto, completare i passaggi descritti nelle sezioni successive.

> [!NOTE]
> Attualmente, Azure Machine Learning supporta i repository Git solo negli account Team Services. Il supporto per i repository Git generali, come GitHub, è previsto per il futuro.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Passaggio 1. Creare un account di Sperimentazione di Machine Learning
Creare un account di Sperimentazione di Machine Learning e installare l'app Azure Machine Learning Workbench. Per altre informazioni, vedere la [Guida introduttiva all'installazione e alla creazione](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Passaggio 2. Creare un progetto team o usare un progetto team esistente
Nel [portale di Azure](https://portal.azure.com/) creare un nuovo progetto team:
1. Selezionare **+**.
2. Cercare **Progetto team**.
3. Immettere le informazioni necessarie:
    - **Nome**: il nome di un team.
    - **Controllo della versione**: selezionare **Git**.
    - **Sottoscrizione**: selezionare una sottoscrizione con un account di Sperimentazione di Machine Learning.
    - **Percorso**: scegliere idealmente un'area vicina alle proprie risorse di Sperimentazione di Machine Learning.
4. Selezionare **Crea**. 

![Creare un progetto team nel portale di Azure](media/using-git-ml-project/create_vsts_team.png)

Assicurarsi di eseguire l'accesso usando lo stesso account di Azure Active Directory (Azure AD) usato per accedere a Machine Learning Workbench. In caso contrario, il sistema non potrà accedere a Machine Learning Workbench usando le credenziali di Azure AD, a meno che non si usi la riga di comando per creare il progetto di Machine Learning e fornire un token di accesso personale per accedere al repository Git. Questo argomento viene illustrato più dettagliatamente più avanti nell'articolo.

Per passare direttamente al progetto team creato, usare l'URL https://\<nome progetto team\>.visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Passaggio 3. Configurare un progetto di Machine Learning e un repository Git

Per configurare un progetto di Machine Learning, sono disponibili due opzioni:
- Creare un progetto di Machine Learning con un repository Git remoto
- Associare un progetto di Machine Learning esistente a un repository Git di Team Services

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Creare un progetto di Machine Learning con un repository Git remoto
Aprire Machine Learning Workbench e creare un nuovo progetto. Nella casella del **repository Git** immettere l'URL del repository Git di Team Services ottenuto al passaggio 2. In genere è simile al seguente: https://\<nome account Team Services\>.visualstudio.com/_git/\<nome progetto\>.

![Creare un progetto di Machine Learning con un repository Git](media/using-git-ml-project/create_project_with_git_rep.png)

È anche possibile creare il progetto usando lo strumento da riga di comando di Azure (interfaccia della riga di comando di Azure). È possibile specificare un token di accesso personale che Machine Learning può usare per accedere al repository Git invece di usare le credenziali di Azure AD dell'utente:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Se si sceglie il modello di progetto vuoto, il repository Git scelto potrebbe già disporre di un ramo master. Machine Learning clona semplicemente il ramo master in locale e aggiunge la cartella aml_config e altri file di metadati del progetto alla cartella del progetto locale. 
>
> Se si sceglie un altro modello di progetto, il repository Git *non può* avere già un ramo master, altrimenti verrà visualizzato un errore. L'alternativa consiste nell'usare il comando `az ml project create` per creare il progetto, indicando un'opzione `--force`. Questa operazione elimina i file nel ramo master originale e li sostituisce con i nuovi file nel modello scelto.

Viene creato un nuovo progetto di Machine Learning con l'integrazione del repository Git remoto abilitata. La cartella di progetto è sempre inizializzata per Git come repository Git locale. Il Git remoto è impostato sul repository Git di Team Services remoto, pertanto è possibile eseguire il push dei commit nel repository Git remoto.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Associare un progetto di Machine Learning esistente a un repository Git di Team Services
È possibile creare un progetto di Machine Learning senza un repository Git di Team Services e fare affidamento sul repository Git locale per gli snapshot della cronologia di esecuzione. In un secondo momento, è possibile associare un repository Git di Team Services con il progetto di Machine Learning esistente usando il comando seguente:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> È possibile eseguire l'operazione di aggiornamento del repository solo su un progetto di Machine Learning che non dispone di un repository Git associato. Inoltre, dopo avere associato un repository Git con un progetto di Machine Learning, non sarà possibile rimuoverlo.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Passaggio 4. Acquisire uno snapshot di progetto nel repository Git
Eseguire alcuni script nel progetto e apportare alcune modifiche tra un'esecuzione e l'altra. È possibile farlo nell'app desktop o dall'interfaccia della riga di comando di Azure usando il comando `az ml experiment submit`. Per altre informazioni, vedere l'[esercitazione sulla classificazione Iris](tutorial-classifying-iris-part-1.md). Per ogni esecuzione, nel caso in cui sia stata apportata una modifica ai file nella cartella del progetto, viene eseguito il commit e il push di uno snapshot della cartella dell'intero progetto nel repository Git remoto del ramo denominato AzureMLHistory/\<GUID progetto\>. Per visualizzare i rami e i commit, incluso il ramo AzureMLHistory /\<GUID progetto\>, passare all'URL del repository Git di Team Services. 

> [!NOTE] 
> Si esegue il commit dello snapshot solo prima dell'esecuzione dello script. Attualmente, l'esecuzione della preparazione dei dati o di una cella Notebook non genera lo snapshot.

![Ramo della cronologia di esecuzione](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> È consigliabile non usare i comandi Git nel ramo della cronologia. Questa operazione potrebbe interferire con la cronologia di esecuzione. In alternativa, usare il ramo master o creare altri rami per le operazioni Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Passaggio 5. Ripristinare uno snapshot di progetto precedente 
Per ripristinare l'intera cartella del progetto allo stato di un precedente snapshot della cronologia di esecuzione, in Machine Learning Workbench:
1. Nella barra delle attività (icona della clessidra) selezionare **Esecuzioni**.
2. Nella visualizzazione **Elenco di esecuzione** selezionare l'esecuzione che si vuole ripristinare.
3. Nella visualizzazione **Dettagli esecuzione** selezionare **Ripristina**.

![Ramo della cronologia di esecuzione](media/using-git-ml-project/restore_project.png)

Facoltativamente, è possibile usare i comandi seguenti nella finestra dell'interfaccia della riga di comando di Azure in Machine Learning Workbench:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Prestare attenzione quando si esegue questo comando. L'esecuzione di questo comando sovrascrive la cartella dell'intero progetto con lo snapshot acquisito quando quella specifica esecuzione è stata avviata. Il progetto resta nel ramo attuale. Ciò significa che **si perdono tutte le modifiche** nella cartella del progetto corrente.  

Potrebbe essere necessario usare Git per eseguire il commit delle modifiche nel ramo attuale prima di eseguire questa operazione.

## <a name="step-6-use-the-master-branch"></a>Passaggio 6. Usare il ramo master
Un modo per evitare la perdita accidentale dello stato del progetto corrente consiste nell'eseguire il commit del progetto nel ramo master del repository Git o in un ramo qualsiasi creato dall'utente. È possibile usare Git dalla riga di comando o dallo strumento client Git preferito per operare sul ramo master, ad esempio:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

A questo punto, è possibile ripristinare in modo sicuro il progetto a uno snapshot precedente completando il passaggio 5. È sempre possibile tornare al commit appena eseguito sul ramo master.

## <a name="authentication"></a>Autenticazione
Se si fa affidamento solo sulle funzioni della cronologia di esecuzione in Machine Learning per la creazione degli snapshot di progetto e il loro ripristino, non occorre preoccuparsi dell'autenticazione del repository Git. L'autenticazione è gestita dal livello del servizio Sperimentazione di Machine Learning.

Tuttavia, se si usano i propri strumenti Git per gestire il controllo della versione, è necessario gestire l'autenticazione con il repository Git remoto in Team Services. In Machine Learning il repository Git remoto viene aggiunto al repository locale come un Git remoto usando il protocollo HTTPS. Ciò significa che quando si usano i comandi Git, come il push o il pull, in remoto, è necessario indicare nome utente e password o un token di accesso personale. Per creare un token di accesso personale in un repository Git di Team Services, seguire le istruzioni in [Use a personal access token to authenticate](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) (Usare un token di accesso personale per l'autenticazione).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [usare Team Data Science Process per organizzare la struttura del progetto](how-to-use-tdsp-in-azure-ml.md).
