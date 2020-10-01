---
title: Hub eventi-Acquisisci eventi di streaming con portale di Azure
description: Questo articolo descrive come abilitare l'acquisizione di eventi in streaming tramite Hub eventi di Azure tramite il portale di Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 2381bfa627d00a78ed91af0ba81579588ee016ce
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613578"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Abilitare l'acquisizione di eventi in streaming tramite Hub eventi di Azure

L' [acquisizione di hub eventi][capture-overview] di Azure consente di recapitare automaticamente i dati in streaming in hub eventi a un account di [archiviazione BLOB di azure](https://azure.microsoft.com/services/storage/blobs/) o a [Azure Data Lake storage Gen1 o alla seconda generazione](https://azure.microsoft.com/services/data-lake-store/) scelta.

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com). È possibile acquisire i dati in un contenitore di [archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/) di Azure o in un account [Azure Data Lake storage generazione 1 o di seconda generazione](https://azure.microsoft.com/services/data-lake-store/) .

Per altre informazioni, vedere la [panoramica della funzionalità di acquisizione di Hub eventi][capture-overview].

> [!IMPORTANT]
> L'account di archiviazione di destinazione (archiviazione di Azure o Azure Data Lake Storage) deve trovarsi nella stessa sottoscrizione dell'hub eventi.

## <a name="capture-data-to-azure-storage"></a>Acquisire i dati in archiviazione di Azure

Quando si crea un hub eventi, è possibile abilitare l'acquisizione facendo clic sul pulsante **Sì** nella schermata del portale **Crea hub eventi**. È quindi possibile specificare un account e un contenitore di archiviazione facendo clic su **Archiviazione di Azure** nella casella **Provider di acquisizione**. Poiché Acquisizione di Hub eventi usa l'autenticazione da servizio a servizio con la risorsa di archiviazione, non è necessario specificare una stringa di connessione di archiviazione. Il selettore risorse seleziona automaticamente l'URI della risorsa per l'account di archiviazione. Se si usa Azure Resource Manager, è necessario specificare questo URI in modo esplicito come stringa.

L'intervallo di tempo predefinito è di 5 minuti. Il valore minimo è 1, quello massimo 15. La finestra **Dimensione** ha un intervallo compreso tra 10 e 500 MB.

![Intervallo di tempo per l'acquisizione][1]

> [!NOTE]
> È possibile abilitare o disabilitare la creazione di file vuoti quando non si verificano eventi durante l'intervallo di acquisizione. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Acquisire i dati in Azure Data Lake Storage generazione 2 

1. Per creare un account di archiviazione di Azure, vedere l'articolo [creare un account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) . Impostare **spazio dei nomi gerarchico** su **abilitato** nella scheda **avanzate** per impostarlo come Azure Data Lake storage account di generazione 2.
2. Quando si crea un hub eventi, seguire questa procedura: 

    1. Selezionare **on** per l' **acquisizione**. 
    2. Selezionare **archiviazione di Azure** come provider di acquisizione. L'opzione **Azure Data Lake Store** visualizzata per il **provider di acquisizione** è per la generazione 1 di Azure Data Lake storage. Per usare una generazione 2 di Azure Data Lake Storage, è necessario selezionare **archiviazione di Azure**.
    2. Selezionare il pulsante **Seleziona contenitore** . 

        ![Abilitare l'acquisizione a Data Lake Storage generazione 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selezionare l'account **Azure Data Lake storage generazione 2** dall'elenco. 

    ![Selezionare Data Lake Storage generazione 2](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selezionare il **contenitore** (file system in data Lake storage generazione 2).

    ![Selezionare file system nell'archiviazione](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Nella pagina **Crea Hub eventi** selezionare **Crea**. 

    ![Selezionare il pulsante Crea](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Il contenitore creato in una Azure Data Lake Storage generazione 2 con questa interfaccia utente viene visualizzato in **file system** **Storage Explorer**. Analogamente, il file system creato in un account Data Lake Storage generazione 2 viene visualizzato come contenitore in questa interfaccia utente. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Acquisire i dati Azure Data Lake Storage generazione 1 

Per acquisire i dati in Azure Data Lake Storage generazione 1, è necessario creare un account Data Lake Storage di generazione 1 e un hub eventi:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Creazione di un account e di cartelle di Azure Data Lake Storage generazione 1

1. Creare un account Data Lake Storage, seguendo le istruzioni [riportate in Introduzione a Azure Data Lake storage generazione 1 con il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Seguire le istruzioni nella sezione [assegnare autorizzazioni a hub eventi](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) per creare una cartella all'interno dell'account di data Lake storage generazione 1 in cui si vogliono acquisire i dati da Hub eventi e assegnare autorizzazioni a hub eventi in modo che sia in grado di scrivere dati nell'account data Lake storage di generazione 1.  


### <a name="create-an-event-hub"></a>Creare un hub eventi

1. L'hub eventi deve trovarsi nella stessa sottoscrizione di Azure dell'account di Azure Data Lake Storage generazione 1 creato. Creare l'hub eventi facendo clic sul pulsante **Sì** sotto **Acquisisci** nella pagina del portale **Crea hub eventi**. 
2. Nella pagina del portale **Crea hub eventi** selezionare **Azure Data Lake Store** nella casella **Provider di acquisizione**.
3. In **Seleziona archivio** accanto all'elenco a discesa **Data Lake Store** , specificare l'account di data Lake storage generazione 1 creato in precedenza e nel campo **Data Lake percorso** immettere il percorso della cartella dati creata.

    ![Selezionare l'account Data Lake Storage][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Aggiungere o configurare l'acquisizione in un hub eventi esistente

È possibile configurare l'acquisizione in hub eventi esistenti che si trovano in spazi dei nomi di Hub eventi. Per abilitare l'acquisizione in un hub eventi esistente o per modificarne le impostazioni, fare clic sullo spazio dei nomi per caricare la schermata Panoramica e quindi sull'hub eventi per cui si vuole abilitare l'acquisizione o modificarne l'impostazione. Infine fare clic sull'opzione **Acquisisci** nella parte sinistra della pagina aperta e quindi modificare le impostazioni come illustrato nelle figure seguenti:

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

![Configurare Archiviazione BLOB di Azure][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Configurare Azure Data Lake Storage generazione 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

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
- [Introduzione all'uso di Azure Data Lake Store con il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
