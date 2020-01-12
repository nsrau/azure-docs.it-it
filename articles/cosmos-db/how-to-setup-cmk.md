---
title: Configurare chiavi gestite dal cliente per l'account Azure Cosmos DB
description: Informazioni su come configurare le chiavi gestite dal cliente per l'account Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: thweiss
ms.openlocfilehash: 32266abd5bcf8d7e137095d130ee872cc07edaf0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904081"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Configurare chiavi gestite dal cliente per l'account Azure Cosmos DB

> [!NOTE]
> A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

I dati archiviati nell'account di Azure Cosmos DB vengono crittografati automaticamente e senza interruzioni. Azure Cosmos DB offre due opzioni per la gestione delle chiavi usate per crittografare i dati inattivi:
- **Chiavi gestite dal servizio**. Per impostazione predefinita, Microsoft gestisce le chiavi usate per crittografare l'account Azure Cosmos DB.
- **Chiavi gestite dal cliente (CMK)** . Facoltativamente, è possibile scegliere di aggiungere un secondo livello di crittografia con le chiavi gestite.

Le chiavi gestite dal cliente devono essere archiviate in [Azure Key Vault](../key-vault/key-vault-overview.md). È necessario fornire una chiave per ogni account abilitato per CMK e per crittografare tutti i dati archiviati nell'account.

## <a name="setup"></a>Configurazione

Attualmente, le chiavi gestite dal cliente sono disponibili solo per i nuovi account e devono essere impostate durante la creazione dell'account.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Assicurarsi che il provider di risorse Azure Cosmos DB sia registrato per la sottoscrizione di Azure

Dalla portale di Azure passare alla sottoscrizione di Azure e selezionare "provider di risorse" dal menu a sinistra:

![Voce "provider di risorse" dal menu a sinistra](./media/how-to-setup-cmk/portal-rp.png)

