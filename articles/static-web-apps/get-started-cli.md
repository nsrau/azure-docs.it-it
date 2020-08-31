---
title: "Avvio rapido: Creazione della prima app Web statica con App Web statiche di Azure usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare un'istanza di App Web statiche di Azure con l'interfaccia della riga di comando per App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 7e0fdbc50dd36e4ea23903a5929735c1c83bd394
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752871"
---
# <a name="quickstart-building-your-first-static-web-app-using-the-azure-cli"></a>Avvio rapido: Creazione della prima app Web statica con l'interfaccia della riga di comando di Azure

App Web statiche di Azure consente di pubblicare un sito Web in un ambiente di produzione creando app da un repository GitHub. In questa guida di avvio rapido si distribuisce un'applicazione Web in App Web statiche di Azure usando l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- [Token di accesso personale di GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- Account [Azure](https://portal.azure.com)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installata (versione 2.8.0 e successive)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Passare quindi alla nuova cartella usando il comando seguente.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

Dopo aver creato il repository, è possibile creare un'app Web statica dall'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Assicurarsi di trovarsi nella cartella _my-first-static-web-app_ nel terminale.

1. Accedere all'interfaccia della riga di comando di Azure usando il comando seguente.

    ```bash
    az login
    ```

1. Creare una nuova app Web statica dal repository.

    # <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Sostituire questo valore con il nome di un gruppo di risorse di Azure esistente.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Sostituire questo valore con il nome utente GitHub.

    - `<LOCATION>`: Sostituire questo valore con la località più vicina. Le opzioni includono: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ e _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Sostituire questo valore con il [token di accesso personale GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) generato in precedenza.

    È ora possibile visualizzare l'app creata in Azure.

1. Aprire il [portale di Azure](https://portal.azure.com).

1. Cercare **my-first-web-static-app** nella barra di ricerca in alto.

1. Selezionare **my-first-web-static-app**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Pulizia delle risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure eseguendo questo comando:

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
