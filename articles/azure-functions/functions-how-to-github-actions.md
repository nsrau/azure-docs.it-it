---
title: Usare le azioni GitHub per eseguire aggiornamenti del codice in Funzioni di AzureUse GitHub Actions to make code updates in Azure Functions
description: Informazioni su come usare le azioni di GitHub per definire un flusso di lavoro per compilare e distribuire progetti di Funzioni di Azure in GitHub.Learn how to use GitHub Actions to define a workflow to build and deploy Azure Functions projects in GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878205"
---
# <a name="continuous-delivery-by-using-github-action"></a>Distribuzione continua tramite GitHub Action

[GitHub Actions](https://github.com/features/actions) consente di definire un flusso di lavoro per compilare e distribuire automaticamente il codice delle funzioni per funzionare app in Azure.GitHub Actions lets you define a workflow to automatically build and deploy your functions code to function app in Azure. 

In GitHub Actions, un [flusso di lavoro](https://help.github.com/articles/about-github-actions#workflow) è un processo automatizzato definito nel repository GitHub.In GitHub Actions, a workflow is an automated process that you define in your GitHub repository. Questo processo indica a GitHub come compilare e distribuire il progetto di app per le funzioni in GitHub.This process tells GitHub how to build and deploy your functions app project on GitHub. 

Un flusso di lavoro è definito da un file `/.github/workflows/` YAML (.yml) nel percorso nel repository. Questa definizione contiene i vari passaggi e parametri che costituiscono il flusso di lavoro. 

Per un flusso di lavoro di Funzioni di Azure, il file include tre sezioni:For an Azure Functions workflow, the file has three sections: 

| Sezione | Attività |
| ------- | ----- |
| **autenticazione** | <ol><li>Definire un'entità servizio.</li><li>Scaricare il profilo di pubblicazione.</li><li>Creare un segreto GitHub.Create a GitHub secret.</li></ol>|
| **Compilare** | <ol><li>Configurare l'ambiente.</li><li>Compilare l'app per le funzioni.</li></ol> |
| **Distribuire** | <ol><li>Distribuire l'app per le funzioni.</li></ol>|

> [!NOTE]
> Non è necessario creare un'entità servizio se si decide di usare il profilo di pubblicazione per l'autenticazione.

## <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare un'entità servizio usando il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) nell'interfaccia della riga di comando di [Azure.](/cli/azure/) [service principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) È possibile eseguire questo comando usando [Azure Cloud Shell](https://shell.azure.com) nel portale di Azure o selezionando il pulsante **Prova.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

In questo esempio sostituire i segnaposto nella risorsa con l'ID sottoscrizione, il gruppo di risorse e il nome dell'app per le funzioni. L'output è le credenziali di assegnazione di ruolo che fornisce l'accesso all'app per le funzioni. Copiare questo oggetto JSON, che è possibile usare per l'autenticazione da GitHub.Copy this JSON object, which you can use to authenticate from GitHub.

> [!IMPORTANT]
> È sempre buona norma concedere un accesso minimo. Questo è il motivo per cui l'ambito nell'esempio precedente è limitato all'app per le funzioni specifica e non all'intero gruppo di risorse.

## <a name="download-the-publishing-profile"></a>Scaricare il profilo di pubblicazione

È possibile scaricare il profilo di pubblicazione dell'app per le funzioni, accedendo alla pagina **Panoramica** dell'app e facendo clic su **Ottieni profilo**di pubblicazione .

   ![Scarica profilo di pubblicazione](media/functions-how-to-github-actions/get-publish-profile.png)

Copiare il contenuto del file.

## <a name="configure-the-github-secret"></a>Configurare il segreto GitHubConfigure the GitHub secret

1. In [GitHub](https://github.com), passare al repository, selezionare**Segreti** >  **impostazioni** > **Aggiungere un nuovo segreto**.

   ![Aggiungi segreto](media/functions-how-to-github-actions/add-secret.png)

1. Aggiungere un nuovo segreto.

   * Se si usa l'entità servizio creata tramite l'interfaccia della riga di comando di Azure, usare `AZURE_CREDENTIALS` per **nome**. Incollare quindi l'output dell'oggetto JSON copiato per **Valore**e selezionare **Aggiungi segreto**.
   * Se si utilizza un profilo `SCM_CREDENTIALS` di pubblicazione, utilizzare per il **nome**. Utilizzare quindi il contenuto del file del profilo di pubblicazione per **Valore**e selezionare **Aggiungi segreto**.

GitHub can now authenticate to your function app in Azure.

## <a name="set-up-the-environment"></a>Configurare l'ambiente 

L'impostazione dell'ambiente viene eseguita utilizzando un'azione di installazione di pubblicazione specifica della lingua.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nell'esempio seguente viene illustrata la `actions/setup-node` parte del flusso di lavoro che utilizza l'azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrata la `actions/setup-python` parte del flusso di lavoro che utilizza l'azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

Nell'esempio seguente viene illustrata la `actions/setup-dotnet` parte del flusso di lavoro che utilizza l'azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata la `actions/setup-java` parte del flusso di lavoro che utilizza l'azione per configurare l'ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Compilare l'app per le funzioniBuild the function app

Questo dipende dalla lingua e per i linguaggi supportati da Funzioni di Azure, questa sezione deve essere la procedura di compilazione standard di ogni lingua.

L'esempio seguente mostra la parte del flusso di lavoro che compila l'app per le funzioni, che è specifica della lingua:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Distribuire il codice della funzione

Per distribuire il codice in un'app per `Azure/functions-action` le funzioni, è necessario usare l'azione. Questa azione ha due parametri:

|Parametro |Spiegazione  |
|---------|---------|
|**_nome app_** | (Obbligatorio) Nome dell'app per le funzioni. |
|_**nome-slot**_ | (Facoltativo) Nome dello [slot](functions-deployment-slots.md) di distribuzione in cui si desidera eseguire la distribuzione. Lo slot deve essere già definito nell'app per le funzioni. |


Nell'esempio seguente viene `functions-action`utilizzata la versione 1 di :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare un flusso di lavoro completo .yaml, vedere uno dei file `functionapp` nel repository del flusso di lavoro Azioni di Azure [GitHub](https://aka.ms/functions-actions-samples) che hanno il nome. È possibile utilizzare questi esempi come punto di partenza per il flusso di lavoro.

> [!div class="nextstepaction"]
> [Ulteriori informazioni sulle azioni GitHubLearn more about GitHub Actions](https://help.github.com/en/articles/about-github-actions)
