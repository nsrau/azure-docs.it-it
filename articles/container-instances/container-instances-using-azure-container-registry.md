---
title: Eseguire la distribuzione in Istanze di Azure Container da Registro Azure Container
description: Informazioni su come distribuire i contenitori in Istanze di Azure Container usando le immagini contenitore in un registro contenitori di Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f577a7dd9f517be6ab7b632a82227e4807862ba5
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727909"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Eseguire la distribuzione in Istanze di Azure Container da Registro Azure Container

[Registro Azure Container](../container-registry/container-registry-intro.md) è un servizio gestito di registro contenitori usato per archiviare immagini di un contenitore Docker privato. Questo articolo illustra come distribuire in Istanze di Azure Container immagini del contenitore archiviate in un registro contenitori di Azure.

## <a name="prerequisites"></a>Prerequisiti

**Registro Azure Container**: per completare i passaggi descritti in questo articolo, sono necessari un registro Azure Container e almeno un'immagine del contenitore nel registro. Se occorre un registro, vedere [Creare un registro contenitori usando l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Interfaccia della riga di comando di Azure**: gli esempi della riga di comando in questo articolo usano l'[interfaccia della riga di comando di Azure](/cli/azure/) e sono formattati per la shell Bash. È possibile [installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) localmente o usare [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurare l'autenticazione del registro

In qualsiasi scenario di produzione l'accesso a un registro contenitori di Azure deve essere fornito tramite [entità servizio](../container-registry/container-registry-auth-service-principal.md). Le entità servizio consentono di fornire il [controllo degli accessi in base al ruolo](../container-registry/container-registry-roles.md) alle immagini del contenitore. Ad esempio, è possibile configurare un'entità servizio con accesso pull-only a un registro.

Nella sezione seguente vengono creati un insieme di credenziali delle chiavi e un'entità servizio di Azure e le credenziali dell'entità servizio vengono archiviate nell'insieme. 

### <a name="create-key-vault"></a>Creare un insieme di credenziali delle chiavi

Se non si ha già un insieme di credenziali delle chiavi in [Azure Key Vault](/azure/key-vault/), crearne uno usando i comandi seguenti nell'interfaccia della riga di comando di Azure.

Aggiornare la variabile `RES_GROUP` con il nome di un gruppo di risorse esistente in cui creare l'insieme di credenziali delle chiavi e `ACR_NAME` con il nome del registro contenitori. Specificare un nome per il nuovo insieme di credenziali delle chiavi in `AKV_NAME`. Il nome dell'insieme di credenziali deve essere univoco in Azure e avere una lunghezza compresa tra 3 e 24 caratteri alfanumerici, iniziare con una lettera, finire con una lettera o una cifra e non contenere trattini consecutivi.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Creare un'entità servizio e archiviare le credenziali

A questo punto occorre creare un'entità servizio e archiviarne le credenziali nell'insieme di credenziali delle chiavi.

Il comando seguente usa [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] per creare l'entità servizio e [az keyvault secret set][az-keyvault-secret-set] per archiviare la **password** dell'entità servizio nell'insieme di credenziali.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

L'argomento `--role` nel comando precedente configura l'entità servizio con il ruolo *acrpull*, che concede l'accesso al registro con autorizzazioni solo di pull. Per concedere l'accesso con autorizzazioni sia di push che di pull, impostare l'argomento `--role` su *acrpush*.

Quindi, archiviare nell'insieme di credenziali il valore *appId* dell'entità servizio, che corrisponde al **nome utente** passato a Registro Azure Container per l'autenticazione.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

È stato creato un insieme di credenziali delle chiavi di Azure e vi sono stati archiviati due segreti:

* `$ACR_NAME-pull-usr`: ID dell'entità servizio, da usare come **nome utente** del registro contenitori.
* `$ACR_NAME-pull-pwd`: password dell'entità servizio, da usare come **password** del registro contenitori.

Ora è possibile fare riferimento a questi segreti per nome quando gli utenti o le applicazioni e i servizi eseguono il pull di immagini dal registro.

## <a name="deploy-container-with-azure-cli"></a>Distribuire un contenitore con l'interfaccia della riga di comando di Azure

Ora che le credenziali dell'entità servizio sono archiviate nei segreti di Azure Key Vault, le applicazioni e i servizi possono usarle per accedere al registro privato.

Ottenere prima di tutto il nome del server di accesso del registro usando il comando [az acr show][az-acr-show]. Il nome del server di accesso contiene solo lettere minuscole ed è simile a `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Eseguire il comando [az container create][az-container-create] seguente per distribuire un'istanza di contenitore. Il comando usa le credenziali dell'entità servizio archiviate in Azure Key Vault per eseguire l'autenticazione con il registro contenitori e presuppone che sia stato precedentemente eseguito il push dell'immagine [aci-helloworld](container-instances-quickstart.md) nel registro. Aggiornare il valore di `--image` se si vuole usare un'immagine del registro diversa.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Il valore di `--dns-name-label` deve essere univoco in Azure, quindi il comando precedente accoda un numero casuale all'etichetta del nome DNS del contenitore. L'output del comando visualizza il nome di dominio completo (FQDN) del contenitore, ad esempio:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Una volta avviato il contenitore, è possibile passare al suo FQDN nel browser per verificare che l'applicazione sia correttamente in esecuzione.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuire con un modello di Azure Resource Manager

È possibile specificare le proprietà di Registro Azure Container in un modello di Azure Resource Manager includendo la proprietà `imageRegistryCredentials` nella definizione del gruppo di contenitori:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Per informazioni dettagliate sul riferimento ai segreti di Azure Key Vault in un modello di Resource Manager, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuire con il portale di Azure

Se si conservano le immagini del contenitore in un registro contenitori di Azure, è possibile creare facilmente un contenitore in Istanze di Azure Container usando il portale di Azure. Quando si usa il portale per distribuire un'istanza di contenitore da un registro contenitori, è necessario abilitare l'[account amministratore](../container-registry/container-registry-authentication.md#admin-account) del registro. L'account amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. 

1. Nel portale di Azure passare al registro contenitori.

1. Per confermare che l'account amministratore è abilitato, selezionare **Chiavi di accesso** e quindi in **Utente amministratore** selezionare **Abilitare**.

1. Selezionare **Repository**, quindi selezionare il repository da cui si desidera eseguire la distribuzione, fare clic con il pulsante destro del mouse sul tag dell'immagine del contenitore che si vuole distribuire e selezionare **Esegui istanza**.

    !["Esegui istanza" in Registro Azure Container nel portale di Azure][acr-runinstance-contextmenu]

1. Immettere un nome per il contenitore e un nome per il gruppo di risorse. Se si vuole, è anche possibile cambiare i valori predefiniti.

    ![Menu di creazione per Istanze di Azure Container][acr-create-deeplink]

1. Al termine della distribuzione, è possibile passare al gruppo di contenitori dal riquadro delle notifiche per trovare l'indirizzo IP e le altre proprietà.

    ![Visualizzazione dei dettagli del gruppo di contenitori in Istanze di Azure Container][aci-detailsview]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'autenticazione con Registro Azure Container, vedere [Eseguire l'autenticazione con un registro contenitori Docker privato](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set