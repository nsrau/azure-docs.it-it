---
title: Personalizzare l'interfaccia utente della soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come accedere al codice sorgente dell'interfaccia utente dell'acceleratore di soluzioni di monitoraggio remoto ed effettuare alcune personalizzazioni.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: aed63e332375be4f8ed939cf162545c9f366f329
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317596"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalizzare l'acceleratore di soluzioni di monitoraggio remoto

Questo articolo fornisce informazioni su come accedere al codice sorgente e personalizzare l'interfaccia utente dell'acceleratore di soluzioni di monitoraggio remoto.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>La preparazione di un ambiente di sviluppo locale per l'interfaccia utente

Il codice dell'interfaccia utente dell'acceleratore di soluzioni di monitoraggio remoto viene implementato usando il framework React.js. È possibile trovare il codice sorgente nel repository GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Per apportare modifiche all'interfaccia utente, è possibile eseguirne una copia locale. Per eseguire azioni come il recupero dei dati di telemetria, la copia locale si connette a un'istanza distribuita della soluzione.

I passaggi seguenti illustrano il processo per configurare un ambiente locale per lo sviluppo dell'interfaccia utente:

1. Distribuire un'istanza **base** dell'acceleratore di soluzioni usando l'interfaccia della riga di comando **pcs**. Prendere nota del nome della distribuzione e delle credenziali specificate per la macchina virtuale. Per altre informazioni, vedere [Deploy using the CLI](iot-accelerators-remote-monitoring-deploy-cli.md) (Distribuire usando l'interfaccia della riga di comando).

1. Per abilitare l'accesso SSH alla macchina virtuale che ospita i microservizi nella soluzione, usare il portale di Azure o Azure Cloud Shell. Ad esempio: 

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Abilitare l'accesso SSH solo durante il test e lo sviluppo. Se si abilita SSH, [disabilitarlo appena si termina di usarlo](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Usare il portale di Azure o Azure Cloud Shell per trovare il nome e l'indirizzo IP pubblico della macchina virtuale. Ad esempio: 

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Usare SSH per connettersi alla macchina virtuale. Usare l'indirizzo IP dal passaggio precedente e le credenziali specificate durante l'esecuzione di **pcs** per distribuire la soluzione. Il comando `ssh` è disponibile in Azure Cloud Shell.

1. Per consentire la connessione dell'esperienza utente locale, eseguire i comandi seguenti nella shell bash nella macchina virtuale:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Dopo aver visualizzato il completamento del comando e l'avvio del sito Web, è possibile disconnettersi dalla macchina virtuale.

1. Nella copia locale del repository [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui), modificare il file con estensione **env** per aggiungere l'URL della soluzione distribuita:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Al prompt dei comandi passare alla copia locale della cartella `azure-iot-pcs-remote-monitoring-webui`.

1. Per installare le librerie richieste ed eseguire localmente l'interfaccia utente, eseguire i comandi seguenti:

    ```cmd/sh
    npm install
    npm start
    ```

1. Il comando precedente esegue l'interfaccia utente in locale in http:\//localhost:3000 / dashboard. È possibile modificare il codice mentre il sito è in esecuzione e visualizzarne gli aggiornamenti in modo dinamico.

## <a name="customize-the-layout"></a>Personalizzare il layout

Ogni pagina nella soluzione di monitoraggio remoto è costituita da un set di controlli, a cui si fa riferimento nel codice sorgente come *pannelli*. La pagina **Dashboard** è costituita da cinque pannelli: Panoramica, Mappa, Avvisi, Telemetria e Analisi. È possibile trovare il codice sorgente che definisce ogni pagina e i pannelli nel repository GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Ad esempio, il codice che definisce la pagina **Dashboard**, il layout e i pannelli nella pagina si trova nella cartella [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Poiché i pannelli gestiscono il proprio layout e il ridimensionamento, è possibile modificare facilmente il layout di una pagina. Apportare le modifiche seguenti all'elemento **PageContent** nel file `src/components/pages/dashboard/dashboard.js` per:

* Scambiare le posizioni dei pannelli Mappa e Telemetria.
* Modificare le larghezze relative dei pannelli Mappa e Analytics.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Modificare il layout dei pannelli](./media/iot-accelerators-remote-monitoring-customize/layout.png)

