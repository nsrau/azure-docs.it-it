---
title: Integrare Time Series Insights con il monitoraggio remoto - Azure | Microsoft Docs
description: In questa procedura si apprenderà come configurare Azure Time Series Insights per una soluzione di monitoraggio remoto esistente che non includa già Azure Time Series Insights.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 655d65ebfbb0141acd829a64414d9ba20dd2c697
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633743"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrare Azure Time Series Insights con il monitoraggio remoto

Azure Time Series Insights è un servizio di analisi, archiviazione e visualizzazione completamente gestito per la gestione di dati delle serie temporali su scala IoT sul cloud. Azure Time Series Insights consente di archiviare e gestire dati relativi alle serie temporali, esplorare e visualizzare contemporaneamente gli eventi, eseguire analisi delle cause radice e confrontare più siti e asset.

L'acceleratore di soluzioni per il monitoraggio remoto ora offre la distribuzione e l'integrazione automatica con Azure Time Series Insights. In questa procedura si apprenderà come configurare Azure Time Series Insights per una soluzione di monitoraggio remoto esistente che non includa già Azure Time Series Insights.

> [!NOTE]
> Azure Time Series Insights non è attualmente disponibile nel cloud Azure Cina. Le nuove distribuzioni dell'acceleratore di soluzioni per il monitoraggio remoto nel cloud Azure Cina usano Cosmos DB per tutte le operazioni di archiviazione.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura, è necessario aver già distribuito una soluzione di monitoraggio remoto:

