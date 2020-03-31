---
title: Crittografia inattivi con chiavi gestite dal cliente
description: Informazioni sulla crittografia a riposo del registro dei contenitori di Azure e su come crittografare il Registro di sistema con una chiave gestita dal cliente archiviata in Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498957"
---
# <a name="encryption-using-customer-managed-keys"></a>Crittografia con chiavi gestite dal cliente

Quando si archiviano immagini e altri elementi in un registro contenitori di Azure, Azure crittografa automaticamente il contenuto del Registro di sistema inattivi con [chiavi gestite dal servizio.](../security/fundamentals/encryption-atrest.md#data-encryption-models) You can supplement default encryption with an additional encryption layer using a key that you create and manage in Azure Key Vault. Questo articolo illustra i passaggi usando l'interfaccia della riga di comando di Azure e il portale di Azure.This article walks you through the steps using the Azure CLI and the Azure portal.

La crittografia lato server con chiavi gestite dal cliente è supportata tramite l'integrazione con [Azure Key Vault](../key-vault/key-vault-overview.md). È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con l'insieme di credenziali delle chiavi di Azure è anche possibile controllare l'utilizzo delle chiavi.

Questa funzionalità è disponibile nel livello del servizio registro contenitore **Premium.This** feature is available in the Premium container registry service tier. Per informazioni sui limiti e i livelli del servizio Registro di sistema, vedere SKU del [Registro di sistema del contenitore di Azure.For](container-registry-skus.md)information about registry service tiers and limits, see Azure Container Registry SKUs .

> [!IMPORTANT]
> Questa funzione è attualmente in anteprima e si applicano alcune [limitazioni.](#preview-limitations) Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>
   
## <a name="preview-limitations"></a>Limiti di anteprima 

* Attualmente è possibile abilitare questa funzionalità solo quando si crea un Registro di sistema.
* Dopo aver abilitato una chiave gestita dal cliente in un Registro di sistema, non è possibile disabilitarla.
* [L'attendibilità del](container-registry-content-trust.md) contenuto non è attualmente supportata in un Registro di sistema crittografato con una chiave gestita dal cliente.
* In un Registro di sistema crittografato con una chiave gestita dal cliente, i registri di esecuzione per [le attività ACR](container-registry-tasks-overview.md) vengono attualmente conservati solo per 24 ore. Se è necessario conservare i registri per un periodo più lungo, vedere le linee guida per [esportare e archiviare i registri di esecuzione delle attività](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Prerequisiti

Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è necessaria l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If](/cli/azure/install-azure-cli)you need to install or upgrade, see Install Azure CLI.

## <a name="enable-customer-managed-key---cli"></a>Abilitare la chiave gestita dal cliente - CLIEnable customer-managed key - CLI

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il comando [az group create][az-group-create] per creare un gruppo di risorse per la creazione dell'insieme di credenziali delle chiavi, del registro contenitori e di altre risorse necessarie.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare [un'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) assegnata dall'utente per le risorse di Azure con il comando [az identity create.][az-identity-create] Questa identità verrà utilizzata dal Registro di sistema per accedere al servizio Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Nell'output del comando prendere nota `id` `principalId`dei seguenti valori: e . Questi valori sono necessari nei passaggi successivi per configurare l'accesso al Registro di sistema all'insieme di credenziali delle chiavi.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Per comodità, archiviare questi valori nelle variabili di ambiente:For convenience, store these values in environment variables:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare un insieme di credenziali delle chiavi con [az keyvault creare][az-keyvault-create] per archiviare una chiave gestita dal cliente per la crittografia del Registro di sistema. 

Per evitare la perdita di dati causata da eliminazioni accidentali di chiavi o di credenziali delle chiavi, è necessario attivare le seguenti impostazioni: **Eliminazione temporanea** ed eliminazione **della protezione**. L'esempio seguente include parametri per queste impostazioni:The following example includes parameters for these settings: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Aggiungere criteri di accesso all'insieme di credenziali delle chiaviAdd key vault access policy

Configurare un criterio per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi. Nel comando [az keyvault set-policy][az-keyvault-set-policy] seguente si passa l'ID principale dell'identità gestita creata, archiviata in precedenza in una variabile di ambiente. Impostare le autorizzazioni chiave per **ottenere**, **unwrapKey**e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Crea chiave e ottieni ID chiave

Eseguire il comando [az keyvault create][az-keyvault-key-create] per creare una chiave nell'insieme di credenziali delle chiavi.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Nell'output del comando prendere nota dell'ID della chiave, `kid`. Questo ID viene utilizzato nel passaggio successivo:You use this ID in the next step:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Per comodità, archiviare questo valore in una variabile di ambiente:For convenience, store this value in an environment variable:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Creare un registro con una chiave gestita dal clienteCreate a registry with customer-managed key

Eseguire il comando [az acr create][az-acr-create] per creare un Registro di sistema e abilitare la chiave gestita dal cliente. Passare l'ID entità identità gestita e l'ID chiave, archiviati in precedenza nelle variabili di ambiente:Pass the managed identity principal ID and the key ID, stored previously in environment variables:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostra stato crittografia

Per indicare se la crittografia del Registro di sistema con una chiave gestita dal cliente è abilitata, eseguire il comando [az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Abilitare la chiave gestita dal cliente - portaleEnable customer-managed key - portal

### <a name="create-a-managed-identity"></a>Creare un'identità gestitaCreate a managed identity

Creare [un'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) assegnata dall'utente per le risorse di Azure nel portale di Azure.Create a user-assigned managed identity for Azure resources in the Azure portal. Per la procedura, vedere [Creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Prendere nota del **nome della risorsa** dell'identità gestita. Questo nome è necessario nei passaggi successivi.

![Creare un'identità gestita assegnata dall'utente nel portale di AzureCreate user-assigned managed identity in the Azure portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per la procedura di creazione di un insieme di credenziali delle chiavi, vedere [Guida introduttiva: Impostare e recuperare un segreto dall'insieme](../key-vault/quick-create-portal.md)di credenziali delle chiavi di Azure usando il portale di Azure.For steps to create a key vault, see Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal.

Quando si crea un insieme di credenziali delle chiavi per una chiave gestita dal cliente, nella scheda **Nozioni di base** è necessario abilitare le seguenti impostazioni di protezione: **Protezione temporanea** ed **Eliminazione**protezione . Queste impostazioni consentono di evitare la perdita accidentale di dati o di credenziali delle chiavi.

![Creare un insieme di credenziali delle chiavi nel portale di AzureCreate key vault in the Azure portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Aggiungere criteri di accesso all'insieme di credenziali delle chiaviAdd key vault access policy

Configurare un criterio per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi.

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare Criteri di accesso **alle impostazioni** > **> Aggiungi criteri**di accesso .
1. Selezionare **Autorizzazioni chiave**e selezionare **Ottieni**, Annulla ritorno **a capo**e Testo **a capo**.
1. Selezionare **Seleziona entità** e selezionare il nome della risorsa dell'identità gestita assegnata dall'utente.  
1. Selezionare **Aggiungi**, quindi **Salva**.

![Creare criteri di accesso all'insieme di credenziali delle chiaviCreate key vault access policy](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Creare la chiave

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **tasti**.
1. Selezionare **Genera/Importa** e immettere un nome univoco per la chiave.
1. Accettare i restanti valori predefiniti e selezionare **Crea**.
1. Dopo la creazione, selezionare la chiave e prendere nota della versione della chiave corrente.

### <a name="create-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

1. Selezionare **Create a resource** > **Containers** > **Container Registry**.
1. Nella scheda **Nozioni di base** selezionare o creare un gruppo di risorse e immettere un nome del Registro di sistema. In **SKU**selezionare **Premium**.
1. Nella scheda **Crittografia,** in **Chiave gestita dal cliente,** selezionare **Abilitato**.
1. In **Identità**selezionare l'identità gestita creata.
1. In **Chiave di crittografia**selezionare Seleziona da Vault **chiave**.
1. Nella finestra **Seleziona chiave dall'insieme** di credenziali delle chiavi di Azure selezionare l'insieme di credenziali delle chiavi, la chiave e la versione creati nella sezione precedente.
1. Nella scheda **Crittografia,** selezionare **Revisione e creazione**.
1. Selezionare **Crea** per distribuire l'istanza del Registro di sistema.

![Creare il registro contenitori nel portale di Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Per visualizzare lo stato di crittografia del Registro di sistema nel portale, passare al Registro di sistema. In **Impostazioni**selezionare **Crittografia (anteprima)**.

## <a name="enable-customer-managed-key---template"></a>Abilita chiave gestita dal cliente - modello

È anche possibile usare un modello di Resource Manager per creare un Registro di sistema e abilitare la crittografia con una chiave gestita dal cliente. 

Il modello seguente crea un nuovo registro contenitori e un'identità gestita assegnata dall'utente. Copiare il contenuto seguente in un nuovo file `CMKtemplate.json`e salvarlo utilizzando un nome di file, ad esempio .

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Seguire i passaggi nelle sezioni precedenti per creare le risorse seguenti:Follow the steps in the previous sections to create the following resources:

* Insieme di credenziali delle chiavi, identificato in base al nome
* Key vault key, identified by key ID

Eseguire il comando [az group deployment create][az-group-deployment-create] seguente per creare il Registro di sistema utilizzando il file modello precedente. Dove indicato, specificare un nuovo nome del Registro di sistema e un nome di identità gestita, nonché il nome dell'insieme di credenziali delle chiavi e l'ID della chiave creati. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Mostra stato crittografia

Per visualizzare lo stato della crittografia del Registro di sistema, eseguire il comando [az acr encryption show-status][az-acr-encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Utilizzare il Registro di sistema

Dopo aver abilitato un Registro di sistema per crittografare i dati utilizzando una chiave gestita dal cliente, è possibile eseguire le stesse operazioni del Registro di sistema eseguite in un Registro di sistema non crittografato con una chiave gestita dal cliente. Ad esempio, è possibile eseguire l'autenticazione con il Registro di sistema e le immagini Docker push. Vedere Comandi di esempio in [Spingere e trascinare un'immagine](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Tasto Ruota

È possibile ruotare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure in base ai criteri di conformità. Creare una nuova chiave e quindi aggiornare il Registro di sistema per crittografare i dati utilizzando la nuova chiave. È possibile eseguire questi passaggi usando l'interfaccia della riga di comando di Azure o nel portale.

Ad esempio, eseguire il comando [az keyvault key create][az-keyvault-key-create] per creare una nuova chiave:For example, run the az keyvault key create command to create a new key:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Eseguire quindi il comando [az acr encryption rotate-key,][az-acr-encryption-rotate-key] passando il nuovo ID chiave e l'ID principale dell'identità gestita configurata in precedenza:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Chiave di revoca

Revocare la chiave di crittografia gestita dal cliente modificando i criteri di accesso nell'insieme di credenziali delle chiavi o eliminando la chiave. Ad esempio, utilizzare il comando [az keyvault delete-policy][az-keyvault-delete-policy] per modificare i criteri di accesso dell'identità gestita utilizzata dal Registro di sistema. Ad esempio:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

La revoca della chiave blocca in modo efficace l'accesso a tutti i dati del Registro di sistema, poiché il Registro di sistema non può accedere alla chiave di crittografia. Se l'accesso alla chiave è abilitato o la chiave eliminata viene ripristinata, il Registro di sistema selezionerà la chiave in modo da poter accedere nuovamente ai dati crittografati del Registro di sistema.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [crittografia inattivi in Azure](../security/fundamentals/encryption-atrest.md).Learn more about encryption at rest in Azure .
* Ulteriori informazioni sui criteri di accesso e su come [proteggere l'accesso a un insieme di credenziali delle chiavi.](../key-vault/key-vault-secure-your-key-vault.md)
* Per fornire commenti e suggerimenti sulle chiavi gestite dal cliente per il Registro di sistema del contenitore di Azure, visitare il [sito ACR GitHub](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
