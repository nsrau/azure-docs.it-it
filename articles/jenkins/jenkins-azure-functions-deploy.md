---
title: Eseguire la distribuzione in Funzioni di Azure tramite il plug-in Jenkins per Funzioni di Azure
description: Informazioni su come eseguire la distribuzione in Funzioni di Azure tramite il plug-in Jenkins per Funzioni di Azure
ms.service: jenkins
keywords: jenkins, azure, devops, java, funzioni di azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 1138af0e073f68842861df86acd4d9d6eb467782
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824711"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Eseguire la distribuzione in Funzioni di Azure tramite il plug-in Jenkins per Funzioni di Azure

[Funzioni di Azure](/azure/azure-functions/) è un servizio di calcolo senza server. Usando Funzioni di Azure è possibile eseguire codice on demand senza provisioning o gestione dell'infrastruttura. Questa esercitazione mostra come distribuire una funzione Java in Funzioni di Azure tramite il plug-in per Funzioni di Azure.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
- **Server Jenkins**: se non è installato un server Jenkins, vedere l'articolo [Creare un server Jenkins in Azure](./install-jenkins-solution-template.md).

 > [!TIP]
 > Il codice sorgente usato per questa esercitazione si trova nel [repository GitHub Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Creare una funzione Java

Per creare una funzione Java con lo stack di runtime Java, usare il [portale di Azure](https://portal.azure.com) o l'[interfaccia della riga di comando di Azure](/cli/azure/?view=azure-cli-latest).

La procedura seguente mostra come creare una funzione Java tramite l'interfaccia della riga di comando di Azure:

1. Creare un gruppo di risorse, sostituendo il segnaposto  **&lt;resource_group>** con il nome del gruppo di risorse.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Creare un account di archiviazione di Azure, sostituendo i segnaposto con i valori appropriati.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Creare l'app per le funzioni di test, sostituendo i segnaposto con i valori appropriati.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. Eseguire l'aggiornamento al runtime versione 2.x, sostituendo i segnaposto con i valori appropriati.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Preparare il server Jenkins

La procedura seguente descrive come preparare il server Jenkins:

1. Distribuire un [server Jenkins](https://aka.ms/jenkins-on-azure) in Azure. Se non è già installata un'istanza del server Jenkins, l'articolo [Creare un server Jenkins in Azure](./install-jenkins-solution-template.md) descrive in modo dettagliato questo processo.

1. Accedere all'istanza di Jenkins con SSH.

1. Nell'istanza di Jenkins installare maven usando il comando seguente:

    ```terminal
    sudo apt install -y maven
    ```

1. Nell'istanza di Jenkins installare [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) eseguendo i comandi seguenti da un prompt del terminale:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Nel dashboard di Jenkins installare i plug-in seguenti:

    - Plug-in per Funzioni di Azure
    - Plug-in EnvInject

1. Jenkins richiede un'entità servizio di Azure per l'autenticazione e l'accesso alle risorse di Azure. Per istruzioni dettagliate, fare riferimento a [Eseguire la distribuzione in Servizio app di Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md).

1. Usando l'entità servizio di Azure aggiungere un tipo di credenziale "Microsoft Azure Service Principal" (Entità servizio di Microsoft Azure) in Jenkins. Fare riferimento all'esercitazione [Eseguire la distribuzione in Servizio app di Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="create-a-jenkins-pipeline"></a>Creare una pipeline Jenkins

In questa sezione viene creata la [pipeline Jenkins](https://jenkins.io/doc/book/pipeline/).

1. Nel dashboard di Jenkins creare una pipeline.

1. Selezionare **Prepare an environment for the run** (Preparare un ambiente per l'esecuzione).

1. Aggiungere le variabili di ambiente seguenti in **Properties Content** (Contenuto proprietà), sostituendo i segnaposto con i valori appropriati per l'ambiente:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Nella sezione **Pipeline > Definition** (Pipeline > Definizione) selezionare **Pipeline script from SCM** (Script di pipeline da SCM).

1. Immettere l'URL del repository SCM e il percorso dello script usando l'[esempio di script](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile) fornito.

## <a name="build-and-deploy"></a>Eseguire la compilazione e la distribuzione

È ora possibile eseguire il processo Jenkins.

1. Prima di tutto, ottenere la chiave di autorizzazione tramite le istruzioni contenute nell'articolo [Trigger e associazioni HTTP di Funzioni di Azure](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys).

1. Nel browser immettere l'URL dell'app. Sostituire i segnaposto con i valori appropriati e specificare un valore numerico per **&lt;input_number>** come input per la funzione Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Verranno visualizzati risultati simili all'output di esempio seguente (in cui è stato usato un numero dispari, 365, come test):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare le risorse associate seguendo questa procedura:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Funzioni di Azure, vedere la risorsa seguente:
> [!div class="nextstepaction"]
> [Documentazione di Funzioni di Azure](/azure/azure-functions/)