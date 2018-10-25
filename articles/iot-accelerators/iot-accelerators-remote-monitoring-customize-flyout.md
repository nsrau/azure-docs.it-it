---
title: Aggiungere un riquadro a comparsa all'interfaccia utente della soluzione Monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo illustra come aggiungere un nuovo riquadro a comparsa su una pagina nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167427"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Aggiungere un riquadro a comparsa personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto

Questo articolo illustra come aggiungere un nuovo riquadro a comparsa in una pagina nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto. L'articolo descrive:

- Come preparare un ambiente di sviluppo locale.
- Come aggiungere un nuovo riquadro a comparsa a una pagina nell'interfaccia utente Web.

Il riquadro a comparsa di esempio in questo articolo viene visualizzato sulla pagina con la griglia la cui aggiunta è descritta nella procedura dettagliata [Aggiungere una griglia personalizzata all'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-grid.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questa guida pratica, è necessario che nel computer di sviluppo locale sia installato il software seguente:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Prima di iniziare

Prima di continuare, è necessario completare le procedure negli articoli seguenti:

- [Aggiungere una pagina personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-page.md).
- [Aggiungere un servizio personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-service.md)
- [Aggiungere una griglia personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Aggiungere un riquadro a comparsa

Per aggiungere un riquadro a comparsa all'interfaccia utente Web occorre aggiungere i file di origine che definiscono il riquadro a comparsa e modificare alcuni file esistenti in modo che l'interfaccia utente Web rilevi il nuovo componente.

### <a name="add-the-new-files-that-define-the-flyout"></a>Aggiungere i nuovi file che definiscono il riquadro a comparsa

Per iniziare, la cartella **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** contiene i file che definiscono un riquadro a comparsa:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Copiare la cartella **src/walkthrough/components/pages/pageWithFlyout/flyouts** nella cartella **src/components/pages/example**.

### <a name="add-the-flyout-to-the-page"></a>Aggiungere il riquadro a comparsa alla pagina

Modificare **src/components/pages/example/basicPage.js** per aggiungere il riquadro a comparsa.

Aggiungere **Btn** alle importazioni da **components/shared** e aggiungere importazioni per **svgs** e **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Aggiungere una definizione **const** per **closedFlyoutState** e aggiungerla allo stato nel costruttore:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Aggiungere le funzioni seguenti alla classe **BasicPage**:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Aggiungere le definizioni **const** seguenti alla funzione **render**:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Aggiungere un pulsante per aprire il riquadro a comparsa al menu di scelta rapida:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Aggiungere un testo e il contenitore del riquadro a comparsa al contenuto della pagina:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testare il riquadro a comparsa

Se l'interfaccia utente Web non è già in esecuzione in locale, eseguire il comando seguente nella radice della copia locale del repository:

```cmd/sh
npm start
```

Il comando precedente esegue localmente l'interfaccia utente all'indirizzo [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Passare alla pagina di **esempio** e fare clic su **Open Flyout** (Apri riquadro a comparsa).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state descritte le risorse disponibili per aggiungere o personalizzare pagine nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.

Ora che si è definito un riquadro a comparsa in una pagina, il passaggio successivo è [Aggiungere un nuovo pannello al dashboard nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-panel.md).

Per altre informazioni concettuali sull'acceleratore di soluzione Monitoraggio remoto, vedere l'articolo sull'[architettura di Monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
