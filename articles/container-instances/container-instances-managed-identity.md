---
title: Abilitare l'identità gestita nel gruppo di contenitoriEnable managed identity in container group
description: Informazioni su come abilitare un'identità gestita nelle istanze del contenitore di Azure che possono eseguire l'autenticazione con altri servizi di AzureLearn how to enable a managed identity in Azure Container Instances that can authenticate with other Azure services
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901942"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Come usare identità gestite con Istanze di Azure Container

Usare [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) per eseguire codice in Istanze di Azure Container che interagisce con altri servizi di Azure, senza dover gestire segreti o credenziali nel codice. La funzionalità offre una distribuzione di Istanze di Azure Container con un'identità gestita automaticamente in Azure Active Directory.

In questo articolo sono fornite altre informazioni sulle identità gestite in Istanze di Azure Container e viene illustrato come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dall'utente o un'identità assegnata dal sistema in un gruppo di contenitori
> * Concedere all'identità l'accesso a un insieme di credenziali delle chiavi di AzureGrant the identity access to an Azure key vault
> * Usare l'identità gestita per accedere a un insieme di credenziali delle chiavi da un contenitore in esecuzioneUse the managed identity to access a key vault from a running container

Adattare gli esempi per abilitare e usare le identità in Istanze di Azure Container per accedere ad altri servizi di Azure. Questi esempi sono interattivi. Tuttavia, in pratica le immagini del contenitore dovrebbero eseguire codice per accedere ai servizi di Azure.

> [!NOTE]
> Non è attualmente possibile usare un'identità gestita in un gruppo di contenitori distribuito in una rete virtuale.

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Con un'identità gestita in un contenitore in esecuzione è possibile eseguire l'autenticazione a qualsiasi [servizio che supporti l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) senza dover gestire le credenziali nel codice del contenitore. Per i servizi che non supportano l'autenticazione di Active Directory, è possibile archiviare segreti in un insieme di credenziali delle chiavi di Azure e usare l'identità gestita per accedere all'insieme di credenziali delle chiavi per recuperare le credenziali. Per altre informazioni sull'uso di un'identità gestita, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale. Attualmente, le identità gestite nelle istanze del contenitore di Azure sono supportate solo con contenitori Linux e non ancora con contenitori Windows.Currently, managed identities on Azure Container Instances, are only supported with Linux containers and not yet with Windows containers.
>  

### <a name="enable-a-managed-identity"></a>Abilitare un'identità gestita

 In Istanze di Azure Container, le identità gestite per le risorse di Azure sono supportate a partire dalla versione API REST 2018-10-01 e da SDK e strumenti corrispondenti. Quando si crea un gruppo di contenitori, abilitare una o più identità gestite impostando una proprietà [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity). È anche possibile abilitare o aggiornare le identità gestite dopo l'esecuzione di un gruppo di contenitori: entrambe le azioni determinano il riavvio del gruppo di contenitori. Per impostare le identità in un gruppo di contenitori nuovo o esistente, usare l'interfaccia della riga di comando di Azure, un modello di Resource Manager o un file YAML. 

Istanze di Azure Container supporta entrambi i tipi di identità di Azure gestite: assegnata dall'utente e assegnata dal sistema. In un gruppo di contenitori è possibile abilitare un'identità assegnata dal sistema, una o più identità assegnate dall'utente o entrambi i tipi di identità. 

* Un'identità gestita **assegnata dall'utente** viene creata come risorsa di Azure autonoma nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più risorse di Azure (in Istanze di Azure Container o in altri servizi Azure). Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita dei gruppi di contenitori o di altre risorse di servizio a cui l'identità è assegnata. Questo comportamento è particolarmente utile in Istanze di Azure Container. Poiché l'identità si estende oltre il ciclo di vita di un gruppo di contenitori, è possibile riutilizzarla insieme alle altre impostazioni standard per rendere altamente replicabili le distribuzioni di gruppi di contenitori.

* Un'identità gestita **assegnata dal sistema** viene abilitata direttamente in un gruppo di contenitori in Istanze di Azure Container. Quando è abilitata, Azure crea un'identità per il gruppo nel tenant di Azure AD considerato attendibile dalla sottoscrizione dell'istanza. Dopo la creazione dell'identità, viene effettuato il provisioning delle credenziali in ogni contenitore del gruppo. Il ciclo di vita di un'identità assegnata dal sistema è direttamente collegato al gruppo di contenitori in cui l'identità è abilitata. Quando il gruppo viene eliminato, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.

### <a name="use-a-managed-identity"></a>Usare un'identità gestita

Per usare un'identità gestita, all'identità deve essere inizialmente concesso l'accesso a una o più risorse del servizio di Azure (ad esempio un'app Web, un insieme di credenziali delle chiavi o un account di archiviazione) nella sottoscrizione. Per accedere a risorse di Azure da un contenitore in esecuzione, è necessario che il codice acquisisca un *token di accesso* da un endpoint di Azure AD. In seguito il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD. 

