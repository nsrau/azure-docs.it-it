---
title: 'Avvio rapido di Azure: Eseguire il primo processo batch nel portale di Azure'
description: Informazioni su come usare il portale di Azure per creare un account Batch, un pool di nodi di calcolo e un processo che esegue attività di base nel pool.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: cf65a681764a848f8132ec44b8ba313ef1a83235
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88511372"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Guida introduttiva: Eseguire il primo processo Batch nel portale di Azure

Introduzione ad Azure Batch con informazioni su come usare il portale di Azure per creare un account Batch, un pool di nodi di calcolo (macchine virtuali) e un processo che esegue attività nel pool. Completando questo argomento di avvio rapido si apprenderanno i concetti di base del servizio Batch e sarà possibile provare a usarlo con carichi di lavoro più realistici e su larga scala.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Creare un account Batch

Seguire questa procedura per creare un account Batch di esempio a scopo di test. È necessario un account Batch per creare pool e processi. Come illustrato, è possibile collegare un account di archiviazione di Azure all'account Batch. Anche se non è necessario per questa guida introduttiva, l'account di archiviazione è utile per distribuire applicazioni e archiviare dati di input e output per la maggior parte dei carichi di lavoro concreti.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Calcolo** > **Servizio Batch**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Screenshot del servizio Batch in Azure Marketplace.":::

1. Nel campo **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.

1. Immettere un valore per **Nome account**. Il nome deve essere univoco all'interno della **località** di Azure selezionata. Può contenere solo lettere minuscole e numeri e deve essere composto da un numero di caratteri compreso tra 3 e 24.

1. In **Account di archiviazione** selezionare un account di archiviazione esistente o crearne uno nuovo.

1. Lasciare invariate tutte le altre impostazioni. Selezionare **Rivedi e crea**, quindi **Crea** per creare l'account Batch.

Quando viene visualizzato il messaggio **La distribuzione è riuscita**, passare all'account Batch creato.

## <a name="create-a-pool-of-compute-nodes"></a>Creare un pool di nodi di calcolo

Ora che è disponibile un account Batch, creare un pool di esempio di nodi di calcolo Windows a scopo di test. Il pool per questo breve esempio è costituito da due nodi che eseguono un'immagine di Windows Server 2019 da Azure Marketplace.

1. Nell'account Batch selezionare **Pool** > **Aggiungi**.

1. Immettere un **ID pool** denominato *mypool*.

1. In **Sistema operativo** selezionare le impostazioni seguenti (è possibile esplorare altre opzioni).
  
   |Impostazione  |valore  |
   |---------|---------|
   |**Tipo di immagine**|Marketplace|
   |**Autore**     |microsoftwindowsserver|
   |**Offerta**     |windowsserver|
   |**Sku**     |2019-datacenter-core-smalldisk|

1. Scorrere verso il basso per immettere le impostazioni di **Dimensioni nodo** e **Piano**. Le dimensioni consigliate per i nodi offrono un buon compromesso in termini di costi/prestazioni per questo esempio rapido.
  
   |Impostazione  |Valore  |
   |---------|---------|
   |**Piano tariffario del nodo**     |A1 Standard|
   |**Nodi dedicati target**     |2|

1. Mantenere le impostazioni predefinite per le altre opzioni e selezionare **OK** per creare il pool.

Batch crea immediatamente il pool, ma richiede alcuni minuti per allocare e avviare i nodi di calcolo. In questa fase, lo **stato allocazione** del pool è **Ridimensionamento**. È possibile proseguire e creare un processo e attività durante il ridimensionamento del pool.

Dopo alcuni minuti, lo stato dell'allocazione diventa **Stabile** e i nodi vengono avviati. Per verificare lo stato dei nodi, selezionare il pool e quindi selezionare **Nodi**. Quando lo stato di un nodo è **Inattivo**, è pronto per l'esecuzione delle attività.

## <a name="create-a-job"></a>Creare un processo

Dopo aver creato un pool, creare un processo da eseguire nel pool stesso. Un processo Batch è un gruppo logico per una o più attività. Un processo include le impostazioni comuni per le attività, ad esempio la priorità e il pool nel quale eseguire le attività. Inizialmente il processo è privo di attività.

1. Nella visualizzazione dell'account Batch selezionare **Processi** > **Aggiungi**.

1. Immettere un **ID processo** denominato *myjob*. In **Pool** selezionare *mypool*. Mantenere le impostazioni predefinite per le altre opzioni e fare clic su **OK**.

## <a name="create-tasks"></a>Creare attività

Selezionare ora il processo per aprire la pagina **Attività**. Verranno create le attività di esempio da eseguire nel processo. In genere si creano più attività che Batch accoda e distribuisce per l'esecuzione nei nodi di calcolo. In questo esempio si creano due attività identiche. Ogni attività esegue una riga di comando per visualizzare le variabili di ambiente Batch in un nodo di calcolo e quindi attende 90 secondi.

Quando si usa Batch, in questa riga di comando si specifica l'app o lo script. Batch offre una serie di modi per distribuire app e script nei nodi di calcolo.

Per creare la prima attività:

1. Selezionare **Aggiungi**.

1. Immettere un **ID attività** denominato *mytask*.

1. In **Riga di comando** immettere `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Mantenere le impostazioni predefinite per le altre opzioni e fare clic su **Invia**.

Dopo aver creato un'attività, Batch la accoda per l'esecuzione nel pool. Quando è disponibile un nodo per l'esecuzione, l'attività viene eseguita.

Per creare una seconda attività, ripetere i passaggi precedenti. Immettere un altro **ID attività**, ma specificare una riga di comando identica. Se la prima attività è ancora in esecuzione, Batch avvia la seconda sull'altro nodo nel pool.

## <a name="view-task-output"></a>Visualizzare l'output dell'attività

Le attività di esempio create verranno completate in un paio di minuti. Per visualizzare l'output di un'attività completata, selezionarla, quindi selezionare **File nel nodo**. Selezionare il file `stdout.txt` per visualizzare l'output standard dell'attività. Il contenuto è simile al seguente:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Screenshot del servizio Batch in Azure Marketplace.":::

Il contenuto visualizza le variabili di ambiente di Azure Batch impostate nel nodo. Quando si creano processi e attività Batch, è possibile fare riferimento a queste variabili di ambiente nelle righe di comando delle attività e nelle app e negli script eseguiti dalle righe di comando.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole proseguire con le esercitazioni e gli esempi di Batch, usare l'account Batch e l'account di archiviazione collegato creati in questa guida introduttiva. Non è previsto alcun addebito per l'account Batch in sé.

Vengono addebitati i costi del pool mentre i nodi sono in esecuzione, anche se non sono pianificati processi. Quando il pool non è più necessario, eliminarlo. Nella visualizzazione dell'account selezionare **Pool** e il nome del pool. Selezionare **Elimina**.  Quando si elimina il pool, tutto l'output delle attività nei nodi viene eliminato.

Quando non sono più necessari, eliminare il gruppo di risorse, l'account Batch e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per l'account Batch, quindi selezionare **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati creati un account Batch, un pool Batch e un processo Batch. Il processo ha eseguito attività di esempio ed è stato esaminato l'output creato in uno dei nodi. Ora che sono stati appresi i concetti fondamentali del servizio Batch, è possibile provare Batch con carichi di lavoro più realistici su scala più ampia. Per altre informazioni su Azure Batch, passare alle esercitazioni di Azure Batch.

> [!div class="nextstepaction"]
> [Esercitazioni di Azure Batch](./tutorial-parallel-dotnet.md)
