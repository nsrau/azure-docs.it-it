---
title: "Esercitazione: Usare un'identità gestita per richiamare Funzioni di Azure da un'app di Azure Spring Cloud"
description: Usare l'identità gestita per richiamare Funzioni di Azure da un'app di Azure Spring Cloud
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 44268bf1b7805ece8de4a3499a7d53fc851af142
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91664988"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Esercitazione: Usare un'identità gestita per richiamare Funzioni di Azure da un'app di Azure Spring Cloud

Questo articolo illustra come creare un'identità gestita per un'app di Azure Spring Cloud e usarla per richiamare funzioni attivate tramite HTTP.

Funzioni di Azure e Servizi app includono il supporto predefinito per l'autenticazione di Azure Active Directory (Azure AD). Sfruttando questa funzionalità di autenticazione predefinita insieme a Identità gestite per Azure Spring Cloud, è possibile richiamare i servizi RESTful usando la moderna semantica di OAuth. Questo metodo non richiede l'archiviazione di segreti nel codice e fornisce controlli più granulari dell'accesso a risorse esterne. 


## <a name="prerequisites"></a>Prerequisiti

* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/free/)
* [Installare l'interfaccia della riga di comando di Azure versione 2.0.67 o successiva](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Installare Maven 3.0 o versione successiva](https://maven.apache.org/download.cgi)
* [Installare Azure Functions Core Tools versione 3.0.2009 o successiva](https://docs.microsoft.com/azure/azure-functions/functions-run-local#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse che contenga sia l'app per le funzioni che Spring Cloud usando il comando [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Creare un'app per le funzioni
Per creare un'app per le funzioni, è necessario creare prima un account di archiviazione di backup usando il comando [az storage account create](/cli/azure/storage/account#az-storage-account-create):

> [!Important]
> È necessario che ogni app per le funzioni e ogni account di archiviazione abbiano un nome univoco. Negli esempi seguenti sostituire <your-functionapp-name> con il nome dell'app per le funzioni e <your-storageaccount-name> con il nome dell'account di archiviazione.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Una volta creato l'account di archiviazione, è possibile creare l'app per le funzioni.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Prendere nota del valore di **hostNames** restituito, che avrà il formato "https://<your-functionapp-name>.azurewebsites.net". Verrà usato in un passaggio successivo.


## <a name="enable-azure-active-directory-authentication"></a>Abilitare l'autenticazione di Azure Active Directory

Accedere all'app per le funzioni appena creata dal [portale di Azure](https://portal.azure.com) e scegliere "Autenticazione/Autorizzazione" dal menu Impostazioni. Abilitare l'autenticazione del servizio app e impostare "Azione da eseguire quando la richiesta non è autenticata" su "Accedi con Azure Active Directory". Questa impostazione assicura che tutte le richieste non autenticate vengano negate (risposta 401).

![Impostazioni di autenticazione che mostrano Azure Active Directory come provider predefinito](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

In Provider di autenticazione selezionare Azure Active Directory per configurare la registrazione dell'applicazione. Se si seleziona la modalità di gestione rapida, nel tenant di Azure AD verrà automaticamente creata una registrazione dell'applicazione con la configurazione corretta.

![Provider di Azure Active Directory impostato sulla modalità di gestione rapida](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Dopo aver salvato le impostazioni, l'app per le funzioni verrà riavviata e per tutte le richieste successive verrà chiesto di effettuare l'accesso tramite Azure AD. È possibile verificare che le richieste non autenticate vengano ora rifiutate passando all'URL radice delle app per le funzioni (restituito nell'output di **hostNames** nel passaggio precedente). Si dovrebbe essere reindirizzati alla schermata di accesso di Azure AD dell'organizzazione.


## <a name="create-an-http-triggered-function"></a>Creare una funzione attivata tramite HTTP

In una directory locale vuota creare una nuova app per le funzioni e aggiungere una funzione attivata tramite HTTP.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Per impostazione predefinita, le funzioni usano l'autenticazione basata su chiave per proteggere gli endpoint HTTP. Poiché verrà abilitata l'autenticazione di Azure AD per proteggere l'accesso alle funzioni, è necessario [impostare il livello di autenticazione della funzione su anonimo](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger#secure-an-http-endpoint-in-production).

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

L'app può ora essere pubblicata nell'istanza dell'[app per le funzioni](#create-a-function-app) creata nel passaggio precedente.

```console
func azure functionapp publish <your-functionapp-name>
```

L'output del comando di pubblicazione dovrà includere l'URL della funzione appena creata.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Creare il servizio e l'app di Azure Spring Cloud
Dopo aver installato l'estensione spring-cloud, creare un'istanza di Azure Spring Cloud con il comando `az spring-cloud create` dell'interfaccia della riga di comando di Azure. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

L'esempio seguente crea un'app denominata `msiapp` con un'identità gestita assegnata dal sistema, come richiesto dal parametro `--assign-identity`.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --is-public true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Creare un'app Spring Boot di esempio per richiamare la funzione

Questo esempio richiama la funzione attivata tramite HTTP chiedendo prima di tutto all'[endpoint delle identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) un token di accesso che userà per autenticare la richiesta HTTP della funzione.

1. Clonare il progetto di esempio. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Specificare l'URI della funzione e il nome del trigger nelle proprietà dell'app. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Per usare l'identità gestita per le app di Azure Spring Cloud, aggiungere proprietà con il contenuto seguente a *src/main/resources/application.properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Creare il pacchetto dell'app di esempio. 

    ```console
    mvn clean package
    ```

4. A questo punto, distribuire l'app in Azure con il comando dell'interfaccia della riga di comando di Azure `az spring-cloud app deploy`. 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Accedere all'endpoint pubblico o all'endpoint di test per testare l'app. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Nel corpo della risposta verrà restituito il messaggio seguente.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    È possibile provare a passare valori diversi alla funzione cambiando il parametro path.

## <a name="next-steps"></a>Passaggi successivi

* [Come abilitare l'identità gestita assegnata dal sistema per l'applicazione Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [Altre informazioni sulle identità gestite per le risorse di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Configurare un'applicazione client daemon per le chiamate da servizio a servizio](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad#configure-a-daemon-client-application-for-service-to-service-calls)
