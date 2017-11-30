---
title: Acquisire dati da Hub eventi in Azure Data Lake Store | Microsoft Docs
description: Usare Azure Data Lake Store per acquisire dati da Hub eventi
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 221ee6990fe0b5bfc9e745fc85543c4e04e41bd3
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Usare Azure Data Lake Store per acquisire dati da Hub eventi

Informazioni su come usare Azure Data Lake Store per acquisire i dati ricevuti da Hub eventi di Azure.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Uno spazio dei nomi di Hub eventi**. Per istruzioni, vedere [Creare uno spazio dei nomi di Hub eventi](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Assicurarsi che l'account Data Lake Store e lo spazio dei nomi di Hub eventi si trovino nella stessa sottoscrizione di Azure.


## <a name="assign-permissions-to-event-hubs"></a>Assegnare autorizzazioni a Hub eventi

In questa sezione si crea una cartella nell'account in cui si vuole acquisire i dati da Hub eventi. Si assegnano anche le autorizzazioni a Hub eventi, in modo da consentire la scrittura di dati in un account Data Lake Store. 

1. Aprire l'account Data Lake Store in cui si vuole acquisire i dati da Hub eventi e quindi fare clic su **Esplora dati**.

    ![Esplora dati di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Esplora dati di Data Lake Store")

2.  Fare clic su **Nuova cartella** e quindi immettere un nome per la cartella in cui si vuole acquisire i dati.

    ![Creare una nuova cartella in Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Creare una nuova cartella in Data Lake Store")

3. Assegnare le autorizzazioni alla radice di Data Lake Store. 

    a. Fare clic su **Esplora dati**, selezionare la radice dell'account Data Lake Store e quindi fare clic su **Accesso**.

    ![Assegnare le autorizzazioni per la radice di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Assegnare le autorizzazioni per la radice di Data Lake Store")

    b. In **Accesso** fare clic su **Aggiungi**, fare clic su **Selezionare l'utente o il gruppo** e quindi cercare `Microsoft.EventHubs`. 

    ![Assegnare le autorizzazioni per la radice di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assegnare le autorizzazioni per la radice di Data Lake Store")
    
    Fare clic su **Seleziona**.

    c. In **Assegna autorizzazioni** fare clic su **Selezionare le autorizzazioni**. Impostare **Autorizzazioni** su **Esegui**. Impostare **Aggiungi a** su **Questa cartella e tutti gli elementi figlio**. Impostare **Aggiungi come** su **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.

    ![Assegnare le autorizzazioni per la radice di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assegnare le autorizzazioni per la radice di Data Lake Store")

    Fare clic su **OK**.

4. Assegnare le autorizzazioni per la cartella nell'account Data Lake Store in cui si vuole acquisire i dati.

    a. Fare clic su **Esplora dati**, selezionare la cartella nell'account Data Lake Store e quindi fare clic su **Accesso**.

    ![Assegnare le autorizzazioni per la cartella di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Assegnare le autorizzazioni per la cartella di Data Lake Store")

    b. In **Accesso** fare clic su **Aggiungi**, fare clic su **Selezionare l'utente o il gruppo** e quindi cercare `Microsoft.EventHubs`. 

    ![Assegnare le autorizzazioni per la cartella di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assegnare le autorizzazioni per la cartella di Data Lake Store")
    
    Fare clic su **Seleziona**.

    c. In **Assegna autorizzazioni** fare clic su **Selezionare le autorizzazioni**. Impostare **Autorizzazioni** su **Leggi, Scrivi** ed **Esegui**. Impostare **Aggiungi a** su **Questa cartella e tutti gli elementi figlio**. Impostare infine **Aggiungi come** su **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.

    ![Assegnare le autorizzazioni per la cartella di Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Assegnare le autorizzazioni per la cartella di Data Lake Store")
    
    Fare clic su **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Configurare Hub eventi per acquisire i dati in Data Lake Store

In questa sezione si crea un hub eventi in uno spazio dei nomi di Hub eventi. Si configura anche l'hub eventi per acquisire i dati in un account Azure Data Lake Store. Questa sezione presuppone che sia già stato creato uno spazio dei nomi di Hub eventi.

2. Dal riquadro **Panoramica** dello spazio dei nomi di Hub eventi fare clic su **+ Hub eventi**.

    ![Creare un hub eventi](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Creare un hub eventi")

3. Fornire i valori seguenti per configurare Hub eventi per acquisire i dati in Data Lake Store.

    ![Creare un hub eventi](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Creare un hub eventi")

    a. Specificare un nome per l'hub eventi.
    
    b. Per questa esercitazione, impostare **Numero di partizioni** e **Conservazione messaggi** sui valori predefiniti.
    
    c. Impostare **Acquisisci** su **Sì**. Impostare **Intervallo di tempo** (frequenza di acquisizione) e **Intervallo dimensioni** (dimensioni dei dati da acquisire). 
    
    d. Per **Capture Provider** (Provider di acquisizione) selezionare **Azure Data Lake Store** e quindi selezionare l'istanza di Data Lake Store creata in precedenza. Per **Data Lake Path** (Percorso Data Lake) immettere il nome della cartella creata nell'account Data Lake Store. È sufficiente fornire il percorso relativo della cartella.

    e. Lasciare il valore predefinito per **Formati dei nomi file di acquisizione di esempio**. Questa opzione controlla la struttura di cartelle che viene creata nella cartella di acquisizione.

    f. Fare clic su **Crea**.

## <a name="test-the-setup"></a>Testare la configurazione

È ora possibile testare la soluzione inviando i dati all'hub di eventi di Azure. Seguire le istruzioni in [Inviare eventi a Hub eventi di Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Una volta avviato l'invio dei dati, si vedranno i dati riflessi in Data Lake Store con la struttura di cartelle specificata. Si vedrà ad esempio in Data Lake Store una struttura di cartelle come quella illustrata nello screenshot seguente.

![Dati dell'hub eventi di esempio in Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Dati dell'hub eventi di esempio in Data Lake Store")

> [!NOTE]
> Anche se non ci sono messaggi in arrivo in Hub eventi, Hub eventi scrive file vuoti con solo le intestazioni nell'account Data Lake Store. I file vengono scritti con lo stesso intervallo di tempo specificato durante la creazione di Hub di eventi.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analizzare i dati in Archivio Data Lake

Una volta che i dati si trovano in Data Lake Store, è possibile eseguire processi di analisi per elaborarli ed esaminarli. Vedere l'[esempio USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) relativo a come eseguire questa operazione usando Azure Data Lake Analytics.
  

## <a name="see-also"></a>Vedere anche
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
