---
title: Creazione collaborativa di codice con Git - Azure Machine Learning | Microsoft Docs
description: "Informazioni su come eseguire attività di sviluppo collaborativo di codice per i progetti di data science con Git tramite metodi di pianificazione Agile."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: e6808c0d9d4ca14101eeebe8b3e286d64e2328f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="collaborative-coding-with-git"></a>Creazione collaborativa di codice con Git

In questo articolo viene descritto come eseguire attività di sviluppo collaborativo di codice per i progetti di data science con Git come framework di sviluppo di codice condiviso. Viene illustrato come collegare queste attività di codifica al lavoro pianificato nell'ambiente di [sviluppo Agile](agile-development.md) e come eseguire la revisione del codice.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Collegare un elemento di lavoro a un ramo Git 

Visual Studio Team Services fornisce un modo pratico per connettere un elemento di lavoro (una storia o attività) a un ramo Git. In questo modo è possibile collegare direttamente la storia o l'attività al codice associato. 

Per connettere un elemento di lavoro a un nuovo ramo, fare doppio clic su un elemento di lavoro e, nella finestra popup, fare clic su **Crea un nuovo ramo** in **+ Aggiungi collegamento**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Fornire le informazioni per il nuovo ramo, ad esempio il nome del ramo, il repository Git di base e il ramo. Il repository Git scelto deve essere il repository nello stesso progetto team a cui appartiene l'elemento di lavoro. Il ramo di base può essere il ramo master o un altro ramo esistente.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Una procedura consigliata consiste nel creare un ramo Git per ogni elemento di lavoro della storia. Per ogni elemento di lavoro dell'attività creare un ramo di base sul ramo della storia. Organizzare i rami in questo modo gerarchico, che corrisponde alle relazioni della storia-attività è utile quando si dispone di più persone che lavorano su storie diverse dello stesso progetto, o quando si dispone di più persone che lavorano su attività diverse della stessa storia. I conflitti possono essere ridotti quando ogni membro del team lavora su un ramo diverso e quando ogni membro lavora su diversi codici o su altri elementi quando si condivide un ramo. 

Nella figura seguente viene illustrata la strategia consigliata di creazione di rami per TDSP. Potrebbe non essere necessario disporre di tutti i rami mostrati di seguito, in particolare quando si dispone solamente di una o due persone che lavorano sullo stesso progetto, o di una persona che lavora su tutte le attività di una storia. Tuttavia è sempre consigliabile separare il ramo di sviluppo dal ramo master. Questo può aiutare ad impedire che il rilascio del ramo venga interrotto dalle attività di sviluppo. Una descrizione più completa del modello di ramo Git è reperibile in [A Successful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/) (Un modello efficiente per la creazione di un ramo Git).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Per passare al ramo che si desidera usare, eseguire il comando seguente in un comando di shell (Windows o Linux). 

    git checkout <branch name>

Modificando il *<nome del ramo\>* in **master** si ritorna al ramo **master**. Dopo essere passati al ramo in funzione, è possibile iniziare a lavorare sullo stesso elemento di lavoro, sviluppando gli elementi di codice o di documentazione necessari per completare l'elemento. 

È anche possibile collegare un elemento di lavoro a un ramo esistente. Nella pagina **Dettaglio** di un elemento di lavoro invece di fare clic su **Crea un nuovo ramo**, fare clic su **+ Aggiungi collegamento**. Quindi, selezionare il ramo che si desidera collegare all'elemento di lavoro. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

È anche possibile creare un nuovo ramo nei comandi di Git Bash. Se <nome del ramo di base\> è mancante, il <nome del nuovo ramo\> si basa sul ramo _master_. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Lavorare su un ramo e salvare le modifiche 

Ora si supponga di apportare alcune modifiche al ramo *dati\_inserimento* per l'elemento di lavoro, ad esempio l'aggiunta di un file R sul ramo nel computer locale. È possibile eseguire il commit del file R aggiunto al ramo per questo elemento di lavoro, purché ci si trovi in quel ramo nel Git shell, usando i seguenti comandi Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Creare una richiesta pull in Visual Studio Team Services 

Quando si è pronti dopo qualche commit e push, per unire il ramo corrente al relativo ramo di base, è possibile inviare una **richiesta pull** al server di Visual Studio Team Services. 

Passare alla pagina principale del progetto team e fare clic su **CODICE**. Selezionare il ramo da unire e il nome del repository Git che si desidera unire al ramo. Quindi fare clic su **Richieste Pull**, fare clic su **Nuova richiesta pull** per creare una revisione della richiesta pull prima che il lavoro sul ramo venga unito al relativo ramo di base.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Riempire delle descrizioni relative a questa richiesta pull, aggiungere i revisori e inviarle.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Revisionare e unire 

Quando viene creata la richiesta pull, i revisori ricevono una notifica di posta elettronica per revisionare le richieste pull. I revisori devono verificare se le modifiche funzionano e testare le modifiche con il richiedente, se possibile. In base alla loro valutazione, i revisori possono approvare o rifiutare la richiesta pull. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Dopo aver completato la revisione, il ramo in funzione viene unito al relativo ramo di base facendo clic sul pulsante **Completa**. È possibile scegliere di eliminare il ramo in funzione dopo che è stato unito. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Verificare in alto a sinistra che la richiesta sia contrassegnata come **COMPLETATA**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Quando si torna indietro al repository in **CODICE**, all'utente viene detto che ci si è spostati al ramo master.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Inoltre, è possibile usare i seguenti comandi Git per unire il ramo di in funzione al relativo ramo di base ed eliminare il ramo in funzione dopo l'unione:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Passaggi successivi

Nell'argomento [Esecuzione di attività di data science](execute-data-science-tasks.md) viene illustrato come usare le utilità per completare molte attività di data science comuni, ad esempio l'esplorazione interattiva dei dati, l'analisi dei dati, nonché la creazione di report e modelli.

Sono anche disponibili esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

