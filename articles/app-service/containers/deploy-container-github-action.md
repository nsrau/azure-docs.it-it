---
title: Integrazione continua/distribuzione continua del contenitore personalizzato dalle azioni di GitHub
description: Informazioni su come usare le azioni di GitHub per distribuire il contenitore Linux personalizzato al servizio app da una pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78246978"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Distribuire un contenitore personalizzato nel servizio app usando le azioni di GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con l' [azione del servizio app Azure per i contenitori](https://github.com/Azure/webapps-container-deploy), è possibile automatizzare il flusso di lavoro per distribuire le app come [contenitori personalizzati nel servizio app](https://azure.microsoft.com/services/app-service/containers/) usando le azioni di GitHub.

> [!IMPORTANT]
> GitHub Actions è attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando l'account GitHub.
> 

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del contenitore del servizio app Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. definire un'entità servizio. <br /> 2. creare un segreto GitHub. |
|**Build** | 1. configurare l'ambiente. <br /> 2. compilare l'immagine del contenitore. |
|**Distribuzione** | 1. distribuire l'immagine del contenitore. |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'[entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando il comando [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/). È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che consentono di accedere all'app del servizio app in modo simile a quanto riportato di seguito. Copiare questo oggetto JSON per l'autenticazione da GitHub.

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
> È sempre consigliabile concedere l'accesso minimo. È possibile limitare l'ambito nel comando AZ CLI precedente all'app del servizio app specifica e al Container Registry di Azure in cui viene eseguito il push delle immagini del contenitore.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

L'esempio seguente usa le credenziali a livello di utente, ad esempio l'entità servizio di Azure per la distribuzione. Per configurare il segreto seguire questa procedura:

1. In [GitHub](https://github.com/) esplorare il repository e selezionare **Settings > Secrets > Add a new secret** (Impostazioni > Segreti > Aggiungi un nuovo segreto).

2. Incollare il contenuto del comando seguente `az cli` come valore della variabile Secret. Ad esempio: `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. A questo punto, nel file del flusso di lavoro nel ramo, `.github/workflows/workflow.yml` sostituire il segreto nell'azione di accesso di Azure con il segreto.

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
    - name: 'Checkout GitHub Action' 
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

Per distribuire l'immagine in un contenitore personalizzato nel servizio app, usare l' `azure/webapps-container-deploy@v1` azione. Questa azione ha cinque parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **app-name** | (Obbligatorio) Nome dell'app del servizio app | 
| **slot-name** | (Facoltativo) Immettere uno slot esistente diverso dallo slot di produzione |
| **images** | Necessaria Specificare il nome o le immagini del contenitore completo. Ad esempio,' myregistry.azurecr.io/nginx:latest ' o ' Python: 3.7.2-Alpine/'. Per un'app multi-contenitore è possibile specificare più nomi di immagini del contenitore (separati da più righe) |
| **file di configurazione** | Opzionale Percorso del file Docker-compose. Deve essere un percorso completo o relativo alla directory di lavoro predefinita. Obbligatorio per le app a più contenitori. |
| **container-Command** | Opzionale Immettere il comando di avvio. Per es. esecuzione DotNet o DotNet filename.dll |

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app Node.js in un contenitore personalizzato nel servizio app.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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

Il set di azioni raggruppate in repository diversi è disponibile in GitHub. Ogni repository contiene documentazione ed esempi che consentono di usare GitHub per CI/CD e distribuire le app in Azure.

- [Accesso ad Azure](https://github.com/Azure/login)

- [App Web di Azure](https://github.com/Azure/webapps-deploy)

- [App Web per contenitori di Azure](https://github.com/Azure/webapps-container-deploy)

- [Docker login/logout](https://github.com/Azure/docker-login)

- [Eventi che attivano i flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione Kubernetes](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro CI Starter](https://github.com/actions/starter-workflows)

- [Flussi di lavoro Starter per la distribuzione in Azure](https://github.com/Azure/actions-workflow-samples)
