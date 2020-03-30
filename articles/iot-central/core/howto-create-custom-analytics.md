---
title: Estendere Azure IoT Central con l'analisi personalizzata . Documenti Microsoft
description: Gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per eseguire visualizzazioni e analisi personalizzate. Questa soluzione usa Azure Databricks.This solution uses Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158198"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estendere Azure IoT Central con analisi personalizzate con Azure DatabricksExtend Azure IoT Central with custom analytics using Azure Databricks

Questa guida alle procedure illustra, in qualità di sviluppatore di soluzioni, come estendere l'applicazione IoT Central con visualizzazioni e analisi personalizzate. Nell'esempio viene utilizzata un'area di lavoro [di Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) per analizzare il flusso di telemetria di IoT Central e generare visualizzazioni quali [box plot](https://wikipedia.org/wiki/Box_plot).

Questa guida alle procedure illustra come estendere IoT Central oltre a ciò che può già fare con gli strumenti di [analisi incorporati.](./howto-create-custom-analytics.md)

In questa guida alle procedure viene illustrato come:

* Trasmettere in flusso i dati di telemetria da un'applicazione IoT Central usando *l'esportazione continua dei dati.*
* Creare un ambiente Azure Databricks per analizzare e tracciare i dati di telemetria dei dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central nel sito Web di gestione delle applicazioni di Azure IoT Central con le impostazioni seguenti:Create an IoT Central application on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Piano tariffario | Standard |
| Modello di applicazione | Analisi in negozio – monitoraggio delle condizioni |
| Nome applicazione | Accettare l'impostazione predefinita o scegliere il proprio nome |
| URL | Accettare il valore predefinito o scegliere il proprio prefisso URL univoco |
| Directory | Il tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | La regione più vicina |

Gli esempi e le schermate in questo articolo usano l'area **degli Stati Uniti.The** examples and screenshots in this article use the United States region. Scegli una posizione vicina a te e assicurati di creare tutte le tue risorse nella stessa area.

Questo modello di applicazione include due dispositivi termostato simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Resource group

