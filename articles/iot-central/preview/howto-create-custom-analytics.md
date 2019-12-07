---
title: Estendi IoT Central di Azure con analisi personalizzate | Microsoft Docs
description: Per gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per eseguire analisi e visualizzazioni personalizzate. Questa soluzione USA Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 59fb0dfbc44746853f25437e8e13a1cbc317e151
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895550"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks-preview-features"></a>Estendi IoT Central di Azure con analisi personalizzate con Azure Databricks (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa guida dettagliata illustra come uno sviluppatore di soluzioni, come estendere l'applicazione IoT Central con le analisi e le visualizzazioni personalizzate. Nell'esempio viene usata un'area di lavoro di [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) per analizzare il flusso di dati di telemetria IOT Central e generare visualizzazioni, ad esempio i [tracciati box](https://wikipedia.org/wiki/Box_plot).

Questa guida dettagliata illustra come estendere IoT Central oltre quello che può già fare con gli [strumenti di analisi incorporati](./howto-create-custom-analytics.md).

In questa guida dettagliata si apprenderà come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare un ambiente di Azure Databricks per analizzare e tracciare i dati di telemetria del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| Piano di pagamento | Pagamento in base al consumo |
| Modello di applicazione | Analisi in-Store-monitoraggio delle condizioni |
| Nome dell'applicazione | Accetta il nome predefinito o scegli il tuo nome |
| URL | Accettare l'impostazione predefinita o scegliere il prefisso URL univoco |
| Directory | Tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Area geografica | Area geografica più vicina |

Gli esempi e le schermate in questo articolo usano l'area **Stati Uniti** . Scegliere una località vicina e assicurarsi di creare tutte le risorse nella stessa area.

Questo modello di applicazione include due dispositivi termotermostati simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Gruppo di risorse

Usare il [portale di Azure per creare un gruppo di risorse](https://portal.azure.com/#create/Microsoft.ResourceGroup) denominato **IoTCentralAnalysis** per contenere le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il [portale di Azure per creare uno spazio dei nomi di hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| name    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | IoTCentralAnalysis |
| Località | Stati Uniti Orientali |
| Unità elaborate | 1 |

### <a name="azure-databricks-workspace"></a>Area di lavoro Azure Databricks

Usare il [portale di Azure per creare un servizio Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| Nome dell'area di lavoro    | Scegliere il nome dell'area di lavoro |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | IoTCentralAnalysis |
| Località | Stati Uniti Orientali |
| Piano tariffario | Standard |

Quando sono state create le risorse necessarie, il gruppo di risorse **IoTCentralAnalysis** è simile allo screenshot seguente:

![Gruppo di risorse di analisi IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere i dati di telemetria dall'applicazione IoT Central. L'hub eventi recapita i dati di telemetria al processo di analisi di flusso per l'elaborazione.

1. Nella portale di Azure passare allo spazio dei nomi di hub eventi e selezionare **+ Hub eventi**.
1. Denominare il **centralexport**dell'hub eventi e selezionare **Crea**.
1. Nell'elenco degli hub eventi nello spazio dei nomi selezionare **centralexport**. Quindi scegliere **criteri di accesso condiviso**.
1. Selezionare **+ Aggiungi**. Creare un criterio denominato **Listen** con l'attestazione **Listen** .
1. Quando il criterio è pronto, selezionarlo nell'elenco e quindi copiare il valore della **stringa di connessione-chiave primaria** .
1. Prendere nota di questa stringa di connessione, che verrà usata in un secondo momento quando si configura il notebook di databricks per la lettura dall'hub eventi.

Lo spazio dei nomi di hub eventi è simile allo screenshot seguente:

![Spazio dei nomi di Hub eventi](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

Nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) passare all'applicazione IoT Central creata dal modello contoso. In questa sezione l'applicazione viene configurata in modo da trasmettere i dati di telemetria dai dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **esportazione dati** , selezionare **+ nuovo**, quindi **Hub eventi di Azure**.
1. Usare le impostazioni seguenti per configurare l'esportazione, quindi selezionare **Salva**:

    | Impostazione | Value |
    | ------- | ----- |
    | Nome visualizzato | Esporta in hub eventi |
    | Attivato | On |
    | Spazio dei nomi di Hub eventi | Nome dello spazio dei nomi di hub eventi |
    | Hub eventi | centralexport |
    | Misurazioni | On |
    | Dispositivi | Off |
    | Modelli di dispositivo | Off |

![Configurazione esportazione dati](media/howto-create-custom-analytics/cde-configuration.png)

Prima di continuare, attendere che lo stato di esportazione sia **in esecuzione** .

## <a name="configure-databricks-workspace"></a>Configura area di lavoro databricks

Nella portale di Azure passare al servizio Azure Databricks e selezionare **Avvia area di lavoro**. Viene aperta una nuova scheda nel browser e viene effettuato l'accesso all'area di lavoro.

### <a name="create-a-cluster"></a>Creare un cluster

Nell'elenco delle attività comuni della pagina **Azure Databricks** selezionare **nuovo cluster**.

Usare le informazioni nella tabella seguente per creare il cluster:

| Impostazione | Value |
| ------- | ----- |
| Cluster Name | centralanalysis |
| Cluster Mode | Standard |
| Databricks Runtime Version | 5,5 LTS (scala 2,11, Spark 2.4.3) |
| Versione di Python | 3 |
| Abilita la scalabilità automatica | No |
| Termina dopo minuti di inattività | 30 |
| Worker Type | Standard_DS3_v2 |
| Ruoli di lavoro | 1 |
| Driver Type | Same as worker |

La creazione di un cluster può richiedere alcuni minuti, attendere il completamento della creazione del cluster prima di continuare.

### <a name="install-libraries"></a>Installare le librerie

Nella pagina **cluster** attendere che lo stato del cluster sia **in esecuzione**.

La procedura seguente illustra come importare la libreria necessaria per l'esempio nel cluster:

1. Nella pagina **cluster** attendere che lo stato del cluster interattivo **Centralanalysis** sia **in esecuzione**.

1. Selezionare il cluster e quindi scegliere la scheda **librerie** .

1. Nella scheda **librerie** scegliere **Installa nuovo**.

1. Nella pagina **Installa libreria** scegliere **Maven** come origine della libreria.

1. Nella casella di testo **Coordinate** immettere il valore seguente: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Scegliere **Installa** per installare la libreria nel cluster.

1. Lo stato della libreria è ora **installato**:

    ![Libreria installata](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importare un notebook di databricks

Usare la procedura seguente per importare un notebook di databricks contenente il codice Python per analizzare e visualizzare i dati di telemetria IoT Central:

1. Passare alla pagina dell' **area di lavoro** nell'ambiente databricks. Selezionare l'elenco a discesa accanto al nome dell'account e quindi scegliere **Importa**.

1. Scegliere di importare da un URL e immettere l'indirizzo seguente: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Per importare il notebook, scegliere **Importa**.

1. Selezionare l' **area di lavoro** per visualizzare il notebook importato:

    ![Notebook importato](media/howto-create-custom-analytics/import-notebook.png)

1. Modificare il codice nella prima cella Python per aggiungere la stringa di connessione dell'hub eventi salvata in precedenza:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Esegui analisi

Per eseguire l'analisi, è necessario alleghi il notebook al cluster:

1. Selezionare **scollegato** e quindi selezionare il cluster **centralanalysis** .
1. Se il cluster non è in esecuzione, avviarlo.
1. Per avviare il notebook, selezionare il pulsante Run (Esegui).

Nell'ultima cella potrebbe essere visualizzato un errore. In caso affermativo, controllare che le celle precedenti siano in esecuzione, attendere un minuto per la scrittura dei dati nella risorsa di archiviazione, quindi eseguire di nuovo l'ultima cella.

### <a name="view-smoothed-data"></a>Visualizza dati smussati

Nel notebook scorrere fino alla cella 14 per visualizzare un tracciato dell'umidità media mobile per tipo di dispositivo. Questo tracciato viene aggiornato continuamente durante l'arrivo della telemetria di streaming:

![Tracciato di telemetria smussato](media/howto-create-custom-analytics/telemetry-plot.png)

È possibile ridimensionare il grafico nel notebook.

### <a name="view-box-plots"></a>Visualizza grafici box

Nel notebook scorrere fino alla cella 20 per visualizzare i [tracciati box](https://en.wikipedia.org/wiki/Box_plot). I tracciati box sono basati sui dati statici, pertanto per aggiornarli è necessario eseguire di nuovo la cella:

![Box Plot](media/howto-create-custom-analytics/box-plots.png)

È possibile ridimensionare i tracciati nel notebook.

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare i costi non necessari, eliminare il gruppo di risorse **IoTCentralAnalysis** nel portale di Azure.

È possibile eliminare l'applicazione IoT Central dalla pagina di **gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare un ambiente di Azure Databricks per analizzare e tracciare i dati di telemetria.

Ora che si è appreso come creare analisi personalizzate, il passaggio successivo suggerito consiste nell'apprendere come [visualizzare e analizzare i dati di IOT Central di Azure in un dashboard Power bi](../core/howto-connect-powerbi.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
