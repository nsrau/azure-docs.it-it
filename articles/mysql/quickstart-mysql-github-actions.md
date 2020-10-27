---
title: 'Avvio rapido: Connettersi ad Azure MySQL con GitHub Actions'
description: Usare Azure MySQL da un flusso di lavoro GitHub Actions
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 7b4620c739b2f94cb6b96743280cd1decbbb746e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326119"
---
# <a name="use-github-actions-to-connect-to-azure-mysql"></a>Usare GitHub Actions per connettersi ad Azure MySQL

Usare un flusso di lavoro [GitHub Actions](https://docs.github.com/en/actions) per distribuire aggiornamenti di database a [Database di Azure per MySQL](https://azure.microsoft.com/services/mysql/). 

## <a name="prerequisites"></a>Prerequisiti

Prerequisiti: 
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un repository GitHub con i dati di esempio (`data.sql`). Se non si ha un account GitHub, è possibile [iscriversi gratuitamente](https://github.com/join).  
- Un'istanza di Database di Azure per il server MySQL.
    - [Avvio rapido: Creare un server di Database di Azure per MySQL nel portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro GitHub Actions viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file è costituito da due sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub. |
|**Distribuzione** | 1. Distribuire il database. |

## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova** .

Sostituire i segnaposto `server-name` con il nome del server MySQL ospitato in Azure. Sostituire `subscription-id` e `resource-group` con l'ID sottoscrizione e il gruppo di risorse connesso al server MySQL.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso al database in modo simile a questo esempio. Copiare l'oggetto JSON di output per un uso successivo.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. L'ambito dell'esempio precedente è limitato al server specifico e non include l'intero gruppo di risorse.

## <a name="copy-the-mysql-connection-string"></a>Copiare la stringa di connessione MySQL 

Nel portale di Azure passare a Database di Azure per MySQL e aprire **Impostazioni** > **Stringhe di connessione** . Copiare la stringa di connessione per **ADO.NET** . Sostituire i valori segnaposto `your_database` e `your_password`. La stringa di connessione avrà un aspetto simile a questo. 

```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
```
La stringa di connessione verrà usata come segreto GitHub. 

## <a name="configure-the-github-secrets"></a>Configurare i segreti GitHub

1. In [GitHub](https://github.com/) esplorare il repository.

1. Selezionare **Settings > Secrets > New secret** (Impostazioni > Segreti > Nuovo segreto).

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

    Quando in seguito si configura il file del flusso di lavoro, si usa il segreto come `creds` di input dell'azione di accesso di Azure. Ad esempio:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Selezionare di nuovo **New secret** (Nuovo segreto). 

1. Incollare il valore della stringa di connessione nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_MYSQL_CONNECTION_STRING`.


## <a name="add-your-workflow"></a>Aggiungere il flusso di lavoro

1. Passare ad **Actions** per il repository GitHub. 

2. Selezionare **Set up your workflow yourself** (Configurare manualmente il flusso di lavoro). 

2. Eliminare tutti quello che segue la sezione `on:` del file del flusso di lavoro. Il flusso di lavoro rimanente, ad esempio, potrà essere simile al seguente. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Rinominare il flusso di lavoro `MySQL for GitHub Actions` e aggiungere le azioni checkout e login. Queste azioni eseguiranno il checkout del codice del sito e l'autenticazione con Azure usando il segreto GitHub `AZURE_CREDENTIALS` creato in precedenza. 

    ```yaml
    name: MySQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Usare l'azione di distribuzione di Azure MySQL per connettersi all'istanza di MySQL. Sostituire `MYSQL_SERVER_NAME` con il nome del server. È necessario avere un file di dati MySQL denominato `data.sql` al livello radice del repository. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Completare il flusso di lavoro aggiungendo un'azione di disconnessione da Azure. Ecco il flusso di lavoro completato. Il file verrà visualizzato nella cartella `.github/workflows` del repository.

    ```yaml
   name: MySQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Esaminare la distribuzione

1. Passare ad **Actions** per il repository GitHub. 

1. Aprire il primo risultato per visualizzare i log dettagliati dell'esecuzione del flusso di lavoro. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Log di esecuzione di GitHub Actions":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il database MySQL di Azure e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'integrazione di Azure e GitHub](https://docs.microsoft.com/azure/developer/github/)
