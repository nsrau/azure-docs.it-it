---
title: Distribuire il contenitore da una pipeline di integrazione continua/recapito continuo con azioni GitHub-servizio app Azure | Microsoft Docs
description: Informazioni su come usare le azioni di GitHub per distribuire il contenitore nel servizio app
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809812"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>Azioni di GitHub per la distribuzione in app Web per contenitori

[Azioni di GitHub](https://help.github.com/en/articles/about-github-actions) offre la flessibilità necessaria per creare un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con le azioni di servizio app Azure per GitHub, è possibile automatizzare il flusso di lavoro per distribuire [app Web di Azure per contenitori](https://azure.microsoft.com/services/app-service/containers/) usando le azioni di GitHub.

> [!IMPORTANT]
> Le azioni di GitHub sono attualmente in versione beta. È prima di tutto necessario [iscriversi per partecipare all'anteprima](https://github.com/features/actions) usando il proprio account github.
> 

Un flusso di lavoro viene definito da un file YAML (. yml) nel percorso `/.github/workflows/` nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro.

Per un flusso di lavoro del contenitore di app Web di Azure, il file è costituito da tre sezioni:

|Sezione  |Attività  |
|---------|---------|
|**Autenticazione** | 1. definire un'entità servizio <br /> 2. creare un segreto GitHub |
|**Build** | 1. configurare l'ambiente <br /> 2. compilare l'immagine del contenitore |
|**Distribuzione** | 1. distribuire l'immagine del contenitore |

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un' [entità servizio](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) usando il comando [AZ ad SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/). È possibile eseguire questo comando usando [Azure cloud Shell](https://shell.azure.com/) nel portale di Azure o selezionando il pulsante **prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

In questo esempio, sostituire i segnaposto nella risorsa con l'ID sottoscrizione, il gruppo di risorse e il nome dell'app Web. L'output corrisponde alle credenziali di assegnazione di ruolo che consentono di accedere all'app Web. Copiare questo oggetto JSON, che è possibile usare per eseguire l'autenticazione da GitHub.

> [!NOTE]
> Non è necessario creare un'entità servizio se si decide di usare il profilo di pubblicazione per l'autenticazione.

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. Questo è il motivo per cui l'ambito nell'esempio precedente è limitato all'app Web specifica e non all'intero gruppo di risorse.

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

L'esempio seguente usa le credenziali a livello di utente, ad esempio l'entità servizio di Azure per la distribuzione. Seguire i passaggi per configurare il segreto:

1. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto**

2. Incollare il contenuto del comando `az cli` seguente come valore della variabile Secret. Ad esempio `AZURE_CREDENTIALS`.

    
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

5. I segreti vengono visualizzati come mostrato di seguito, una volta definiti.

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
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Distribuisci nel contenitore dell'app Web

Per distribuire l'immagine in un contenitore di app Web, sarà necessario usare l'azione `Azure/appservice-actions/webapp@master`. Questa azione ha 5 parametri:

| **Parametro**  | **Spiegazione**  |
|---------|---------|
| **Nome app** | Necessaria Nome dell'app Web di Azure | 
| **nome slot** | Opzionale Immettere uno slot esistente diverso dallo slot di produzione |
| **images** | Necessaria Specificare il nome o le immagini del contenitore completo. Ad esempio,' myregistry.azurecr.io/nginx:latest ' o ' Python: 3.7.2-Alpine/'. Per scenari a più contenitori è possibile specificare più nomi di immagini del contenitore (separati da più righe) |
| **file di configurazione** | Opzionale Percorso del file Docker-compose. Deve essere un percorso completo o relativo alla directory di lavoro predefinita. Necessaria per uno scenario con più contenitori |
| **container-Command** | Opzionale Immettere il comando di avvio. Per es. DotNet Run o dotnet filename. dll |

Di seguito è riportato il flusso di lavoro di esempio per compilare e distribuire un'app Web Node. js nel contenitore di app Web di Azure.

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
      uses: azure/actions/login@v1
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

- [Accesso ad Azure](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp per i contenitori](https://github.com/Azure/webapps-container-deploy)

- [Accesso/disconnessione di Docker](https://github.com/Azure/docker-login)

- [Eventi che attivano flussi di lavoro](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Distribuzione di K8s](https://github.com/Azure/k8s-deploy)

- [Flussi di lavoro Starter](https://github.com/actions/starter-workflows)
