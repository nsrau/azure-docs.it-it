---
title: Crittografa il registro di sistema con una chiave gestita dal cliente
description: Informazioni sulla crittografia al resto del registro contenitori di Azure e su come crittografare il registro di sistema Premium con una chiave gestita dal cliente archiviata in Azure Key Vault
ms.topic: article
ms.date: 11/17/2020
ms.custom: ''
ms.openlocfilehash: 6dac2239f223b5dee6ec728833caa01562873210
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255021"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Crittografare il registro usando una chiave gestita dal cliente

Quando si archiviano immagini e altri artefatti in un registro contenitori di Azure, Azure crittografa automaticamente il contenuto inattivo del registro con le [chiavi gestite dal servizio](../security/fundamentals/encryption-models.md). È possibile integrare la crittografia predefinita con un livello di crittografia aggiuntivo usando una chiave creata e gestita in Azure Key Vault (chiave gestita dal cliente). Questo articolo illustra i passaggi usando l'interfaccia della riga di comando di Azure e il portale di Azure.

La crittografia lato server con chiavi gestite dal cliente è supportata tramite l'integrazione con [Azure Key Vault](../key-vault/general/overview.md): 

* È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare le chiavi. 
* Con Azure Key Vault è anche possibile controllare l'utilizzo delle chiavi.
* Azure Container Registry supporta la rotazione automatica delle chiavi di crittografia del registro di sistema quando è disponibile una nuova versione della chiave nell'Azure Key Vault. È anche possibile ruotare manualmente le chiavi di crittografia del registro di sistema.

Questa funzionalità è disponibile nel livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).


## <a name="things-to-know"></a>Informazioni importanti

