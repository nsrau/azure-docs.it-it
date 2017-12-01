---
title: Uso del repository Git con un progetto Azure Machine Learning Workbench | Microsoft Docs
description: Questo articolo spiega come usare un repository Git in combinazione con un progetto Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Uso del repository Git con un progetto Azure Machine Learning Workbench
Questo documento contiene informazioni su come Azure Machine Learning Workbench usa Git per consentire il controllo della versione e garantire la riproducibilità nell'esperimento di data science. Vengono fornite anche istruzioni su come associare il progetto con un repository Git su cloud.

## <a name="introduction"></a>Introduzione
Azure Machine Learning Workbench è progettato con l'integrazione Git da zero. Quando si crea un nuovo progetto, la cartella del progetto è automaticamente "Git-initialized" come un repository Git locale. Nel frattempo, viene creato anche un secondo repository Git locale nascosto con un ramo denominato _AzureMLHistory/<project_GUID>_ per tenere traccia delle modifiche alla cartella del progetto per ogni esecuzione. 

Associando il progetto Azure ML con un repository Git, ospitato all'interno di un progetto Visual Studio Team Service (VSTS), è possibile eseguire il controllo della versione automatico sia localmente che in modalità remota. Questa associazione consente a qualsiasi persona con accesso al repository remoto di scaricare il codice sorgente più recente in un altro computer (roaming).  

> [!NOTE]
> VSTS ha un proprio elenco di controllo di accesso indipendente del servizio sperimentazione di Azure Machine Learning. L'accesso utente può variare fra un repository Git e un progetto o area di lavoro di Azure ML e può richiedere di essere gestito. Perciò, se si vuole condividere il progetto Azure ML con un membro del team, incluso l'accesso a livello di codice, oltre a condividere semplicemente l'area di lavoro, è necessario concedere esplicitamente l'accesso appropriato al repository Git di Visual Studio Team Services. 

Con Git è anche possibile gestire il controllo della versione in modo esplicito usando il ramo _master_ o creando altri rami nel repository. È possibile usare solo il repository Git locale e anche eseguire il push al repository Git remoto se ne è stato eseguito il provisioning.

Questo diagramma illustrata la relazione tra un repository Git di Visual Studio Team Services e un progetto Azure ML:

![Git di AML](media/using-git-ml-project/aml_git.png)

Per iniziare a usare un repository Git remoto, seguire queste istruzioni di base.

