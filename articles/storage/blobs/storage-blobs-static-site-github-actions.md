---
title: Usare GitHub Actions per distribuire un sito statico in Archiviazione di Azure
description: Hosting di siti web statici di archiviazione di Azure con azioni GitHub
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 799c0dbc3ddb673cafe0fe4962f7346a841bd4b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543103"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Configurare un flusso di lavoro di azioni GitHub per distribuire il sito Web statico in archiviazione di Azure

Iniziare a usare le [azioni di GitHub](https://docs.github.com/en/actions) usando un flusso di lavoro per distribuire un sito statico in un account di archiviazione di Azure. Dopo aver configurato un flusso di lavoro di azioni di GitHub, sarà possibile distribuire automaticamente il sito in Azure da GitHub quando si apportano modifiche al codice del sito.

> [!NOTE]
> Se si usano [app Web statiche di Azure](../../static-web-apps/index.yml), non è necessario configurare manualmente un flusso di lavoro di azioni di GitHub.
> App Web statiche di Azure crea automaticamente un flusso di lavoro di azioni GitHub. 

## <a name="prerequisites"></a>Prerequisiti

Una sottoscrizione di Azure e un account GitHub. 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un repository GitHub con il codice del sito Web statico. Se non si ha un account GitHub, è possibile [iscriversi gratuitamente](https://github.com/join).  
- Un sito Web statico funzionante ospitato in archiviazione di Azure. Informazioni su come [ospitare un sito Web statico in archiviazione di Azure](storage-blob-static-website-how-to.md). Per seguire questo esempio, è necessario distribuire anche la rete [CDN di Azure](static-website-content-delivery-network.md).

> [!NOTE]
> È comune usare una rete per la distribuzione di contenuti (CDN) per ridurre la latenza agli utenti in tutto il mondo e per ridurre il numero di transazioni per l'account di archiviazione. La distribuzione di contenuto statico in un servizio di archiviazione basato su cloud può ridurre la necessità di un'istanza di calcolo potenzialmente costosa. Per altre informazioni, vedere [modello di hosting del contenuto statico](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

È possibile creare un'[entità servizio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

Sostituire il segnaposto `myStaticSite` con il nome del sito ospitato in archiviazione di Azure. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

Nell'esempio precedente sostituire i segnaposto con l'ID sottoscrizione e il nome del gruppo di risorse. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'account di archiviazione simile a quello riportato di seguito. Copiare questo oggetto JSON per un momento successivo.

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
> È sempre consigliabile concedere l'accesso minimo. L'ambito nell'esempio precedente è limitato all'app del servizio app specifica e non all'intero gruppo di risorse.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

1. In [GitHub](https://github.com/) esplorare il repository.

1. Selezionare **Settings > Secrets > New secret** (Impostazioni > Segreti > Nuovo segreto).

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto un nome come `AZURE_CREDENTIALS` .

    Quando in seguito si configura il file del flusso di lavoro, si usa il segreto come `creds` di input dell'azione di accesso di Azure. Ad esempio:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Aggiungere il flusso di lavoro

1. Passare ad **Actions** per il repository GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Voce di menu azioni di GitHub":::

1. Selezionare **Set up your workflow yourself** (Configurare manualmente il flusso di lavoro). 

1. Eliminare tutti quello che segue la sezione `on:` del file del flusso di lavoro. Il flusso di lavoro rimanente, ad esempio, potrà essere simile al seguente. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Rinominare il flusso di lavoro `Blob storage website CI` e aggiungere le azioni checkout e login. Queste azioni eseguiranno il checkout del codice del sito e l'autenticazione con Azure usando il segreto GitHub `AZURE_CREDENTIALS` creato in precedenza. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Usare l'azione dell'interfaccia della riga di comando di Azure per caricare il codice nell'archivio BLOB ed eliminare l'endpoint della rete CDN. Per `az storage blob upload-batch` , sostituire il segnaposto con il nome dell'account di archiviazione. Lo script viene caricato nel `$web` contenitore. Per `az cdn endpoint purge` , sostituire i segnaposto con il nome del profilo della rete CDN, il nome dell'endpoint della rete CDN e il gruppo di risorse.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Completare il flusso di lavoro aggiungendo un'azione di disconnessione da Azure. Ecco il flusso di lavoro completato. Il file verrà visualizzato nella cartella `.github/workflows` del repository.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Esaminare la distribuzione

1. Passare ad **Actions** per il repository GitHub. 

1. Aprire il primo risultato per visualizzare i log dettagliati dell'esecuzione del flusso di lavoro. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Log di esecuzione di GitHub Actions":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il sito Web statico e il repository GitHub non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle app Web statiche di Azure](../../static-web-apps/index.yml)