* Attualmente è possibile abilitare una chiave gestita dal cliente solo quando si crea un registro. Quando si Abilita la chiave, viene configurata un'identità gestita *assegnata dall'utente* per accedere all'insieme di credenziali delle chiavi.
* Dopo aver abilitato la crittografia con una chiave gestita dal cliente in un registro, non è possibile disabilitare la crittografia.  
* Azure Container Registry supporta solo le chiavi RSA o RSA-HSM. Le chiavi a curva ellittica non sono attualmente supportate.
* L'[attendibilità del contenuto](container-registry-content-trust.md) non è attualmente supportata in un registro crittografato con una chiave gestita dal cliente.
* In un registro crittografato con una chiave gestita dal cliente, i log di esecuzione per le [Attività del Registro Azure Container](container-registry-tasks-overview.md) attualmente vengono conservati solo per 24 ore. Se è necessario conservare i log per un periodo di tempo più lungo, vedere le istruzioni per [esportare e archiviare i log di esecuzione delle attività](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Se l'accesso all'insieme di credenziali delle chiavi di Azure viene limitato usando una rete virtuale con un [firewall di Key Vault](../key-vault/general/network-security.md), sono necessari passaggi di configurazione aggiuntivi. Dopo la creazione del registro di sistema e l'abilitazione della chiave gestita dal cliente, configurare l'accesso alla chiave utilizzando l'identità gestita *assegnata dal sistema* del registro di sistema e configurare il registro di sistema in modo da ignorare il firewall Key Vault. Attenersi alla procedura descritta in questo articolo per abilitare la crittografia con una chiave gestita dal cliente e quindi vedere le linee guida per [uno scenario avanzato: Key Vault firewall](#advanced-scenario-key-vault-firewall) più avanti in questo articolo.

## <a name="automatic-or-manual-update-of-key-versions"></a>Aggiornamento automatico o manuale delle versioni chiave

Una considerazione importante per la sicurezza di un registro crittografato con una chiave gestita dal cliente è la frequenza con cui si aggiorna (ruota) la chiave di crittografia. L'organizzazione potrebbe disporre di criteri di conformità che richiedono aggiornamenti regolari delle [versioni](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) delle chiavi archiviate nel Azure Key Vault quando vengono usate come chiavi gestite dal cliente. 

Quando si configura la crittografia del registro di sistema con una chiave gestita dal cliente, sono disponibili due opzioni per l'aggiornamento della versione della chiave usata per la crittografia:

* **Aggiorna automaticamente la versione della chiave** : per aggiornare automaticamente una chiave gestita dal cliente quando è disponibile una nuova versione in Azure Key Vault, omettere la versione della chiave quando si Abilita la crittografia del registro di sistema con una chiave gestita dal cliente. Quando un registro di sistema è crittografato con una chiave senza versione, Azure Container Registry controlla periodicamente l'insieme di credenziali delle chiavi per una nuova versione della chiave e aggiorna la chiave gestita dal cliente entro un'ora. Azure Container Registry usa automaticamente la versione più recente della chiave.

* **Aggiornare manualmente la versione della chiave** : per usare una versione specifica di una chiave per la crittografia del registro di sistema, specificare la versione della chiave quando si Abilita la crittografia del registro di sistema con una chiave gestita dal cliente. Quando un registro di sistema è crittografato con una versione di chiave specifica, Azure Container Registry usa tale versione per la crittografia fino a quando non si ruota manualmente la chiave gestita dal cliente.

> [!NOTE]
> Attualmente è possibile usare l'interfaccia della riga di comando di Azure solo per configurare il registro di sistema in modo da aggiornare automaticamente la versione della chiave gestita dal cliente. Quando si usa il portale per abilitare la crittografia, è necessario aggiornare manualmente la versione della chiave.

Per informazioni dettagliate, vedere [scegliere l'ID chiave con o senza](#choose-key-id-with-or-without-key-version) la versione della chiave e la [versione della chiave di aggiornamento](#update-key-version)più avanti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo è necessaria la versione 2.2.0 o successiva dell'interfaccia della riga di comando di Azure o Azure Cloud Shell Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Abilitare la chiave gestita dal cliente - interfaccia della riga di comando

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il comando [az group create][az-group-create] per creare un gruppo di risorse per la creazione dell'insieme di credenziali delle chiavi, del registro contenitori e di altre risorse richieste.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'[identità gestita assegnata dall'utente per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) con il comando [az identity create][az-identity-create]. Questa identità verrà usata dal registro per accedere al servizio Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Nell'output del comando prendere nota dei valori seguenti: `id` e `principalId`. Questi valori sono necessari nei passaggi successivi per configurare l'accesso del registro all'insieme di credenziali delle chiavi.

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

Creare un insieme di credenziali delle chiavi con [az keyvault create][az-keyvault-create] per archiviare una chiave gestita dal cliente per la crittografia del registro.

Per impostazione predefinita, l'impostazione di **eliminazione** temporanea viene abilitata automaticamente in un nuovo insieme di credenziali delle chiavi. Per evitare la perdita di dati causata da eliminazioni accidentali di chiavi o Key Vault, abilitare anche l'impostazione **Ripulisci protezione** :

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Per l'uso nei passaggi successivi, ottenere l'ID risorsa dell'insieme di credenziali delle chiavi:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Abilitare l'accesso all'insieme di credenziali delle chiavi

Configurare un criterio per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi. Nel comando [az keyvault set-policy][az-keyvault-set-policy] che segue viene passato l'ID entità di sicurezza dell'identità gestita creata, archiviato in precedenza in una variabile di ambiente. Impostare le autorizzazioni delle chiavi per **get**, **unwrapKey** e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

In alternativa, usare il controllo degli accessi in base al ruolo di [Azure per Key Vault](../key-vault/general/rbac-guide.md) (anteprima) per assegnare autorizzazioni all'identità per accedere all'insieme di credenziali delle chiavi Ad esempio, assegnare il ruolo crittografia servizio Key Vault Crypto all'identità usando il comando [AZ Role Assignment create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Creare la chiave e ottenere l'ID chiave

Per creare una chiave nell'insieme di credenziali delle chiavi, eseguire il comando [az keyvault key create][az-keyvault-key-create].

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Nell'output del comando prendere nota dell'ID della chiave, `kid`. Questo ID verrà usato nel passaggio successivo:

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
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Scegliere l'ID chiave con o senza la versione della chiave

Per praticità, archiviare il formato scelto per l'ID chiave nella variabile di ambiente $keyID. È possibile usare un ID chiave con una versione o una chiave senza una versione.

#### <a name="manual-key-rotation---key-id-with-version"></a>Rotazione manuale chiave-ID chiave con versione

Quando viene usata per crittografare un registro con una chiave gestita dal cliente, questa chiave consente solo la rotazione manuale della chiave in Azure Container Registry.

In questo esempio viene archiviata la proprietà della chiave `kid` :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Rotazione automatica della chiave-ID chiave che omette la versione 

Quando viene usata per crittografare un registro con una chiave gestita dal cliente, questa chiave Abilita la rotazione automatica della chiave quando viene rilevata una nuova versione della chiave in Azure Key Vault.

In questo esempio la versione viene rimossa dalla `kid` proprietà della chiave:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Creare un registro con la chiave gestita dal cliente

Eseguire il comando [az acr create][az-acr-create] per creare un registro nel livello di servizio Premium e abilitare la chiave gestita dal cliente. Passare l'ID identità gestita e l'ID chiave, archiviati in precedenza nelle variabili di ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Visualizzare lo stato della crittografia

Per indicare se la crittografia del registro con una chiave gestita dal cliente è abilitata, eseguire il comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

A seconda della chiave utilizzata per crittografare il registro di sistema, l'output è simile al seguente:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Abilitare la chiave gestita dal cliente - portale

### <a name="create-a-managed-identity"></a>Creare un'identità gestita

Creare un'[identità gestita assegnata dall'utente per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) nel portale di Azure. Per la procedura, vedere [Creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Il nome dell'identità verrà usato nei passaggi successivi.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Creare un'identità assegnata dall'utente nel portale di Azure":::

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per i passaggi necessari per creare un insieme di credenziali delle chiavi, vedere [Guida introduttiva: creare un Azure Key Vault con il portale di Azure](../key-vault/general/quick-create-portal.md).

Quando si crea un insieme di credenziali delle chiavi per una chiave gestita dal cliente, nella scheda **nozioni di base** abilitare l'impostazione **Ripulisci protezione** . Questa impostazione consente di evitare la perdita di dati causata da eliminazioni accidentali di chiavi o Key Vault.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Creare un insieme di credenziali delle chiavi nel portale di Azure":::

### <a name="enable-key-vault-access"></a>Abilitare l'accesso all'insieme di credenziali delle chiavi

Configurare un criterio per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi.

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **Criteri di accesso > +Aggiungi un criterio di accesso**.
1. Selezionare **Autorizzazioni chiave**, quindi **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.
1. In **Seleziona entità** selezionare il nome della risorsa dell'identità gestita assegnata dall'utente.  
1. Selezionare **Aggiungi** e quindi **Salva**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Creare un criterio di accesso dell'insieme di credenziali delle chiavi":::

In alternativa, usare il controllo degli accessi in base al ruolo di [Azure per Key Vault](../key-vault/general/rbac-guide.md) (anteprima) per assegnare autorizzazioni all'identità per accedere all'insieme di credenziali delle chiavi Ad esempio, assegnare il ruolo crittografia servizio Key Vault Crypto all'identità.

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **controllo di accesso (IAM)**  >  **+ Aggiungi**  >  **assegnazione ruolo**.
1. Nella finestra **Aggiungi assegnazione ruolo** :
    1. Selezionare **Key Vault ruolo crittografia servizio di crittografia (anteprima)** . 
    1. Assegnare l'accesso all' **identità gestita assegnata dall'utente**.
    1. Selezionare il nome della risorsa dell'identità gestita assegnata dall'utente e selezionare **Salva**.

### <a name="create-key"></a>Creare la chiave

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **Chiavi**.
1. Selezionare **+Genera/Importa** e immettere un nome univoco per la chiave.
1. Accettare i rimanenti valori predefiniti e selezionare **Crea**.
1. Al termine della creazione, selezionare la chiave e prendere nota della versione corrente della chiave.

### <a name="create-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

1. Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.
1. Nella scheda **Nozioni di base** selezionare o creare un gruppo di risorse e immettere un nome di registro. In **SKU** selezionare **Premium**.
1. Nella scheda **Crittografia**, selezionare **Abilitata** in **Chiave gestita dal cliente**.
1. In **Identità** selezionare l'identità gestita creata.
1. In **Crittografia** selezionare **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Nella finestra **Selezionare la chiave da Azure Key Vault** selezionare l'insieme di credenziali delle chiavi, la chiave e la versione creati nella sezione precedente.
1. Nella scheda **Crittografia** selezionare **Rivedi e crea**.
1. Selezionare **Crea** per creare l'istanza del registro di sistema.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Creare un registro crittografato nel portale di Azure":::

Per visualizzare lo stato della crittografia del registro nel portale, passare al registro. In **Impostazioni** selezionare **Crittografia**.

## <a name="enable-customer-managed-key---template"></a>Abilitare la chiave gestita dal cliente - modello

È anche possibile usare un modello di Resource Manager per creare un registro e abilitare la crittografia con una chiave gestita dal cliente.

Il modello seguente crea un nuovo registro contenitori e un'identità gestita assegnata dall'utente. Copiare il contenuto seguente in un nuovo file e salvarlo usando un nome file, ad esempio `CMKtemplate.json`.

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

Seguire i passaggi delle sezioni precedenti per creare le risorse seguenti:

* Insieme di credenziali delle chiavi, identificato dal nome
* Insieme di credenziali delle chiavi, identificato dall'ID chiave

Eseguire il comando [AZ Deployment Group create][az-deployment-group-create] seguente per creare il registro di sistema usando il file di modello precedente. Dove indicato, specificare un nuovo nome del registro e il nome dell'identità gestita, nonché il nome dell'insieme di credenziali delle chiavi e l'ID chiave creato.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Visualizzare lo stato della crittografia

Per visualizzare lo stato della crittografia del registro, eseguire il comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Usare il registro

Dopo aver abilitato una chiave gestita dal cliente in un registro, è possibile eseguire le stesse operazioni del registro che si eseguono in un registro non crittografato con una chiave gestita dal cliente. Ad esempio, è possibile eseguire l'autenticazione con il registro ed effettuare il push delle immagini Docker. Vedere i comandi di esempio in [Effettuare il push e il pull di un'immagine](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Ruotare la chiave

Aggiornare la versione della chiave in Azure Key Vault o creare una nuova chiave, quindi aggiornare il registro di sistema per crittografare i dati usando la chiave. È possibile eseguire questi passaggi usando l'interfaccia della riga di comando di Azure o il portale.

Quando si ruota una chiave, in genere si specifica la stessa identità usata durante la creazione del registro. Facoltativamente, configurare una nuova identità assegnata dall'utente per l'accesso alla chiave o abilitare e specificare l'identità assegnata dal sistema del registro.

> [!NOTE]
> Assicurarsi che l'accesso all'insieme di credenziali delle [chiavi](#enable-key-vault-access) richiesto sia impostato per l'identità configurata per l'accesso alla chiave.

### <a name="update-key-version"></a>Aggiornare la versione della chiave

Uno scenario comune consiste nell'aggiornare la versione della chiave utilizzata come chiave gestita dal cliente. A seconda di come viene configurata la crittografia del registro di sistema, la chiave gestita dal cliente in Azure Container Registry viene aggiornata automaticamente oppure deve essere aggiornata manualmente.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare i comandi [az keyvault key][az-keyvault-key] per creare o gestire le chiavi dell'insieme di credenziali delle chiavi. Per creare una nuova versione della chiave, eseguire il comando [AZ Key Vault Key create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Il passaggio successivo dipende dalla configurazione della crittografia del registro di sistema:

* Se il registro di sistema è configurato per rilevare gli aggiornamenti della versione della chiave, la chiave gestita dal cliente viene aggiornata automaticamente entro 1 ora.

* Se il registro di sistema è configurato in modo da richiedere l'aggiornamento manuale per una nuova versione della chiave, eseguire il comando [AZ ACR Encryption ruota-Key][az-acr-encryption-rotate-key] , passando il nuovo ID chiave e l'identità che si vuole configurare:

Per aggiornare manualmente la versione della chiave gestita dal cliente:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Quando si esegue `az acr encryption rotate-key` , è possibile passare un ID chiave con versione o un ID chiave senza versione. Se si usa un ID chiave senza versione, il registro di sistema viene configurato per rilevare automaticamente gli aggiornamenti della versione della chiave in seguito.

### <a name="portal"></a>Portale

Usare le impostazioni di **crittografia** del registro di sistema per aggiornare l'insieme di credenziali delle chiavi, la chiave o le impostazioni di identità usate per la chiave gestita dal cliente.

Ad esempio, per configurare una nuova chiave:

1. Passare al registro nel portale.
1. In **Impostazioni** selezionare **Crittografia** > **Modifica chiave**.
1. Selezionare **Seleziona chiave**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Ruotare la chiave nel portale di Azure":::
1. Nella finestra **Selezionare la chiave da Azure Key Vault** selezionare l'insieme di credenziali delle chiavi e la chiave configurati in precedenza e in **Versione** selezionare **Crea nuovo**.
1. Nella finestra **Creare una chiave** selezionare **Genera** e quindi **Crea**.
1. Completare la selezione della chiave e selezionare **Salva**.

## <a name="revoke-key"></a>Revocare la chiave

Revocare la chiave di crittografia gestita dal cliente modificando il criterio di accesso o le autorizzazioni per l'insieme di credenziali delle chiavi oppure eliminando la chiave. Ad esempio, usare il comando [az keyvault delete-policy][az-keyvault-delete-policy] per modificare il criterio di accesso dell'identità gestita usata dal registro:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

La revoca della chiave blocca efficacemente l'accesso a tutti i dati del registro, poiché il registro non può accedere alla chiave di crittografia. Se l'accesso alla chiave è abilitato o viene ripristinata la chiave eliminata, il registro selezionerà la chiave in modo che sia possibile accedere di nuovo ai dati crittografati del registro.

## <a name="advanced-scenario-key-vault-firewall"></a>Scenario avanzato: firewall Key Vault

Se l'insieme di credenziali delle chiavi di Azure viene distribuito in una rete virtuale con un firewall Key Vault, seguire questa procedura aggiuntiva dopo aver abilitato la crittografia delle chiavi gestita dal cliente nel registro di sistema.

1. Configurare la crittografia del registro di sistema per usare l'identità assegnata dal sistema del registro di sistema
1. Abilitare il registro di sistema per ignorare il firewall Key Vault
1. Ruotare la chiave gestita dal cliente

### <a name="configure-system-assigned-identity"></a>Configurare l'identità assegnata dal sistema

È possibile configurare l'identità gestita assegnata dal sistema di un registro per accedere all'insieme di credenziali delle chiavi per le chiavi di crittografia. Se non si ha familiarità con i diversi tipi di identità gestita per le risorse di Azure, vedere la [panoramica](../active-directory/managed-identities-azure-resources/overview.md).

Per abilitare l'identità assegnata dal sistema del registro nel portale:

1. Passare al registro nel portale.
1. Selezionare **Impostazioni** >  **Identità**.
1. In **Assegnata dal sistema** impostare **Stato** su **Attiva**. Selezionare **Salva**.
1. Copiare l'**ID oggetto** dell'identità.

Per concedere all'identità l'accesso all'insieme di credenziali delle chiavi:

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **Criteri di accesso > +Aggiungi un criterio di accesso**.
1. Selezionare **Autorizzazioni chiave**, quindi **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.
1. Scegliere **Seleziona entità** e cercare l'ID oggetto dell'identità gestita assegnata dal sistema oppure il nome del registro.  
1. Selezionare **Aggiungi** e quindi **Salva**.

Per aggiornare le impostazioni di crittografia del registro per l'uso dell'identità:

1. Passare al registro nel portale.
1. In **Impostazioni** selezionare **Crittografia** > **Modifica chiave**.
1. In **Identità** selezionare **Assegnata dal sistema** e selezionare **Salva**.

### <a name="enable-key-vault-bypass"></a>Abilitare il bypass del Key Vault

Per accedere a un insieme di credenziali delle chiavi configurato con un firewall Key Vault, il registro di sistema deve ignorare il firewall. Verificare che l'insieme di credenziali delle chiavi sia configurato per consentire l'accesso da parte di qualsiasi [servizio attendibile](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry è uno dei servizi attendibili.

1. Nel portale passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni**  >  **rete**.
1. Confermare, aggiornare o aggiungere le impostazioni della rete virtuale. Per la procedura dettagliata, vedere [Configurare reti virtuali e firewall di Azure Key Vault](../key-vault/general/network-security.md).
1. In **Consenti a Microsoft Trusted Services di ignorare questo firewall**, selezionare **Sì**. 

### <a name="rotate-the-customer-managed-key"></a>Ruotare la chiave gestita dal cliente

Dopo aver completato i passaggi precedenti, ruotare la chiave in una nuova chiave nell'insieme di credenziali delle chiavi protetto da un firewall. Per la procedura, vedere [ruotare la chiave](#rotate-key) in questo articolo.

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="removing-user-assigned-identity"></a>Rimozione dell'identità assegnata dall'utente

Se si tenta di rimuovere un'identità assegnata dall'utente da un registro di sistema usato per la crittografia, è possibile che venga visualizzato un messaggio di errore simile al seguente:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Non sarà possibile modificare (ruotare) la chiave di crittografia. Se si verifica questo problema, prima di tutto riassegnare l'identità utilizzando il GUID visualizzato nel messaggio di errore. Ad esempio:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Quindi, dopo aver modificato la chiave e aver assegnato un'identità diversa, è possibile rimuovere l'identità originale assegnata dall'utente.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md).
* Altre informazioni sui criteri di accesso e su come [proteggere l'accesso a un insieme di credenziali delle chiavi](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
