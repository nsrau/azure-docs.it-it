---
title: Avvio rapido di Azure - Creare un cluster Batch per intelligenza artificiale - Portale | Microsoft Docs
description: Avvio rapido - Creare un cluster Batch per intelligenza artificiale per eseguire il training di modelli di apprendimento automatico e di intelligenza artificiale - Portale di Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 8b9daa0fbbf84e0f602498a0847c9e120f709b17
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057740"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>Avvio rapido: Creare un cluster per i processi di training di Batch per intelligenza artificiale usando il portale di Azure

Questo avvio rapido illustra come usare il portale di Azure per creare un cluster Batch per intelligenza artificiale da usare per il training di modelli di apprendimento automatico e di intelligenza artificiale. Batch per intelligenza artificiale è un servizio gestito che consente ai data scientist e ai ricercatori in ambito di intelligenza artificiale di eseguire il training di modelli di intelligenza artificiale e di apprendimento automatico su larga scala in cluster di macchine virtuali di Azure.

Inizialmente, il cluster presenta un nodo GPU singolo e un file server associato. Dopo aver completato questo avvio rapido, sarà possibile aumentare il cluster e usarlo per eseguire il training di modelli di apprendimento avanzato. Inviare i processi di training al cluster usando Batch per intelligenza artificiale, gli strumenti di [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) oppure [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Per completare questa guida introduttiva è necessaria una coppia di chiavi SSH. Se è già disponibile una coppia di chiavi SSH, questo passaggio può essere ignorato.

Per creare una coppia di chiavi SSH, eseguire il comando seguente da una shell Bash e seguire le istruzioni visualizzate. Ad esempio, è possibile usare [Azure Cloud Shell](../cloud-shell/overview.md) oppure, in Windows, il [sottosistema di Windows per Linux](/windows/wsl/install-win10). L'output del comando include il nome del file di chiave pubblica. Copiare il contenuto del file di chiave pubblica (`cat ~/.ssh/id_rsa.pub`) negli Appunti o in un'altra posizione a cui accedere in un secondo momento.

```bash
ssh-keygen -t rsa -b 2048
```

Per altre informazioni su come creare una coppia di chiavi SSH, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-batch-ai-workspace"></a>Creare un'area di lavoro Batch per intelligenza artificiale

Creare innanzitutto un'area di lavoro Batch per intelligenza artificiale in cui organizzare le risorse disponibili. Un'area di lavoro può contenere uno o più cluster o altre risorse di Batch per intelligenza artificiale.

1. Selezionare **Tutti i servizi** e quindi **Batch per intelligenza artificiale**.

2. Selezionare **Aggiungi area di lavoro**.

3. Immettere i valori nei campi **Nome dell'area di lavoro** e **Gruppo di risorse**. È anche possibile selezionare diverse opzioni per la **Sottoscrizione** e il **Percorso** dell'area di lavoro. Selezionare **Crea area di lavoro**.

  ![Creare un'area di lavoro di Azure Batch per intelligenza artificiale](./media/quickstart-create-cluster-portal/create-workspace.png)

Quando viene visualizzato il messaggio **La distribuzione è riuscita**, passare alla risorsa creata e selezionare l'area di lavoro.

## <a name="create-a-file-server"></a>Creare un file server

Un file server di Batch per intelligenza artificiale è un server NFS a nodo singolo, che può essere montato automaticamente nei nodi del cluster. Rappresenta una delle diverse modalità con cui fornire risorse di archiviazione ai dati di input e all'output dei processi di training.

1. Nell'area di lavoro, selezionare **File server** > **Add batch ai file server** (Aggiungi file server Batch per intelligenza artificiale).

2. Immettere i valori nei campi **Nome file server** e **Dimensioni macchina virtuale**. In questo avvio rapido viene suggerita una dimensione della macchina virtuale *Standard D1_v2* per il file server. Scegliere una dimensione diversa se è necessario memorizzare maggiori quantità di dati di input o output per i processi di training.

3. Immettere un **Nome utente amministratore** e copiare il contenuto del file di chiave pubblica SSH in **Chiave SSH**. Accettare le impostazioni predefinite per i restanti valori e selezionare **Create File server** (Crea file server).

  ![Creare un file server di Batch per intelligenza artificiale](./media/quickstart-create-cluster-portal/create-file-server.png)

La distribuzione del file server può richiedere alcuni minuti.

Dopo aver creato il server, fare clic su **Proprietà** e prendere nota delle **Impostazioni di montaggio**. È possibile connettersi all'indirizzo IP pubblico del server tramite SSH per caricare e scaricare i dati di training e i file di output nella directory indicata (*/data*).

![Proprietà del file server](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>Creare un cluster

La procedura seguente crea un cluster con un nodo GPU singolo. Il nodo del cluster esegue un'immagine di Ubuntu Server predefinita progettata per ospitare applicazioni basate su contenitore, che è possibile usare per la maggior parte dei carichi di lavoro di training. Il nodo del cluster consente di montare il file server in corrispondenza del punto di montaggio. 

1. Nell'area di lavoro Batch per intelligenza artificiale, selezionare **Cluster** > **Add batch ai cluster** (Aggiungi cluster Batch per intelligenza artificiale).

2. Immettere i valori relativi al **Nome cluster** e le impostazioni seguenti. La dimensione consigliata per la macchina virtuale ha un processore GPU NVIDIA Tesla K80.
  
   |Impostazione  |Valore  |
   |---------|---------|
   |**Dimensioni macchina virtuale**     |NC6 Standard|
   |**Numero di nodi di destinazione**     |1|

3. Immettere un **Nome utente amministratore** e copiare il contenuto del file di chiave pubblica SSH in **Chiave SSH**. Accettare le impostazioni predefinite per i restanti valori della pagina e selezionare **Passaggio successivo: Configurazione nodo**.

   ![Immettere le informazioni di base del cluster](./media/quickstart-create-cluster-portal/create-cluster.png)

4. In **Monta volumi**, selezionare **Riferimenti file server** > **Aggiungi**. Selezionare il file server creato in precedenza. Immettere il **Percorso di montaggio relativo** nel quale il file server viene montato in ogni nodo del cluster. Selezionare **Salva e continua**.

   ![Aggiungere il riferimento al file server](./media/quickstart-create-cluster-portal/file-server-reference.png)

Salvare la configurazione del nodo e selezionare **Create cluster** (Crea cluster).

L'allocazione del nodo può richiedere alcuni minuti. In questo periodo, lo **Stato allocazione** del cluster è **Ridimensionamento**. Dopo alcuni minuti, lo stato del cluster è **Stabile** e il nodo si avvia.

![Avvio del cluster](./media/quickstart-create-cluster-portal/cluster-resizing.png)

Selezionare il nome del cluster per verificare lo stato del nodo. Quando lo stato di un nodo è **Inattivo**, è pronto per l'esecuzione dei processi.

### <a name="list-cluster-nodes"></a>Elencare i nodi del cluster

È possibile ottenere le informazioni di connessione dal portale se è necessario connettersi ai nodi del cluster (in questo caso, a un nodo singolo) per installare le applicazioni o eseguire attività di manutenzione. Dopo aver creato il cluster, fare clic su **Nodi** e prendere nota delle impostazioni della **Connessione** SSH (indirizzo IP e numero di porta).

![Nodi del cluster](./media/quickstart-create-cluster-portal/cluster-nodes.png)

Usare queste informazioni per stabilire una connessione SSH con il nodo. Ad esempio, sostituire l'indirizzo IP e il numero di porta corretti del nodo nel comando seguente:

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>Ridimensionare il cluster

Se il cluster viene usato per eseguire il training di un modello, potrebbero essere necessarie più risorse di calcolo. Ad esempio, per aumentare a 2 nodi la dimensione di un processo di training distribuito, selezionare **Ridimensiona** e impostare **Target number of node** (Numero di nodi di destinazione) su 2. Salvare la configurazione.

![Ridimensionare un cluster](./media/quickstart-create-cluster-portal/scale-cluster.png)

Il ridimensionamento del cluster può richiedere alcuni minuti.

## <a name="clean-up-resources"></a>Pulire le risorse

Per proseguire con le esercitazioni e gli esempi di Batch per intelligenza artificiale, usare l'area di lavoro, il cluster e il file server di Batch per intelligenza artificiale creati in questo avvio rapido.

Quando le macchine virtuali sottostanti sono in esecuzione, vengono addebitati il cluster e il file server di Batch per intelligenza artificiale, anche se non sono pianificati processi. Se si vuole mantenere la configurazione del cluster quando non sono previsti processi da eseguire, ridimensionare il cluster su 0 nodi. Per eseguire i processi, è possibile eseguire un successivo ridimensionamento a 1 o più nodi. 

Quando non sono più necessari, eliminare l'area di lavoro Batch per intelligenza artificiale che contiene il cluster e il file server. A tale scopo, selezionare l'area di lavoro Batch per intelligenza artificiale e fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido si è appreso come creare un cluster Batch per intelligenza artificiale e un file server associato usando il portale di Azure. Per altre informazioni su come usare un cluster Batch per intelligenza artificiale per eseguire il training di un modello, passare all'avvio rapido per il training di un modello di apprendimento avanzato.

> [!div class="nextstepaction"]
> [Eseguire il training di un modello di apprendimento avanzato](./quickstart-tensorflow-training-cli.md)