Cercare il provider di risorse "Microsoft. DocumentDB".
- Se il provider di risorse è già contrassegnato come registrato, non è necessario eseguire alcuna operazione.
- In caso contrario, selezionarlo e fare clic su "Register" (registra):

    ![Registrazione del provider di risorse Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. configurare l'istanza di Azure Key Vault

L'uso di chiavi gestite dal cliente con Azure Cosmos DB richiede l'impostazione di due proprietà nell'istanza di Azure Key Vault che si prevede di usare per ospitare le chiavi di crittografia: **eliminazione** temporanea e non **ripulitura**. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate tramite PowerShell o l'interfaccia della riga di comando di Azure.

Per informazioni sull'abilitazione di queste proprietà in un'istanza di Azure Key Vault esistente, vedere le sezioni intitolate abilitazione dell'eliminazione temporanea e abilitazione della protezione di ripulitura in uno degli articoli seguenti:
- [Come usare l'eliminazione temporanea con PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Come usare l'eliminazione temporanea con l'interfaccia della riga di comando di Azure](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. aggiungere un criterio di accesso all'istanza di Azure Key Vault

Dal portale di Azure passare all'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia. Selezionare quindi "criteri di accesso" dal menu a sinistra:

!["Criteri di accesso" dal menu a sinistra](./media/how-to-setup-cmk/portal-akv-ap.png)

- Fare clic su "+ Aggiungi criteri di accesso"
- Nel menu a discesa "autorizzazioni chiave" selezionare "Get", "Unwrap Key" e "wrap Key":

    ![Selezione delle autorizzazioni corrette](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- In "Seleziona entità" fare clic su "nessuno selezionato", cercare e selezionare l'entità "Azure Cosmos DB", quindi fare clic su "Seleziona" nella parte inferiore. se non è possibile trovare l'entità "Azure Cosmos DB", potrebbe essere necessario registrare nuovamente la risorsa "Microsoft. DocumentDB". provider al passaggio 2):

    ![Selezione dell'entità Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Fare clic su "Aggiungi" per aggiungere il nuovo criterio di accesso

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. generare una chiave in Azure Key Vault

Dal portale di Azure passare all'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia. Selezionare quindi "chiavi" nel menu a sinistra:

![Voce "chiavi" nel menu a sinistra](./media/how-to-setup-cmk/portal-akv-keys.png)

- Fare clic su "genera/importa"
- Specificare un nome per la nuova chiave, selezionare una dimensione della chiave RSA (per la sicurezza ottimale è consigliabile un minimo di 3072) e fare clic su "Crea":

    ![Creazione di una nuova chiave](./media/how-to-setup-cmk/portal-akv-gen.png)

- Una volta creata la chiave, fare clic sulla chiave appena creata, quindi sulla versione corrente
- Copiare il "identificatore di chiave" della chiave eccetto la parte dopo l'ultima barra:

    ![Copia dell'identificatore di chiave della chiave](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. creare un nuovo account Azure Cosmos DB

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Quando si crea un nuovo account Azure Cosmos DB dal portale di Azure, scegliere "chiave gestita dal cliente" nel passaggio "crittografia". Nel campo "URI chiave" passare l'URI della chiave di Azure Key Vault copiata al passaggio 4:

![Impostazione dei parametri CMK nel portale di Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>Con PowerShell

Quando si crea un nuovo account Azure Cosmos DB con PowerShell,
- passare l'URI della chiave di Azure Key Vault copiato dal passaggio 4 alla proprietà "keyVaultKeyUri" in "PropertyObject",
- Assicurarsi di usare "2019-12-12" come versione dell'API.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

#### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

Quando si crea un nuovo account Azure Cosmos DB tramite un modello di Azure Resource Manager:
- passare l'URI della chiave di Azure Key Vault copiato dal passaggio 4 alla proprietà "keyVaultKeyUri" nell'oggetto "Properties"
- Assicurarsi di usare "2019-12-12" come versione dell'API

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Sono previsti addebiti aggiuntivi quando si usano chiavi gestite dal cliente?

Sì. Per tenere conto del carico di calcolo aggiuntivo necessario per gestire la crittografia e la decrittografia dei dati con chiavi gestite dal cliente, tutte le operazioni eseguite con l'account Azure Cosmos DB ottengono un aumento del 25% delle [unità richiesta](./request-units.md) utilizzate.

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Quali dati vengono crittografati con CMK?

Tutti i dati archiviati nell'account di Azure Cosmos DB vengono crittografati con CMK, eccetto i metadati seguenti:
- nomi degli account di Azure Cosmos DB [, dei database e dei contenitori](./account-overview.md#elements-in-an-azure-cosmos-account),
- nomi delle [stored procedure](./stored-procedures-triggers-udfs.md).
- percorsi delle proprietà dichiarati nei [criteri di indicizzazione](./index-policy.md),
- valori della [chiave di partizione](./partitioning-overview.md)dei contenitori.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Le chiavi gestite dal cliente saranno supportate per gli account esistenti?

Questa funzionalità è attualmente disponibile solo per i nuovi account.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>È previsto un piano per supportare una maggiore granularità rispetto alle chiavi a livello di account?

Attualmente, tuttavia, vengono prese in considerazione le chiavi a livello di contenitore.

### <a name="how-does-customer-managed-keys-affect-backups"></a>In che modo le chiavi gestite dal cliente influiscono sui backup?

Azure Cosmos DB esegue [backup regolari e automatici](./online-backup-and-restore.md) dei dati archiviati nell'account. Questa operazione esegue il backup dei dati crittografati. Affinché un backup ripristinato sia utilizzabile, è necessario che la chiave di crittografia utilizzata al momento del backup sia ancora disponibile. Ciò significa che non è stata effettuata alcuna revoca e che la versione della chiave usata al momento del backup sarà comunque abilitata.

### <a name="how-do-i-revoke-an-encryption-key"></a>Ricerca per categorie revocare una chiave di crittografia?

La revoca della chiave viene eseguita disabilitando la versione più recente della chiave:

![Disabilitazione della versione di una chiave](./media/how-to-setup-cmk/portal-akv-rev2.png)

In alternativa, per revocare tutte le chiavi da un'istanza di Azure Key Vault, è possibile eliminare i criteri di accesso concessi all'entità Azure Cosmos DB:

![Eliminazione dei criteri di accesso per l'entità Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quali operazioni sono disponibili dopo la revoca di una chiave gestita dal cliente?

L'unica operazione possibile quando la chiave di crittografia è stata revocata è l'eliminazione dell'account.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [crittografia dei dati in Azure Cosmos DB](./database-encryption-at-rest.md)
- Ottenere una panoramica dell' [accesso sicuro ai dati in Cosmos DB](secure-access-to-data.md)