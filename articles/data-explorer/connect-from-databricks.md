---
title: Connettersi a Esplora dati di Azure da Azure Databricks tramite Python
description: Questo argomento illustra come usare la libreria Python in Azure Databricks per accedere ai dati da Esplora dati di Azure usando uno di due metodi di autenticazione.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52429926"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Connettersi a Esplora dati di Azure da Azure Databricks tramite Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure. Questo articolo illustra come usare la libreria Python in Azure Databricks per accedere ai dati da Esplora dati di Azure. Esistono diversi modi per eseguire l'autenticazione in Esplora dati di Azure, tra cui l'accesso al dispositivo e l'app Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Prerequisiti

- [Creare un database e un cluster di Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal)
- [Creare un'area di lavoro di Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    In **Servizio Azure Databricks**, nell'elenco a discesa **Piano tariffario** selezionare **Premium**. In questo modo è possibile usare i segreti di Azure Databricks per archiviare le credenziali e farvi riferimento in notebook e processi.

- [Creare un cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) in Azure Databricks con le specifiche seguenti (impostazioni minime necessarie per eseguire i notebook di esempio):

![Creare cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Installare la libreria Python nel cluster Azure Databricks

Per installare la [libreria Python](/azure/kusto/api/python/kusto-python-client-library) nel cluster Azure Databricks:

1. Passare all'area di lavoro Azure Databricks e [creare una libreria](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Caricare un pacchetto Python PyPI o Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Caricare, installare e collegare la libreria al cluster Databricks.
    - Immettere il nome PyPi: *azure-kusto-data*

## <a name="connect-to-adx-using-device-login"></a>Connettersi a Esplora dati di Azure usando l'accesso del dispositivo

[Importare un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) usando il notebook [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) per connettersi a Esplora dati di Azure con le credenziali personali.

## <a name="connect-to-adx-using-azure-ad-app"></a>Connettersi a Esplora dati di Azure tramite l'app Azure AD

1. Creare l'app Azure AD effettuando il [provisioning di un'applicazione AAD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Concedere l'accesso all'app Azure AD nel database di Esplora dati di Azure come indicato di seguito:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | nome del database |
    | ```AAD App ID``` | ID dell'app Azure AD |
    | ```AAD Tenant ID``` | ID del tenant di Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Trovare l'ID del tenant di Azure AD

Per autenticare un'applicazione, Esplora dati di Azure usa l'ID del tenant di Azure AD. Per trovare l'ID del tenant, usare l'URL seguente, sostituendo il dominio a *YourDomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ad esempio, se il dominio è *contoso.com*, l'URL è: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Fare clic su questo URL per visualizzare i risultati; la prima riga è come indicato di seguito. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

L'ID del tenant è `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Archiviare e proteggere l'ID e la chiave dell'app Azure AD 

Archiviare e proteggere l'ID e la chiave dell'app Azure AD usando i [segreti](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) di Azure Databricks come indicato di seguito:
1. [Configurare l'interfaccia della riga di comando](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [Installare l'interfaccia della riga di comando](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [Configurare l'autenticazione](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurare i [segreti](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) usando i comandi di esempio seguenti:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importare un notebook
[Importare un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) usando il notebook [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) per connettersi a Esplora dati di Azure. Aggiornare i valori segnaposto con il nome del cluster, il nome del database e l'ID del tenant di Azure AD.