---
title: Configurare le impostazioni dell'applicazione per App Web statiche di Azure
description: Informazioni su come configurare le impostazioni dell'applicazione per App Web statiche di Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-javascript
ms.openlocfilehash: 33a40e7a0e7e3be4433b3cbd4ba7a8e09a84e689
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428051"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Configurare le impostazioni dell'applicazione per App Web statiche di Azure (anteprima)

Le impostazioni dell'applicazione contengono le impostazioni di configurazione per i valori che possono cambiare, ad esempio le stringhe di connessione del database. L'aggiunta delle impostazioni dell'applicazione consente di modificare l'input di configurazione per l'app, senza dover modificare il codice dell'applicazione.

Le impostazioni dell'applicazione sono:

- Crittografia dei dati inattivi
- Copia negli ambienti di [staging](review-publish-pull-requests.md) e produzione

Le impostazioni dell'applicazione vengono anche talvolta definite variabili di ambiente.

> [!IMPORTANT]
> Le impostazioni dell'applicazione descritte in questo articolo si applicano solo all'API back-end di un'app Web statica di Azure.
>
> Per informazioni sull'uso delle variabili di ambiente con l'applicazione Web front-end, vedere la documentazione per il [framework JavaScript](#javascript-frameworks-and-libraries) o il [generatore di siti statici](#static-site-generators).

## <a name="prerequisites"></a>Prerequisiti

- Applicazione App Web statiche di Azure
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="types-of-application-settings"></a>Tipi di impostazioni dell'applicazione

Esistono in genere due aspetti di un'applicazione App Web statiche di Azure. Il primo è l'applicazione Web, o contenuto statico, rappresentato da codice HTML, CSS, JavaScript e immagini. Il secondo è l'API back-end, basata su un'applicazione Funzioni di Azure.

Questo articolo illustra come gestire le impostazioni dell'applicazione per l'API back-end in Funzioni di Azure.

Le impostazioni dell'applicazione descritte in questo articolo non possono essere usate nelle applicazioni Web statiche e non è possibile farvi riferimento. Tuttavia, molti framework front-end e generatori di siti statici consentono di usare le variabili di ambiente durante lo sviluppo. In fase di compilazione queste variabili vengono sostituite dai relativi valori nel codice HTML o JavaScript generato. Poiché i dati in HTML e JavaScript sono facilmente individuabili dal visitatore del sito, è consigliabile evitare di includere informazioni sensibili nell'applicazione front-end. È invece opportuno collocare le impostazioni che contengono dati sensibili nella parte relativa all'API dell'applicazione.

Per informazioni dettagliate su come usare le variabili di ambiente con il framework o la libreria JavaScript, vedere gli articoli seguenti.

### <a name="javascript-frameworks-and-libraries"></a>Framework e librerie JavaScript

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Generatori di siti statici

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Informazioni sulle impostazioni dell'app per le API

Le API in App Web statiche di Azure sono basate su Funzioni di Azure, che consente di definire le impostazioni dell'applicazione nel file _local.settings.json_. Questo file definisce le impostazioni dell'applicazione nella proprietà `Values` della configurazione.

Il file _local.settings.json_ di esempio seguente mostra come aggiungere un valore per la stringa di connessione del database in `DATABASE_CONNECTION_STRING`.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

È possibile fare riferimento alle impostazioni definite nella proprietà `Values` dal codice come variabili di ambiente, disponibili dall'oggetto `process.env`.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

Il file `local.settings.json` non viene monitorato dal repository GitHub perché le informazioni sensibili, ad esempio le stringhe di connessione del database, sono spesso incluse nel file. Poiché le impostazioni locali rimangono nel computer, è necessario caricare manualmente le impostazioni in Azure.

In genere, il caricamento delle impostazioni viene eseguito raramente e non è obbligatorio con ogni compilazione.

## <a name="uploading-application-settings"></a>Caricamento delle impostazioni dell'applicazione

È possibile configurare le impostazioni dell'applicazione tramite il portale di Azure o con l'interfaccia della riga di comando di Azure.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Il portale di Azure fornisce un'interfaccia per la creazione, l'aggiornamento e l'eliminazione delle impostazioni dell'applicazione.

1. Passare al [portale di Azure](https://portal.azure.com).

1. Nella barra di ricerca cercare e selezionare **App Web statiche**.

1. Fare clic sull'opzione **Configurazione** nella barra laterale.

1. Selezionare l'ambiente a cui applicare le impostazioni dell'applicazione. Gli ambienti di staging vengono creati automaticamente quando viene generata una richiesta pull e ne viene alzato il livello in produzione dopo l'unione della richiesta pull. È possibile definire le impostazioni dell'applicazione per ogni ambiente.

1. Fare clic sul pulsante **Aggiungi** per aggiungere una nuova impostazione dell'app.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Visualizzazione di configurazione delle app Web statiche di Azure":::

1. Immettere un **nome** e un **valore**.

1. Fare clic su **OK**.

1. Fare clic su **Salva**.

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

È possibile usare il comando `az rest` per eseguire caricamenti in blocco delle impostazioni in Azure. Il comando accetta le impostazioni dell'applicazione come oggetti JSON in una proprietà padre denominata `properties`.

Il modo più semplice per creare un file JSON con i valori appropriati consiste nel creare una versione modificata del file _local.settings.json_.

1. Per assicurarsi che il nuovo file con dati sensibili non venga esposto pubblicamente, aggiungere la voce seguente nel file _.gitignore_.

   ```bash
   local.settings*.json
   ```

2. Successivamente, creare una copia del file _local.settings.json_ e assegnargli il nome _local.settings.properties.json_.

3. All'interno del nuovo file rimuovere tutti gli altri dati dal file ad eccezione delle impostazioni dell'applicazione e sostituire il nome `Values` con `properties`.

   Il file dovrebbe avere un aspetto simile all'esempio seguente:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Il comando dell'interfaccia della riga di comando di Azure richiede diversi valori specifici dell'account per l'esecuzione del caricamento. Dalla finestra _Panoramica_ della risorsa App Web statiche, è possibile accedere alle informazioni seguenti:

1. Nome del sito statico
2. Nome del gruppo di risorse
3. ID sottoscrizione

:::image type="content" source="media/application-settings/overview.png" alt-text="Panoramica di App Web statiche di Azure":::

4. Da un terminale o da una riga di comando, eseguire il comando seguente. Assicurarsi di sostituire i segnaposto di `<YOUR_STATIC_SITE_NAME>`, `<YOUR_RESOURCE_GROUP_NAME>`e `<YOUR_SUBSCRIPTION_ID>` con i valori della finestra _Panoramica_.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> Il file "local.settings.properties.json" deve trovarsi nella stessa directory in cui viene eseguito il comando. È possibile assegnare al file il nome desiderato, in quanto non è significativo.

### <a name="view-application-settings-with-the-azure-cli"></a>Visualizzare le impostazioni dell'applicazione con l'interfaccia della riga di comando di Azure

È possibile visualizzare le impostazioni dell'applicazione tramite l'interfaccia della riga di comando di Azure.

- Da un terminale o da una riga di comando, eseguire il comando seguente. Assicurarsi di sostituire i segnaposto `<YOUR_SUBSCRIPTION_ID>`, `<YOUR_RESOURCE_GROUP_NAME>` e `<YOUR_STATIC_SITE_NAME>` con i valori effettivi.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare lo sviluppo locale](local-development.md)
