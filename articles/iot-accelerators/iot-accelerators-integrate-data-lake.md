---
title: Integrare la soluzione di monitoraggio remoto con Azure Data Lake Store | Microsoft Docs
description: Informazioni su come integrare la soluzione di monitoraggio remoto con Azure Data Lake Store usando un processo di Analisi di flusso di Azure.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 3bd29e348fd067c12def8ca36fbdc1d7e35b2874
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627587"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrare la soluzione di monitoraggio remoto con Azure Data Lake Store

Se si hanno requisiti di analisi più elevati rispetto a quelli che può soddisfare la soluzione di monitoraggio remoto, il servizio Azure Data Lake Store offre la soluzione ideale per questa applicazione poiché può archiviare dati provenienti da grandi set di dati eterogenei e può essere integrato con Azure Data Lake Analytics per fornire analisi on demand.

In questa procedura si userà un processo di Analisi di flusso di Azure per eseguire lo streaming di dati dall'hub IoT nella soluzione di monitoraggio remoto a un'istanza di Azure Data Lake Store.

## <a name="prerequisites"></a>prerequisiti

Per completare questa procedura, è necessario quanto segue:

* [Distribuire l'acceleratore di soluzioni di monitoraggio remoto](iot-accelerators-remote-monitoring-deploy.md).
  * La soluzione implementerà l'hub IoT e il processo di Analisi di flusso di Azure usati in questo articolo nella sottoscrizione Azure.
* [Distribuire un'istanza di Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
  * L'istanza di Data Lake Store deve essere distribuita nella stessa area della soluzione di monitoraggio remoto.
  * [Creare una cartella](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) denominata "streaming" nell'account.

## <a name="create-a-consumer-group"></a>Creare un gruppo di consumer

Creare un gruppo di consumer dedicato nell'hub IoT della soluzione di monitoraggio remoto. Questo gruppo verrà usato dal processo di Analisi di flusso per lo streaming dei dati a Data Lake Store.

> [!NOTE]
> I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati dall'hub IoT di Azure. È necessario creare un nuovo gruppo per ogni cinque consumer di output. Sono consentiti al massimo 32 gruppi di consumer.

1. Accedere al portale di Azure.

1. Nel portale di Azure fare clic sul pulsante **Shell Cloud**.

    ![Icona di avvio del portale](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Eseguire il comando seguente per creare un nuovo gruppo di consumer:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Per il gruppo di risorse e l'hub IoT usare i nomi definiti nella soluzione di monitoraggio remoto.

## <a name="create-stream-analytics-job"></a>Creare un processo di Analisi di flusso

Creare un processo di Analisi di flusso di Azure per eseguire lo streaming dei dati dall'hub IoT all'istanza di Azure Data Lake Store.

1. Fare clic su **Crea una risorsa**, selezionare Internet delle cose da Marketplace e quindi fare clic su **Processo di Analisi di flusso**.

    ![Nuovo processo di Analisi di flusso](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Immettere un nome di processo e selezionare la sottoscrizione e il gruppo di risorse appropriati.

1. Selezionare una località nella stessa area dell'istanza di Data Lake Store o in un'area adiacente. In questo esempio viene usata l'area Stati Uniti orientali.

1. Assicurarsi di lasciare l'impostazione predefinita **Cloud** per l'ambiente di hosting.

1. Fare clic su **Crea**.

    ![Creare un processo di Analisi di flusso](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configurare il processo di Analisi di flusso

1. Passare al **processo di Analisi di flusso** nel gruppo di risorse della soluzione di monitoraggio remoto.

1. Nella pagina Panoramica fare clic su **Input**.

    ![Pagina Panoramica](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Fare clic su **Aggiungi input del flusso** e selezionare **Hub IoT** dall'elenco a discesa.

    ![Aggiungere l'input](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Nella scheda Nuovo input immettere l'alias di input **IoTHub**.

1. Dall'elenco a discesa Gruppo di consumer selezionare il gruppo di consumer creato in precedenza. In questo esempio viene usato **streamanalyticsjob**.

    ![Selezionare l'input](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Fare clic su **Save**.

1. Nella pagina Panoramica fare clic su **Output**.

    ![Aggiungere un'istanza di Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Fare clic su **Aggiungi** e selezionare **Data Lake Store** dall'elenco a discesa.

    ![Aggiungere l'output](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Nella scheda Nuovo output immettere l'alias di output **DataLakeStore**.

1. Selezionare l'account di Data Lake Store creato nei passaggi precedenti e specificare la struttura di cartelle per lo streaming dei dati nell'archivio.

1. Nel campo Formato data immettere **/streaming/{date}/{time}**. Lasciare i valori predefiniti per il formato della data (YYYY/MM/DD) e dell'ora (HH).

    ![Specificare la struttura di cartelle](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Fare clic su **Autorizza**.

    Sarà necessario autorizzare l'accesso a Data Lake Store per consentire al processo di Analisi di flusso di scrivere nel file system.

    ![Autorizzare l'accesso di Analisi di flusso a Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Verrà visualizzata una finestra popup e, dopo la chiusura della finestra, il pulsante Autorizza verrà disabilitato al termine dell'autorizzazione.

    > [!NOTE]
    > Se nella finestra popup viene visualizzato un errore, aprire una nuova finestra del browser in incognito e riprovare.

1. Fare clic su **Save**.

## <a name="edit-the-stream-analytics-query"></a>Modificare la query di Analisi di flusso

Analisi di flusso di Azure usa un linguaggio di query simile a SQL per specificare un'origine di input che genera un flusso di dati, trasformare i dati nel modo desiderato e inviare l'output in diverse destinazioni di archiviazione o elaborazione.

1. Nella scheda Panoramica fare clic su **Modifica query**.

    ![Edit Query](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. Nell'editor di query sostituire i segnaposto [YourOutputAlias] e [YourInputAlias] con i valori definiti in precedenza.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Query di Analisi di flusso](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Fare clic su **Save**.
1. Fare clic su **Sì** per accettare le modifiche.

## <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso

1. Nella scheda Panoramica fare clic su **Avvia**.

    ![Avvia un processo di Analisi di flusso](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Nella scheda Avvia processo fare clic su **Personalizzato**.

1. Impostare l'ora personalizzata per tornare indietro di alcune ore e selezionare i dati dal momento in cui il dispositivo ha iniziato a eseguire lo streaming.

1. Fare clic su **Avvia**.

    ![Selezionare la data personalizzata](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Attendere che il processo passi allo stato di esecuzione. Se vengono visualizzati errori è possibile che la query non sia corretta. Verificarne la sintassi.

    ![Processo in esecuzione](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Il processo di streaming inizierà a leggere i dati dall'hub IoT e ad archiviarli nell'istanza di Data Lake Store. I dati potrebbero iniziare ad apparire in Data Lake Store solo dopo alcuni minuti.

## <a name="explore-the-streaming-data"></a>Esplorare i dati di streaming

1. Passare a Data Lake Store.

1. Nella scheda Panoramica fare clic su **Esplora dati**.

1. In Esplora dati eseguire il drill-down fino alla cartella **/streaming**. Verranno visualizzate cartelle create con il formato AAAA/MM/GG/HH.

    ![Esplorare i dati di streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Verranno quindi visualizzati file con estensione json, uno per ogni ora.

    ![Esplorare i dati di streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Passaggi successivi

Per eseguire analisi di Big Data sui set di dati di Data Lake Store è possibile usare Azure Data Lake Analytics. Per altre informazioni, vedere la [documentazione di Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics).
