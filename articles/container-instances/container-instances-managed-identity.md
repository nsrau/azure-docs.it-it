---
title: Abilitare l'identità gestita nel gruppo di contenitori
description: Informazioni su come abilitare un'identità gestita in istanze di contenitore di Azure in grado di eseguire l'autenticazione con altri servizi di Azure
ms.topic: article
ms.date: 10/22/2018
ms.openlocfilehash: ccf754fac0d884c8a2af9db01578f97b73d54ac4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776524"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Come usare identità gestite con Istanze di Azure Container

Usare [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) per eseguire codice in Istanze di Azure Container che interagisce con altri servizi di Azure, senza dover gestire segreti o credenziali nel codice. La funzionalità offre una distribuzione di Istanze di Azure Container con un'identità gestita automaticamente in Azure Active Directory.

In questo articolo sono fornite altre informazioni sulle identità gestite in Istanze di Azure Container e viene illustrato come:

> [!div class="checklist"]
> * Abilitare un'identità assegnata dall'utente o un'identità assegnata dal sistema in un gruppo di contenitori
> * Concedere all'identità di accedere ad Azure Key Vault
> * Usare l'identità gestita per accedere a un insieme di credenziali delle chiavi da un contenitore in esecuzione

Adattare gli esempi per abilitare e usare le identità in Istanze di Azure Container per accedere ad altri servizi di Azure. Questi esempi sono interattivi. Tuttavia, in pratica le immagini del contenitore dovrebbero eseguire codice per accedere ai servizi di Azure.

> [!NOTE]
> Non è attualmente possibile usare un'identità gestita in un gruppo di contenitori distribuito in una rete virtuale.

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Con un'identità gestita in un contenitore in esecuzione è possibile eseguire l'autenticazione a qualsiasi [servizio che supporti l'autenticazione di Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) senza dover gestire le credenziali nel codice del contenitore. Per i servizi che non supportano l'autenticazione di AD, è possibile archiviare i segreti in Azure Key Vault e usare l'identità gestita per accedere a Key Vault e recuperare le credenziali. Per altre informazioni sull'uso di un'identità gestita, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale. Attualmente, le identità gestite nelle istanze di contenitore di Azure sono supportate solo con i contenitori Linux e non ancora con i contenitori di Windows.
>  

### <a name="enable-a-managed-identity"></a>Abilitare un'identità gestita

 In Istanze di Azure Container, le identità gestite per le risorse di Azure sono supportate a partire dalla versione API REST 2018-10-01 e da SDK e strumenti corrispondenti. Quando si crea un gruppo di contenitori, abilitare una o più identità gestite impostando una proprietà [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity). È anche possibile abilitare o aggiornare identità gestite quando un gruppo di contenitori è in esecuzione. Entrambe le azioni causano il riavvio del gruppo di contenitori. Per impostare le identità in un gruppo di contenitori nuovo o esistente, usare l'interfaccia della riga di comando di Azure, un modello di Resource Manager o un file YAML. 

Istanze di Azure Container supporta entrambi i tipi di identità di Azure gestite: assegnata dall'utente e assegnata dal sistema. In un gruppo di contenitori è possibile abilitare un'identità assegnata dal sistema, una o più identità assegnate dall'utente o entrambi i tipi di identità. 

* Un'identità gestita **assegnata dall'utente** viene creata come risorsa di Azure autonoma nel tenant di Azure AD considerato attendibile dalla sottoscrizione in uso. Dopo la creazione, l'identità può essere assegnata a una o più risorse di Azure (in Istanze di Azure Container o in altri servizi Azure). Il ciclo di vita di un'identità assegnata dall'utente viene gestito separatamente dal ciclo di vita dei gruppi di contenitori o di altre risorse di servizio a cui l'identità è assegnata. Questo comportamento è particolarmente utile in Istanze di Azure Container. Poiché l'identità si estende oltre il ciclo di vita di un gruppo di contenitori, è possibile riutilizzarla insieme alle altre impostazioni standard per rendere altamente replicabili le distribuzioni di gruppi di contenitori.

* Un'identità gestita **assegnata dal sistema** viene abilitata direttamente in un gruppo di contenitori in Istanze di Azure Container. Quando è abilitata, Azure crea un'identità per il gruppo nel tenant di Azure AD considerato attendibile dalla sottoscrizione dell'istanza. Dopo la creazione dell'identità, viene effettuato il provisioning delle credenziali in ogni contenitore del gruppo. Il ciclo di vita di un'identità assegnata dal sistema è direttamente collegato al gruppo di contenitori in cui l'identità è abilitata. Quando il gruppo viene eliminato, Azure pulisce automaticamente le credenziali e l'identità in Azure AD.

### <a name="use-a-managed-identity"></a>Usare un'identità gestita

Per usare un'identità gestita, è innanzitutto necessario che all'identità venga concesso l'accesso a una o più risorse del servizio di Azure (ad esempio, un'app Web, un insieme di credenziali delle chiavi o un account di archiviazione) nella sottoscrizione. Per accedere a risorse di Azure da un contenitore in esecuzione, è necessario che il codice acquisisca un *token di accesso* da un endpoint di Azure AD. In seguito il codice invia il token di accesso in una chiamata a un servizio che supporta l'autenticazione di Azure AD. 

