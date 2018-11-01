---
title: Connettersi ad Archiviazione BLOB di Azure - App per la logica di Azure | Microsoft Docs
description: Creare e gestire BLOB in Archiviazione di Azure con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7d6845624c838ac0cf49e5963cae1ca0ea15ec15
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230608"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Creare e gestire BLOB in Archiviazione BLOB di Azure con App per la logica di Azure

Questo articolo illustra come accedere ai file archiviati come BLOB nell'account di archiviazione di Azure da un'app per la logica e come gestire tali file con il connettore di Archiviazione BLOB di Azure. In questo modo, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per la gestione dei file. È ad esempio possibile compilare app per la logica che creano, ottengono, aggiornano ed eliminano file nell'account di archiviazione.

Si supponga di avere uno strumento che viene aggiornato in un sito Web di Azure. L'evento funge da trigger per l'app per la logica. Quando l'evento si verifica, è possibile impostare l'app per la logica per aggiornare alcuni file nel contenitore di archiviazione BLOB, eseguendo un'azione nell'app per la logica. 

> [!NOTE]
> App per la logica non supporta la connessione diretta agli account di archiviazione di Azure attraverso firewall. Per accedere a questi account di archiviazione, usare una delle opzioni descritte qui: 
>
> * Creare un [ambiente del servizio di integrazione](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), che può connettersi alle risorse presenti in una rete virtuale di Azure. 
> 
> * Se si usa già Gestione API, è possibile usare questo servizio in questo scenario. Per altre informazioni, vedere [Architettura di integrazione aziendale semplice](https://aka.ms/aisarch).

Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Per informazioni tecniche specifiche del connettore, vedere le <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">informazioni di riferimento sul connettore di Archiviazione BLOB di Azure</a>.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Un [account di archiviazione di Azure e un contenitore di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md)

* L'app per la logica da cui accedere all'account di archiviazione BLOB di Azure. Per avviare l'app per la logica con un trigger di Archiviazione BLOB di Azure, è necessaria un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Aggiungere un trigger di archiviazione BLOB

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

Questo esempio illustra come avviare un flusso di lavoro di un'app logica con il trigger **Archiviazione BLOB di Azure - Quando viene aggiunto o modificato un BLOB (solo proprietà)** quando le proprietà di un BLOB vengono aggiunte o aggiornate nel contenitore di archiviazione. 

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nella casella di ricerca immettere "BLOB di Azure" come filtro. Nell'elenco di trigger selezionare il trigger desiderato.

   L'esempio usa questo trigger: **Archiviazione BLOB di Azure - Quando viene aggiunto o modificato un BLOB (solo proprietà)**

   ![Selezionare il trigger](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Se vengono chiesti i dati della connessione, [creare la connessione all'archiviazione BLOB](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per il trigger.

   Per questo esempio, selezionare il contenitore e la cartella da monitorare.

   1. Nella casella **Contenitore** selezionare l'icona di cartella.

   2. Nell'elenco delle cartelle scegliere la parentesi uncinata chiusa (**>**) e quindi individuare e selezionare la cartella desiderata. 

      ![Selezionare la cartella](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selezionare la frequenza e l'intervallo in base a cui si vuole che il trigger controlli le modifiche alla cartella.

4. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

5. Continuare ad aggiungere una o più azioni all'app per la logica per le attività da eseguire con i risultati del trigger.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Aggiungere un'azione di archiviazione BLOB

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio l'app per la logica viene avviata con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md).

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. In Progettazione app per la logica, sotto il trigger o l'azione, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

   ![Aggiungere un'azione](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. 
   Fare clic sul segno più (**+**) visualizzato e quindi scegliere **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "BLOB di Azure" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

   Questo esempio usa l'azione: **Archiviazione BLOB di Azure - Recupera contenuto BLOB**

   ![Seleziona azione](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Se vengono chiesti i dati della connessione, [creare la connessione ad Archiviazione BLOB di Azure](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per l'azione. 

   Per questo esempio, selezionare il file desiderato.

   1. Nella casella **BLOB** selezionare l'icona di cartella.
  
      ![Selezionare la cartella](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Individuare e selezionare il file desiderato in base al numero **ID** del BLOB. È possibile trovare il numero **ID** nei metadati del BLOB restituiti dal trigger di archiviazione BLOB descritto in precedenza.

5. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.
Per testare l'app per la logica, verificare che la cartella selezionata contenga un BLOB.

Questo esempio ottiene solo i contenuti di un BLOB. Per visualizzare i contenuti, aggiungere un'altra azione che crea un file con il BLOB usando un altro connettore. Aggiungere, ad esempio, un'azione di OneDrive che crea un file in base ai contenuti del BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Eseguire la connessione all'account di archiviazione

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
