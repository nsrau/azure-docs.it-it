---
title: Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure
description: Informazioni su come distribuire i contenitori nelle istanze di contenitore di Azure usando le immagini contenitore in un registro contenitori di Azure.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 5c3cf162caf5cf9aa88b012257d4caab37b7893c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424212"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Eseguire la distribuzione in Istanze di contenitore di Azure da Registro contenitori di Azure

Registro contenitori di Azure è un registro privato basato su Azure per le immagini del contenitore Docker. Questo articolo illustra come distribuire in Istanze di contenitore di Azure immagini del contenitore archiviate in un registro contenitori di Azure.

## <a name="prerequisites"></a>Prerequisiti

**Registro contenitori di Azure**: per completare i passaggi descritti in questo articolo sono necessari un registro contenitori di Azure e almeno un'immagine del contenitore nel registro. Se occorre un registro, vedere [Creare un registro di contenitori usando l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Interfaccia della riga di comando di Azure**: gli esempi della riga di comando in questo articolo usano l'[interfaccia della riga di comando di Azure](/cli/azure/) e sono formattati per la shell Bash. È possibile [installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) localmente o usare [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurare l'autenticazione del registro

In qualsiasi scenario di produzione l'accesso a un registro contenitori di Azure deve essere fornito tramite [entità servizio](../container-registry/container-registry-auth-service-principal.md). Mediante le entità servizio è possibile fornire il controllo degli accessi in base al ruolo alle immagini del contenitore. Ad esempio, è possibile configurare un'entità servizio con accesso pull-only a un registro.

In questa sezione vengono creati un insieme di credenziali delle chiavi di Azure e un'entità servizio e le credenziali dell'entità servizio vengono archiviate nell'insieme.

### <a name="create-key-vault"></a>Creare un insieme di credenziali delle chiavi

Se non si ha già un insieme di credenziali delle chiavi in [Azure Key Vault](/azure/key-vault/), crearne uno usando i comandi seguenti nell'interfaccia della riga di comando di Azure.

Aggiornare la variabile `RES_GROUP` con il nome del gruppo di risorse in cui creare l'insieme di credenziali delle chiavi e `ACR_NAME` con il nome del registro contenitori. Specificare un nome per il nuovo insieme di credenziali delle chiavi in `AKV_NAME`. Il nome dell'insieme di credenziali deve essere univoco in Azure e avere una lunghezza compresa tra 3 e 24 caratteri alfanumerici, iniziare con una lettera, finire con una lettera o una cifra e non contenere trattini consecutivi.

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
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

L'argomento `--role` nel comando precedente configura l'entità servizio con il ruolo *lettore*, che concede l'accesso di tipo pull-only al registro. Per concedere l'accesso sia push che pull, impostare l'argomento `--role` su *collaboratore*.

Quindi, archiviare nell'insieme di credenziali il valore *appId* dell'entità servizio, che corrisponde al **nome utente** passato a Registro contenitori di Azure per l'autenticazione.

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

Eseguire il comando [az container create][az-container-create] seguente per distribuire un'istanza di contenitore. Il comando usa le credenziali dell'entità servizio archiviate in Azure Key Vault per eseguire l'autenticazione con il registro contenitori e presuppone che sia stato precedentemente eseguito il push dell'immagine [aci-helloworld](container-instances-quickstart.md) nel registro. Aggiornare il valore di `--image` se si vuole usare un'immagine del registro diversa.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Il valore di `--dns-name-label` deve essere univoco in Azure, quindi il comando precedente accoda un numero casuale all'etichetta del nome DNS del contenitore. L'output del comando visualizza il nome di dominio completo (FQDN) del contenitore, ad esempio:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Una volta avviato il contenitore, è possibile passare al suo FQDN nel browser per verificare che l'applicazione sia correttamente in esecuzione.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuire con un modello di Azure Resource Manager

È possibile specificare le proprietà di Registro contenitori di Azure in un modello di Azure Resource Manager includendo la proprietà `imageRegistryCredentials` nella definizione del gruppo di contenitori:

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

Se si conservano le immagini del contenitore in Registro contenitori di Azure, è possibile creare facilmente un contenitore in Istanze di contenitore di Azure usando il portale di Azure.

1. Nel portale di Azure passare al registro contenitori.

1. Selezionare **Repository**, quindi selezionare il repository da cui si desidera eseguire la distribuzione, fare clic con il pulsante destro del mouse sul tag dell'immagine del contenitore che si vuole distribuire e selezionare **Esegui istanza**.

    !["Esegui istanza" nel Registro contenitori di Azure nel portale di Azure][acr-runinstance-contextmenu]

1. Immettere un nome per il contenitore e un nome per il gruppo di risorse. Se si vuole, è anche possibile cambiare i valori predefiniti.

    ![Menu di creazione per Istanze di contenitore di Azure][acr-create-deeplink]

1. Al termine della distribuzione, è possibile passare al gruppo di contenitori dal riquadro delle notifiche per trovare l'indirizzo IP e le altre proprietà.

    ![Visualizzazione dei dettagli del gruppo di contenitori in Istanze di contenitore di Azure][aci-detailsview]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'autenticazione con Registro contenitori di Azure, vedere [Eseguire l'autenticazione con un registro contenitori Docker privato](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set