L'uso di un'identità gestita in un contenitore in esecuzione equivale fondamentalmente a usare un'identità in una macchina virtuale di Azure. Per usare un [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell o l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) o gli [SDK di Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md), vedere le indicazioni sulle macchine virtuali.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.49 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Gli esempi in questo articolo usano un'identità gestita in Istanze di Azure Container per accedere a un segreto di Azure Key Vault. 

Per iniziare, creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus* con il comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) seguente:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Per creare un insieme di credenziali delle chiavi usare il comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Assicurarsi di specificare un nome univoco per l'insieme di credenziali delle chiavi. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Archiviare un segreto di esempio nell'insieme di credenziali delle chiavi usando il comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Continuare con gli esempi seguenti per accedere all'insieme di credenziali delle chiavi usando un'identità gestita assegnata dall'utente o assegnata dal sistema in Istanze di Azure Container.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Esempio 1: usare un'identità assegnata dall'utente per accedere ad Azure Key Vault

### <a name="create-an-identity"></a>Creare un'identità

Innanzitutto creare un'identità nella sottoscrizione usando il comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). È possibile usare lo stesso gruppo di risorse usato per creare l'insieme di credenziali delle chiavi oppure usarne uno diverso.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Per usare l'identità nei passaggi seguenti, usare il comando [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) per archiviare l'ID dell'entità servizio e l'ID risorsa dell'identità nelle variabili.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Abilitare un'identità assegnata dall'utente in un gruppo di contenitori

Eseguire il comando [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) seguente per creare un'istanza di contenitore basata su Ubuntu Server. Questo esempio presenta un gruppo con contenitore singolo che è possibile usare per accedere in modo interattivo agli altri servizi di Azure. Il parametro `--assign-identity` passa al gruppo l'identità gestita assegnata dall'utente. Il comando con esecuzione prolungata mantiene in esecuzione il contenitore. Questo esempio usa lo stesso gruppo di risorse usato per creare l'insieme di credenziali delle chiavi, tuttavia è possibile specificarne uno diverso.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Entro pochi secondi si dovrebbe ricevere una risposta dall'interfaccia della riga di comando di Azure che indica che è stata completata la distribuzione. Controllarne lo stato con il comando [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

La sezione `identity` dell'output è simile alla seguente e mostra che l'identità è impostata nel gruppo di contenitori. `principalID` sotto `userAssignedIdentities` è l'entità servizio dell'identità creata in Azure Active Directory:

