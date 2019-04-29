---
title: Aggiungere una griglia all'interfaccia utente della soluzione Monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo illustra come aggiungere una nuova griglia in una pagina nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/04/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447098"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Aggiungere una griglia personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto

Questo articolo illustra come aggiungere una nuova griglia a una pagina nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto. L'articolo descrive:

- Come preparare un ambiente di sviluppo locale.
- Come aggiungere una nuova griglia a una pagina nell'interfaccia utente Web.

La griglia di esempio in questo articolo visualizza i dati del servizio la cui aggiunta è descritta nella procedura dettagliata [Aggiungere un servizio personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-service.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questa guida pratica, è necessario che nel computer di sviluppo locale sia installato il software seguente:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Prima di iniziare

Prima di continuare, è necessario completare le procedure negli articoli seguenti:

- [Aggiungere una pagina personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-page.md).
- [Aggiungere un servizio personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Aggiungere una griglia

Per aggiungere una griglia all'interfaccia utente Web occorre aggiungere i file di origine che definiscono la griglia e modificare alcuni file esistenti in modo che l'interfaccia utente Web rilevi il nuovo componente.

### <a name="add-the-new-files-that-define-the-grid"></a>Aggiungere i nuovi file che definiscono la griglia

Per iniziare, la cartella **src/walkthrough/components/pages/pageWithGrid/exampleGrid** contiene i file che definiscono una griglia:

**exampleGrid.js**



**exampleGridConfig.js**



Copiare la cartella **src/walkthrough/components/pages/pageWithGrid/exampleGrid** nella cartella **src/components/pages/example**.

### <a name="add-the-grid-to-the-page"></a>Aggiungere la griglia alla pagina

Modificare **src/components/pages/example/basicPage.container.js** come indicato di seguito per importare le definizioni del servizio:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Modificare **src/components/pages/example/basicPage.js** come indicato di seguito per aggiungere la griglia:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Modificare **src/components/pages/example/basicPage.test.js** come indicato di seguito per aggiornare i test:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testare la griglia

Se l'interfaccia utente Web non è già in esecuzione in locale, eseguire il comando seguente nella radice della copia locale del repository:

```cmd/sh
npm start
```

Il comando precedente esegue localmente l'interfaccia utente all'indirizzo [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Passare alla pagina **Esempio** pagina per vedere la griglia visualizzare dati dal servizio.

## <a name="select-rows"></a>Selezionare le righe

Sono disponibili due opzioni per consentire a un utente di selezionare le righe della griglia:

### <a name="hard-select-rows"></a>Righe con selezione hard

Se un utente deve eseguire operazioni su più righe contemporaneamente, usare caselle di controllo sulle righe:

1. Abilitare la selezione hard delle righe aggiungendo un oggetto **checkboxColumn** agli elementi **columnDefs** forniti alla griglia. **checkboxColumn** è definito in **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Per accedere agli elementi selezionati si ottiene un riferimento all'API della griglia interna:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Fornire pulsanti contestuali alla pagina quando una riga nella griglia è con selezione hard:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Quando viene selezionato un pulsante contestuale, recuperare gli elementi con selezione hard su cui intervenire:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Righe con selezione soft

Se l'utente deve eseguire operazioni su una sola riga, configurare un collegamento di selezione soft per una o più colonne di **columnDefs**.

1. In **exampleGridConfig.js** aggiungere **SoftSelectLinkRenderer** come **cellRendererFramework** per un oggetto **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Quando viene selezionato un collegamento di selezione soft, viene attivato l'evento **onSoftSelectChange**. Eseguire qualsiasi azione desiderata per la riga, ad esempio l'apertura di un riquadro a comparsa di dettagli. Questo esempio scrive semplicemente nella console:

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state descritte le risorse disponibili per aggiungere o personalizzare pagine nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.

Ora che si è definita una griglia, il passaggio successivo è [Aggiungere un riquadro a comparsa personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-flyout.md) che viene visualizzato sulla pagina di esempio.

Per altre informazioni concettuali sull'acceleratore di soluzione Monitoraggio remoto, vedere l'articolo sull'[architettura di Monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
