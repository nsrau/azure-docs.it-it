---
title: 'Hub eventi: acquisire eventi di streaming tramite il portale di AzureEvent Hubs - Capture streaming events using Azure portal'
description: Questo articolo descrive come abilitare l'acquisizione di eventi in streaming tramite Hub eventi di Azure tramite il portale di Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187367"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Abilitare l'acquisizione di eventi in streaming tramite Hub eventi di Azure

[Azure Event Hubs Capture][capture-overview] consente di distribuire automaticamente i dati di streaming negli hub eventi a un archivio BLOB di [Azure](https://azure.microsoft.com/services/storage/blobs/) o a un account Azure Data Lake [Storage Gen1 o Gen 2](https://azure.microsoft.com/services/data-lake-store/) di propria scelta.

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com). È possibile acquisire i dati in un contenitore di archiviazione BLOB di Azure o in un account Azure Data Lake Storage Gen 1 o Gen 2.You can either capture the data to an Azure [Blob storage](https://azure.microsoft.com/services/storage/blobs/) container, or to an [Azure Data Lake Storage Gen 1 or Gen 2](https://azure.microsoft.com/services/data-lake-store/) account.

Per altre informazioni, vedere la [panoramica della funzionalità di acquisizione di Hub eventi][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Acquisire dati in Archiviazione di AzureCapture data to Azure Storage

Quando si crea un hub eventi, è possibile abilitare l'acquisizione facendo clic sul pulsante **Sì** nella schermata del portale **Crea hub eventi**. È quindi possibile specificare un account e un contenitore di archiviazione facendo clic su **Archiviazione di Azure** nella casella **Provider di acquisizione**. Poiché Acquisizione di Hub eventi usa l'autenticazione da servizio a servizio con la risorsa di archiviazione, non è necessario specificare una stringa di connessione di archiviazione. Il selettore risorse seleziona automaticamente l'URI della risorsa per l'account di archiviazione. Se si usa Azure Resource Manager, è necessario specificare questo URI in modo esplicito come stringa.

L'intervallo di tempo predefinito è di 5 minuti. Il valore minimo è 1, quello massimo 15. La finestra **Dimensione** ha un intervallo compreso tra 10 e 500 MB.

![Intervallo di tempo per l'acquisizione][1]

> [!NOTE]
> È possibile abilitare o disabilitare la creazione di file vuoti quando non si verificano eventi durante l'intervallo di acquisizione. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Acquisire dati in Azure Data Lake Storage Gen 2 

1. Seguire l'articolo [Creare un account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) per creare un account di archiviazione di Azure.Follow Create a storage account article to create an Azure Storage account. Impostare **Spazio dei nomi gerarchico su** Abilitato nella scheda Avanzate per renderlo un account Azure Data Lake Storage Gen 2.Set Hierarchical namespace to **Enabled** on the **Advanced** tab to make it an Azure Data Lake Storage Gen 2 account.
2. Quando si crea un hub eventi, eseguire la procedura seguente:When creating an event hub, do the following steps: 

    1. Selezionare **Attivato** per **Acquisizione**. 
    2. Selezionare **Archiviazione di Azure** come provider di acquisizione. L'opzione Azure Data Lake Store visualizzata per il provider di **acquisizione** è per la generazione 1 di Archiviazione di Azure Data Lake.The **Azure Data Lake Store** option you see for the Capture provider is for the Gen 1 of Azure Data Lake Storage. Per usare la generazione 2 di Archiviazione di Azure Data Lake, selezionare **Archiviazione di Azure**.
    2. Selezionare il **pulsante Seleziona contenitore.** 

        ![Abilitare l'acquisizione per Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selezionare l'account **Azure Data Lake Storage Gen 2** dall'elenco. 

    ![Seleziona Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selezionare il **contenitore** (file system in Data Lake Storage Gen 2).

    ![Selezionare il file system nella](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Nella pagina **Crea hub eventi** selezionare **Crea**. 

    ![Selezionare il pulsante Crea](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Il contenitore creato in Un archivio dati di Azure usando questa interfaccia utente (UI) viene visualizzato in **File system** in **Esplora archivi**. Analogamente, il file system creato in un account Data Lake Storage Gen 2 viene visualizzato come contenitore in questa interfaccia utente. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Acquisire dati in Azure Data Lake Storage Gen 1 

Per acquisire dati in Azure Data Lake Storage Gen 1, creare un account Data Lake Storage Gen 1 e un hub eventi:To capture data to Azure Data Lake Storage Gen 1, you create a Data Lake Storage Gen 1 account, and an event hub:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Creare un account e una cartella di Azure Data Lake Storage Gen 1Create an Azure Data Lake Storage Gen 1 account and folders

1. Creare un account di archiviazione di Data Lake seguendo le istruzioni in Introduzione ad Azure Data Lake Storage Gen 1 usando il portale di Azure.Create a Data Lake Storage account, following the instructions in [Get started with Azure Data Lake Storage Gen 1 using the Azure portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Seguire le istruzioni nella sezione [Assegnare autorizzazioni agli hub eventi](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) per creare una cartella all'interno dell'account Data Lake Storage Gen 1 in cui si desidera acquisire i dati dagli hub eventi e assegnare autorizzazioni agli hub eventi in modo che possano scrivere dati nell'account Data Lake Storage Gen 1.  


### <a name="create-an-event-hub"></a>Creare un hub eventi

1. L'hub eventi deve trovarsi nella stessa sottoscrizione di Azure dell'account Azure Data Lake Storage Gen 1 creato. Creare l'hub eventi facendo clic sul pulsante **Sì** sotto **Acquisisci** nella pagina del portale **Crea hub eventi**. 
2. Nella pagina del portale **Crea hub eventi** selezionare **Azure Data Lake Store** nella casella **Provider di acquisizione**.
3. In **Seleziona archivio** accanto all'elenco a discesa **Data Lake Store** specificare l'account Data Lake Storage Gen 1 creato in precedenza e nel campo Percorso data **Lake** immettere il percorso della cartella di dati creata.

    ![Selezionare l'account Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Aggiungere o configurare l'acquisizione in un hub eventi esistente

È possibile configurare l'acquisizione in hub eventi esistenti che si trovano in spazi dei nomi di Hub eventi. Per abilitare l'acquisizione in un hub eventi esistente o per modificarne le impostazioni, fare clic sullo spazio dei nomi per caricare la schermata Panoramica e quindi sull'hub eventi per cui si vuole abilitare l'acquisizione o modificarne l'impostazione. Infine fare clic sull'opzione **Acquisisci** nella parte sinistra della pagina aperta e quindi modificare le impostazioni come illustrato nelle figure seguenti:

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

![Configurare Archiviazione BLOB di Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Configurare Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Configurare Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'acquisizione, vedere la [panoramica della funzionalità di acquisizione di Hub eventi][capture-overview].
- È anche possibile configurare Acquisizione di Hub eventi usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Abilitare Acquisizione usando un modello di Azure Resource Manager).
- [Informazioni su come creare una sottoscrizione di Griglia di eventi di Azure con uno spazio dei nomi di hub eventi come origine](store-captured-data-data-warehouse.md)
- [Introduzione ad Azure Data Lake Store con il portale di AzureGet started with Azure Data Lake Store using the Azure portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
