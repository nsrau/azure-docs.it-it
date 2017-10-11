---
title: Roaming e collaborazione in Azure Machine Learning Workbench | Microsoft Docs
description: Elenco di problemi noti e guida alla risoluzione
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e collaborazione in Azure Machine Learning Workbench
Questo documento illustra in dettaglio in che modo Azure Machine Learning Workbench consente il roaming dei progetti tra computer diversi e la collaborazione tra i diversi membri del team. 

Quando si crea un progetto di Azure Machine Learning con un collegamento a un repository Git remoto, i metadati e gli snapshot del progetto vengono archiviati nel cloud. Il collegamento cloud consente di accedere al progetto da un altro computer (roaming). È anche possibile concedere l'accesso ai colleghi, consentendo quindi a questi di collaborare al progetto. 

## <a name="prerequisites"></a>Prerequisiti
Prima di tutto, installare Azure Machine Learning Workbench con accesso a un account di Sperimentazione. Per altri dettagli, seguire le istruzioni della [guida all'installazione](quickstart-installation.md).

Accedere quindi a [Visual Studio Team System](https://www.visualstudio.com) e creare un repository a cui collegare il progetto. Per informazioni dettagliate su Git, fare riferimento all'articolo [Using Git Repo with an Azure Machine Learning Workbench Project](using-git-ml-project.md) (Uso del repository Git con un progetto di Azure Machine Learning Workbench).

## <a name="create-a-new-azure-machine-learning-project"></a>Creare un nuovo progetto di Azure Machine Learning
Avviare Azure Machine Learning Workbench e creare un nuovo progetto (ad esempio, _iris_). Nella casella di testo **URL del repository GIT di Visualstudio.com** inserire l'URL di un repository Git VSTS valido. 
>[!IMPORTANT]
>La creazione del progetto non riesce se non si ha accesso in lettura/scrittura al repository Git e se il repository Git non è vuoto, ad esempio se contiene già un ramo principale.

Dopo aver creato il progetto, avviare diverse esecuzioni di script presenti all'interno del progetto. Questa azione esegue il commit dello stato del progetto nel ramo della cronologia di esecuzione del repository Git. 

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

>Si noti che se una cartella nella directory di Azure ML ha lo stesso nome del progetto, il download ha esito negativo. Per il momento, per ovviare a questo problema è necessario rinominare la cartella esistente.


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

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

Dopo l'assegnazione del ruolo, direttamente o tramite ereditarietà, Bob può visualizzare il progetto nell'elenco progetti di Workbench. Per visualizzare il progetto può essere necessario riavviare l'applicazione. Bob può quindi scaricare il progetto come descritto nella sezione [Roaming](#roaming) e collaborare con Alice. 

Per tutti gli utenti che collaborano a un progetto, viene eseguito il commit della cronologia di esecuzione nello stesso repository Git remoto. Quando Alice effettua un'esecuzione, quindi, Bob può visualizzarla nella sezione della cronologia di esecuzione del progetto nell'app Workbench. Per il progetto Bob può anche ripristinare lo stato di qualsiasi esecuzione, incluse le esecuzioni avviate da Alice. 

La condivisione di un repository Git remoto per il progetto consente ad Alice e a Bob di collaborare anche nel ramo principale. Se necessario per la collaborazione, possono anche creare rami personali e usare richieste pull e merge. 

### <a name="using-azure-portal-to-add-users"></a>Aggiungere utenti tramite il portale di Azure
<a name="portal"></a>

Gli account, le aree di lavoro e i progetti di Sperimentazione di Azure Machine Learning sono risorse di Azure Resource Manager. Per assegnare ruoli, è possibile usare il collegamento Controllo di accesso nel [portale di Azure](https://portal.azure.com). 

Nella vista Tutte le risorse è possibile trovare le risorse a cui si vogliono aggiungere utenti. Fare clic sul collegamento Controllo di accesso (IAM) all'interno della pagina. Aggiungi utenti 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">


