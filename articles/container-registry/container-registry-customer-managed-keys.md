---
title: Crittografia inattiva con chiavi gestite dal cliente
description: Informazioni sulla crittografia al resto del registro contenitori di Azure e su come crittografare il registro con una chiave gestita dal cliente archiviata in Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 8bce77c776fe088e5c317f02cd2757738a287069
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096569"
---
# <a name="encryption-using-customer-managed-keys"></a>Crittografia con chiavi gestite dal cliente

Quando si archiviano immagini e altri elementi in un registro contenitori di Azure, Azure crittografa automaticamente il contenuto del registro di sistema inattivo con le [chiavi gestite dal servizio](../security/fundamentals/encryption-atrest.md#data-encryption-models). È possibile integrare la crittografia predefinita con un livello di crittografia aggiuntivo usando una chiave creata e gestita in Azure Key Vault. Questo articolo illustra la procedura con l'interfaccia della riga di comando di Azure e il portale di Azure.

La crittografia lato server con chiavi gestite dal cliente è supportata tramite l'integrazione con [Azure Key Vault](../key-vault/key-vault-overview.md). È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault è anche possibile controllare l'utilizzo della chiave.

Questa funzionalità è disponibile nel livello di servizio del registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e si applicano alcune [limitazioni](#preview-limitations) . Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>
   
## <a name="preview-limitations"></a>Limiti di anteprima 

* Attualmente è possibile abilitare questa funzionalità solo quando si crea un registro di sistema.
* Dopo aver abilitato una chiave gestita dal cliente in un registro, non è possibile disabilitarla.
* In un registro crittografato con una chiave gestita dal cliente, i log di esecuzione per le [attività ACR](container-registry-tasks-overview.md) sono attualmente conservati solo per 24 ore. Se è necessario conservare i log per un periodo di tempo più lungo, vedere linee guida per [esportare e archiviare i log di esecuzione dell'attività](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Prerequisites

Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è necessaria l'interfaccia della riga di comando di Azure versione 2.2.0 Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Abilitare la chiave gestita dal cliente-interfaccia della riga di comando

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il comando [AZ Group create][az-group-create] per creare un gruppo di risorse per la creazione dell'insieme di credenziali delle chiavi, del registro contenitori e di altre risorse richieste.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente [per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) con il comando [AZ Identity create][az-identity-create] . Questa identità verrà usata dal registro di sistema per accedere al servizio Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Nell'output del comando prendere nota dei valori seguenti: `id` e `principalId`. Questi valori sono necessari nei passaggi successivi per configurare l'accesso al registro di sistema all'insieme di credenziali delle chiavi.

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

Per praticità, archiviare questi valori nelle variabili di ambiente:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare un insieme di credenziali delle chiavi con [AZ Key Vault create][az-keyvault-create] per archiviare una chiave gestita dal cliente per la crittografia del registro di sistema. 

Per evitare la perdita di dati causata da eliminazioni accidentali di chiavi o Key Vault, è necessario abilitare le impostazioni seguenti: **eliminazione** temporanea e **ripulitura della protezione**. L'esempio seguente include i parametri per queste impostazioni: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Aggiungere i criteri di accesso di Key Vault

Configurare i criteri per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi. Nel comando [AZ per set-Policy][az-keyvault-set-policy] seguente viene passato l'ID entità dell'identità gestita creata, archiviata in precedenza in una variabile di ambiente. Impostare le autorizzazioni chiave per **Get**, **unwrapKey**e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Crea chiave e Ottieni ID chiave

Eseguire il comando [AZ Key Vault Key create][az-keyvault-key-create] per creare una chiave nell'insieme di credenziali delle chiavi.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Nell'output del comando prendere nota dell'ID della chiave `kid`. Questo ID verrà usato nel passaggio successivo:

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
Per praticità, archiviare questo valore in una variabile di ambiente:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Creare un registro con una chiave gestita dal cliente

Eseguire il comando [AZ ACR create][az-acr-create] per creare un registro e abilitare la chiave gestita dal cliente. Passare l'ID dell'entità di identità gestita e l'ID chiave, archiviati in precedenza nelle variabili di ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostra stato crittografia

Per indicare se la crittografia del registro di sistema con una chiave gestita dal cliente è abilitata, eseguire il comando [AZ ACR Encryption Show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Abilitare la chiave gestita dal cliente-portale

### <a name="create-a-managed-identity"></a>Creare un'identità gestita

Creare un'identità gestita assegnata dall'utente [per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) nel portale di Azure. Per i passaggi, vedere [creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Prendere nota del **nome della risorsa** dell'identità gestita. Questo nome sarà necessario nei passaggi successivi.

![Creare un'identità gestita assegnata dall'utente nell'portale di Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per i passaggi necessari per creare un insieme di credenziali delle chiavi, vedere [Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault usando il portale di Azure](../key-vault/quick-create-portal.md).

Quando si crea un insieme di credenziali delle chiavi per una chiave gestita dal cliente, nella scheda **nozioni di base** è necessario abilitare le impostazioni di protezione seguenti: **eliminazione** temporanea e **ripulitura**. Queste impostazioni consentono di evitare la perdita di dati causata da eliminazioni accidentali di chiavi o Key Vault.

![Creare un insieme di credenziali delle chiavi nella portale di Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Aggiungere i criteri di accesso di Key Vault

Configurare i criteri per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi.

1. Passare a Key Vault.
1. Selezionare **impostazioni** > **criteri di accesso > + Aggiungi criteri di accesso**.
1. Selezionare **autorizzazioni chiave**e selezionare **Get**, **Unwrap Key**e **Wrap Key**.
1. Selezionare **Seleziona entità** e selezionare il nome della risorsa dell'identità gestita assegnata dall'utente.  
1. Selezionare **Aggiungi**, quindi fare clic su **Salva**.

![Creare i criteri di accesso di Key Vault](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Creare la chiave

1. Passare a Key Vault.
1. Selezionare **impostazioni** > **chiavi**.
1. Selezionare **+ genera/importa** e immettere un nome univoco per la chiave.
1. Accettare i valori predefiniti rimanenti e selezionare **Crea**.
1. Dopo la creazione, selezionare la chiave e prendere nota della versione della chiave corrente.

### <a name="create-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

1. Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.
1. Nella scheda **nozioni di base** selezionare o creare un gruppo di risorse e immettere un nome del registro di sistema. In **SKU**selezionare **Premium**.
1. Nella scheda **crittografia** , in **chiave gestita dal cliente**, selezionare **abilitato**.
1. In **Identity (identità**) selezionare l'identità gestita creata.
1. In **chiave di crittografia**selezionare **Seleziona da Key Vault**.
1. Nella finestra **Seleziona chiave da Azure Key Vault** selezionare l'insieme di credenziali delle chiavi, la chiave e la versione creati nella sezione precedente.
1. Nella scheda **crittografia** selezionare **Verifica + crea**.
1. Selezionare **Crea** per distribuire l'istanza del registro di sistema.

![Creare il registro contenitori nel portale di Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Per visualizzare lo stato di crittografia del registro di sistema nel portale, passare al registro di sistema. In **Impostazioni**selezionare **crittografia (anteprima)** .

## <a name="enable-customer-managed-key---template"></a>Abilitare il modello di chiave gestita dal cliente

È anche possibile usare un modello di Gestione risorse per creare un registro di sistema e abilitare la crittografia con una chiave gestita dal cliente. 

Il modello seguente crea un nuovo registro contenitori e un'identità gestita assegnata dall'utente. Copiare il contenuto seguente in un nuovo file e salvarlo utilizzando un nome file, ad esempio `CMKtemplate.json`.

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

Seguire i passaggi nelle sezioni precedenti per creare le risorse seguenti:

* Key Vault, identificato dal nome
* Chiave dell'insieme di credenziali delle chiavi, identificata dall'ID chiave

Eseguire il comando [AZ Group Deployment create][az-group-deployment-create] seguente per creare il registro di sistema usando il file di modello precedente. Dove indicato, fornire un nuovo nome del registro di sistema e il nome identità gestita, nonché il nome dell'insieme di credenziali delle chiavi e l'ID chiave creati. 

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

Per visualizzare lo stato della crittografia del registro di sistema, eseguire il comando [AZ ACR Encryption show-status] [AZ-ACR-Encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Usare il registro di sistema

Dopo aver abilitato un registro per la crittografia dei dati tramite una chiave gestita dal cliente, è possibile eseguire le stesse operazioni del registro di sistema eseguite in un registro non crittografato con una chiave gestita dal cliente. Ad esempio, è possibile eseguire l'autenticazione con il registro di sistema e le immagini Docker push. Vedere comandi di esempio in [push ed estrazione di un'immagine](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Ruota chiave

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Creare una nuova chiave e quindi aggiornare il registro di sistema per crittografare i dati usando la nuova chiave. È possibile eseguire questi passaggi usando l'interfaccia della riga di comando di Azure o nel portale.

Ad esempio, eseguire il comando [AZ Key Vault Key create][az-keyvault-key-create] per creare una nuova chiave:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Eseguire quindi il comando [AZ ACR Encryption ruot-Key][az-acr-encryption-rotate-key] , passando il nuovo ID chiave e l'ID entità dell'identità gestita configurata in precedenza:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Revoca chiave

Revocare la chiave di crittografia gestita dal cliente modificando il criterio di accesso nell'insieme di credenziali delle chiavi o eliminando la chiave. Ad esempio, usare il comando [AZ chiave Vault Delete-Policy][az-keyvault-delete-policy] per modificare i criteri di accesso dell'identità gestita usata dal registro di sistema. Ad esempio:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

La revoca della chiave blocca efficacemente l'accesso a tutti i dati del registro di sistema, poiché il registro di sistema non può accedere alla chiave di crittografia. Se l'accesso alla chiave è abilitato o viene ripristinata la chiave eliminata, il registro di sistema selezionerà la chiave in modo che sia possibile accedere nuovamente ai dati crittografati del registro di sistema.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla crittografia dei tuoi inattivi [in Azure](../security/fundamentals/encryption-atrest.md).
* Altre informazioni sui criteri di accesso e su come [proteggere l'accesso a un insieme di](../key-vault/key-vault-secure-your-key-vault.md)credenziali delle chiavi.
* Per inviare commenti e suggerimenti sulle chiavi gestite dal cliente per Container Registry di Azure, visitare il [sito GitHub di ACR](https://aka.ms/acr/issues).


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
[az-keyvault-key-create]: /cli/azure/keyvault/keyt#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault/keyt#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault/keyt#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
