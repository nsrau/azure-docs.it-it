---
title: Aggiungere una pagina per la soluzione di monitoraggio remoto dell'interfaccia utente - Azure | Microsoft Docs
description: Questo articolo illustra come aggiungere una nuova pagina all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447081"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Aggiungere una pagina personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto

Questo articolo illustra come aggiungere una nuova pagina all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto. L'articolo descrive:

- Come preparare un ambiente di sviluppo locale.
- Come aggiungere una nuova pagina all'interfaccia utente Web.

Altre guide pratiche estendono questo scenario per aggiungere ulteriori funzionalità alla pagina aggiunta.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questa guida pratica, è necessario che nel computer di sviluppo locale sia installato il software seguente:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>La preparazione di un ambiente di sviluppo locale per l'interfaccia utente

Il codice dell'interfaccia utente dell'acceleratore di soluzione Monitoraggio remoto viene implementato usando il framework JavaScript [React](https://reactjs.org/). È possibile trovare il codice sorgente nel repository GitHub [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui).

Per apportare e testare modifiche all'interfaccia utente, è possibile eseguirle nel computer di sviluppo locale. Facoltativamente, la copia locale può connettersi a un'istanza distribuita dell'acceleratore di soluzione per interagire con dispositivi simulati o reali.

Per preparare l'ambiente di sviluppo locale, usare Git per clonare il repository [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) nel computer locale:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Aggiungere una pagina

Per aggiungere una pagina all'interfaccia utente Web occorre aggiungere i file di origine che definiscono la pagina e modificare alcuni file esistenti in modo che l'interfaccia utente Web rilevi la nuova pagina.

### <a name="add-the-new-files-that-define-the-page"></a>Aggiungere i nuovi file che definiscono la pagina

Per iniziare, la cartella **src/walkthrough/components/pages/basicPage** contiene quattro file che definiscono una pagina semplice:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Creare una nuova cartella **src/components/pages/example** e copiare questi quattro file al suo interno.

### <a name="add-the-new-page-to-the-web-ui"></a>Aggiungere la nuova pagina all'interfaccia utente Web

Per aggiungere la nuova pagina all'interfaccia utente Web, apportare le modifiche seguenti ai file esistenti:

1. Aggiungere il nuovo contenitore di pagina al file **src/components/pages/index.js**:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Facoltativo) Aggiungere un'icona SVG per la nuova pagina. Per altre informazioni, vedere [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). È possibile usare un file SVG esistente.

1. Aggiungere il nome della pagina al file delle traduzioni **public/locales/en/translations.json**. L'interfaccia utente Web usa [i18next](https://www.i18next.com/) per l'internazionalizzazione.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Aprire il file **src/components/app.js** che definisce la pagina di livello superiore dell'applicazione. Aggiungere la nuova pagina all'elenco di importazioni:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Nello stesso file aggiungere la nuova pagina alla matrice `pagesConfig`. Impostare l'indirizzo `to` per la route, referenziare l'icona SVG e le traduzioni aggiunte in precedenza e impostare `component` sul contenitore della pagina:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Aggiungere eventuali nuovi percorsi di navigazione alla matrice `crumbsConfig`:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Questa pagina di esempio ha un solo percorso di navigazione, ma alcune pagine possono avere di più.

Salvare tutte le modifiche. Si è pronti per eseguire l'interfaccia utente Web con la nuova pagina aggiunta.

### <a name="test-the-new-page"></a>Testare la nuova pagina

Al prompt dei comandi passare alla cartella radice della copia locale del repository ed eseguire i comandi seguenti per installare le librerie richieste ed eseguire localmente l'interfaccia utente Web:

```cmd/sh
npm install
npm start
```

Il comando precedente esegue localmente l'interfaccia utente all'indirizzo [http://localhost:3000/dashboard](http://localhost:3000/dashboard).

Senza connettere l'istanza locale dell'interfaccia utente Web locale a un'istanza distribuita dell'acceleratore di soluzione, vengono visualizzati errori nel dashboard. Questi errori non influiscono sulla possibilità di testare la nuova pagina.

Ora è possibile modificare il codice mentre il sito è in esecuzione in locale e visualizzare gli aggiornamenti dell'interfaccia utente Web in modo dinamico.

## <a name="optional-connect-to-deployed-instance"></a>[Facoltativo] Connettersi all'istanza distribuita

Facoltativamente, è possibile connettere la copia in esecuzione locale dell'interfaccia utente Web all'acceleratore di soluzione Monitoraggio remoto nel cloud:

1. Distribuire un'istanza **base** dell'acceleratore di soluzioni usando l'interfaccia della riga di comando **pcs**. Prendere nota del nome della distribuzione e delle credenziali specificate per la macchina virtuale. Per altre informazioni, vedere [Deploy using the CLI](iot-accelerators-remote-monitoring-deploy-cli.md) (Distribuire usando l'interfaccia della riga di comando).

1. Usare il portale di Azure o [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per abilitare l'accesso SSH alla macchina virtuale che ospita i microservizi nella soluzione. Ad esempio:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Attivare l'accesso SSH solo durante il test e lo sviluppo. Se si abilita SSH, [disabilitarlo appena possibile](../security/azure-security-network-security-best-practices.md).

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

1. Nella copia locale del repository [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) modificare il file con estensione **env** per aggiungere l'URL della soluzione distribuita:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha fornito informazioni sulle risorse disponibili per personalizzare l'interfaccia utente Web nell'acceleratore di soluzioni di monitoraggio remoto.

Ora che si è definita una pagina, il passaggio successivo è [Aggiungere un servizio personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-service.md) che recupera i dati da visualizzare nell'interfaccia utente.

Per altre informazioni concettuali sull'acceleratore di soluzione Monitoraggio remoto, vedere l'articolo sull'[architettura di Monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
