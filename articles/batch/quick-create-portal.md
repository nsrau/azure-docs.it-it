---
title: 'Guida introduttiva di Azure: Eseguire un processo Batch - Portale'
description: Imparare rapidamente a eseguire un processo Batch con il portale di Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 01/19/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a00c8ea07c31d2ab4ba2638f2a7e4adcf5ca4a10
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Guida introduttiva: Eseguire il primo processo Batch nel portale di Azure

Questa guida introduttiva illustra come usare il portale di Azure per creare un account Batch, un *pool* di nodi di calcolo (macchine virtuali) e un *processo* che esegue *attività* di base nel pool. Dopo aver completato questa guida introduttiva, saranno chiari i concetti fondamentali del servizio Batch e sarà possibile provare Batch con carichi di lavoro più realistici su scala più ampia.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-batch-account"></a>Creare un account Batch

Seguire questa procedura per creare un account Batch di esempio a scopo di test. È necessario un account Batch per creare pool e processi. Come illustrato, è possibile collegare un account di archiviazione di Azure all'account Batch. Anche se non è necessario per questa guida introduttiva, l'account di archiviazione è utile per distribuire applicazioni e archiviare dati di input e output per la maggior parte dei carichi di lavoro concreti.


1. Fare clic su **Nuovo** > **Calcolo** > **Servizio Batch**. 

  ![Batch in Marketplace][marketplace_portal]

2. Immettere i valori nei campi **Nome account** e **Gruppo di risorse**. Il nome dell'account deve essere univoco nella **località** di Azure selezionata, usare solo lettere minuscole o numeri e avere una lunghezza di 3-24 caratteri. 

3. In **Account di archiviazione** selezionare un account di archiviazione per utilizzo generico esistente o crearne uno nuovo.

4. Mantenere le impostazioni predefinite per le altre opzioni e fare clic su **Crea** per creare l'account.

  ![Creare un account Batch][account_portal]  

Quando viene visualizzato il messaggio **La distribuzione è riuscita**, passare all'account Batch nel portale.

## <a name="create-a-pool-of-compute-nodes"></a>Creare un pool di nodi di calcolo

Ora che è disponibile un account Batch, creare un pool di esempio di nodi di calcolo Windows a scopo di test. Il pool per questo esempio rapido è costituito da 2 nodi che eseguono un'immagine di Windows Server 2012 R2 da Azure Marketplace.


1. Nell'account Batch fare clic su **Pool** > **Aggiungi**.

2. Immettere un **ID pool** denominato *mypool*. 

3. In **Sistema operativo** selezionare le impostazioni seguenti (è possibile esplorare altre opzioni).
  
  |Impostazione  |Valore  |
  |---------|---------|
  |**Tipo di immagine**|Marketplace (Linux/Windows)|
  |**Autore**     |MicrosoftWindowsServer|
  |**Offerta**     |WindowsServer|
  |**Sku**     |2012-R2-Datacenter-smalldisk|

  ![Selezionare un sistema operativo per il pool][pool_os] 

4. Scorrere verso il basso per immettere le impostazioni di **Dimensioni nodo** e **Piano**. Le dimensioni consigliate per i nodi offrono un buon compromesso in termini di costi/prestazioni per questo esempio rapido.
  
  |Impostazione  |Valore  |
  |---------|---------|
  |**Piano tariffario del nodo**     |Standard_A1|
  |**Nodi dedicati target**     |2|

  ![Selezionare le dimensioni del pool][pool_size] 

5. Mantenere le impostazioni predefinite per le altre opzioni e fare clic su **OK** per creare il pool.

Batch crea immediatamente il pool, ma richiede alcuni minuti per allocare e avviare i nodi di calcolo. In questa fase, lo **stato allocazione** del pool è **Ridimensionamento**. È possibile proseguire e creare un processo e attività durante il ridimensionamento del pool. 

![Pool nello stato di ridimensionamento][pool_resizing]

