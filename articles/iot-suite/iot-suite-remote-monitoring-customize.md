---
title: Personalizzare l'interfaccia utente della soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo fornisce informazioni su come accedere al codice sorgente dell'interfaccia utente dell'acceleratore di soluzioni di monitoraggio remoto ed effettuare alcune personalizzazioni.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: be20d45b380f66208884f15f4644f36f2a403837
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalizzare l'acceleratore di soluzioni di monitoraggio remoto

Questo articolo fornisce informazioni su come accedere al codice sorgente e personalizzare l'interfaccia utente dell'acceleratore di soluzioni di monitoraggio remoto. L'articolo descrive:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>La preparazione di un ambiente di sviluppo locale per l'interfaccia utente

Il codice dell'interfaccia utente dell'acceleratore di soluzioni di monitoraggio remoto viene implementato usando il framework React.js. È possibile trovare il codice sorgente nel repository GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Per apportare modifiche all'interfaccia utente, è possibile eseguirne una copia locale. La copia locale si connette a un'istanza distribuita della soluzione per eseguire azioni come il recupero dei dati di telemetria.

I passaggi seguenti illustrano il processo per configurare un ambiente locale per lo sviluppo dell'interfaccia utente:

1. Distribuire un'istanza **base** dell'acceleratore di soluzioni usando l'interfaccia della riga di comando **pcs**. Prendere nota del nome della distribuzione e delle credenziali specificate per la macchina virtuale. Per altre informazioni, vedere [Deploy using the CLI](iot-suite-remote-monitoring-deploy-cli.md) (Distribuire usando l'interfaccia della riga di comando).

