---
title: Uso del repository Git con un progetto Azure Machine Learning Workbench | Microsoft Docs
description: Questo articolo spiega come usare un repository Git in combinazione con un progetto Azure Machine Learning Workbench.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Uso del repository Git con un progetto Azure Machine Learning Workbench
Questo documento fornisce informazioni su come Azure Machine Learning Workbench usa Git per garantire la riproducibilità nell'esperimento di data science. Vengono fornite anche istruzioni su come associare il progetto con un repository Git su cloud.

## <a name="introduction"></a>Introduzione
Azure Machine Learning Workbench è progettato con l'integrazione Git da zero. Quando si crea un nuovo progetto, la cartella del progetto viene automaticamente "inizializzata per Git" come un repository Git locale (repo) mentre viene creato anche un secondo repository Git locale nascosto con un ramo denominato _AzureMLHistory/<project_GUID>_ per tenere traccia delle modifiche della cartella di progetto per ogni esecuzione. 

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


> [!TIP]
> Assicurarsi di eseguire l'accesso con l'account di Azure Active Directory (AAD) utilizzato per accedere ad Azure Machine Learning Workbench. In caso contrario, il nuovo progetto team potrebbe fine sotto l'ID Tenant sbagliato e Azure Machine Learning potrebbe non riuscire a trovarlo. In questo caso è necessario usare l'interfaccia della riga di comando e fornire il token VSTS.

Una volta creato il progetto team, si può procedere al passaggio successivo.

Per passare direttamente al progetto team appena creato, l'URL è `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Attualmente Azure Machine Learning supporta solo i repository Git vuoti senza ramo master. Dall'interfaccia della riga di comando è possibile usare l'argomento --force per eliminare prima il ramo master. 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Passaggio 3. Creare un nuovo progetto Azure ML con un repository Git remoto
Avviare Azure ML Workbench e creare un nuovo progetto. Compilare la casella di testo del repository Git con l'URL del repository Git di VSTS ottenuto al passaggio 2. In genere è simile al seguente: http://<vsts_account_name>.visualstudio.com/_git/<project_name>

![Creare un progetto Azure ML con un repository Git](media/using-git-ml-project/create_project_with_git_rep.png)

A questo punto viene creato un nuovo progetto Azure ML con l'integrazione del repository Git remoto abilitata e pronta. La cartella di progetto è sempre inizializzata per Git come repository Git locale. E il Git _remoto_ è impostato sul repository Git di VSTS remoto, pertanto i commit possono essere inseriti nel repository Git remoto.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Passaggio 3.a Associare un progetto Azure ML esistente con un repository Git di VSTS
Facoltativamente è possibile anche creare un progetto Azure ML senza un repository Git di VSTS e fare affidamento sul repository Git locale per gli snapshot della cronologia di esecuzione. Ed è possibile associare un repository Git di VSTS in un secondo momento con questo progetto Azure ML esistente usando il comando seguente:

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Passaggio 4. Acquisire uno snapshot di progetto in un repository Git
Ora è possibile avviare alcune esecuzioni nel progetto e apportare alcune modifiche fra le esecuzioni. È possibile farlo dall'app desktop o dall'interfaccia della riga di comando usando il comando `az ml experiment submit`. Per altre informazioni è possibile seguire l'[esercitazione sulla classificazione Iris](tutorial-classifying-iris-part-1.md). Per ogni esecuzione, in caso sia stata apportata qualsiasi modifica ai file nella cartella del progetto, viene eseguito il commit e il push di uno snapshot della cartella dell'intero progetto nel repository Git remoto. È possibile visualizzare i rami e i commit accedendo all'URL del repository Git di VSTS.

![ramo cronologia di esecuzione](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>Passaggio 5. Ripristinare uno snapshot di progetto precedente 
Per ripristinare l'intera cartella del progetto allo stato di un precedente snapshot dello stato del progetto da AML Workbench.
1. Fare clic su **Esecuzioni** nella barra delle attività (icona della clessidra).
2. Dalla vista **Elenco di esecuzione** fare clic sull'esecuzione che si vuole ripristinare.
3. Dalla vista **Dettagli esecuzione** fare clic su **Ripristina**.

![ramo cronologia di esecuzione](media/using-git-ml-project/restore_project.png)

In alternativa è possibile usare il comando seguente dalla finestra dell'interfaccia della riga di comando di Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

Eseguendo questo comando si sovrascrive la cartella dell'intero progetto con lo snapshot acquisito quando quell'esecuzione è stata avviata. Ciò significa che **si perderanno tutte le modifiche** nella cartella del progetto corrente. Pertanto è necessario prestare particolare attenzione quando si esegue questo comando.

## <a name="step-6-use-the-master-branch"></a>Passaggio 6. Usare il ramo master
Un modo per evitare la perdita accidentale dello stato del progetto corrente è eseguire il commit del progetto al ramo master del repository Git. È possibile usare Git direttamente dalla riga di comando (o qualsiasi altro strumento client Git preferito) per operare sul ramo master. ad esempio:

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

Ora è possibile ripristinare in modo sicuro il progetto a uno snapshot precedente seguendo il passaggio 5, con la consapevolezza che è sempre possibile tornare al commit appena eseguito sul ramo master.

## <a name="authentication"></a>Autenticazione
Se si fa affidamento solo sulle funzioni della cronologia di esecuzione in Azure ML per la creazione degli snapshot di progetto e il loro ripristino, non occorre preoccuparsi dell'autenticazione del repository Git. Se ne prende cura il livello del servizio di sperimentazione.

Tuttavia, se si usano i propri strumenti Git per gestire il controllo della versione, è necessario gestire correttamente l'autenticazione con il repository Git remoto in VSTS. Ovvero, è necessario configurare l'autenticazione con il repository Git nel computer locale prima di poter eseguire comandi Git su tale repository Git remoto. 

Il modo più semplice per farlo consiste nel creare una coppia di chiavi SSH e caricare la porzione della chiave pubblica nelle impostazioni di sicurezza del repository Git.

### <a name="generate-ssh-key"></a>Generare la chiave SSH 
Prima di tutto è necessario generare una coppia di chiavi SSH nel computer in uso.

#### <a name="on-windows"></a>In Windows:
Avviare l'app desktop GUI Git in Windows e nel menu _Guida_ fare clic su _Mostra la chiave SSH_.

![Chiave SSH](media/using-git-ml-project/git_gui.png)

Copiare la chiave SSH negli Appunti.

#### <a name="on-macos"></a>In MacOS:
Passaggi rapidi dalla shell dei comandi:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
I passaggi dettagliati sono riportati in [questo articolo su GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Caricare la chiave pubblica nel repository Git
Passare alla home page dell'account di Visual Studio: https://<vsts_account_name>.visualstudio.com and log in, quindi fare clic su Sicurezza sotto il proprio avatar.

Aggiungere quindi una chiave pubblica SSH, incollare la chiave pubblica SSH ottenuta al passaggio precedente e assegnarle un nome. Qui è possibile aggiungere più chiavi.

Ora è possibile eseguire i comandi Git localmente sul repository remoto senza che sia necessaria alcuna ulteriore autenticazione esplicita.

### <a name="read-more"></a>Altre informazioni
Per altre informazioni su come abilitare l'autenticazione locale per il repository Git remoto in VSTS, vedere questi due articoli (entrambi gli approcci possono funzionare).
- [Usare l'autenticazione con chiave SSH](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Usare i gestori delle credenziali Git](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare il processo di data science dei team per organizzare la struttura del progetto, vedere [Strutturare un progetto con TDSP](how-to-use-tdsp-in-azure-ml.md)