Dopo alcuni minuti, lo stato del pool è **Stabile** e i nodi si avviano. Fare clic su **Nodi** per controllare lo stato dei nodi. Quando lo stato di un nodo è **Inattivo**, è pronto per l'esecuzione delle attività. 

## <a name="create-a-job"></a>Creare un processo

Dopo aver creato un pool, creare un processo da eseguire nel pool stesso. Un processo Batch è un gruppo logico per una o più attività. Un processo include le impostazioni comuni per le attività, ad esempio la priorità e il pool nel quale eseguire le attività. Inizialmente il processo è privo di attività. 

1. Nella visualizzazione dell'account Batch fare clic su **Processi** > **Aggiungi**. 

2. Immettere un **ID processo** denominato *myjob*. In **Pool** selezionare *mypool*. Lasciare le impostazioni predefinite per le altre opzioni e fare clic su **OK**.

  ![Creare un processo][job_create]

Dopo aver creato il processo si aprirà la pagina **Attività**.

## <a name="create-tasks"></a>Creare le attività

Creare ora attività di esempio da eseguire del processo. Si creano in genere più attività che Batch accoda e distribuisce per l'esecuzione nei nodi di calcolo. In questo esempio si creano due attività identiche. Ogni attività esegue una riga di comando per visualizzare le variabili di ambiente Batch in un nodo di calcolo e quindi attende 90 secondi. 

Quando si usa Batch, in questa riga di comando si specifica l'app o lo script. Batch offre una serie di modi per distribuire app e script nei nodi di calcolo. 

Per creare la prima attività:

1. Fare clic su **Aggiungi**.

2. Immettere un **ID attività** denominato *mytask*. 

3. In **Riga di comando** immettere `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Lasciare le impostazioni predefinite per le altre opzioni e fare clic su **OK**.

  ![Crea un'attività][task_create]

Dopo aver creato un'attività, Batch la accoda per l'esecuzione nel pool. Quando è disponibile un nodo per l'esecuzione, l'attività viene eseguita.

Per creare una seconda attività, tornare al passaggio 1. Immettere un altro **ID attività**, ma specificare una riga di comando identica. Se la prima attività è ancora in esecuzione, Batch avvia la seconda sull'altro nodo nel pool.

## <a name="view-task-output"></a>Visualizzare l'output dell'attività

Gli esempi di attività precedenti vengono completati in alcuni minuti. Per visualizzare l'output di un'attività completata, fare clic su **File nel nodo** e quindi selezionare il file `stdout.txt`. Questo file visualizza l'output standard dell'attività. Il contenuto è simile al seguente:

![Visualizzare l'output dell'attività][task_output]

Il contenuto visualizza le variabili di ambiente di Azure Batch impostate nel nodo. Quando si creano processi e attività Batch, è possibile fare riferimento a queste variabili di ambiente nelle righe di comando delle attività e nelle app e negli script eseguiti dalle righe di comando.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole proseguire con le esercitazioni e gli esempi di Batch, usare l'account Batch e l'account di archiviazione collegato creati in questa guida introduttiva. Non è previsto alcun addebito per l'account Batch in sé.

Vengono addebitati i costi del pool mentre i nodi sono in esecuzione, anche se non sono pianificati processi. Quando il pool non è più necessario, eliminarlo. Nella visualizzazione dell'account fare clic su **Pool** e sul nome del pool. Fare quindi clic su **Elimina**.  Quando si elimina il pool, tutto l'output delle attività nei nodi viene eliminato. 

Quando non sono più necessari, eliminare il gruppo di risorse, l'account Batch e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per l'account Batch e fare clic su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un account Batch, un pool Batch e un processo Batch. Il processo ha eseguito attività di esempio ed è stato esaminato l'output creato in uno dei nodi. Ora che sono stati appresi i concetti fondamentali del servizio Batch, è possibile provare Batch con carichi di lavoro più realistici su scala più ampia. Per altre informazioni su Azure Batch, passare alle esercitazioni di Azure Batch. 

> [!div class="nextstepaction"]
> [Esercitazioni di Azure Batch](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png