> [!NOTE]
> Attualmente Azure Machine Learning supporta i repository Git solo negli account VSTS. Il supporto per il repository Git generale (ad esempio GitHub e così via) è previsto per il futuro.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Passaggio 1. Creare un account di sperimentazione di Azure ML
Se non è già stato fatto, creare un account di sperimentazione di Azure ML e installare l'app Azure ML Workbench. Per altre informazioni vedere la [Guida introduttiva all'installazione e alla creazione](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Passaggio 2. Creare un progetto Team o usare un progetto Team esistente
Nel [portale di Azure](https://portal.azure.com/) creare un nuovo **progetto Team**.
1. Fare clic su **+**
2. Cercare **"Progetto Team"**
3. Immettere le informazioni necessarie.
    - Nome: il nome di un team.
    - Controllo della versione: **Git**
    - Sottoscrizione: quella con un account di sperimentazione di Azure Machine Learning.
    - Percorso: rimanere idealmente in un'area vicina alle proprie risorse di sperimentazione di Azure Machine Learning.
4. Fare clic su **Crea**. 

![Creare un nuovo progetto Team dal portale di Azure](media/using-git-ml-project/create_vsts_team.png)

Assicurarsi di eseguire l'accesso con lo stesso account di Azure Active Directory usato per accedere ad Azure Machine Learning Workbench. Altrimenti il sistema non può accedervi usando le credenziali di Azure Active Directory, a meno che non si usi la riga di comando per creare il progetto di Azure ML e generare un token di accesso personale per accedere al repository Git. Questa argomento verrà approfondito più avanti.

Una volta creato il progetto team, si può procedere al passaggio successivo.

Per passare direttamente al progetto team appena creato, l'URL è `https://<team_project_name>.visualstudio.com`.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Passaggio 3. Creare un nuovo progetto Azure ML con un repository Git remoto
Avviare Azure ML Workbench e creare un nuovo progetto. Compilare la casella di testo del repository Git con l'URL del repository Git di VSTS ottenuto al passaggio 2. In genere è simile al seguente: `http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Creare un progetto Azure ML con un repository Git](media/using-git-ml-project/create_project_with_git_rep.png)

È anche possibile creare il progetto tramite lo strumento della riga di comando. È possibile generare un token di accesso personale. Azure ML può usare questo token per accedere al repository Git per conto dell'utente, invece di usare le credenziali di Azure Active Directory:

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> La scelta del modello di progetto vuoto è adatta se il repository Git scelto dispone già di un ramo _master_. Azure ML duplica semplicemente il ramo _master_ in locale e aggiunge la cartella `aml_config` e altri file di metadati del progetto alla cartella del progetto locale. Ma se si sceglie un altro modello di progetto, il repository Git non deve avere già un ramo _master_, altrimenti verrà visualizzato un errore. L'alternativa consiste nell'usare lo strumento della riga di comando `az ml project create` per creare il progetto e indicare un commutatore `--force`. Questa operazione consente di eliminare i file nel ramo master originale e di sostituirli con i nuovi file nel modello scelto.

A questo punto viene creato un nuovo progetto Azure ML con l'integrazione del repository Git remoto abilitata e pronta. La cartella di progetto è sempre inizializzata per Git come repository Git locale. E il Git _remoto_ è impostato sul repository Git di VSTS remoto, pertanto i commit possono essere inseriti nel repository Git remoto.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Passaggio 3a. Associare un progetto di Azure ML esistente a un repository Git di VSTS
Facoltativamente è possibile anche creare un progetto Azure ML senza un repository Git di VSTS e fare affidamento sul repository Git locale per gli snapshot della cronologia di esecuzione. Ed è possibile associare un repository Git di VSTS in un secondo momento con questo progetto Azure ML esistente usando il comando seguente:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> È possibile eseguire l'operazione di aggiornamento del repository solo su un progetto di Azure ML che non dispone di un repository Git associato. Dopo aver associato il repository Git, questo non può essere rimosso.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Passaggio 4. Acquisire uno snapshot di progetto in un repository Git
Ora è possibile eseguire alcuni script nel progetto e apportare alcune modifiche fra le esecuzioni. È possibile farlo dall'app desktop o dall'interfaccia della riga di comando usando il comando `az ml experiment submit`. Per altre informazioni è possibile seguire l'[esercitazione sulla classificazione Iris](tutorial-classifying-iris-part-1.md). Per ogni esecuzione, in caso sia stata apportata una modifica ai file nella cartella del progetto, viene eseguito il commit e il push di uno snapshot della cartella dell'intero progetto nel repository Git remoto del ramo denominato `AzureMLHistory/<Project_GUID>`. È possibile visualizzare i rami e i commit passando all'URL del repository Git di VSTS e individuando il ramo. 

> [!NOTE] 
> Si esegue il commit dello snapshot solo prima dell'esecuzione dello script. Attualmente l'esecuzione della preparazione dei dati o di una cella Notebook non genera lo snapshot.

![ramo cronologia di esecuzione](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> È consigliabile non usare personalmente i comandi Git nel ramo della cronologia. Questa operazione potrebbe danneggiare la cronologia di esecuzione. Usare il ramo master o creare altri rami invece delle operazioni Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Passaggio 5. Ripristinare uno snapshot di progetto precedente 
Per ripristinare l'intera cartella del progetto allo stato di una precedente esecuzione snapshot dello stato del progetto di cronologia da Azure ML Workbench:
1. Fare clic su **Esecuzioni** nella barra delle attività (icona della clessidra).
2. Dalla vista **Elenco di esecuzione** fare clic sull'esecuzione che si vuole ripristinare.
3. Dalla vista **Dettagli esecuzione** fare clic su **Ripristina**.

![ramo cronologia di esecuzione](media/using-git-ml-project/restore_project.png)

In alternativa è possibile usare il comando seguente dalla finestra dell'interfaccia della riga di comando di Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

Eseguendo questo comando si sovrascrive la cartella dell'intero progetto con lo snapshot acquisito quando quell'esecuzione è stata avviata. Tuttavia il progetto resta nel ramo attuale. Ciò significa che **si perderanno tutte le modifiche** nella cartella del progetto corrente. Pertanto è necessario prestare particolare attenzione quando si esegue questo comando. Potrebbe essere necessario fare in modo che Git esegua il commit delle modifiche nel ramo attuale prima di eseguire le operazioni indicate in precedenza. Vedere la parte precedente per altri dettagli.

## <a name="step-6-use-the-master-branch"></a>Passaggio 6. Usare il ramo master
Un modo per evitare la perdita accidentale dello stato del progetto corrente consiste nell'eseguire il commit del progetto nel ramo master o in un ramo qualsiasi creato dall'utente del repository Git. È possibile usare Git direttamente dalla riga di comando (o qualsiasi altro strumento client Git preferito) per operare sul ramo master. ad esempio:

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Ora è possibile ripristinare in modo sicuro il progetto a uno snapshot precedente seguendo il passaggio 5, con la consapevolezza che è sempre possibile tornare al commit appena eseguito sul ramo master.

## <a name="authentication"></a>Autenticazione
Se si fa affidamento solo sulle funzioni della cronologia di esecuzione in Azure ML per la creazione degli snapshot di progetto e il loro ripristino, non occorre preoccuparsi dell'autenticazione del repository Git. Se ne prende cura il livello del servizio di sperimentazione.

Tuttavia, se si usano i propri strumenti Git per gestire il controllo della versione, è necessario gestire correttamente l'autenticazione con il repository Git remoto in VSTS. In Azure Machine Learning il repository Git remoto viene aggiunto al repository locale come un Git remoto tramite il protocollo HTTPS. Ciò significa che quando si usano i comandi Git in remoto, ad esempio il push o il pull, è necessario indicare nome utente e password o il token di accesso personale. Seguire [queste istruzioni](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) per creare il token di accesso personale in un repository Git di Visual Studio Team Services.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare il processo di data science dei team per organizzare la struttura del progetto, vedere [Strutturare un progetto con TDSP](how-to-use-tdsp-in-azure-ml.md)
