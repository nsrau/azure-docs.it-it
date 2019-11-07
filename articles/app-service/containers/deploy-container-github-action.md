---
title: Distribuire il contenitore da una pipeline di integrazione continua/recapito continuo con azioni GitHub-servizio app Azure | Microsoft Docs
description: Informazioni su come usare le azioni di GitHub per distribuire il contenitore nel servizio app
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 7fbd7b571f5590ff35d52062cc621069a47b619c
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620225"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuire un contenitore personalizzato nel servizio app usando le azioni di GitHub

[Azioni di GitHub](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con l' [azione del servizio app Azure per i contenitori](https://github.com/Azure/webapps-container-deploy), è possibile automatizzare il flusso di lavoro per distribuire le app come [contenitori personalizzati nel servizio app](https://azure.microsoft.com/services/app-service/containers/) usando le azioni di GitHub.

> [!IMPORTANT]
> Le azioni di GitHub sono attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando il proprio account github.
> 

Un flusso di lavoro viene definito da un file YAML (. yml) nel percorso `/.github/workflows/` nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del contenitore del servizio app Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**Autenticazione** | 1. definire un'entità servizio. <br /> 2. creare un segreto GitHub. |
|**Build** | 1. configurare l'ambiente. <br /> 2. compilare l'immagine del contenitore. |
|**Distribuire** | 1. distribuire l'immagine del contenitore. |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un' [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando il comando [AZ ad SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/). È possibile eseguire questo comando usando [Azure cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che consentono di accedere all'app del servizio app in modo simile a quanto riportato di seguito. Copiare questo oggetto JSON per l'autenticazione da GitHub.

 ```azurecli 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. È possibile limitare l'ambito nel comando AZ CLI precedente all'app del servizio app specifica e al Container Registry di Azure in cui viene eseguito il push delle immagini del contenitore.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

L'esempio seguente usa le credenziali a livello di utente, ad esempio l'entità servizio di Azure per la distribuzione. Seguire i passaggi per configurare il segreto:

1. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**

2. Incollare il contenuto del comando `az cli` seguente come valore della variabile Secret. Ad esempio, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. A questo punto, nel file del flusso di lavoro nel ramo: `.github/workflows/workflow.yml` sostituire il segreto nell'azione di accesso di Azure con il segreto.

4. Analogamente, definire i seguenti segreti aggiuntivi per le credenziali del registro contenitori e impostarli in azione Docker login. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Vengono visualizzati i segreti come illustrato di seguito, una volta definiti.

    ![segreti del contenitore](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Nell'esempio seguente viene mostrata una parte del flusso di lavoro che compila l'immagine docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Eseguire la distribuzione in un contenitore del servizio app

Per distribuire l'immagine in un contenitore personalizzato nel servizio app, usare l'azione `azure/webapps-container-deploy@v1`. Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **Nome app** | Necessaria Nome dell'app del servizio app | 
| **nome slot** | Opzionale Immettere uno slot esistente diverso dallo slot di produzione |
| **images** | Necessaria Specificare il nome o le immagini del contenitore completo. Ad esempio,' myregistry.azurecr.io/nginx:latest ' o ' Python: 3.7.2-Alpine/'. Per un'app multi-contenitore è possibile specificare più nomi di immagini del contenitore (separati da più righe) |
| **file di configurazione** | Opzionale Percorso del file Docker-compose. Deve essere un percorso completo o relativo alla directory di lavoro predefinita. Obbligatorio per le app a più contenitori. |
| **container-Command** | Opzionale Immettere il comando di avvio. Per es. DotNet Run o dotnet filename. dll |

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app node. js in un contenitore personalizzato nel servizio app.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare il set di azioni raggruppate in repository diversi su GitHub, ognuno dei quali contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Accesso ad Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp per i contenitori](https://github.com/Azure/webapps-container-deploy)

- [Accesso/disconnessione di Docker](https://github.com/Azure/docker-login)

- [Eventi che attivano flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione di K8s](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro CI Starter](https://github.com/actions/starter-workflows)

- [Flussi di lavoro Starter per la distribuzione in Azure](https://github.com/Azure/actions-workflow-samples)