L'uso di un'identità gestita in un contenitore in esecuzione equivale fondamentalmente a usare un'identità in una macchina virtuale di Azure. Per usare un [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell o l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) o gli [SDK di Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md), vedere le indicazioni sulle macchine virtuali.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.49 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If](/cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI.

## <a name="create-an-azure-key-vault"></a>Creare un insieme di credenziali delle chiavi di AzureCreate an Azure key vault

Gli esempi in questo articolo usano un'identità gestita nelle istanze del contenitore di Azure per accedere a un segreto dell'insieme di credenziali delle chiavi di Azure.The examples in this article use a managed identity in Azure Container Instances to access an Azure key vault secret. 

Per iniziare, creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus* con il comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) seguente:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilizzare il comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) per creare un insieme di credenziali delle chiavi. Assicurarsi di specificare un nome univoco dell'insieme di credenziali delle chiavi. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Archiviare un segreto di esempio nell'insieme di credenziali delle chiavi usando il comando [az keyvault secret set:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Continuare con gli esempi seguenti per accedere all'insieme di credenziali delle chiavi usando un'identità gestita assegnata dall'utente o dal sistema nelle istanze del contenitore di Azure.Continue with the following examples to access the key vault using either a user-assigned or system-assigned managed identity in Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Esempio 1: Usare un'identità assegnata dall'utente per accedere all'insieme di credenziali delle chiavi di AzureExample 1: Use a user-assigned identity to access Azure key vault

### <a name="create-an-identity"></a>Creare un'identità

Innanzitutto creare un'identità nella sottoscrizione usando il comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). È possibile usare lo stesso gruppo di risorse usato per creare l'insieme di credenziali delle chiavi o usarne uno diverso.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Per usare l'identità nei passaggi seguenti, usare il comando [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) per archiviare l'ID dell'entità servizio e l'ID risorsa dell'identità nelle variabili.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Abilitare un'identità assegnata dall'utente in un gruppo di contenitori

Eseguire il comando [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) seguente per creare `azure-cli` un'istanza del contenitore in base all'immagine di Microsoft.Run the following az container create command to create a container instance based on Microsoft's image. Questo esempio fornisce un gruppo a contenitore singolo che è possibile usare in modo interattivo per eseguire l'interfaccia della riga di comando di Azure per accedere ad altri servizi di Azure.This example provides a single-container group that you can use interactively to run the Azure CLI to access other Azure services. In questa sezione viene utilizzato solo il sistema operativo Ubuntu di base. 

Il parametro `--assign-identity` passa al gruppo l'identità gestita assegnata dall'utente. Il comando con esecuzione prolungata mantiene in esecuzione il contenitore. In questo esempio viene usato lo stesso gruppo di risorse usato per creare l'insieme di credenziali delle chiavi, ma è possibile specificarne uno diverso.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Entro pochi secondi si dovrebbe ricevere una risposta dall'interfaccia della riga di comando di Azure che indica che è stata completata la distribuzione. Controllare il suo stato con il comando [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

La sezione `identity` dell'output è simile alla seguente e mostra che l'identità è impostata nel gruppo di contenitori. `principalID` sotto `userAssignedIdentities` è l'entità servizio dell'identità creata in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Concedere l'accesso dell'identità assegnata dall'utente all'insieme di credenziali delle chiavi

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) seguente per impostare un criterio di accesso all'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità assegnata dall'utente di ottenere segreti dall'insieme di credenziali delle chiavi:The following example allows the user-assigned identity to get secrets from the key vault:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Usare l'identità assegnata dall'utente per ottenere il segreto dall'insieme di credenziali delle chiaviUse user-assigned identity to get secret from key vault

A questo punto è possibile utilizzare l'identità gestita all'interno dell'istanza del contenitore in esecuzione per accedere all'insieme di credenziali delle chiavi. Primo lancio di una shell bash nel contenitore:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Eseguire i comandi seguenti nella shell bash nel contenitore. Per ottenere un token di accesso per usare Azure Active Directory per l'autenticazione nell'insieme di credenziali delle chiavi, eseguire il comando seguente:To get an access token to use Azure Active Directory to authenticate to key vault, run the following command:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Output:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Per archiviare il token di accesso in una variabile da usare nei comandi successivi per l'autenticazione, eseguire il comando seguente:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Utilizzare ora il token di accesso per eseguire l'autenticazione nell'insieme di credenziali delle chiavi e leggere un segreto. Assicurarsi di sostituire il nome dell'insieme di credenziali delle chiavi nell'URL (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

La risposta avrà un aspetto simile a quanto riportato di seguito e mostrerà il segreto. Nel codice occorrerebbe analizzare questo output per ottenere il segreto. Quindi, usare il segreto in un'operazione successiva per accedere a un'altra risorsa di Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Esempio 2: Usare un'identità assegnata dal sistema per accedere all'insieme di credenziali delle chiavi di AzureExample 2: Use a system-assigned identity to access Azure key vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Abilitare un'identità assegnata dal sistema in un gruppo di contenitori

Eseguire il comando [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) seguente per creare `azure-cli` un'istanza del contenitore in base all'immagine di Microsoft.Run the following az container create command to create a container instance based on Microsoft's image. Questo esempio fornisce un gruppo a contenitore singolo che è possibile usare in modo interattivo per eseguire l'interfaccia della riga di comando di Azure per accedere ad altri servizi di Azure.This example provides a single-container group that you can use interactively to run the Azure CLI to access other Azure services. 

Il parametro `--assign-identity` senza valori aggiuntivi abilita un'identità gestita assegnata dal sistema nel gruppo. L'identità ha come ambito il gruppo di risorse del gruppo di contenitori. Il comando con esecuzione prolungata mantiene in esecuzione il contenitore. In questo esempio viene usato lo stesso gruppo di risorse usato per creare l'insieme di credenziali delle chiavi, ma è possibile specificarne uno diverso.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Entro pochi secondi si dovrebbe ricevere una risposta dall'interfaccia della riga di comando di Azure che indica che è stata completata la distribuzione. Controllare il suo stato con il comando [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

La sezione `identity` nell'output ha un aspetto simile a quanto riportato di seguito e mostra che in Azure Active Directory viene creata un'identità assegnata dal sistema:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Impostare una variabile sul valore di `principalId` (l'ID dell'entità servizio) dell'identità per usarla nei passaggi successivi.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Concedere l'accesso al gruppo di contenitori all'insieme di credenziali delle chiaviGrant container group access to the key vault

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) seguente per impostare un criterio di accesso all'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità gestita dal sistema di ottenere segreti dall'insieme di credenziali delle chiavi:The following example allows the system-managed identity to get secrets from the key vault:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Usare l'identità del gruppo di contenitori per ottenere il segreto dall'insieme di credenziali delle chiaviUse container group identity to get secret from key vault

A questo punto è possibile utilizzare l'identità gestita per accedere all'insieme di credenziali delle chiavi all'interno dell'istanza del contenitore in esecuzione. Primo lancio di una shell bash nel contenitore:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Eseguire i comandi seguenti nella shell bash nel contenitore. Accedere innanzitutto all'interfaccia della riga di comando di Azure usando l'identità gestita:First log in to the Azure CLI using the managed identity:

```bash
az login --identity
```

Dal contenitore in esecuzione, recuperare il segreto dall'insieme di credenziali delle chiavi:From the running container, retrieve the secret from the key vault:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Viene recuperato il valore del segreto:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Abilitare l'identità gestita con un modello di Resource Manager

Per abilitare un'identità gestita in un gruppo di contenitori usando un [modello di Resource Manager](container-instances-multi-container-group.md), impostare la proprietà `identity` dell'oggetto `Microsoft.ContainerInstance/containerGroups` con un oggetto `ContainerGroupIdentity`. I frammenti di codice seguenti mostrano la proprietà `identity` configurata per scenari diversi. Vedere il [riferimento al modello di Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Specificare `apiVersion` un `2018-10-01`minimo di .

### <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Un'identità assegnata dall'utente è un ID risorsa nel formato seguente:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

È possibile abilitare una o più identità assegnate dall'utente.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identità assegnata dal sistema

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identità assegnate dal sistema e dall'utente

In un gruppo di contenitori è possibile abilitare sia un'identità assegnata dal sistema che una o più identità assegnate dall'utente.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Abilitare l'identità gestita usando un file YAML

Per abilitare un'identità gestita in un gruppo di contenitori distribuiti usando un [file YAML](container-instances-multi-container-yaml.md), includere il codice YAML seguente.
Specificare `apiVersion` un `2018-10-01`minimo di .

### <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Un'identità assegnata dall'utente è un ID risorsa nel formato seguente 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

È possibile abilitare una o più identità assegnate dall'utente.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identità assegnata dal sistema

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identità assegnate dal sistema e dall'utente

In un gruppo di contenitori è possibile abilitare sia un'identità assegnata dal sistema che una o più identità assegnate dall'utente.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite altre informazioni sulle identità gestite in Istanze di Azure Container ed è stato illustrato come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dall'utente o un'identità assegnata dal sistema in un gruppo di contenitori
> * Concedere all'identità l'accesso a un insieme di credenziali delle chiavi di AzureGrant the identity access to an Azure key vault
> * Usare l'identità gestita per accedere a un insieme di credenziali delle chiavi da un contenitore in esecuzioneUse the managed identity to access a key vault from a running container

* Altre informazioni sulle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/).

* Vedere un esempio di Azure Go SDK di uso di un'identità gestita per accedere a un insieme di credenziali delle chiavi dalle istanze del contenitore di Azure.See an [Azure Go SDK example](https://medium.com/@samkreter/c98911206328) of using a managed identity to access a key vault from Azure Container Instances.
