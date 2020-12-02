---
title: 'Avvio rapido: Usare i segreti di Azure Key Vault nei flussi di lavoro di GitHub Actions'
description: Usare i segreti di Key Vault in GitHub Actions per automatizzare i flussi di lavoro dello sviluppo di software
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920232"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Usare i segreti di Azure Key Vault nei flussi di lavoro di GitHub Actions

Usare i segreti di Key Vault in [GitHub Actions](https://help.github.com/en/articles/about-github-actions) e archiviare in modo sicuro le password e altri segreti in un insieme di credenziali delle chiavi di Azure. Vedere altre informazioni su [Key Vault](../general/overview.md). 

Con Key Vault e GitHub Actions, si ottengono i vantaggi di uno strumento centralizzato per la gestione dei segreti, oltre a tutti i vantaggi di GitHub Actions. GitHub Actions è una suite di funzionalità di GitHub per l'automazione dei flussi di lavoro dello sviluppo di software. È possibile distribuire i flussi di lavoro nella stessa posizione in cui si archivia il codice, oltre a collaborare alle richieste pull e ai problemi. 


## <a name="prerequisites"></a>Prerequisiti 
- Un account GitHub. Se non è disponibile, iscriversi per riceverne uno [gratuito](https://github.com/join).  
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'app Azure connessa a un repository GitHub. Questo esempio si basa sull'articolo [Distribuire contenitori nel servizio app di Azure](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Un insieme di credenziali delle chiavi di Azure.  È possibile creare un'istanza di Azure Key Vault con il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file include due sezioni per l'autenticazione con GitHub Actions:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub. <br /> 3. Aggiungere un'assegnazione di ruolo. |
|**Insieme di credenziali di chiave** | 1. Aggiungere l'azione dell'insieme di credenziali delle chiavi. <br /> 2. Fare riferimento al segreto dell'insieme di credenziali delle chiavi. |

## <a name="authentication"></a>Authentication

È possibile creare un'[entità servizio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

Nell'esempio precedente sostituire i segnaposto con l'ID sottoscrizione e il nome del gruppo di risorse. Sostituire il segnaposto `myApp` con il nome dell'applicazione. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso all'app del servizio app simile a questo esempio. Copiare l'oggetto JSON per un uso successivo. Saranno necessarie solo le sezioni con i valori `clientId`, `clientSecret`, `subscriptionId` e `tenantId`. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

Creare segreti per le credenziali di Azure, il gruppo di risorse e le sottoscrizioni. 

1. In [GitHub](https://github.com/) esplorare il repository.

1. Selezionare **Settings > Secrets > New secret** (Impostazioni > Segreti > Nuovo segreto).

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

1. Copiare il valore di `clientId` per usarlo in seguito. 

### <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo 
 
È necessario concedere l'accesso all'entità servizio di Azure, in modo da poter accedere all'insieme di credenziali delle chiavi per le operazioni `get` e `list`. Se non si concede l'accesso, non sarà possibile usare l'entità servizio. 

Sostituire `keyVaultName` con il nome dell'insieme di credenziali delle chiavi e `clientIdGUID` con il valore di `clientId`. 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Usare l'azione di Azure Key Vault

Con l'[azione di Azure Key Vault](https://github.com/marketplace/actions/azure-key-vault-get-secrets) è possibile recuperare uno o più segreti da un'istanza di Azure Key Vault e utilizzarli nei flussi di lavoro di GitHub Actions.

I segreti recuperati vengono impostati come output e anche come variabili di ambiente. Le variabili vengono automaticamente mascherate quando vengono stampate nella console o nei log.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Per usare un insieme di credenziali delle chiavi nel flusso di lavoro, è necessario avere l'azione di Key Vault e farvi riferimento. 

In questo esempio l'insieme di credenziali delle chiavi è denominato `containervault`. Con l'azione di Key Vault vengono aggiunti all'ambiente due segreti dell'insieme di credenziali delle chiavi, ovvero `containerPassword` e `containerUsername`. 

Ai valori dell'insieme di credenziali delle chiavi viene in seguito fatto riferimento nell'attività di accesso Docker con il prefisso `steps.myGetSecretAction.outputs`. 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando l'app Azure, il repository GitHub e l'insieme di credenziali delle chiavi non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse per l'app, il repository GitHub e l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su Azure Key Vault](../general/overview.md)
