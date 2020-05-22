---
title: Configurare le chiavi gestite dal cliente per l'account Azure Cosmos DB
description: Informazioni su come configurare le chiavi gestite dal cliente per l'account Azure Cosmos DB con Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: thweiss
ms.openlocfilehash: 5629ddfe496ef1abd071ab579c885cbe1adeb344
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592100"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configurare le chiavi gestite dal cliente per l'account Azure Cosmos con Azure Key Vault

I dati archiviati nell'account Azure Cosmos vengono crittografati automaticamente e facilmente con le chiavi gestite da Microsoft (**chiavi gestite dal servizio**). Facoltativamente, è possibile scegliere di aggiungere un secondo livello di crittografia con **chiavi gestite dal cliente**.

![Livelli di crittografia relativi ai dati dei clienti](./media/how-to-setup-cmk/cmk-intro.png)

È necessario archiviare le chiavi gestite dal cliente in [Azure Key Vault](../key-vault/general/overview.md) e fornire una chiave per ogni account Azure Cosmos abilitato con chiavi gestite dal cliente. Questa chiave viene usata per crittografare tutti i dati archiviati nell'account.

> [!NOTE]
> Attualmente, le chiavi gestite dal cliente sono disponibili solo per i nuovi account Azure Cosmos. È necessario configurarle durante la creazione dell'account.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registrare il provider di risorse Azure Cosmos DB per la sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/), passare alla sottoscrizione di Azure e selezionare **Provider di risorse** nella scheda **Impostazioni**:

   ![Voce "Provider di risorse" nel menu a sinistra](./media/how-to-setup-cmk/portal-rp.png)