* [Distribuire l'acceleratore di soluzioni per il monitoraggio remoto](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Creare un gruppo di consumer

Creare un gruppo di consumer dedicato nell'hub IoT, che verrà usato per i dati di streaming in Azure Time Series Insights.

> [!NOTE]
> I gruppi di consumer vengono usati dalle applicazioni per eseguire il pull dei dati dall'hub IoT di Azure. In ogni gruppo di consumer sono consentiti un massimo di cinque consumer di output. È consigliabile creare un nuovo gruppo di consumer ogni cinque sink di output; è possibile creare un massimo di 32 gruppi di consumer.

1. Nel portale di Azure, fare clic sul pulsante Cloud Shell.

1. Eseguire il comando seguente per creare un nuovo gruppo di consumer. Usare il nome dell'hub IoT nella distribuzione di monitoraggio remoto e il nome della distribuzione di monitoraggio remoto come nome del gruppo di risorse:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Distribuire Azure Time Series Insights

Successivamente, distribuire di Azure Time Series Insights come risorsa aggiuntiva nella soluzione di monitoraggio remoto e connetterla all'hub IoT.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Time Series Insights**.

    ![Nuovo ambiente Azure Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Per creare l'ambiente Azure Time Series Insights, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome ambiente | Nella schermata seguente viene usato il nome **contorosrmtsi**. Quando si completa questo passaggio, scegliere il proprio nome univoco. |
    | Sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Gruppo di risorse | **Usa esistente**. Selezionare il nome del gruppo di risorse di monitoraggio remoto esistente. |
    | Località | In questo esempio viene usata l'area **Stati Uniti orientali**. Creare l'ambiente nella stessa area della soluzione di monitoraggio remoto, se possibile. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Creare Azure Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Fare clic su **Create**(Crea). La creazione dell'ambiente può richiedere qualche minuto.

## <a name="create-event-source"></a>Creare un'origine evento

Creare una nuova origine evento per la connessione all'hub IoT. Verificare di usare il gruppo di consumer creato nei passaggi precedenti. Azure Time Series Insights richiede che ogni servizio disponga di un gruppo consumer dedicato, non usato da un altro servizio.

1. Passare al nuovo ambiente Azure Time Series Insights.

1. A sinistra, selezionare **Origine evento**.

    ![Visualizzare le origini evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Fare clic su **Aggiungi**.

    ![Aggiungere un'origine evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Per configurare l'hub IoT come nuova origine evento, usare i valori nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome origine evento | Nella schermata seguente viene usato il nome **contosorm-iot-hub**. Quando si completa questo passaggio, usare il proprio nome univoco. |
    | Sorgente | **Hub IoT** |
    | Opzione di importazione | **Usare un hub IoT delle sottoscrizioni disponibili** |
    | ID sottoscrizione | Selezionare la sottoscrizione di Azure nell'elenco a discesa. |
    | Nome dell'hub IoT | **contosorma57a6**. Usare il nome dell'hub IoT della soluzione di monitoraggio remoto. |
    | Nome dei criteri dell'hub IoT | **iothubowner** Verificare che il criterio usato sia un criterio di proprietario. |
    | Chiave dei criteri dell'hub IoT | Il campo viene popolato automaticamente. |
    | Gruppo di consumer dell'hub IoT | **timeseriesinsights** |
    | Formato di serializzazione eventi | **JSON**     | Nome della proprietà Timestamp | Lasciare vuoto |

    ![Creare un'origine evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Fare clic su **Create**(Crea).

## <a name="configure-the-data-access-policy"></a>Configurare i criteri di accesso ai dati

Per assicurarsi che tutti gli utenti autorizzati ad accedere alla soluzione di monitoraggio remoto siano in grado di esplorare i dati in Azure Time Series Insights, aggiungere l'applicazione e gli utenti ai criteri di accesso dati nel portale di Azure. 

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere il gruppo di risorse **ContosoRM**.

1. Scegliere **contosormtsi** nell'elenco delle risorse di Azure.

1. Scegliere **Data Access Policies** (Criteri di accesso ai dati) per visualizzare l'elenco corrente dei ruoli assegnati per l'elenco attuale.

1. Scegliere **Aggiungi** per aprire il riquadro **Select User Rule** (Seleziona regola utente).

   Se l'utente non dispone delle autorizzazioni per assegnare i ruoli, l'opzione **Aggiungi** non verrà visualizzata.

1. Nell'elenco a discesa **Ruolo** selezionare un ruolo, ad esempio **Lettore** e **Collaboratore**.

1. Nell'elenco **Selezione** selezionare un utente, un gruppo o un'applicazione. Se l'entità di sicurezza non è visualizzata nell'elenco, è possibile digitare nella casella **Selezione** per cercare nella directory i nomi visualizzati, gli indirizzi e-mail e gli identificatori di oggetto.

1. Scegliere **Salva** per creare l'assegnazione di ruolo. Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo nei criteri di accesso ai dati.

> [!NOTE]
> Se è necessario concedere ad altri utenti l'accesso allo strumento di esplorazione di Azure Time Series Insights, eseguire la procedura seguente per [concedere l'accesso ai dati](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Configurare Analisi di flusso di Azure 

Il passaggio successivo consiste nel configurare il microservizio di gestione di Analisi di flusso di Azure per interrompere l'invio dei messaggi a Cosmos DB e archiviarli solo in Azure Time Series Insights. Se si desidera duplicare i messaggi in Cosmos DB, ignorare questo passaggio.

1. Nell'elenco di spostamento scegliere **Gruppi di risorse**.

1. Scegliere il gruppo di risorse **ContosoRM**.

1. Trovare il processo di streaming Analisi di flusso di Azure nell'elenco delle risorse. Il nome della risorsa inizia per **streamingjobs-**.

1. Nella parte superiore fare clic sul pulsante per arrestare i processi di streaming di Analisi di flusso di Azure.

1. Modificare la query di Analisi di flusso di Azure e rimuovere le clausole **SELECT**, **INTO** e **FROM** che fanno riferimento al flusso di messaggi in Cosmos DB. Queste clausole sono generalmente in fondo alla query come nell'esempio seguente:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Riavviare il processi di streaming di Analisi di flusso di Azure.

7. Estrarre le modifiche più recenti per il microservizio di gestione di Analisi di flusso di Azure digitando il comando seguente al prompt dei comandi:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Configurare il microservizio telemetria

Estrarre il microservizio telemetria più recente digitando il comando seguente nel prompt dei comandi:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Facoltativo]*  Configurare l'interfaccia utente Web per connettersi a Time Series Insights Explorer

Per visualizzare facilmente i dati in Time Series Insights Explorer, si consiglia di personalizzare l'interfaccia utente per collegarsi con facilità all'ambiente. A tale scopo, estrarre le ultime modifiche apportate all'interfaccia utente Web usando il comando seguente:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Configurare le variabili di ambiente

Per completare l'integrazione di Azure Time Series Insights, è necessario configurare l'ambiente della distribuzione per i microservizi aggiornati.

### <a name="basic-deployments"></a>Distribuzioni di base

Configurare l'ambiente della distribuzione `basic` per i microservizi aggiornati.

1. Nel portale di Azure fare clic sulla scheda **Azure Active Directory** nel riquadro di spostamento sinistro.

1. Fare clic su **Registrazioni per l'app**.

1. Cercare l'applicazione **ContosoRM** e farci clic sopra.

1. Passare a **Impostazioni** > **Chiavi** e creare una nuova chiave per l'applicazione. Assicurarsi di copiare il valore della chiave in una posizione sicura.

1. Estrarre il [file yaml di Docker Compose più recente](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) dal repository GitHub usando il tag più recente. 

1. Collegarsi con una chiave SSH alla macchina virtuale seguendo la procedura descritta in [Come usare le chiavi SSH con Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Una volta connessi, digitare `cd /app`.

1. Aggiungere le seguenti variabili di ambiente a ogni microservizio del file yaml di Docker Compose e allo script `env-setup` nella macchina virtuale:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Passare al **servizio telemetria** e modificare il file di Docker Compose aggiungendo le stesse variabili di ambiente precedenti.

1. Passare al **servizio ASA manager** e modificare il file di Docker Compose aggiungendo `PCS_TELEMETRY_STORAGE_TYPE`.

1. Riavviare i contenitori Docker usando `sudo ./start.sh` dalla macchina virtuale.

### <a name="standard-deployments"></a>Distribuzioni standard

Configurare l'ambiente della distribuzione `standard` per i precedenti microservizi aggiornati

1. Nella riga di comando, eseguire `kubectl proxy`. Per altre informazioni, vedere [Use an HTTP Proxy to Access the Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server) (Usare un proxy HTTP per accedere all'API Kubernetes).

1. Aprire la console di gestione di Kubernetes.

1. Trovare la mappa di configurazione per aggiungere le seguenti nuove variabili di ambiente per Azure Time Series Insights:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Modificare il file yaml del modello per il pod del servizio telemetria:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Modificare il file yaml del modello per il pod del servizio ASA manager:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come esplorare i dati e diagnosticare un avviso generato in Time Series Insights Explorer, vedere l'esercitazione relativa a [condurre un'analisi delle cause principali](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Per altre informazioni su come esplorare i dati ed eseguire query su di essi in Time Series Insights Explorer, vedere [Strumento di esplorazione di Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
