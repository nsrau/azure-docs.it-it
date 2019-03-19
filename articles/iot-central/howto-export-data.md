---
title: Esportare i dati da Azure IoT Central | Microsoft Docs
description: Come esportare dati dall'applicazione Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 98e19cccff1c02f653022f2061854697ee11d1a2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759900"
---
# <a name="export-your-data-from-azure-iot-central"></a>Esportare i dati da Azure IoT Central

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare i dati nelle proprie istanze di **Archiviazione BLOB di Azure**, **Hub eventi di Azure** e **Bus di servizio di Azure**. È possibile esportare **misurazioni**, **dispositivi** e **modelli di dispositivo** in una destinazione a scopo di analisi di percorsi ad accesso frequente e ad accesso sporadico. È possibile esportare i dati nell'archiviazione Blob per l'esecuzione di analisi delle tendenze a lungo termine in Microsoft Power BI o esportare dati in hub eventi e Bus di servizio per trasformare ed estendere i dati in tempo quasi reale con App per la logica di Azure o funzioni di Azure.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.

## <a name="prerequisites"></a>Prerequisiti

È necessario essere amministratore nell'applicazione IoT Central

## <a name="export-to-blob-storage"></a>Eseguire l'esportazione nell'archiviazione BLOB

I dati di misurazioni, dispositivi e modelli di dispositivo vengono esportati nell'account di archiviazione ogni minuto e ogni file contiene il batch delle modifiche apportate dall'ultimo file esportato. I dati esportati sono in formato [Apache AVRO](https://avro.apache.org/docs/current/index.html).

Altre informazioni sull'[esportazione nell'archivio BLOB](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Eseguire l'esportazione nell'hub eventi e nel bus di servizio

I dati di misurazioni, dispositivi e modelli di dispositivo vengono esportati nell'hub eventi oppure in una coda o un argomento del bus di servizio. I dati delle misurazioni esportati arrivano in tempo quasi reale e contengono l'intero messaggio inviato dai dispositivi a IoT Central, non solo i valori delle misurazioni stesse. I dati dei dispositivi esportati arrivano in batch ogni minuto e contengono le modifiche alle proprietà e alle impostazioni di tutti i dispositivi, mentre i dati dei modelli di dispositivo esportati contengono le modifiche a tutti i modelli di dispositivo.

Altre informazioni sull'[esportazione in hub eventi e bus di servizio](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

Se non si ha un archivio, un hub eventi o un bus di servizio in cui esportare i dati, seguire questa procedura:

### <a name="create-storage-account"></a>Creare l'account di archiviazione

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Per altre informazioni, vedere la [documentazione di Archiviazione di Azure](https://aka.ms/blobdocscreatestorageaccount).

2. Come tipo di account scegliere **Utilizzo generico** o **Archiviazione BLOB**.

3. Scegliere una sottoscrizione.

    > [!Note]
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. Connettersi usando una stringa di connessione in questo caso.

4. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

### <a name="create-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

1. Creare [un nuovo spazio dei nomi di Hub eventi nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Per altre informazioni, vedere la [documentazione di Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

2. Scegliere una sottoscrizione.

    > [!Note]
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. Connettersi usando una stringa di connessione in questo caso.

3. Creare un hub eventi nello spazio dei nomi di Hub eventi. Passare allo spazio dei nomi e selezionare **+ Hub eventi** in alto per creare un'istanza di hub eventi.

### <a name="create-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Creare un [nuovo spazio dei nomi del bus di servizio nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Per altre informazioni, vedere la [documentazione del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).

2. Scegliere una sottoscrizione.

    > [!Note]
    > A questo punto è possibile esportare i dati in altre sottoscrizioni **diverse** da quella dell'applicazione IoT Central con pagamento in base al consumo. Connettersi usando una stringa di connessione in questo caso.

3. Passare allo spazio dei nomi del bus di servizio e selezionare **+ Coda** o **+ Argomento** in alto per creare una coda o un argomento in cui esportare i dati.

## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di una destinazione, come un archivio, un hub eventi o un bus di servizio, in cui esportare i dati, seguire questa procedura per configurare l'esportazione continua dei dati.

1. Accedere all'applicazione IoT Central.

2. Nel menu a sinistra, selezionare **esportazione continua dei dati**.

    > [!Note]
    > Se questa opzione non è presente nel menu a sinistra, significa che non si è amministratori dell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

    ![Crea nuovo Hub eventi](media/howto-export-data/export_menu.png)

3. Selezionare il **+ nuovo** pulsante in alto a destra. Scegliere **Archiviazione BLOB di Azure**, **Hub eventi di Azure** o **Bus di servizio di Azure** come destinazione dell'esportazione.

    > [!NOTE]
    > Il numero massimo di esportazioni per app è cinque.

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data/export_new.png)

4. Nell'elenco a discesa selezionare **l'account di archiviazione, lo spazio dei nomi dell'hub eventi o lo spazio dei nomi del bus di servizio**. È anche possibile selezionare l'ultima opzione dell'elenco, ossia **Immettere una stringa di connessione**. 

    > [!NOTE]
    > È possibile visualizzare solo gli account di archiviazione/hub/servizio spazi dei nomi del Bus gli spazi dei nomi di **stessa sottoscrizione dell'App IoT Central**. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.

    > [!NOTE]
    > Per le app in versione di valutazione per 7 giorni l'unico modo per configurare l'esportazione continua dei dati è tramite una stringa di connessione. A queste app non è infatti associata una sottoscrizione di Azure.

    ![Creare un nuovo hub eventi per l'esportazione continua dei dati](media/howto-export-data/export_create.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per:
    - L'account di archiviazione, passare all'account di archiviazione nel portale di Azure.
        - Sotto **le impostazioni**, selezionare **chiavi di accesso**
        - Copiare la stringa di connessione key1 o la stringa di connessione key2
    - Hub eventi o bus di servizio, passare allo spazio dei nomi nel portale di Azure.
        - Sotto **le impostazioni**, selezionare **criteri di accesso condiviso**
        - Scegliere il valore predefinito **RootManageSharedAccessKey** oppure crearne uno nuovo
        - Copiare la stringa di connessione primaria o secondaria.

6. Selezionare un contenitore/hub eventi/coda o argomento nell'elenco a discesa.

7. In **Data to export** (Dati da esportare) specificare ogni tipo di dati da esportare impostando il tipo su **On** (Attivato).

8. Per attivare l'esportazione continua dei dati, verificare che l'opzione **Esportazione dati** sia **attivata**. Selezionare **Salva**.

    ![Configurare l'esportazione continua dei dati](media/howto-export-data/export_list.png)

9. Dopo alcuni minuti, vengono visualizzati i dati nella cartella selezionata.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come esportare i dati, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Esportare i dati nell'archivio BLOB di Azure](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Esportare i dati in Hub eventi di Azure e nel bus di servizio di Azure](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Come visualizzare i dati in Power BI](howto-connect-powerbi.md)
