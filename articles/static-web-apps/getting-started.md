---
title: 'Avvio rapido: Creazione della prima app Web statica con App Web statiche di Azure'
description: Informazioni su come creare un'istanza di App Web statiche di Azure con il framework front-end preferito.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 72a76fb513dc6eb008fcf1d1e19ffc33e713cfdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259253"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Avvio rapido: Creazione della prima app Web statica

App Web statiche di Azure consente di pubblicare siti Web in un ambiente di produzione creando app da un repository GitHub. In questo argomento di avvio rapido si compila un'applicazione Web usando il framework front-end preferito da un repository GitHub.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- Account [Azure](https://portal.azure.com)

## <a name="create-a-repository"></a>Creare un repository

Per semplificare la creazione di un nuovo repository, in questo articolo si usano repository di modelli GitHub. I modelli includono app starter create con framework front-end diversi.

# <a name="angular"></a>[Angular](#tab/angular)

- Assicurarsi di aver effettuato l'accesso a GitHub e passare al percorso seguente per creare un nuovo repository
  - https://github.com/staticwebdev/angular-basic/generate
- Assegnare al repository il nome **my-first-static-web-app**

# <a name="react"></a>[React](#tab/react)

- Assicurarsi di aver effettuato l'accesso a GitHub e passare al percorso seguente per creare un nuovo repository
  - https://github.com/staticwebdev/react-basic/generate
- Assegnare al repository il nome **my-first-static-web-app**

# <a name="vue"></a>[Vue](#tab/vue)

- Assicurarsi di aver effettuato l'accesso a GitHub e passare al percorso seguente per creare un nuovo repository
  - https://github.com/staticwebdev/vue-basic/generate
- Assegnare al repository il nome **my-first-static-web-app**

# <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)

- Assicurarsi di aver effettuato l'accesso a GitHub e passare al percorso seguente per creare un nuovo repository
  - https://github.com/staticwebdev/vanilla-basic/generate
- Assegnare al repository il nome **my-first-static-web-app**

> [!NOTE]
> Con App Web statiche di Azure è necessario almeno un file HTML per creare un'app Web. Il repository creato in questo passaggio include un singolo file _index.html_.

---

Fare clic su **Create repository from template** (Crea repository da modello).

:::image type="content" source="media/getting-started/create-template.png" alt-text="Opzione Create repository from template":::

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

Dopo aver creato il repository, è possibile creare un'app Web statica dal portale di Azure.

- Passare al [portale di Azure](https://portal.azure.com)
- Fare clic su **Crea una risorsa**
- Cercare **App Web statiche**
- Fare clic su **App Web statiche (anteprima)**
- Fare clic su **Crea**

### <a name="basics"></a>Nozioni di base

Per iniziare, configurare la nuova app e collegarla a un repository GitHub.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Scheda Informazioni di base":::

- Selezionare la _sottoscrizione di Azure_
- Selezionare un _gruppo di risorse_ o crearne uno nuovo
- Assegnare all'app il nome **my-first-static-web-app**.
  - I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.
- Selezionare un'_area_ vicina
- Selezionare lo **SKU** _Gratuito_
- Fare clic sul pulsante **Sign-in with GitHub** (Accedi con GitHub) ed eseguire l'autenticazione con GitHub

Dopo aver eseguito l'accesso con GitHub, immettere le informazioni sul repository.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Dettagli del repository":::

- Selezionare l'_organizzazione_ preferita
- Selezionare **my-first-web-static-app** nell'elenco a discesa _Repository_
- Selezionare **master** dall'elenco a discesa _Ramo_
- Fare clic sul pulsante **Avanti: Compilazione >** per modificare la configurazione della build

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Pulsante Avanti > Compilazione":::

### <a name="build"></a>Compilare

Aggiungere quindi i dettagli di configurazione specifici del framework front-end preferito.

# <a name="angular"></a>[Angular](#tab/angular)

- Immettere **/** nella casella _Percorso app_
- Cancellare il valore predefinito nella casella _Percorso_
- Immettere **dist/angular-basic** nella casella _Percorso artefatto app_

# <a name="react"></a>[React](#tab/react)

- Immettere **/** nella casella _Percorso app_
- Cancellare il valore predefinito nella casella _Percorso_
- Immettere **build** nella casella _Percorso artefatto app_

# <a name="vue"></a>[Vue](#tab/vue)

- Immettere **/** nella casella _Percorso app_
- Cancellare il valore predefinito nella casella _Percorso_
- Immettere **dist** nella casella _Percorso artefatto app_

# <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)

- Immettere **/** nella casella _Percorso app_
- Cancellare il valore predefinito nella casella _Percorso_
- Cancellare il valore predefinito dalla casella _Percorso artefatto app_

---

Fare clic sul pulsante **Rivedi e crea**.

:::image type="content" source="media/getting-started/review-create.png" alt-text="Pulsante Rivedi e crea":::

Per modificare questi valori dopo aver creato l'app, è possibile modificare il [file del flusso di lavoro ](github-actions-workflow.md).

### <a name="review--create"></a>Rivedi e crea

Dopo la convalida della richiesta, è possibile continuare a creare l'applicazione.

Fare clic sul pulsante **Create** (Crea)

:::image type="content" source="media/getting-started/create-button.png" alt-text="Pulsante Crea":::

Dopo la creazione della risorsa, fare clic sul pulsante **Vai alla risorsa**

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Pulsante Vai alla risorsa":::

## <a name="view-the-website"></a>Visualizzare il sito Web

La distribuzione di un'app statica è un processo in due passaggi. Il primo richiede il provisioning delle risorse di Azure sottostanti che costituiscono l'app. Il secondo implica l'uso di un flusso di lavoro di GitHub Actions per compilare e pubblicare l'applicazione.

Prima di poter passare al nuovo sito statico, deve terminare l'esecuzione della compilazione di distribuzione.

La finestra della panoramica di App Web statiche visualizza una serie di collegamenti che consentono di interagire con l'app Web.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Finestra della panoramica":::

1. Facendo clic sul banner "Fare clic qui per controllare lo stato dell'azione GitHub", vengono visualizzate le azioni di GitHub Actions in esecuzione nel repository. Dopo aver verificato il completamento del processo di distribuzione, è possibile passare al sito Web tramite l'URL generato.

2. Al completamento del flusso di lavoro di GitHub Actions, è possibile fare clic sul collegamento _URL_ per aprire il sito Web nella nuova scheda.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure seguendo questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com)
1. Cercare **my-first-web-static-app** nella barra di ricerca in alto
1. Fare clic sul nome dell'app
1. Fare clic sul pulsante **Elimina**
1. Fare clic su **Sì** per confermare l'azione di eliminazione

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
