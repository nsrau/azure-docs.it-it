---
title: "Esercitazione: Creazione di un'app Web statica con Blazor in App Web statiche di Azure"
description: Informazioni su come creare un sito Web di App Web statiche di Azure con Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 60e62228e33d2d86bb407e45802f5c0621a94049
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761091"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Esercitazione: Creazione di un'app Web statica con Blazor in App Web statiche di Azure

App Web statiche di Azure consente di pubblicare un sito Web in un ambiente di produzione creando app da un repository GitHub. In questa esercitazione si distribuisce un'applicazione Web in App Web statiche di Azure usando il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- Account [Azure](https://portal.azure.com)

## <a name="application-overview"></a>Panoramica dell'applicazione

App Web statiche di Azure consente di creare applicazioni Web statiche supportate da un back-end serverless. L'esercitazione seguente illustra come distribuire un'applicazione Web Blazor in C# che restituisce dati meteorologici.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="App Blazor completa":::

L'app descritta in questa esercitazione è costituita da tre diversi progetti di Visual Studio:

- **API**: l'applicazione di Funzioni di Azure in C# che implementa l'endpoint API che fornisce informazioni meteorologiche all'app statica. [`WeatherForecastFunction`](https://github.com/ssdeepak/blazor/blob/main/Api/WeatherForecastFunction.cs) restituisce una matrice di oggetti `WeatherForecast`.

- **Client**: il progetto di assembly Web Blazor front-end. Viene implementata una [route di fallback](#fallback-route) per assicurare che a tutte le route venga fornito il file _index.html_.

- **Condiviso**: include classi comuni a cui fanno riferimento i progetti API e Client, consentendo il flusso dei dati dall'endpoint API all'app Web front-end. La classe [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) è condivisa da entrambe le app.

Nell'insieme, questi progetti costituiscono le parti necessarie per creare un'applicazione di assembly Web Blazor in esecuzione nel browser supportata da un back-end API.

## <a name="fallback-route"></a>Route di fallback

L'applicazione espone URL come _/counter_ e _/fetchdata_ mappati a route specifiche dell'applicazione. Poiché questa app viene implementata come applicazione a pagina singola, a ogni route viene fornito il file _index.html_. Per assicurare che la richiesta di qualsiasi percorso restituisca _index.html_, viene implementata una [route di fallback](./routes.md#fallback-routes) nel file _routes.json_ disponibile nella cartella _wwwroot_ del progetto Client.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

La configurazione precedente garantisce che le richieste inviate a qualsiasi route nell'app restituiscano la pagina _index.html_.

## <a name="create-a-repository"></a>Creare un repository

In questo articolo si usa un repository di modelli GitHub per semplificare le operazioni. Il modello include un'app di avvio distribuita in App Web statiche di Azure.

1. Assicurarsi di aver effettuato l'accesso a GitHub e passare alla posizione seguente per creare un nuovo repository:
    - https://github.com/staticwebdev/blazor-starter/generate
1. Assegnare al repository il nome **my-first-static-blazor-app**

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

Dopo aver creato il repository, è possibile creare un'app Web statica dal portale di Azure.

1. Passare al [portale di Azure](https://portal.azure.com)
1. Selezionare **Crea una risorsa**
1. Cercare **App Web statiche**
1. Selezionare **App Web statiche (anteprima)**
1. Selezionare **Crea**

Per iniziare, nella sezione _Informazioni di base_ configurare la nuova app e collegarla a un repository GitHub.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="App Blazor completa":::

1. Selezionare la _sottoscrizione di Azure_
1. Selezionare un _gruppo di risorse_ o crearne uno nuovo
1. Assegnare all'app il nome **my-first-static-blazor-app**
    - I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.
1. Selezionare un'_area_ vicina
1. Selezionare lo **SKU** _Gratuito_
1. Selezionare il pulsante **Accedi con GitHub** ed eseguire l'autenticazione con GitHub

Dopo aver eseguito l'accesso con GitHub, immettere le informazioni sul repository.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="App Blazor completa":::

1. Selezionare l'_organizzazione_ preferita
1. Selezionare **my-first-static-blazor-app** nell'elenco a discesa _Repository_
1. Selezionare **main** nell'elenco a discesa _Ramo_

    Se non viene visualizzato alcun repository, potrebbe essere necessario autorizzare App Web statiche di Azure in GitHub. Passare al repository GitHub e quindi a **Impostazioni > Applicazioni > App OAuth autorizzate**, selezionare **App Web statiche di Azure** e infine **Concedi**. Per concedere le autorizzazioni per i repository dell'organizzazione, è necessario essere un proprietario dell'organizzazione.

1. Nella sezione _Dettagli compilazione_ aggiungere i dettagli di configurazione specifici di Blazor.

    - Selezionare **Blazor** nell'elenco a discesa _Set di impostazioni di compilazione_ e mantenere tutti i valori predefiniti.

1. Selezionare **Rivedi e crea**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="App Blazor completa":::

1. Selezionare **Crea**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="App Blazor completa":::

1. Selezionare **Vai alla risorsa**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="App Blazor completa":::

## <a name="view-the-website"></a>Visualizzare il sito Web

La distribuzione di un'app statica è un processo in due passaggi. Il primo richiede il provisioning delle risorse di Azure sottostanti che costituiscono l'app. Il secondo implica l'uso di un flusso di lavoro di GitHub Actions per compilare e pubblicare l'applicazione.

Prima di poter passare al nuovo sito statico, deve terminare l'esecuzione della compilazione di distribuzione.

La finestra della panoramica di App Web statiche visualizza una serie di collegamenti che consentono di interagire con l'app Web.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="App Blazor completa":::

1. Facendo clic sul banner _Click here to check the status of your GitHub Actions runs_ (Fare clic qui per controllare lo stato delle esecuzioni di GitHub Actions), vengono visualizzate le esecuzioni di GitHub Actions nel repository. Dopo aver verificato il completamento del processo di distribuzione, è possibile passare al sito Web tramite l'URL generato.

2. Una volta completato il flusso di lavoro di GitHub Actions, è possibile selezionare il collegamento _URL_ per aprire il sito Web nella nuova scheda.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure seguendo questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com)
1. Cercare **my-first-static-blazor-app** nella barra di ricerca superiore
1. Selezionare il nome dell'app
1. Selezionare il pulsante **Elimina**
1. Selezionare **Sì** per confermare l'azione di eliminazione

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Autenticazione e autorizzazione](./authentication-authorization.md)
