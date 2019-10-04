---
title: Creazione collaborativa di codice con Git - Processo di data science per i team
description: Informazioni su come eseguire attività di sviluppo collaborativo di codice per i progetti di data science con Git tramite metodi di pianificazione Agile.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 08/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b57621fcec654f11c8e9a68e4568f332dbf9ac6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195357"
---
# <a name="collaborative-coding-with-git"></a>Creazione collaborativa di codice con Git

Questo articolo descrive come usare git come Framework di sviluppo di codice collaborativo per i progetti data science. Questo articolo illustra come collegare il codice in Azure Repos agli elementi di lavoro di [sviluppo agile](agile-development.md) in Azure boards, come eseguire le revisioni del codice e come creare e unire richieste pull per le modifiche.

## <a name='Linkaworkitemwithagitbranch-1'></a>Collegare un elemento di lavoro a un ramo di Azure Repos 

Azure DevOps offre un modo pratico per connettere una Azure Boards storia utente o un elemento di lavoro attività con un Azure Repos ramo repository git. È possibile collegare la storia utente o l'attività direttamente al codice associato. 

Per connettere un elemento di lavoro a un nuovo ramo, selezionare i puntini di sospensione ( **...** ) accanto all'elemento di lavoro e nel menu di scelta rapida scorrere fino a e selezionare **nuovo ramo**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Nella finestra di dialogo **Crea un ramo** specificare il nome del nuovo ramo e il repository e il ramo di base Azure Repos git. Il repository di base deve trovarsi nello stesso progetto Azure DevOps dell'elemento di lavoro. Il ramo di base può essere il ramo master o un altro ramo esistente. Selezionare **Crea ramo**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

È anche possibile creare un nuovo ramo usando il comando git bash seguente in Windows o Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Se non si specifica un \<nome di ramo di base >, il nuovo ramo si `master`basa su. 

Per passare al ramo di lavoro, eseguire il comando seguente: 

```bash
git checkout <working branch name>
```

Dopo il passaggio al ramo di lavoro, è possibile iniziare a sviluppare elementi di codice o di documentazione per completare l'elemento di lavoro. `git checkout master` L'`master` esecuzione consente di tornare al ramo.

È consigliabile creare un ramo git per ogni elemento di lavoro della storia utente. Quindi, per ogni elemento di lavoro attività, è possibile creare un ramo in base al ramo della storia utente. Organizzare i rami in una gerarchia che corrisponde alla relazione storia utente-attività quando più persone lavorano su storie utente diverse per lo stesso progetto o su attività diverse per la stessa storia utente. È possibile ridurre al minimo i conflitti facendo in modo che ogni membro del team lavori su un ramo diverso oppure su codice o altri artefatti diversi quando si condivide un ramo. 

Il diagramma seguente illustra la strategia di creazione di rami consigliata per TDSP. Potrebbero non essere necessari tutti i rami, come illustrato di seguito, in particolare quando solo una o due persone lavorano su un progetto o solo una persona lavora per tutte le attività di una storia utente. Tuttavia, la separazione del ramo di sviluppo dal ramo master è sempre una procedura consigliata e può aiutare a impedire che il ramo di rilascio venga interrotto dalle attività di sviluppo. Per una descrizione completa del modello di ramo git, vedere [un modello di branching git riuscito](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

È anche possibile collegare un elemento di lavoro a un ramo esistente. Nella pagina dei **Dettagli** di un elemento di lavoro selezionare **Aggiungi collegamento**. Selezionare quindi un ramo esistente a cui collegare l'elemento di lavoro e fare clic su **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name='WorkonaBranchandCommittheChanges-2'></a>Lavorare sul ramo e confermare le modifiche 

Dopo aver apportato una modifica per l'elemento di lavoro, ad esempio l'aggiunta di un file di script `script` R al branch del computer locale, è possibile eseguire il commit della modifica dal branch locale al ramo di lavoro upstream usando i comandi Git bash seguenti:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name='CreateapullrequestonVSTS-3'></a>Creare una richiesta pull

Dopo uno o più commit e push, quando si è pronti per unire il ramo di lavoro corrente nel ramo di base, è possibile creare e inviare una *richiesta pull* in Azure Repos. 

Dalla pagina principale del progetto Azure DevOps, puntare alle**richieste pull** del **repository** > nel percorso di spostamento a sinistra. Selezionare quindi uno dei pulsanti **nuova richiesta pull** oppure il collegamento **Crea una richiesta pull** .

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Nella schermata **nuova richiesta pull** , se necessario, passare al repository git e al ramo in cui si vuole eseguire il merge delle modifiche. Aggiungere o modificare le altre informazioni desiderate. Inrevisori aggiungere i nomi di quelli necessari per rivedere le modifiche e quindi selezionare **Crea**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name='ReviewandMerge-4'></a>Revisionare e unire

Una volta creata la richiesta pull, i revisori ricevono una notifica tramite posta elettronica per esaminare la richiesta pull. I revisori verificano se le modifiche funzionano e controllano le modifiche con il richiedente, se possibile. I revisori possono inserire commenti, richiedere modifiche e approvare o rifiutare la richiesta pull in base alla valutazione. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Dopo che i revisori hanno approvato le modifiche, l'utente o un altro utente con autorizzazioni di merge può unire il ramo di lavoro al relativo ramo di base. Selezionare **completa**, quindi selezionare **completa merge** nella finestra di dialogo **completa richiesta pull** . È possibile scegliere di eliminare il ramo di lavoro dopo che è stato Unito. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confermare che la richiesta è contrassegnata come **completata**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando si torna a **repository** nel percorso di spostamento a sinistra, è possibile vedere che è stato passato al ramo master dopo l'eliminazione del `script` ramo.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

È anche possibile usare i comandi di git bash seguenti per unire `script` il ramo di lavoro al relativo ramo di base ed eliminare il ramo di lavoro dopo l'Unione:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Passaggi successivi

[Esegui Data Science attività](execute-data-science-tasks.md) Mostra come utilizzare le utilità di per completare diverse attività comuni di Data Science, ad esempio l'esplorazione interattiva dei dati, l'analisi dei dati, la creazione di report e la creazione di modelli.

[Procedure dettagliate di esempio](walkthroughs.md) elenca le procedure dettagliate di scenari specifici, con collegamenti e descrizioni delle anteprime. Negli scenari collegati viene illustrato come combinare strumenti e servizi cloud e locali in flussi di lavoro o pipeline per creare applicazioni intelligenti. 