```console
...
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
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Concedere all'identità assegnata dall'utente di accedere all'insieme di credenziali delle chiavi

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) seguente per impostare un criterio di accesso nell'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità assegnata dall'utente di ottenere segreti dall'insieme di credenziali delle chiavi:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Usare l'identità assegnata dall'utente per ottenere il segreto dall'insieme di credenziali delle chiavi

A questo punto è possibile usare l'identità gestita per accedere all'insieme di credenziali delle chiavi all'interno dell'istanza di contenitore in esecuzione. Per questo esempio, avviare innanzitutto una shell bash nel contenitore:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Eseguire i comandi seguenti nella shell bash nel contenitore. Per ottenere un token di accesso per usare Azure Active Directory per l'autenticazione all'insieme di credenziali delle chiavi, eseguire il comando seguente:

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

A questo punto è possibile usare il token di accesso per autenticarsi all'insieme di credenziali delle chiavi e leggere un segreto. Assicurarsi di sostituire il nome dell'insieme di credenziali delle chiavi nell'URL ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

La risposta avrà un aspetto simile a quanto riportato di seguito e mostrerà il segreto. Nel codice occorrerebbe analizzare questo output per ottenere il segreto. Quindi, usare il segreto in un'operazione successiva per accedere a un'altra risorsa di Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Esempio 2: usare un'identità assegnata dal sistema per accedere ad Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Abilitare un'identità assegnata dal sistema in un gruppo di contenitori

Eseguire il comando [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) seguente per creare un'istanza di contenitore basata su Ubuntu Server. Questo esempio presenta un gruppo con contenitore singolo che è possibile usare per accedere in modo interattivo agli altri servizi di Azure. Il parametro `--assign-identity` senza valori aggiuntivi abilita un'identità gestita assegnata dal sistema nel gruppo. Il comando con esecuzione prolungata mantiene in esecuzione il contenitore. Questo esempio usa lo stesso gruppo di risorse usato per creare l'insieme di credenziali delle chiavi, tuttavia è possibile specificarne uno diverso.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Entro pochi secondi si dovrebbe ricevere una risposta dall'interfaccia della riga di comando di Azure che indica che è stata completata la distribuzione. Controllarne lo stato con il comando [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

La sezione `identity` nell'output ha un aspetto simile a quanto riportato di seguito e mostra che in Azure Active Directory viene creata un'identità assegnata dal sistema:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Impostare una variabile sul valore di `principalId` (l'ID dell'entità servizio) dell'identità per usarla nei passaggi successivi.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Concedere al gruppo di contenitori di accedere all'insieme di credenziali delle chiavi

Eseguire il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) seguente per impostare un criterio di accesso nell'insieme di credenziali delle chiavi. L'esempio seguente consente all'identità gestita assegnata dal sistema di ricevere segreti dall'insieme di credenziali delle chiavi:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Usare l'identità del gruppo di contenitori per ottenere il segreto dall'insieme di credenziali delle chiavi

A questo punto è possibile usare l'identità gestita per accedere all'insieme di credenziali delle chiavi all'interno dell'istanza di contenitore in esecuzione. Per questo esempio, avviare innanzitutto una shell bash nel contenitore:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Eseguire i comandi seguenti nella shell bash nel contenitore. Per ottenere un token di accesso per usare Azure Active Directory per l'autenticazione all'insieme di credenziali delle chiavi, eseguire il comando seguente:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Output:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Per archiviare il token di accesso in una variabile da usare nei comandi successivi per l'autenticazione, eseguire il comando seguente:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

A questo punto è possibile usare il token di accesso per autenticarsi all'insieme di credenziali delle chiavi e leggere un segreto. Assicurarsi di sostituire il nome dell'insieme di credenziali delle chiavi nell'URL (*https:\//mykeyvault.Vault.Azure.NET/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

La risposta avrà un aspetto simile a quanto riportato di seguito e mostrerà il segreto. Nel codice occorrerebbe analizzare questo output per ottenere il segreto. Quindi, usare il segreto in un'operazione successiva per accedere a un'altra risorsa di Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Abilitare l'identità gestita con un modello di Resource Manager

Per abilitare un'identità gestita in un gruppo di contenitori usando un [modello di Resource Manager](container-instances-multi-container-group.md), impostare la proprietà `identity` dell'oggetto `Microsoft.ContainerInstance/containerGroups` con un oggetto `ContainerGroupIdentity`. I frammenti di codice seguenti mostrano la proprietà `identity` configurata per scenari diversi. Vedere il [riferimento al modello di Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Specificare `2018-10-01` come `apiVersion`.

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
Specificare `2018-10-01` come `apiVersion`.

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
> * Concedere all'identità di accedere ad Azure Key Vault
> * Usare l'identità gestita per accedere a un insieme di credenziali delle chiavi da un contenitore in esecuzione

* Altre informazioni sulle [identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/).

* Vedere un [esempio dell'SDK Azure Go](https://medium.com/@samkreter/c98911206328) sull'uso di un'identità gestita per accedere all'insieme di credenziali delle chiavi da Istanze di Azure Container.
