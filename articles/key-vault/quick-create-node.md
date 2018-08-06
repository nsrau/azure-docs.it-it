---
title: Esercitazione sulla configurazione di un'applicazione Web di Azure per la lettura di un segreto da un insieme di credenziali delle chiavi | Microsoft Docs
description: "Esercitazione: Configurare un'applicazione Node.js per la lettura di un segreto da Key Vault"
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/01/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: cc43081463667eba06af6538f3d78f16544ed2a5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412243"
---
# <a name="quickstart-how-to-set-and-read-a-secret-from-key-vault-in-a-node-web-app"></a>Guida introduttiva: Come impostare e leggere un segreto da Key Vault in un'app Web Node 

Questa guida introduttiva illustra come archiviare un segreto in Key Vault e come recuperarlo usando un'app Web. Questa app Web può essere eseguita in locale o in Azure. La guida introduttiva usa Node.js e identità del servizio gestite

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare un'applicazione Web di Azure.
> * [Abilitare le identità del servizio gestite](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview).
> * Concedere le autorizzazioni necessarie per l'applicazione Web per la lettura dei dati da un insieme di credenziali delle chiavi.

Prima di procedere, assicurarsi di avere familiarità con i [concetti di base](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prerequisiti

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 o successiva
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="login-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Selezionare il nome di un gruppo di risorse e compilare il segnaposto.
L'esempio seguente crea un gruppo di risorse denominato *<YourResourceGroupName>* nella località *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

In tutta l'esercitazione viene usato il gruppo di risorse appena creato.

## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Successivamente si crea un'istanza di Key Vault usando il gruppo di risorse creato nel passaggio precedente. Anche se in tutto l'articolo viene usato "ContosoKeyVault" come nome per l'istanza di Key Vault, è necessario usare un nome univoco. Specificare le informazioni seguenti:

* Nome dell'istanza di Key Vault - **Selezionare qui un nome per l'istanza di Key Vault**.
* Nome gruppo di risorsa - **Selezionare qui un nome di gruppo di risorsa**.
* Località: **Stati Uniti orientali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione. In questa esercitazione la password sarà denominata **AppSecret** e archivierà il valore **MySecret**.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi chiamato **AppSecret** che archivierà il valore **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi sarà disponibili un URI per un segreto in Azure Key Vault. Annotare queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="clone-the-repo"></a>Clonare il repository

Clonare il repository per creare una copia locale e modificare l'origine eseguendo il comando seguente:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Installare le dipendenze

Qui si installano le dipendenze. Eseguire il comando cd key-vault-node-quickstart npm install

Questo progetto usa 2 moduli Node:

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Pubblicare l'applicazione Web in Azure

Di seguito sono riportati i pochi passaggi necessari

- Il primo passaggio consiste nel creare un piano del [servizio app di Azure](https://azure.microsoft.com/services/app-service/). In questo piano è possibile archiviare più app Web.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Successivamente verrà creata un'app Web. Nell'esempio seguente sostituire <app_name> con un nome app univoco a livello globale. I caratteri validi sono a-z, 0-9 e -. Il runtime è impostato su NODE|6.9. Per visualizzare tutti i runtime supportati, eseguire az webapp list-runtimes
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    # PowerShell
    az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
    ```
    Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:
    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    Passare all'app Web appena creata, che dovrebbe essere funzionante. Sostituire <app_name> con un nome app univoco.

    ```
    http://<app name>.azurewebsites.net
    ```
    Il comando precedente crea anche un'app abilitata per Git che consente la distribuzione in Azure dal proprio repository Git locale. 
    Il repository Git locale è configurato con l'URL 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'

- Creare un utente di distribuzione. Al termine del comando precedente, è possibile aggiungere una distribuzione remota di Azure al repository Git locale. Sostituire <url> con l'URL della distribuzione remota Git ottenuto da Abilitare la distribuzione Git per l'app.

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>Abilitare l'identità del servizio gestito

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. Identità del servizio gestito consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory, ovvero Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

Eseguire il comando assign-identity per creare l'identità per l'applicazione:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Questo comando equivale a passare al portale e impostare **Identità del servizio gestito** su **Attivato** nelle proprietà dell'applicazione Web.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Assegnare le autorizzazioni all'applicazione per la lettura dei segreti da Key Vault

Annotare o copiare l'output del comando precedente, che sarà nel formato seguente:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Eseguire quindi questo comando usando il nome dell'insieme di credenziali delle chiavi e il valore PrincipalId copiato in precedenza:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Distribuire l'app Node in Azure e recuperare il valore del segreto

Ora che tutto è pronto, usare il comando seguente per distribuire l'app in Azure

```
git push azure master
```

Successivamente sarà possibile vedere il valore del segreto passando a https://<app_name>.azurewebsites.net.
Assicurarsi di aver sostituito il nome <YourKeyVaultName> con il nome dell'insieme di credenziali

## <a name="next-steps"></a>Passaggi successivi

* [Home page di Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentazione su Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK per Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Informazioni di riferimento sulle API REST](https://docs.microsoft.com/rest/api/keyvault/)