Usare il portale di [Azure per creare un gruppo](https://portal.azure.com/#create/Microsoft.ResourceGroup) di risorse denominato **IoTCentralAnalysis** per contenere le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central.Create your Azure resources in the same location as your IoT Central application.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il portale di Azure per creare uno spazio dei nomi Hub eventi con le impostazioni seguenti:Use the [Azure portal to create an Event Hubs namespace](https://portal.azure.com/#create/Microsoft.EventHub) with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Nome    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Subscription | Sottoscrizione in uso |
| Resource group | IoTCentralAnalysis (Analisi dell'ioTCentral) |
| Location | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="azure-databricks-workspace"></a>Area di lavoro di Azure DatabricksAzure Databricks workspace

Usare il portale di Azure per creare un servizio Azure Databricks con le impostazioni seguenti:Use the [Azure portal to create an Azure Databricks Service](https://portal.azure.com/#create/Microsoft.Databricks) with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Nome dell'area di lavoro    | Scegliere il nome dell'area di lavoro |
| Subscription | Sottoscrizione in uso |
| Resource group | IoTCentralAnalysis (Analisi dell'ioTCentral) |
| Location | Stati Uniti orientali |
| Piano tariffario | Standard |

Dopo aver creato le risorse necessarie, il gruppo di risorse IoTCentralAnalysis è simile alla schermata seguente:When you've created the required resources, your **IoTCentralAnalysis** resource group looks like the following screenshot:

![Gruppo di risorse analisi IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere dati di telemetria dall'applicazione IoT Central.In this section, you create an event hub to receive telemetry from your IoT Central application. L'hub eventi fornisce i dati di telemetria al processo di Analisi di flusso per l'elaborazione.

1. Nel portale di Azure passare allo spazio dei nomi Hub eventi e selezionare **Hub eventi.**
1. Denominare l'hub eventi **centralexport**e selezionare **Crea**.
1. Nell'elenco degli hub eventi nello spazio dei nomi selezionare **centralexport**. Scegliere quindi **Criteri di accesso condiviso**.
1. Selezionare **+ Aggiungi**. Creare un criterio denominato Listen con l'attestazione **Listen.Create** a policy named **Listen** with the Listen claim.
1. Quando il criterio è pronto, selezionarlo nell'elenco e quindi copiare il valore di **chiave primario della stringa di connessione.**
1. Prendere nota di questa stringa di connessione, è consigliabile in un secondo momento quando si configura il blocco appunti Databricks per la lettura dall'hub eventi.

Lo spazio dei nomi Hub eventi è simile alla schermata seguente:Your Event Hubs namespace looks like the following screenshot:

![Spazio dei nomi di Hub eventi](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

Nel sito Web di gestione delle applicazioni di Azure IoT Central passare all'applicazione IoT Central creata dal modello Contoso.On the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, navigate to the IoT Central application you created from the Contoso template. In questa sezione viene configurata l'applicazione per lo streaming dei dati di telemetria dai relativi dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **Esportazione dati,** selezionare **Nuovo**e quindi **Hub eventi**di Azure .
1. Utilizzare le seguenti impostazioni per configurare l'esportazione, quindi selezionare **Salva:**

    | Impostazione | valore |
    | ------- | ----- |
    | Nome visualizzato | Esportazione in hub eventi |
    | Attivato | Attivato |
    | Spazio dei nomi di Hub eventi | Nome dello spazio dei nomi Hub per gli hub di eventi |
    | Hub eventi | centralexport |
    | Misure | Attivato |
    | Dispositivi | Disattivato |
    | Modelli di dispositivo | Disattivato |

![Configurazione dell'esportazione dei dati](media/howto-create-custom-analytics/cde-configuration.png)

Attendere che lo stato dell'esportazione sia **In esecuzione** prima di continuare.

## <a name="configure-databricks-workspace"></a>Configurare l'area di lavoro Databricks

Nel portale di Azure passare al servizio Azure Databricks e selezionare **Avvia area di lavoro**. Nel browser viene aperta una nuova scheda che consente di accedere all'area di lavoro.

### <a name="create-a-cluster"></a>Creare un cluster

Nella pagina **Azure Databricks** selezionare **Nuovo cluster**nell'elenco delle attività comuni.

Utilizzare le informazioni nella tabella seguente per creare il cluster:

| Impostazione | valore |
| ------- | ----- |
| Cluster Name | analisi centrale |
| Modalità cluster | Standard |
| Versione di Databricks Runtime | 5.5 LTS (Scala 2.11, Spark 2.4.3) |
| Versione di Python | 3 |
| Enable Autoscaling (Abilita la scalabilità automatica) | No |
| Terminare dopo minuti di inattività | 30 |
| Tipo di lavoratore | Standard_DS3_v2 |
| Ruoli di lavoro | 1 |
| Tipo di driver | Uguale al lavoratore |

La creazione di un cluster può richiedere alcuni minuti, attendere il completamento della creazione del cluster prima di continuare.

### <a name="install-libraries"></a>Installare le librerie

Nella pagina **Cluster** attendere che lo stato del cluster sia **In esecuzione.**

I passaggi seguenti illustrano come importare la libreria necessaria per l'esempio nel cluster:The following steps show you how to import the library your sample needs into the cluster:

1. Nella pagina **Cluster** attendere che lo stato del cluster interattivo **di analisi centrale** sia In **esecuzione.**

1. Selezionare il cluster e quindi scegliere la scheda **Librerie.**

1. Nella scheda **Librerie** scegliere **Installa nuovo**.

1. Nella pagina **Installa libreria** scegliere **Maven** come origine della libreria.

1. Nella casella di testo **Coordinate** immettere il valore seguente:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Scegliere **Installa** per installare la libreria nel cluster.

1. Lo stato della libreria è ora **Installato**:

    ![Libreria installata](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importare un blocco appunti di Databricks

Usare la procedura seguente per importare un blocco appunti di Databricks contenente il codice Python per analizzare e visualizzare i dati di telemetria di IoT Central:Use the following steps to import a Databricks notebook that contains the Python code to analyze and visualize your IoT Central telemetry:

1. Passare alla pagina **Area di lavoro** nell'ambiente Databricks. Selezionare il menu a discesa accanto al nome dell'account e quindi scegliere **Importa**.

1. Scegliere di importare da un URL e immettere il seguente indirizzo:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Per importare il blocco appunti, scegliere **Importa**.

1. Selezionare **l'area di lavoro** per visualizzare il blocco appunti importato:

    ![Blocco appunti importato](media/howto-create-custom-analytics/import-notebook.png)

1. Modificare il codice nella prima cella Python per aggiungere la stringa di connessione di Event Hubs salvata in precedenza:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Eseguire l'analisi

Per eseguire l'analisi, è necessario collegare il blocco appunti al cluster:

1. Selezionare **Scollegato** e quindi selezionare il cluster **centralanalysis.**
1. Se il cluster non è in esecuzione, avviarlo.
1. Per avviare il blocco appunti, selezionare il pulsante Esegui.

È possibile che venga visualizzato un errore nell'ultima cella. In tal caso, verificare che le celle precedenti siano in esecuzione, attendere un minuto per la scrittura di alcuni dati nell'archivio, quindi eseguire nuovamente l'ultima cella.

### <a name="view-smoothed-data"></a>Visualizzare i dati smussati

Nel blocco appunti scorrere verso il basso fino alla cella 14 per visualizzare un grafico dell'umidità media in sequenza per tipo di dispositivo. Questo grafico viene aggiornato continuamente all'arrivo dei dati di telemetria di streaming:This plot continuously updates as streaming telemetry a come in streaming telemetrys a 3

![Grafico di telemetria con smoothed](media/howto-create-custom-analytics/telemetry-plot.png)

È possibile ridimensionare il grafico nel blocco appunti.

### <a name="view-box-plots"></a>Visualizzare i grafici dei box

Nel blocco appunti scorrere verso il basso fino alla cella 20 per visualizzare i [box plot.](https://en.wikipedia.org/wiki/Box_plot) I box plot sono basati su dati statici in modo che per aggiornarli è necessario rieseguire la cella:

![Grafici a scatola](media/howto-create-custom-analytics/box-plots.png)

È possibile ridimensionare i grafici nel blocco appunti.

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare costi non necessari, eliminare il gruppo di risorse IoTCentralAnalysis nel portale di Azure.To tidy up after this how-to and and avoid unnecessary costs, delete the **IoTCentralAnalysis** resource group in the Azure portal.

È possibile eliminare l'applicazione IoT Central dalla pagina **Gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere in flusso i dati di telemetria da un'applicazione IoT Central usando *l'esportazione continua dei dati.*
* Creare un ambiente Azure Databricks per analizzare e tracciare i dati di telemetria.

Ora che sai come creare analisi personalizzate, il passaggio successivo suggerito consiste nell'imparare a [gestire l'applicazione.](howto-administer.md)
