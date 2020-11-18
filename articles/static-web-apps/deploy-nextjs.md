---
title: 'Esercitazione: Distribuire siti Web Next.js con rendering statico in App Web statiche di Azure'
description: Generare e distribuire siti dinamici Next.js con App Web statiche di Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 20a3337ab054ca1feefc2f5f08f8e6398417fb9e
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441614"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Distribuire siti Web Next.js con rendering statico in App Web statiche di Azure (anteprima)

Questa esercitazione descrive come distribuire un sito Web statico generato da [Next.js](https://nextjs.org) in [App Web statiche di Azure](overview.md). Per iniziare, verrà illustrato come installare, configurare e distribuire un'app Next.js. Durante questo processo verrà spiegato anche come gestire le problematiche comuni che spesso si verificano quando si generano pagine statiche con Next.js.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).
- Un account GitHub. [Creare un account gratuitamente](https://github.com/join).
- [Node.js](https://nodejs.org) installato.

## <a name="set-up-a-nextjs-app"></a>Configurare un'app Next.js

Invece di usare l'interfaccia della riga di comando di Next.js per creare un'app, è possibile usare un repository di avvio che include un'app Next.js esistente. In questo repository è presente un'app Next.js con route dinamiche, che evidenzia un problema di distribuzione comune. Per le route dinamiche è necessaria una configurazione di distribuzione aggiuntiva che verrà illustrata tra qualche istante.

Per iniziare, creare un nuovo repository da un repository di modelli nell'account GitHub. 

1. Passare a <http://github.com/staticwebdev/nextjs-starter/generate>.
1. Assegnare al repository il nome **nextjs-starter**
1. Clonare quindi il nuovo repository nel computer. Assicurarsi di sostituire `<YOUR_GITHUB_ACCOUNT_NAME>` con il nome dell'account.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Passare all'app Next.js appena clonata:

   ```bash
   cd nextjs-starter
   ```

1. Installare le dipendenze:

    ```bash
    npm install
    ```

1. Avviare l'app Next.js in sviluppo:

    ```bash
    npm run dev
    ```

Passare a `http://localhost:3000` per aprire l'app, dove dovrebbe essere visualizzato il sito Web seguente nel browser preferito:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Avviare l'app Next.js":::

Quando si fa clic su un framework o su una libreria, viene visualizzata una pagina dei dettagli sull'elemento selezionato:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Pagina dei dettagli":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Generare un sito Web statico dalla compilazione di Next.js

Quando si compila un sito Next.js con `npm run build`, l'app viene compilata come app Web tradizionale, non come sito statico. Per generare un sito statico, usare la configurazione dell'applicazione seguente.

1. Per configurare le route statiche, creare il file denominato _next.config.js_ nella radice del progetto e aggiungere il codice seguente.

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Questa configurazione esegue il mapping di `/` alla pagina di Next.js visualizzata per la route `/` e che corrisponde al file di pagina _pages/index.js_.

1. Aggiornare lo script di compilazione di _package.json_ in modo da generare anche un sito statico dopo la compilazione, usando il comando `next export`. Con il comando `export` viene generato un sito statico.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Dopo aver definito questo comando, App Web statiche eseguirà lo script `build` ogni volta che si esegue il push di un commit.

1. Generare un sito statico:

    ```bash
    npm run build
    ```

    Il sito statico viene generato e copiato in una cartella _out_ presente nella radice della directory di lavoro.

    > [!NOTE]
    > Questa cartella è elencata nel file _.gitignore_ perché deve essere generata da CI/CD durante la distribuzione.

## <a name="push-your-static-website-to-github"></a>Eseguire il push del sito Web statico in GitHub

App Web statiche di Azure distribuisce l'app da un repository GitHub e continua a eseguire questa operazione per ogni commit di cui viene eseguito il push in un ramo designato. Usare i comandi seguenti per sincronizzare le modifiche apportate a GitHub.

1. Preparare per il commit tutti i file modificati

    ```bash
    git add .
    ```

1. Eseguire il commit di tutte le modifiche

    ```bash
    git commit -m "Update build config"
    ```

1. Eseguire il push delle modifiche in GitHub.

    ```bash
    git push origin master
    ```

## <a name="deploy-your-static-website"></a>Distribuire il sito Web statico

I passaggi seguenti illustrano come collegare l'app di cui è stato appena eseguito il push in GitHub ad App Web statiche di Azure. Una volta in Azure, è possibile distribuire l'applicazione in un ambiente di produzione.

### <a name="create-a-static-app"></a>Creare un'app statica

1. Passare al [portale di Azure](https://portal.azure.com)
1. Fare clic su **Crea una risorsa**
1. Cercare **App Web statiche**
1. Fare clic su **App Web statiche (anteprima)**
1. Fare clic su **Crea**

1. Selezionare una sottoscrizione nell'elenco a discesa *Sottoscrizione* o usare il valore predefinito.
1. Fare clic sul collegamento **Nuovo** sotto l'elenco a discesa *Gruppo di risorse*. In *Nuovo nome del gruppo di risorse* digitare **mystaticsite** e fare clic su **OK**
1. Specificare un nome univoco globale per l'app nella casella di testo **Nome**. I caratteri validi includono `a-z`, `A-Z`, `0-9` e `-`. Questo valore viene usato come prefisso dell'URL per l'app statica in formato `https://<APP_NAME>.azurestaticapps.net`.
1. Nell'elenco a discesa *Area* scegliere l'area più vicina.
1. Selezionare **Gratuito** nell'elenco a discesa SKU.

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Creare l'app Web statica":::

### <a name="add-a-github-repository"></a>Aggiungere un repository GitHub

Per poter distribuire automaticamente i commit, il nuovo account di App Web statiche deve accedere al repository con l'app Next.js.

1. Fare clic sul pulsante **Accedi con GitHub**.
1. Selezionare l'**organizzazione** in cui è stato creato il repository peri l progetto Next.js, il cui nome potrebbe corrispondere al nome utente di GitHub.
1. Trovare e selezionare il nome del repository creato in precedenza.
1. Scegliere il ramo **master** nell'elenco a discesa *Ramo*.

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="Connessione a GitHub":::

### <a name="configure-the-build-process"></a>Configurare il processo di compilazione

App Web statiche di Azure è progettato per eseguire automaticamente attività comuni come l'installazione di moduli npm e l'esecuzione di `npm run build` durante ogni distribuzione. Alcune impostazioni, però, come la cartella di origine dell'applicazione e la cartella di destinazione della compilazione, devono essere configurate manualmente.

1. Fare clic sulla scheda **Compila** per configurare la cartella di output statica.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Scheda Compila":::

2. Digitare **out** nella casella di testo *Percorso artefatto app*.

### <a name="review-and-create"></a>Rivedi e crea

1. Fare clic sul pulsante **Rivedi e crea** per verificare che tutti i dettagli siano corretti.
1. Fare clic su **Crea** per avviare la creazione della risorsa ed effettuare il provisioning di un'azione di GitHub Actions per la distribuzione.
1. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
1. Nella finestra _Panoramica_ fare clic sul collegamento *URL* per aprire l'applicazione distribuita. 

Se il sito Web non viene caricato immediatamente, il flusso di lavoro di GitHub Actions in background è ancora in esecuzione. Una volta completato il flusso di lavoro, è possibile fare clic su Aggiorna nel browser per visualizzare l'app Web.
Se il sito Web non viene caricato immediatamente, il flusso di lavoro di GitHub Actions in background è ancora in esecuzione. Una volta completato il flusso di lavoro, è possibile fare clic su Aggiorna nel browser per visualizzare l'app Web.

Per controllare lo stato dei flussi di lavoro di Actions, passare alle azioni disponibili per il repository:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Sincronizzare le modifiche

Durante la creazione dell'app, App Web statiche di Azure ha creato un file del flusso di lavoro di GitHub Actions nel repository. È necessario riportare questo file nel repository locale per consentire la sincronizzazione della cronologia GIT.

Tornare al terminale ed eseguire il comando `git pull origin master`.

## <a name="configure-dynamic-routes"></a>Configurare le route dinamiche

Passare al sito appena distribuito e fare clic su uno dei logo dei framework o delle librerie. Invece di una pagina di dettagli, viene visualizzata una pagina di errore 404.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 nelle route dinamiche":::

Questo errore dipende dal fatto che Next.js ha generato la home page solo in base alla configurazione dell'applicazione.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generare pagine statiche da route dinamiche

1. Aggiornare il file _next.config.js_ in modo che Next.js usi un elenco di tutti i dati disponibili per generare pagine statiche per ogni framework/libreria:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > `exportPathMap` è una funzione asincrona. È quindi possibile effettuare una richiesta a un'API in questa funzione e usare l'elenco restituito per generare i percorsi.

2. Eseguire il push delle nuove modifiche nel repository GitHub e attendere alcuni minuti che GitHub Actions ricompili il sito. Al termine della compilazione, l'errore 404 scompare.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="Errore 404 risolto nelle route dinamiche":::

> [!div class="nextstepaction"]
> [Configurare un nome di dominio](custom-domain.md)