1. Usare il portale di Azure o [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per abilitare l'accesso SSH alla macchina virtuale che ospita i microservizi nella soluzione. Ad esempio: 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Usare il portale di Azure o [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per trovare il nome e l'indirizzo IP pubblico della macchina virtuale. Ad esempio: 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Usare SSH per la connessione alla macchina virtuale usando l'indirizzo IP dal passaggio precedente e le credenziali specificate durante l'esecuzione di **pcs** per distribuire la soluzione.

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

1. Al prompt dei comandi nella copia locale della cartella `azure-iot-pcs-remote-monitoring-webui`, eseguire i comandi seguenti per installare le librerie richieste ed eseguire localmente l'interfaccia utente:

    ```cmd/sh
    npm install
    npm start
    ```

1. Il comando precedente esegue localmente l'interfaccia utente all'indirizzo http://localhost:3000/dashboard. È possibile modificare il codice mentre il sito è in esecuzione e visualizzarne gli aggiornamenti in modo dinamico.

## <a name="customize-the-layout"></a>Personalizzare il layout

Ogni pagina nella soluzione di monitoraggio remoto è costituita da un set di controlli, a cui si fa riferimento nel codice sorgente come *pannelli*. La pagina **Dashboard**, ad esempio, è costituita da cinque pannelli: Panoramica, Mappa, Avvisi, Telemetria e Indicatori KPI. È possibile trovare il codice sorgente che definisce ogni pagina e i pannelli nel repository GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Ad esempio, il codice che definisce la pagina **Dashboard**, il layout e i pannelli nella pagina si trova nella cartella [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Poiché i pannelli gestiscono il proprio layout e il ridimensionamento, è possibile modificare facilmente il layout di una pagina. Ad esempio, le modifiche seguenti all'elemento **PageContent** nel file `src/components/pages/dashboard/dashboard.js` scambiano le posizioni dei pannelli della mappa e della telemetria e modificano le larghezze relative dei pannelli della mappa e dell'indicatore KPI:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Modificare il layout dei pannelli](media/iot-suite-remote-monitoring-customize/layout.png)

> [!NOTE]
> La mappa non è configurata nella distribuzione locale.

È anche possibile aggiungere più istanze dello stesso pannello, o più versioni a se si [duplica e si personalizza un pannello](#duplicate-and-customize-an-existing-control). L'esempio seguente illustra come aggiungere due istanze del pannello della telemetria modificando il file `src/components/pages/dashboard/dashboard.js`:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

È quindi possibile visualizzare diversi dati di telemetria in ciascun pannello:

![Pannelli di telemetria multipli](media/iot-suite-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> La mappa non è configurata nella distribuzione locale.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicare e personalizzare un controllo esistente

La procedura seguente illustra come usare il pannello **avvisi** come esempio di come duplicare un pannello esistente, modificarlo e usare la versione modificata:

1. Nella copia locale del repository, effettuare una copia della cartella **avvisi** nella cartella `src/components/pages/dashboard/panels`. Denominare la nuova copia **cust_alarms**.

1. Nel file **alarmsPanel.js** nella cartella **cust_alarms**, modificare il nome della classe in **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Aggiungere la riga seguente al file `src/components/pages/dashboard/panels/index.js` :

    ```nodejs
    export * from './cust_alarms';
    ```

1. Sostituire `AlarmsPanel` con `CustAlarmsPanel` nel file `src/components/pages/dashboard/dashboard.js`:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Il pannello **Avvisi** originale è stato sostituito con una copia denominata **CustAlarms**. Questa copia è identica all'originale. È ora possibile modificare la copia. Per modificare ad esempio l'ordinamento delle colonne nel pannello **Avvisi**:

1. Aprire il file `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` .

1. Modificare le definizioni delle colonne come illustrato nel frammento di codice seguente:

    ```nodejs
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

![Pannello Avvisi aggiornato](media/iot-suite-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalizzare il grafico dei dati di telemetria

Il grafico dei dati di telemetria sulla pagina **Dashboard** è definito dai file nella cartella `src/components/pages/dashboard/panels/telemtry`. L'interfaccia utente recupera i dati di telemetria dalla soluzione di back-end nel file `src/services/telemetryService.js`. La procedura seguente illustra come modificare il periodo di tempo visualizzato sul grafico dei dati di telemetria da 15 minuti a 5 minuti:

1. Nel file `src/services/telemetryService.js`, individuare la funzione chiamata **getTelemetryByDeviceIdP15M**. Creare una copia di questa funzione e modificare la copia come segue:

    ```nodejs
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

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Nel grafico dei dati di telemetria verranno ora visualizzati cinque minuti dei dati di telemetria:

![Grafico dei dati di telemetria che mostra un giorno](media/iot-suite-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Aggiungere un nuovo indicatore KPI

La pagina **Dashboard** mostra gli indicatori KPI nel pannello **System KPIs** (Indicatori KPI di sistema). Questi indicatori KPI vengono calcolati nel file `src/components/pages/dashboard/dashboard.js`. Il file `src/components/pages/dashboard/panels/kpis/kpisPanel.js` esegue il rendering degli indicatori KPI. I passaggi seguenti descrivono come calcolare ed eseguire il rendering di un nuovo valore di indicatore KPI sulla pagina **Dashboard**. L'esempio mostra come aggiungere una nuova modifica di percentuale nell'indicatore KPI degli avvisi:

1. Aprire il file `src/components/pages/dashboard/dashboard.js` . Modificare l'oggetto **initialState** per includere una proprietà **warningAlarmsChange** come indicato di seguito:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Modificare l'oggetto **currentAlarmsStats** per includere **totalWarningCount** come proprietà:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Calcolare il nuovo indicatore KPI. Individuare il calcolo per il conteggio degli avvisi critici. Duplicare il codice e modificare la copia come segue:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Includere il nuovo indicatore KPI **warningAlarmsChange** nel flusso degli indicatori KPI:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Aggiornare i dati passati al pannello degli indicatori KPI:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Le modifiche al file `src/components/pages/dashboard/dashboard.js` sono terminate. I passaggi seguenti descrivono le modifiche da apportare nel file `src/components/pages/dashboard/panels/kpis/kpisPanel.js` per visualizzare il nuovo indicatore KPI:

1. Modificare la riga di codice seguente per recuperare il nuovo valore dell'indicatore KPI come indicato di seguito:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Modificare il markup per visualizzare il nuovo valore dell'indicatore KPI come indicato di seguito:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

La pagina **Dashboard** mostra ora il nuovo valore dell'indicatore KPI:

![Indicatore KPI di avviso](media/iot-suite-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalizzare la mappa

Per informazioni dettagliate sui componenti della mappa nella soluzione, vedere la pagina [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Personalizzare la mappa) in GitHub.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Altre opzioni di personalizzazione

Per modificare ulteriormente i livelli di presentazione e visualizzazione nella soluzione di monitoraggio remoto, è possibile modificare il codice. I repository GitHub rilevanti sono i seguenti:

* [The configuration microservice for Azure IoT Solutions (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/) (Microservizio di configurazione per Soluzioni di Azure IoT (.NET))
* [The configuration microservice for Azure IoT Solutions (Java)](https://github.com/Azure/pcs-ui-config-java/) (Microservizio di configurazione per Soluzioni di Azure IoT (Java))
* [Azure IoT PCS Remote Monitoring Web UI](https://github.com/Azure/pcs-remote-monitoring-webui) (Interfaccia utente Web di monitoraggio remoto di Azure IoT PCS)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha fornito informazioni sulle risorse disponibili per personalizzare l'interfaccia utente Web nell'acceleratore di soluzioni di monitoraggio remoto.

Per altre informazioni concettuali sull'acceleratore di soluzioni di monitoraggio remoto, vedere [Architettura della soluzione di monitoraggio remoto](iot-suite-remote-monitoring-sample-walkthrough.md)

Per altre informazioni sulla personalizzazione della soluzione di monitoraggio remoto, vedere [Customize and redeploy a microservice](iot-suite-microservices-example.md) (Personalizzare e ridistribuire un microservizio)
<!-- Next tutorials in the sequence -->