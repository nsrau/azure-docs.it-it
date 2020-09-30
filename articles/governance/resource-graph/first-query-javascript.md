---
title: 'Avvio rapido: Eseguire la prima query JavaScript'
description: Questa guida di avvio rapido descrive la procedura per abilitare la libreria di Resource Graph per JavaScript ed eseguire la prima query.
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: cdc0589829f250851212ad990dde99eb6dcc958f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251967"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Avvio rapido: Eseguire la prima query di Resource Graph con JavaScript

Questa guida di avvio rapido illustra il processo di aggiunta delle librerie all'installazione di JavaScript. Il primo passaggio per usare Azure Resource Graph prevede l'inizializzazione di un'applicazione JavaScript con le librerie necessarie.

Al termine del processo, le librerie risulteranno aggiunte all'installazione di JavaScript e sarà possibile eseguire la prima query di Resource Graph.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- **Node.js**: è richiesto [Node.js](https://nodejs.org/) versione 12 o successiva.

## <a name="application-setup"></a>Configurazione dell'applicazione

Per consentire a JavaScript di eseguire query su Azure Resource Graph, è necessario configurare l'ambiente. Questa configurazione funziona ovunque sia possibile usare JavaScript, incluso [Bash in Windows 10](/windows/wsl/install-win10).

1. Configurare un nuovo progetto Node.js usando il comando seguente.

   ```bash
   npm init -y
   ```

1. Aggiungere un riferimento al modulo yargs.

   ```bash
   npm install yargs
   ```

1. Aggiungere un riferimento al modulo Azure Resource Graph.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Aggiungere un riferimento alla libreria di autenticazione di Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Verificare che la versione di _package.json_ `@azure/arm-resourcegraph` sia **2.0.0** o successiva e che la versione di `@azure/ms-rest-nodeauth` sia **3.0.3** o successiva.

## <a name="query-the-resource-graph"></a>Eseguire una query su Resource Graph

1. Creare un nuovo file denominato _index.js_ e immettere il codice seguente.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Immettere il comando seguente nel terminale:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Assicurarsi di sostituire il segnaposto `<YOUR_SUBSCRIPTION_ID_LIST>` con l'elenco delimitato da virgole degli ID sottoscrizione di Azure.

   > [!NOTE]
   > Poiché questo esempio di query non prevede un modificatore del tipo di ordinamento, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Impostare il primo parametro su `index.js` e cambiare la query per applicare `order by` alla proprietà **Name**. Sostituire `<YOUR_SUBSCRIPTION_ID_LIST>` con l'ID sottoscrizione:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Quando lo script tenta di eseguire l'autenticazione, nel terminale verrà visualizzato un messaggio simile al seguente:

   > Per accedere, usare un Web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice FGB56WJUGK per eseguire l'autenticazione.

   Dopo l'autenticazione nel browser, l'esecuzione dello script continua.

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Impostare il primo parametro su `index.js` e cambiare la query per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati. Sostituire `<YOUR_SUBSCRIPTION_ID_LIST>` con l'ID sottoscrizione:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere le librerie installate dall'applicazione, eseguire il comando seguente.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido sono state aggiunte le librerie di Resource Graph nell'ambiente JavaScript ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)