1. Cercare il provider di risorse **Microsoft.DocumentDB**. Verificare che il provider di risorse sia già contrassegnato come registrato. In caso contrario, scegliere il provider di risorse e selezionare **Registra**:

   ![Registrazione del provider di risorse Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configurare l'istanza di Azure Key Vault

Per usare le chiavi gestite dal cliente con Azure Cosmos DB è necessario impostare due proprietà nell'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia: **Eliminazione temporanea** e **Protezione dall'eliminazione**.

Se si crea una nuova istanza di Azure Key Vault, abilitare queste proprietà durante la creazione:

![Abilitazione delle proprietà Eliminazione temporanea e Protezione dall'eliminazione per una nuova istanza di Azure Key Vault](./media/how-to-setup-cmk/portal-akv-prop.png)

Se si usa un'istanza di Azure Key Vault esistente, è possibile verificare che queste proprietà siano abilitate nella sezione **Proprietà** del portale di Azure. Se una di queste proprietà non è abilitata, vedere le sezioni "Abilitazione della funzione di eliminazione temporanea" e "Abilitazione del flag di protezione dall'eliminazione" in uno degli articoli seguenti:

- [Come usare la funzionalità di eliminazione temporanea con PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Come usare la funzionalità di eliminazione temporanea con l'interfaccia della riga di comando di Azure](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Aggiungere un criterio di accesso all'istanza di Azure Key Vault

1. Dal portale di Azure passare all'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia. Selezionare **Criteri di accesso** nel menu a sinistra:

   !["Criteri di accesso" nel menu a sinistra](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selezionare **+ Aggiungi criterio di accesso**.

1. Nel menu a discesa **Autorizzazioni chiave** selezionare le autorizzazioni **Recupera**, **Annulla il wrapping della chiave** e **Esegui il wrapping della chiave**:

   ![Selezione delle autorizzazioni corrette](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. In **Selezionare un'entità** selezionare **Nessuna selezione**. Cercare quindi l'entità di sicurezza **Azure Cosmos DB** e selezionarla (per facilitarne l'individuazione, è anche possibile eseguire la ricerca in base all'ID entità di sicurezza: `a232010e-820c-4083-83bb-3ace5fc29d0b` per tutte le aree di Azure ad eccezione delle aree di Azure per enti pubblici in cui l'ID entità di sicurezza è `57506a73-e302-42a9-b869-6f12d9ec29e9`). Scegliere infine **Seleziona** nella parte inferiore della schermata. Se l'entità di sicurezza **Azure Cosmos DB** non è presente nell'elenco, potrebbe essere necessario registrare nuovamente il provider di risorse **Microsoft.DocumentDB** come descritto nella sezione [Registrare il provider di risorse](#register-resource-provider) di questo articolo.

   ![Selezionare l'entità di sicurezza Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selezionare **Aggiungi** per aggiungere il nuovo criterio di accesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Generare una chiave in Azure Key Vault

1. Dal portale di Azure passare all'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia. Scegliere quindi **Chiavi** nel menu a sinistra:

   ![Voce "Chiavi" nel menu a sinistra](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selezionare **Genera/Importa**, specificare un nome per la nuova chiave e selezionare le dimensioni della chiave RSA. Si consiglia di usare minimo 3072 per motivi di sicurezza. quindi selezionare **Crea**:

   ![Creare una nuova chiave](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Dopo aver creato la chiave, selezionare la chiave appena creata e quindi la relativa versione corrente.

1. Copiare l'**Identificatore chiave** della chiave, ad eccezione della parte dopo l'ultima barra:

   ![Copia dell'identificatore chiave della chiave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Creare un nuovo account Azure Cosmos

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Quando si crea un nuovo account Azure Cosmos DB dal portale di Azure, scegliere **Chiave gestita dal cliente** nel passaggio **Crittografia**. Nel campo **URI della chiave** incollare l'URI/identificatore chiave della chiave di Azure Key Vault copiata nel passaggio precedente:

![Impostazione dei parametri della chiave gestita dal cliente nel portale di Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Uso di Azure PowerShell

Quando si crea un nuovo account Azure Cosmos DB con PowerShell:

- Passare l'URI della chiave di Azure Key Vault copiata in precedenza nella proprietà **keyVaultKeyUri** in **PropertyObject**.

- Usare **2019-12-12** o versioni successive come versione dell'API.

> [!IMPORTANT]
> Per creare correttamente l'account con chiavi gestite dal cliente, è necessario impostare in modo esplicito la proprietà `locations`.

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

Dopo aver creato l'account, è possibile verificare che le chiavi gestite dal cliente siano state abilitate recuperando l'URI della chiave di Azure Key Vault:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

Quando si crea un nuovo account Azure Cosmos tramite un modello di Azure Resource Manager:

- Passare l'URI della chiave di Azure Key Vault copiata in precedenza nella proprietà **keyVaultKeyUri** nell'oggetto **properties**.

- Usare **2019-12-12** o versioni successive come versione dell'API.

> [!IMPORTANT]
> Per creare correttamente l'account con chiavi gestite dal cliente, è necessario impostare in modo esplicito la proprietà `locations`.

```json
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

Distribuire il modello con lo script di PowerShell seguente:

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

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Uso dell'interfaccia della riga di comando di Azure

Quando si crea un nuovo account Azure Cosmos tramite l'interfaccia della riga di comando di Azure, passare l'URI della chiave di Azure Key Vault copiato in precedenza nel parametro `--key-uri`.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Dopo aver creato l'account, è possibile verificare che le chiavi gestite dal cliente siano state abilitate recuperando l'URI della chiave di Azure Key Vault:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Sono previsti addebiti aggiuntivi per abilitare le chiavi gestite dal cliente?

No, non è previsto alcun addebito per abilitare questa funzionalità.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>In che modo le chiavi gestite dal cliente influiscono sulla pianificazione della capacità?

Quando si usano le chiavi gestite dal cliente, le [Unità richiesta](./request-units.md) utilizzate dalle operazioni del database registrano un aumento per riflettere l'elaborazione aggiuntiva necessaria per eseguire la crittografia e la decrittografia dei dati. Questo può causare un utilizzo leggermente più elevato della capacità di provisioning. Usare la tabella seguente come guida:

| Tipo di operazione | Aumento delle unità richiesta |
|---|---|
| Letture punto (recupero di elementi in base al relativo ID) | + 5% per operazione |
| Qualsiasi operazione di scrittura | + 6% per operazione<br/>circa + 0,06 UR per proprietà indicizzata |
| Query, lettura del feed di modifiche o feed di conflitti | + 15% per operazione |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quali dati vengono crittografati con le chiavi gestite dal cliente?

Con le chiavi gestite dal cliente vengono crittografati tutti i dati archiviati nell'account Azure Cosmos tranne i metadati seguenti:

- Nomi di [account, database e contenitori](./account-overview.md#elements-in-an-azure-cosmos-account) di Azure Cosmos DB

- Nomi delle [stored procedure](./stored-procedures-triggers-udfs.md)

- Percorsi delle proprietà dichiarati nei [criteri di indicizzazione](./index-policy.md)

- Valori delle [chiavi di partizione](./partitioning-overview.md) dei contenitori

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Le chiavi gestite dal cliente sono supportate per gli account Azure Cosmos esistenti?

Questa funzionalità è attualmente disponibile solo per i nuovi account.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>È previsto un piano per supportare una maggiore granularità rispetto alle chiavi a livello di account?

Non attualmente, ma vengono prese in considerazione le chiavi a livello di contenitore.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Come è possibile stabilire se le chiavi gestite dal cliente sono abilitate nell'account Azure Cosmos usato?

È possibile recuperare i dettagli dell'account Azure Cosmos a livello di codice e cercare la presenza della proprietà `keyVaultKeyUri`. Vedere sopra per informazioni su come eseguire questa operazione [in PowerShell](#using-powershell) e [usando l'interfaccia della riga di comando di Azure](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>In che modo le chiavi gestite dal cliente influiscono su un backup?

Azure Cosmos DB esegue [backup regolari e automatici](./online-backup-and-restore.md) dei dati archiviati nell'account. Questa operazione esegue il backup dei dati crittografati. Per usare il backup ripristinato, è necessaria la chiave di crittografia usata al momento del backup. Ciò significa che non è stata effettuata alcuna revoca e che la versione della chiave usata al momento del backup sarà ancora abilitata.

### <a name="how-do-i-rotate-an-encryption-key"></a>Come si ruota una chiave di crittografia?

La rotazione delle chiavi viene eseguita creando una nuova versione della chiave in Azure Key Vault:

![Creare una nuova versione della chiave](./media/how-to-setup-cmk/portal-akv-rot.png)

La versione precedente può essere disabilitata dopo 24 ore o dopo che i [log di controllo di Azure Key Vault](../key-vault/general/logging.md) non mostrano più l'attività di Azure Cosmos DB su tale versione.

### <a name="how-do-i-revoke-an-encryption-key"></a>Come si revoca una chiave di crittografia?

La revoca della chiave viene eseguita disabilitando la versione più recente della chiave:

![Disabilitare la versione di una chiave](./media/how-to-setup-cmk/portal-akv-rev2.png)

In alternativa, per revocare tutte le chiavi da un'istanza di Azure Key Vault, è possibile eliminare i criteri di accesso concessi all'entità di sicurezza di Azure Cosmos DB:

![Eliminazione dei criteri di accesso per l'entità di sicurezza di Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quali operazioni sono disponibili dopo la revoca di una chiave gestita dal cliente?

L'unica operazione possibile quando la chiave di crittografia è stata revocata è l'eliminazione dell'account.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [crittografia dei dati in Azure Cosmos DB](./database-encryption-at-rest.md).
- Ottenere una panoramica dell'[accesso sicuro ai dati in Cosmos DB](secure-access-to-data.md).
