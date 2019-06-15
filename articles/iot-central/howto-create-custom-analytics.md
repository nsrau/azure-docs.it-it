---
title: Estendere Azure IoT Central con analitica personalizzato | Microsoft Docs
description: Gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per eseguire operazioni personalizzate analitica e visualizzazioni. Questa soluzione Usa Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743441"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Estendere Azure IoT Central con analitica personalizzato

Questa guida illustra, gli sviluppatori di soluzioni, come estendere l'applicazione IoT Central con analitica personalizzati e visualizzazioni. L'esempio Usa un' [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) dell'area di lavoro per analizzare il flusso di dati di telemetria IoT Central e generare visualizzazioni, ad esempio [casella tracciati](https://wikipedia.org/wiki/Box_plot).

Questa guida illustra come estendere IoT Central oltre già cosa con il [strumenti di analitica incorporata](howto-create-analytics.md).

In questa guida illustra come:

* Stream i dati di telemetria da un'applicazione che usa IoT Central *esportazione continua dei dati*.
* Creare un ambiente di Azure Databricks per analizzare e tracciare i dati di telemetria di dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central dal [Azure IoT Central - applicazioni personali](https://aka.ms/iotcentral) pagina con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| Piano di pagamento | Pagamento in base al consumo |
| Modello di applicazione | Esempio Contoso |
| Nome dell'applicazione | Accettare il valore predefinito oppure scegliere il proprio nome |
| URL | Accettare il valore predefinito oppure scegliere il proprio prefisso URL univoco |
| Directory | Il tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | Stati Uniti orientali |

Gli esempi e schermate in questo articolo usano il **Stati Uniti orientali** area. Scegliere una località vicina e assicurarsi di che creare tutte le risorse nella stessa area.

### <a name="resource-group"></a>Gruppo di risorse

Usare la [portale di Azure per creare un gruppo di risorse](https://portal.azure.com/#create/Microsoft.ResourceGroup) chiamato **IoTCentralAnalysis** per contenere le altre risorse create. Creare le risorse di Azure nella stessa posizione dell'applicazione IoT Central.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare la [portale di Azure per creare uno spazio dei nomi di hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Name    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | IoTCentralAnalysis |
| Località | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="azure-databricks-workspace"></a>Area di lavoro di Azure Databricks

Usare la [portale di Azure per creare un servizio di Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| Nome dell'area di lavoro    | Scegliere il nome dell'area di lavoro |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | IoTCentralAnalysis |
| Località | Stati Uniti orientali |
| Piano tariffario | Standard |

Dopo aver creato le risorse necessarie, il **IoTCentralAnalysis** gruppo di risorse è simile alla seguente:

![Gruppo di risorse di analisi IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente dati di telemetria a un hub eventi. In questa sezione si crea un hub eventi per ricevere i dati di telemetria dall'applicazione IoT Central. L'hub eventi fornisce i dati di telemetria al processo di Stream Analitica per l'elaborazione.

1. Nel portale di Azure, passare allo spazio dei nomi di hub eventi e selezionare **+ Hub eventi**.
1. Nome dell'hub eventi **centralexport**e selezionare **crea**.
1. Nell'elenco degli hub eventi nello spazio dei nomi, selezionare **centralexport**. Quindi scegliere **criteri di accesso condiviso**.
1. Selezionare **+ Aggiungi**. Creare un criterio denominato **ascolto** con il **ascolto** di attestazione.
1. Una volta pronto il criterio, selezionarlo nell'elenco e quindi copiare il **connessione stringa chiave primaria** valore.
1. Prendere nota di questa stringa di connessione, usarla in un secondo momento quando si configura il notebook di Databricks per leggere da hub eventi.

Lo spazio dei nomi di hub eventi è simile alla seguente:

![Spazio dei nomi di Hub eventi](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

Passare il [applicazione IoT Central](https://aka.ms/iotcentral) creato dal modello di Contoso. In questa sezione configurare l'applicazione per trasmettere i dati di telemetria dai relativi dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare il **esportazione continua dei dati** pagina, selezionare **+ nuovo**e quindi **hub eventi di Azure**.
1. Usare le impostazioni seguenti per configurare l'esportazione e quindi selezionare **salvare**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome visualizzato | Esporta in hub eventi |
    | Enabled | Attivato |
    | Spazio dei nomi di Hub eventi | Il nome dello spazio dei nomi di hub eventi |
    | Hub eventi | centralexport |
    | Misure | Attivato |
    | Dispositivi | Off |
    | Modelli di dispositivo | Off |

![Configurazione per l'esportazione continua dei dati](media/howto-create-custom-analytics/cde-configuration.png)

Attendere fino a ottenere lo stato di esportazione **in esecuzione** prima di continuare.

## <a name="configure-databricks-workspace"></a>Configura area di lavoro di Databricks

Nel portale di Azure, passare al servizio Azure Databricks e selezionare **avvia area di lavoro**. Una nuova scheda viene aperto nel browser e si accede all'area di lavoro.

### <a name="create-a-cluster"></a>Creare un cluster

Nel **Azure Databricks** pagina, sotto l'elenco delle attività comuni, selezionare **nuovo Cluster**.

Usare le informazioni nella tabella seguente per creare il cluster:

| Impostazione | Value |
| ------- | ----- |
| Cluster Name | centralanalysis |
| Modalità cluster | Standard |
| Versione del Runtime di Databricks | 5.3 (Scala 2.11, Spark 2.4.0) |
| Versione di Python | 3 |
| Enable Autoscaling (Abilita la scalabilità automatica) | No |
| Termina dopo pochi minuti di inattività | 30 |
| Tipo di ruolo di lavoro | Standard_DS3_v2 |
| Ruoli di lavoro | 1 |
| Tipo di driver | Stesso come ruolo di lavoro |

Creazione di un cluster può richiedere alcuni minuti, attendere la creazione del cluster per il completamento prima di continuare.

### <a name="install-libraries"></a>Installare le librerie

Nel **cluster** pagina, attendere fino a quando il cluster è stato **esecuzione**.

I passaggi seguenti illustrano come importare la libreria di esempio deve essere in cluster:

1. Nel **cluster** pagina, attendere finché lo stato del **centralanalysis** cluster interattivo è **esegue**.

1. Selezionare il cluster e quindi scegliere il **librerie** scheda.

1. Nel **librerie** scheda, scegliere **installa nuovi**.

1. Nel **installare Library** pagina, scegliere **Maven** come origine della libreria.

1. Nel **coordina** nella casella di testo, immettere il valore seguente: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Scegli **installare** per installare la libreria nel cluster.

1. Lo stato della libreria è ora **Installed**:

    ![Libreria installata](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importare un notebook di Databricks

Usare la procedura seguente per importare un notebook di Databricks che contiene il codice Python per analizzare e visualizzare i dati di telemetria IoT Central:

1. Passare il **dell'area di lavoro** pagina Databricks nell'ambiente in uso. Selezionare l'elenco a discesa accanto al nome dell'account e quindi scegliere **importazione**.

1. Scegliere di importare da un URL e immettere l'indirizzo seguente: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Per importare il notebook, scegliere **importare**.

1. Selezionare il **dell'area di lavoro** per visualizzare il notebook importato:

    ![Notebook importati](media/howto-create-custom-analytics/import-notebook.png)

1. Modificare il codice nella prima cella Python per aggiungere la stringa di connessione di hub eventi che è stato salvato in precedenza:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Eseguire l'analisi

Per eseguire l'analisi, è necessario collegare il notebook nel cluster:

1. Selezionare **Detached** e quindi selezionare la **centralanalysis** cluster.
1. Se il cluster non è in esecuzione, avviarlo.
1. Per avviare il notebook, selezionare il pulsante di esecuzione.

Si verifichi un errore nell'ultima cella. In questo caso, verificare se che vengono eseguite le celle precedenti, attendere un minuto per la scrittura in archiviazione di alcuni dati e quindi eseguire di nuovo all'ultima cella.

### <a name="view-smoothed-data"></a>Visualizzare i dati equalizzati

Nel notebook, scorrere fino alla cella 14 per vedere un tracciato di umidità Media in sequenza dal tipo di dispositivo. Questo grafico viene continuamente aggiornata man mano che arrivano i dati di telemetria di streaming:

![Livellati tracciato i dati di telemetria](media/howto-create-custom-analytics/telemetry-plot.png)

È possibile ridimensionare il grafico nel notebook.

### <a name="view-box-plots"></a>Visualizzazione box plot

Nel notebook, scorrere fino alla cella 20 per vedere le [finestra tracciati](https://en.wikipedia.org/wiki/Box_plot). Box plot si basano sui dati statici in modo che per l'aggiornamento è necessario eseguire nuovamente la cella:

![Box plot](media/howto-create-custom-analytics/box-plots.png)

È possibile ridimensionare i tracciati nel notebook.

## <a name="tidy-up"></a>Operazioni finali

Per ripulire dopo questa procedura ed evitare i costi non necessari, eliminare il **IoTCentralAnalysis** gruppo di risorse nel portale di Azure.

È possibile eliminare l'applicazione IoT Central dal **gestione** pagina all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Stream i dati di telemetria da un'applicazione che usa IoT Central *esportazione continua dei dati*.
* Creare un ambiente di Azure Databricks per analizzare e tracciare i dati di telemetria.

Dopo aver appreso come creare analitica personalizzato, il passaggio successivo consigliato consiste informazioni su come [Visualize e analizzare i dati di Azure IoT Central in un dashboard di Power BI](howto-connect-powerbi.md).
