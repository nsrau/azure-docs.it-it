---
title: Creazione collaborativa di codice con Git - Processo di data science per i team
description: Informazioni su come eseguire attività di sviluppo collaborativo di codice per i progetti di data science con Git tramite metodi di pianificazione Agile.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721898"
---
# <a name="collaborative-coding-with-git"></a>Creazione collaborativa di codice con Git

In questo articolo viene descritto come utilizzare Git come framework di sviluppo di codice collaborativo per i progetti di data science. L'articolo illustra come collegare il codice in Azure Repos agli elementi di lavoro di [sviluppo agile](agile-development.md) nelle bacheche di Azure, come eseguire revisioni del codice e come creare e unire le richieste pull per le modifiche.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Collegare un elemento di lavoro a un ramo di Azure ReposLink a work item to an Azure Repos branch 

Azure DevOps offre un modo pratico per connettere una storia utente o un elemento di lavoro Attività di Azure Boards con un ramo del repository Azure Repos Git.Azure DevOps provides a convenient way to connect an Azure Boards User Story or Task work item with an Azure Repos Git repository branch. È possibile collegare la Storia utente o l'attività direttamente al codice ad essa associato. 

Per connettere un elemento di lavoro a un nuovo ramo, selezionare i ellissi **Azioni** (**...**) accanto all'elemento di lavoro e nel menu di scelta rapida scorrere e selezionare **Nuovo ramo**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Nella finestra di dialogo **Crea un ramo** specificare il nuovo nome del ramo e il repository e il ramo di Azure Repos Git di base. Il repository di base deve trovarsi nello stesso progetto DevOps di Azure dell'elemento di lavoro. Il ramo di base può essere il ramo master o un altro ramo esistente. Selezionare **Crea ramo**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

È inoltre possibile creare un nuovo ramo utilizzando il seguente comando Git bash in Windows o Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Se non si specifica \<un nome di ramo di `master`base>, il nuovo ramo è basato su . 

Per passare al ramo di lavoro, eseguire il comando seguente: 

```bash
git checkout <working branch name>
```

Dopo aver passaggia al ramo di lavoro, è possibile iniziare a sviluppare elementi di codice o documentazione per completare l'elemento di lavoro. In `git checkout master` esecuzione si `master` torna al ramo.

È consigliabile creare un ramo Git per ogni elemento di lavoro User Story.It's a good practice to create a Git branch for each User Story work item. Quindi, per ogni elemento di lavoro Attività, è possibile creare un ramo basato sul ramo User Story. Organizzare i rami in una gerarchia che corrisponde alla relazione User Story-Task quando più persone lavorano su storie utente diverse per lo stesso progetto o su attività diverse per la stessa User Story. È possibile ridurre al minimo i conflitti facendo in modo che ogni membro del team lavori su un ramo diverso o su codice diverso o altri elementi durante la condivisione di un ramo. 

Nel diagramma seguente viene illustrata la strategia di diramazione consigliata per TDSP. Potrebbe non essere necessario il numero di rami come illustrato di seguito, soprattutto quando solo una o due persone lavorano su un progetto o solo una persona lavora su tutte le attività di una User story. Ma separare il ramo di sviluppo dal ramo master è sempre una buona pratica e può aiutare a evitare che il ramo di rilascio venga interrotto dalle attività di sviluppo. Per una descrizione completa del modello di diramazione Git, vedere Un modello di [diramazione Git riuscito](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

È anche possibile collegare un elemento di lavoro a un ramo esistente. Nella pagina **Dettagli** di un elemento di lavoro selezionare **Aggiungi collegamento**. Selezionare quindi un ramo esistente a cui collegare l'elemento di lavoro e scegliere **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Lavorare sul ramo e eseguire il commit delle modifiche 

Dopo aver apportato una modifica per l'elemento di lavoro, ad `script` esempio l'aggiunta di un file di script R al ramo del computer locale, è possibile eseguire il commit della modifica dal ramo locale al ramo di lavoro a monte utilizzando i seguenti comandi Git bash:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Creare una richiesta pullCreate a pull request

Dopo uno o più commit e push, quando si è pronti per unire il ramo di lavoro corrente nel ramo di base, è possibile creare e inviare una richiesta pull in Azure Repos.After one or more commits and pushes, when you're ready to merge your current working branch into its base branch, you can create and submit *a pull request* in Azure Repos. 

Dalla pagina principale del progetto DevOps di Azure scegliere richieste **Repos** > **Pull** nel riquadro di spostamento sinistro. Selezionare quindi uno dei pulsanti **Nuova richiesta pull** o il collegamento Crea una richiesta **pull.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Nella schermata **Nuova richiesta pull,** se necessario, passare al repository Git e al ramo in cui si desidera unire le modifiche. Aggiungere o modificare qualsiasi altra informazione desiderata. In **Revisori**aggiungere i nomi dei revisori e quindi selezionare **Crea**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Revisionare e unire

Dopo aver creato la richiesta pull, i revisori ricevono una notifica tramite posta elettronica per esaminare la richiesta pull. I revisori verificano se le modifiche funzionano e, se possibile, verificano le modifiche con il richiedente. I revisori possono apportare commenti, richiedere modifiche e approvare o rifiutare la richiesta pull in base alla valutazione. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Dopo che i revisori hanno approvato le modifiche, l'utente o un altro utente con autorizzazioni di unione può unire il ramo di lavoro al relativo ramo di base. Selezionare **Completa**, quindi **Completare l'unione** nella finestra di dialogo **Completa richiesta pull.** È possibile scegliere di eliminare il ramo di lavoro dopo l'unione. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Verificare che la richiesta sia **contrassegnata**come COMPLETED . 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando si torna a **Repos** nel riquadro di spostamento sinistro, è possibile `script` vedere che si è passati al ramo master dopo l'eliminazione della diramazione.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

È inoltre possibile utilizzare i seguenti `script` comandi Git bash per unire il ramo di lavoro al relativo ramo di base ed eliminare il ramo di lavoro dopo l'unione:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire attività](execute-data-science-tasks.md) di data science mostra come usare le utilità per completare diverse attività comuni di data science, ad esempio l'esplorazione interattiva dei dati, l'analisi dei dati, la creazione di report e la creazione di modelli.

[Procedure dettagliate](walkthroughs.md) di esempio elencano le procedure dettagliate di scenari specifici, con collegamenti e descrizioni delle anteprime. Gli scenari collegati illustrano come combinare strumenti e servizi cloud e locali in flussi di lavoro o pipeline per creare applicazioni intelligenti. 

