---
title: Avvio rapido - Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web Node | Microsoft Docs
description: In questa guida di avvio rapido si imposta e recupera un segreto da Azure Key Vault usando un'app Web Node
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 4bea46e62f90a41b566781457a39718849ee0e15
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259234"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web Node 

Questa guida di avvio rapido illustra come archiviare un segreto in Azure Key Vault e come recuperarlo usando un'app Web. L'uso di Key Vault consente di mantere protette le informazioni. Per visualizzare il valore del segreto, è necessario eseguire la guida di avvio rapido in Azure. La guida introduttiva usa Node.js e le identità gestite per le risorse di Azure. Si apprenderà come:

* Creare un insieme di credenziali delle chiavi.
* Archiviare un segreto nell'insieme di credenziali delle chiavi.
* Recuperare un segreto dall'insieme di credenziali delle chiavi.
* Creare un'applicazione Web di Azure.
* Abilitare un'[identità gestita](../active-directory/managed-service-identity/overview.md) per l'app Web.
* Concedere le autorizzazioni necessarie per l'applicazione Web per la lettura dei dati dall'insieme di credenziali delle chiavi.

Prima di procedere, assicurarsi di avere familiarità con i [concetti di base relativi ad Azure Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Key Vault è un repository centrale per archiviare i segreti a livello di codice. Per farlo, tuttavia, le applicazioni e gli utenti devono prima autenticarsi in Key Vault, ovvero devono presentare un segreto. Per seguire le procedure consigliate per la sicurezza, questo primo segreto deve essere ruotato periodicamente. 
>
> Con le [identità del servizio gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md), alle applicazioni in esecuzione in Azure viene assegnata un'identità gestita automaticamente da Azure. Ciò aiuta a risolvere il *problema di introduzione del segreto*, in modo tale che gli utenti e le applicazioni possano seguire le procedure consigliate senza doversi preoccupare della rotazione del primo segreto.

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 o versione successiva Questa guida di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure nell'ambiente locale. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Selezionare il nome di un gruppo di risorse e compilare il segnaposto.
L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti orientali.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Il gruppo di risorse appena creato viene usato in tutto l'articolo.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare quindi un insieme di credenziali delle chiavi usando il gruppo di risorse creato nel passaggio precedente. Anche se questo articolo usa "ContosoKeyVault" come nome, è necessario usare un nome univoco. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi.
* Nome del gruppo di risorse. Il nome deve essere una stringa di 3-24 caratteri costituiti esclusivamente da 0-9, a-z, A-Z e un trattino (-).
* Percorso: **Stati Uniti orientali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione. In questa esercitazione la password sarà denominata **AppSecret** e archivierà il valore **MySecret**.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi denominato **AppSecret**. Questo segreto archivierà il valore **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi si avrà un URI per un segreto in un insieme di credenziali delle chiavi. Prendere nota di queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="clone-the-repo"></a>Clonare il repository

Clonare il repository per creare una copia locale in cui è possibile modificare l'origine. Eseguire il comando seguente:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Installare le dipendenze

Eseguire questi comandi per installare le dipendenze:

```
cd key-vault-node-quickstart
npm install
```

Questo progetto usa due moduli di Node: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) e [azure-keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Pubblicare l'app Web in Azure

Creare un [piano di servizio app](https://azure.microsoft.com/services/app-service/) di Azure. In questo piano è possibile archiviare più app Web.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Quindi, creare un'app Web. Nell'esempio seguente sostituire `<app_name>` con un nome app univoco globale (i caratteri validi sono a-z, 0-9 e -). Il runtime è impostato su NODE|6.9. Per visualizzare tutti i runtime supportati, eseguire `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
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
Passare all'app Web appena creata, che dovrebbe essere funzionante. Sostituire `<app_name>` con un nome di app univoco.

    ```
    http://<app name>.azurewebsites.net
    ```
Il comando precedente crea anche un'app abilitata per Git che consente la distribuzione in Azure dal proprio repository Git locale. Il repository Git locale è configurato con questo URL: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`.

Dopo l'esecuzione del comando precedente, è possibile aggiungere un'istanza remota di Azure al repository Git locale. Sostituire `<url>` con l'URL del repository Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Abilitare un'identità gestita per l'app Web

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. [La panoramica delle identità gestite per le risorse Azure](../active-directory/managed-identities-azure-resources/overview.md) consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

Eseguire il comando assign-identity per creare l'identità per l'applicazione:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Questo comando equivale a passare al portale e impostare **Identità/Assegnato dal sistema** su **Attivato** nelle proprietà dell'applicazione Web.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Assegnare le autorizzazioni all'applicazione per la lettura dei segreti da Key Vault

Prendere nota dell'output del comando precedente, che sarà nel formato seguente:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Eseguire quindi il comando seguente usando il nome dell'insieme di credenziali delle chiavi e il valore di **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Distribuire l'app Node in Azure e recuperare il valore del segreto

Usare il comando seguente per distribuire l'app in Azure:

```
git push azure master
```

Sarà quindi possibile vedere il valore del segreto passando a `https://<app_name>.azurewebsites.net`. Assicurarsi di aver sostituito il nome `<YourKeyVaultName>` con il nome dell'insieme di credenziali.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](key-vault-overview.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
- Vedere le informazioni su [chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- Esaminare le [procedure consigliate per Azure Key Vault](key-vault-best-practices.md)