È anche possibile aggiungere più istanze dello stesso pannello, o più versioni a se si [duplica e si personalizza un pannello](#duplicate-and-customize-an-existing-control). L'esempio seguente illustra come aggiungere due istanze del pannello Telemetria. Per apportare queste modifiche, modificare il file `src/components/pages/dashboard/dashboard.js`:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

È quindi possibile visualizzare diversi dati di telemetria in ciascun pannello:

![Pannelli di telemetria multipli](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicare e personalizzare un controllo esistente

La procedura seguente illustra come duplicare un pannello esistente, modificarlo e poi usare la versione modificata. La procedura usa il pannello **Avvisi** come esempio:

1. Nella copia locale del repository, fare una copia della cartella **Avvisi** nella cartella `src/components/pages/dashboard/panels`. Denominare la nuova copia **cust_alerts**.

1. Nel file **alertsPanel.js** nella cartella **cust_alerts** modificare il nome della classe in **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Aggiungere la riga seguente al file `src/components/pages/dashboard/panels/index.js` :

    ```javascript
    export * from './cust_alerts';
    ```

1. Sostituire `alertsPanel` con `CustAlertsPanel` nel file `src/components/pages/dashboard/dashboard.js`:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Il pannello **Avvisi** originale è stato sostituito con una copia denominata **CustAlerts**. La copia è identica all'originale. È ora possibile modificare la copia. Ad esempio, per modificare l'ordinamento delle colonne nel pannello **Avvisi**:

1. Aprire il file `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` .

1. Modificare le definizioni delle colonne come illustrato nel frammento di codice seguente:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Lo screenshot seguente mostra la nuova versione del pannello **Avvisi**:

![Pannello Avvisi aggiornato](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalizzare il grafico dei dati di telemetria

I file nella cartella `src/components/pages/dashboard/panels/telemtry` definiscono il grafico dei dati di telemetria sulla pagina **Dashboard**. L'interfaccia utente recupera i dati di telemetria dalla soluzione di back-end nel file `src/services/telemetryService.js`. La procedura seguente illustra come modificare il periodo di tempo visualizzato sul grafico dei dati di telemetria da 15 a 5 minuti:

1. Nel file `src/services/telemetryService.js`, individuare la funzione chiamata **getTelemetryByDeviceIdP15M**. Creare una copia di questa funzione e modificare la copia come segue:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Per usare questa nuova funzione per popolare il grafico dei dati di telemetria, aprire il file `src/components/pages/dashboard/dashboard.js`. Individuare la riga che inizializza il flusso di dati di telemetria e modificarla come segue:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Nel grafico dei dati di telemetria verranno ora visualizzati cinque minuti dei dati di telemetria:

![Grafico dei dati di telemetria che mostra un giorno](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Aggiungere un nuovo indicatore KPI

La pagina **Dashboard** mostra gli indicatori KPI nel pannello **Analytics**. Questi indicatori KPI vengono calcolati nel file `src/components/pages/dashboard/dashboard.js`. Il file `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` esegue il rendering degli indicatori KPI. I passaggi seguenti descrivono come calcolare ed eseguire il rendering di un nuovo valore di indicatore KPI sulla pagina **Dashboard**. L'esempio mostra come aggiungere una nuova modifica di percentuale nell'indicatore KPI degli avvisi:

1. Aprire il file `src/components/pages/dashboard/dashboard.js` . Modificare l'oggetto **initialState** per includere una proprietà **warningAlertsChange** come indicato di seguito:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Modificare l'oggetto **currentAlertsStats** per includere **totalWarningCount** come proprietà:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Calcolare il nuovo indicatore KPI. Individuare il calcolo per il conteggio degli avvisi critici. Duplicare il codice e modificare la copia come segue:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Includere il nuovo indicatore KPI **warningAlertsChange** nel flusso degli indicatori KPI:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Includere il nuovo indicatore KPI **warningAlertsChange** nei dati di stato utilizzati per il rendering dell'interfaccia utente:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Aggiornare i dati passati al pannello degli indicatori KPI:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Le modifiche al file `src/components/pages/dashboard/dashboard.js` sono terminate. I passaggi seguenti descrivono le modifiche da apportare nel file `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` per visualizzare il nuovo indicatore KPI:

1. Modificare la riga di codice seguente per recuperare il nuovo valore dell'indicatore KPI come indicato di seguito:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modificare il markup per visualizzare il nuovo valore dell'indicatore KPI come indicato di seguito:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

La pagina **Dashboard** mostra ora il nuovo valore dell'indicatore KPI:

![Indicatore KPI di avviso](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalizzare la mappa

Per informazioni dettagliate sui componenti della mappa nella soluzione, vedere la pagina [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Personalizzare la mappa) in GitHub.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Altre opzioni di personalizzazione

Per modificare ulteriormente i livelli di presentazione e visualizzazione nella soluzione di monitoraggio remoto, è possibile modificare il codice. I repository GitHub rilevanti sono i seguenti:

* [The configuration microservice for Azure IoT Solutions (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) (Microservizio di configurazione per Soluzioni di Azure IoT (.NET))
* [The configuration microservice for Azure IoT Solutions (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config) (Microservizio di configurazione per Soluzioni di Azure IoT (Java))
* [Azure IoT PCS Remote Monitoring Web UI](https://github.com/Azure/pcs-remote-monitoring-webui) (Interfaccia utente Web di monitoraggio remoto di Azure IoT PCS)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha fornito informazioni sulle risorse disponibili per personalizzare l'interfaccia utente Web nell'acceleratore di soluzioni di monitoraggio remoto. Per altre informazioni sulla personalizzazione dell'interfaccia utente, vedere gli articoli seguenti:

* [Aggiungere una pagina personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-page.md)
* [Aggiungere un servizio personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-service.md)
* [Aggiungere una griglia personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-grid.md)
* [Aggiungere un riquadro a comparsa personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Aggiungere un nuovo pannello al dashboard nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-panel.md)

Per altre informazioni concettuali sull'acceleratore di soluzioni di monitoraggio remoto, vedere [Architettura della soluzione di monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Per altre informazioni sulla personalizzazione dei microservizi della soluzione Monitoraggio remoto, vedere [Personalizzare e ridistribuire un microservizio](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
