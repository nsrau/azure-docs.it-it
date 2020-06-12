---
title: Usare gli script di distribuzione nei modelli | Microsoft Docs
description: usare gli script di distribuzione nei modelli di Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: jgao
ms.openlocfilehash: 24a0891b57f67bfb78cf3699bddbcf8d345ee679
ms.sourcegitcommit: a3c6efa4d4a48e9b07ecc3f52a552078d39e5732
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83708007"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Usare gli script di distribuzione nei modelli (anteprima)

Informazioni su come usare gli script di distribuzione nei modelli di Azure Resource Manager. Con un nuovo tipo di risorsa denominato `Microsoft.Resources/deploymentScripts`, gli utenti possono eseguire script di distribuzione nelle distribuzioni di modelli ed esaminare i risultati dell'esecuzione. Questi script possono essere usati per eseguire passaggi personalizzati, ad esempio:

- Aggiungere utenti a una directory
- Eseguire operazioni sul piano dati, ad esempio, copiare i BLOB o il database di inizializzazione
- Cercare e convalidare un codice di licenza
- Creare un certificato autofirmato
- Creare un oggetto in Azure AD
- Cercare blocchi di indirizzi IP dal sistema personalizzato

I vantaggi dello script di distribuzione:

- Semplifica la scrittura di codice, l'uso e l'esecuzione del debug. È possibile sviluppare script di distribuzione negli ambienti di sviluppo preferiti. Gli script possono essere incorporati in modelli o in file di script esterni.
- È possibile specificare la piattaforma e il linguaggio dello script. Attualmente gli script di distribuzione di Azure PowerShell e dell'interfaccia della riga di comando di Azure nell'ambiente Linux sono supportati.
- Consente di specificare le identità usate per eseguire gli script. Attualmente è supportata solo l'[Identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
- Consente di specificare gli argomenti della riga di comando da passare allo script.
- Può specificare gli output dello script e passarli nuovamente alla distribuzione.

La risorsa script di distribuzione è disponibile solo nelle aree in cui è disponibile l'istanza di contenitore di Azure.  Vedere [Disponibilità di risorse per Istanze di Azure Container nelle aree di Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili le opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Le due risorse create automaticamente vengono in genere eliminate dal servizio script quando l'esecuzione dello script di distribuzione raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Prerequisiti

- **Identità gestita assegnata dall'utente con il ruolo di collaboratore per il gruppo di risorse di destinazione**. Questa identità viene usata per eseguire gli script di distribuzione. Per eseguire operazioni all'esterno del gruppo di risorse, è necessario concedere autorizzazioni aggiuntive. Ad esempio, assegnare l'identità al livello della sottoscrizione se si vuole creare un nuovo gruppo di risorse.

  > [!NOTE]
  > Il servizio script crea un account di archiviazione (a meno che non si specifichi un account di archiviazione esistente) e un'istanza di contenitore in background.  Un'identità gestita assegnata dall'utente con il ruolo di collaboratore a livello di sottoscrizione è obbligatoria se la sottoscrizione non ha registrato i provider di risorse dell'account di archiviazione di Azure (Microsoft.Storage) e dell'istanza di contenitore di Azure (Microsoft.ContainerInstance).

  Per creare un'identità, vedere [Creare un'identità gestita assegnata dall'utente usando il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) o [usando l'interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) o [usando Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). È necessario l'ID identità per distribuire il modello. Il formato dell'identità è:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Usare lo script dell'interfaccia della riga di comando o di PowerShell seguente per ottenere l'ID fornendo il nome del gruppo di risorse e il nome dell'identità.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** o **interfaccia della riga di comando di Azure**. Per un elenco delle versioni di Azure PowerShell supportate, vedere [qui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list); per un elenco delle versioni dell'interfaccia della riga di comando di Azure supportate, vedere [qui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Lo script di distribuzione usa le immagini dell'interfaccia della riga di comando disponibili del Registro Microsoft Container (MCR). La certificazione di un'immagine dell'interfaccia della riga di comando per uno script di distribuzione richiede circa un mese. Non usare le versioni dell'interfaccia della riga di comando rilasciate negli ultimi 30 giorni. Per trovare le date di rilascio delle immagini, vedere [Note sulla versione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Se viene usata una versione non supportata, nel messaggio di errore vengono elencate le versioni supportate.

    Queste versioni non sono necessarie per la distribuzione di modelli. Queste versioni sono tuttavia necessarie per testare localmente gli script di distribuzione. Vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). È possibile usare un'immagine Docker preconfigurata.  Vedere [Configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="sample-templates"></a>Modelli di esempio

Di seguito è riportato un file json di esempio.  Lo schema di modello più recente è reperibile [qui](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Questo esempio è fornito a scopo dimostrativo.  **scriptContent** e **primaryScriptUris** non possono coesistere in un modello.

Dettagli sui valori delle proprietà:

- **Identità**: Il servizio script di distribuzione usa un'identità gestita assegnata dall'utente per eseguire gli script. Attualmente è supportata solo l'Identità gestita assegnata dall'utente di Azure.
- **kind**: specificare il tipo di script. Attualmente gli script di Azure PowerShell e dell'interfaccia della riga di comando di Azure sono supportati. I valori sono **AzurePowerShell** e **AzureCLI**.
- **forceUpdateTag**: La modifica di questo valore tra le distribuzioni di modelli forza la ripetizione dell'esecuzione dello script di distribuzione. Usare la funzione newGuid() o utcNow() che deve essere impostata come defaultValue di un parametro. Per altre informazioni, vedere [Eseguire lo script più di una volta](#run-script-more-than-once).
- **containerSettings**: Specificare le impostazioni per personalizzare l'istanza di contenitore di Azure.  **containerGroupName** serve a specificare il nome del gruppo di contenitori.  Se non è specificato, il nome del gruppo verrà generato automaticamente.
- **storageAccountSettings**: Specificare le impostazioni per l'uso di un account di archiviazione esistente. Se non è specificato, un account di archiviazione viene creato automaticamente. Vedere [Usare un account di archiviazione esistente](#use-an-existing-storage-account).
- **azPowerShellVersion**/**azCliVersion**: Specificare la versione del modulo da usare. Per un elenco delle versioni supportate di PowerShell e dell'interfaccia della riga di comando, vedere [Prerequisiti](#prerequisites).
- **arguments**: Specificare i valori del parametro. I valori sono separati da uno spazio.
- **environmentVariables**: Specificare le variabili di ambiente da passare allo script. Per altre informazioni, vedere [Sviluppare script di distribuzione](#develop-deployment-scripts).
- **scriptContent**: specificare il contenuto dello script. Per eseguire uno script esterno, usare `primaryScriptUri`. Per esempi, vedere [Usare script inline](#use-inline-scripts) e [Usare script esterni](#use-external-scripts).
- **primaryScriptUri**: Specificare un URL accessibile pubblicamente per lo script di distribuzione primario con estensioni di file supportate.
- **supportingScriptUris**: Specificare una matrice di URL accessibili pubblicamente per supportare i file che vengono chiamati in `ScriptContent` o `PrimaryScriptUri`.
- **timeout**: specificare il tempo di esecuzione dello script massimo consentito nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
- **cleanupPreference**. Specificare la preferenza per la pulizia delle risorse di distribuzione quando l'esecuzione dello script si trova in uno stato terminale. L'impostazione predefinita è **Sempre**, che indica l'eliminazione delle risorse nonostante lo stato del terminale (Riuscito, Non riuscito, Annullato). Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).
- **retentionInterval**: Specificare l'intervallo per cui il servizio mantiene le risorse dello script di distribuzione dopo che l'esecuzione dello script di distribuzione ha raggiunto uno stato finale. Le risorse dello script di distribuzione verranno eliminate alla scadenza di tale durata. La durata è basata sul [modello ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**, ovvero sette giorni. Questa proprietà viene usata quando l'opzione cleanupPreference è impostata su *OnExpiration*. La proprietà *OnExpiration* non è attualmente abilitata. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Altri esempi

- [Creare e assegnare un certificato a un insieme di credenziali delle chiavi](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [Creare e assegnare un'identità gestita assegnata dall'utente a un gruppo di risorse ed eseguire uno script di distribuzione](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> È consigliabile creare un'identità assegnata dall'utente e concedere in anticipo le autorizzazioni. È possibile che vengano restituiti errori di accesso e di autorizzazione se si crea l'identità e si concedono le autorizzazioni nello stesso modello in cui vengono eseguiti gli script di distribuzione. È necessario del tempo prima che le autorizzazioni diventino effettive.

## <a name="use-inline-scripts"></a>Usare script inline

Il modello seguente include una risorsa definita con il tipo `Microsoft.Resources/deploymentScripts`. La parte evidenziata è lo script inline.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Poiché gli script di distribuzione inline sono racchiusi tra virgolette doppie, le stringhe all'interno degli script di distribuzione devono essere racchiuse tra virgolette singole. Il carattere di escape per PowerShell è **&#92;** . È anche possibile prendere in considerazione l'uso della sostituzione di stringhe come illustrato nell'esempio JSON precedente. Vedere il valore predefinito del nome del parametro.

Lo script richiede un parametro e genera l'output del valore del parametro. **DeploymentScriptOutputs** è usato per l'archiviazione degli output.  Nella sezione Output, la riga del **valore** mostra come accedere ai valori archiviati. `Write-Output` è usato a scopo di debug. Per informazioni su come accedere al file di output, vedere [Debug degli script di distribuzione](#debug-deployment-scripts).  Per le descrizioni delle proprietà, vedere [Modelli di esempio](#sample-templates).

Per eseguire lo script, selezionare **Prova** per aprire Cloud Shell e quindi incollare il codice seguente nel riquadro della shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

L'output è simile al seguente:

![Output dello script di distribuzione del modello di Resource Manager hello world](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usare script esterni

Oltre agli script inline, è anche possibile usare file di script esterni. Sono supportati solo gli script di PowerShell primari con l'estensione di file **ps1**. Per gli script dell'interfaccia della riga di comando, gli script primari possono avere estensioni (o senza estensione), purché gli script siano script Bash validi. Per usare file di script esterni, sostituire `scriptContent` con `primaryScriptUri`. Ad esempio:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Per visualizzare un esempio, selezionare [qui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

I file di script esterni devono essere accessibili.  Per proteggere i file di script archiviati negli account di archiviazione di Azure, vedere [Distribuire un modello ARM privato con token di firma di accesso condiviso](./secure-template-with-sas-token.md).

L'utente è responsabile di garantire l'integrità degli script a cui viene fatto riferimento dallo script di distribuzione, **PrimaryScriptUri** o **SupportingScriptUris**.  Fare riferimento solo a script attendibili.

## <a name="use-supporting-scripts"></a>Usare script di supporto

È possibile separare le logiche complesse in uno o più file di script di supporto. Se necessario, la proprietà `supportingScriptURI` consente di fornire una matrice di URI ai file di script di supporto:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

I file script di supporto possono essere chiamati sia da script inline che da file di script primari. I file di script di supporto non prevedono alcuna restrizione sull'estensione di file.

I file di supporto vengono copiati in azscripts/azscriptinput in fase di esecuzione. Usare il percorso relativo per fare riferimento ai file di supporto da script inline e file di script primari.

## <a name="work-with-outputs-from-powershell-script"></a>Usare gli output dello script di PowerShell

Il modello seguente mostra come passare i valori tra due risorse di deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

Nella prima risorsa definire una variabile denominata **$DeploymentScriptOutputs**e usarla per archiviare i valori di output. Per accedere al valore di output da un'altra risorsa all'interno del modello, usare:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Usare gli output dello script dell'interfaccia della riga di comando

Diversamente dallo script di distribuzione di PowerShell, il supporto CLI/Bash non espone una variabile comune per archiviare gli output di script, ma è presente una variabile di ambiente denominata **AZ_SCRIPTS_OUTPUT_PATH** che archivia il percorso in cui risiede il file di output dello script. Se uno script di distribuzione viene eseguito da un modello di Resource Manager, questa variabile di ambiente viene impostata automaticamente dalla shell Bash.

Gli output dello script di distribuzione devono essere salvati nel percorso AZ_SCRIPTS_OUTPUT_PATH e devono essere un oggetto stringa JSON valido. Il contenuto del file deve essere salvato come coppia chiave-valore. Una matrice di stringhe, ad esempio, viene archiviata come { "MyResult": [ "foo", "bar"] }.  L'archiviazione solo dei risultati della matrice, ad esempio [ "foo", "bar" ], non è valida.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

Nell'esempio precedente viene usato [jq](https://stedolan.github.io/jq/). Viene fornito con le immagini del contenitore. Vedere [Configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Sviluppare script di distribuzione

### <a name="handle-non-terminating-errors"></a>Gestire errori non fatali

È possibile controllare il modo in cui PowerShell risponde a errori non fatali usando la variabile [ **$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) nello script di distribuzione. Il servizio script non imposta o modifica il valore.  Nonostante il valore impostato per $ErrorActionPreference, lo script di distribuzione imposta lo stato di provisioning delle risorse su *Non riuscito* quando lo script rileva un errore.

### <a name="pass-secured-strings-to-deployment-script"></a>Passare stringhe protette allo script di distribuzione

L'impostazione delle variabili di ambiente (EnvironmentVariable) nelle istanze di contenitore consente di offrire la configurazione dinamica dell'applicazione o dello script eseguiti dal contenitore. Lo script di distribuzione gestisce variabili di ambiente non protette e protette in modo analogo all'istanza di contenitore di Azure. Per altre informazioni, vedere [Impostare variabili di ambiente in istanze di contenitore](../../container-instances/container-instances-environment-variables.md#secure-values).

La dimensione massima consentita per le variabili di ambiente è 64 kB.

## <a name="debug-deployment-scripts"></a>Debug degli script di distribuzione

Il servizio script crea un [account di archiviazione](../../storage/common/storage-account-overview.md) (a meno che non si specifichi un account di archiviazione esistente) e un'[istanza di contenitore](../../container-instances/container-instances-overview.md) per l'esecuzione dello script. Se queste risorse vengono create automaticamente dal servizio script, entrambe hanno il suffisso **azscripts** nei nomi delle risorse.

![Nomi delle risorse dello script di distribuzione del modello di Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Lo script utente, i risultati dell'esecuzione e il file stdout vengono archiviati nelle condivisioni file dell'account di archiviazione. È presente una cartella denominata **azscripts**. Nella cartella sono presenti altre due cartelle per i file di input e di output: **azscriptinput** e **azscriptoutput**.

La cartella di output contiene un file **executionresult.json** e il file di output dello script. È possibile visualizzare il messaggio di errore dell'esecuzione dello script in **executionresult.json**. Il file di output viene creato solo quando lo script viene eseguito correttamente. La cartella di input contiene un file di script di sistema di PowerShell e i file di script di distribuzione dell'utente. È possibile sostituire il file di script di distribuzione dell'utente con uno modificato ed eseguire nuovamente lo script di distribuzione dall'istanza di contenitore di Azure.

È possibile ottenere le informazioni sulla distribuzione delle risorse dello script di distribuzione a livello di gruppo di risorse e a livello di sottoscrizione usando l'API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

Nell'esempio seguente viene usato [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

L'output è simile a:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

L'output mostra lo stato di distribuzione e gli ID delle risorse dello script di distribuzione.

L'API REST seguente restituisce il log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Funziona solo prima che vengano eliminate le risorse dello script di distribuzione.

Per visualizzare la risorsa deploymentScripts nel portale, selezionare **Mostra tipi nascosti**:

![Script di distribuzione del modello di Resource Manager, mostra tipi nascosti, portale](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Usare un account di archiviazione esistente

Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili le opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Requisiti per l'uso di un account di archiviazione esistente:

- I tipi di account di archiviazione supportati sono: account per utilizzo generico V2, utilizzo generico V1 e FileStorage. Solo FileStorage supporta l'SKU Premium. Per altre informazioni, vedere [Tipi di account di archiviazione](../../storage/common/storage-account-overview.md).
- Le regole del firewall dell'account di archiviazione non sono ancora supportate. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../../storage/common/storage-network-security.md).
- L'identità gestita assegnata dall'utente dello script di distribuzione deve avere le autorizzazioni per gestire l'account di archiviazione, che include condivisioni file di lettura, creazione ed eliminazione.

Per specificare un account di archiviazione esistente, aggiungere il file JSON seguente all'elemento della proprietà di `Microsoft.Resources/deploymentScripts`:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: specificare il nome dell'account di archiviazione.
- **storageAccountKey"** : specificare una delle chiavi dell'account di archiviazione. Per recuperare la chiave, è possibile usare la funzione [`listKeys()`](./template-functions-resource.md#listkeys). Ad esempio:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Vedere [Modelli di esempio](#sample-templates) per un esempio di definizione di `Microsoft.Resources/deploymentScripts` completo.

Quando viene usato un account di archiviazione esistente, il servizio script crea una condivisione file con un nome univoco. Vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources) per informazioni su come il servizio script pulisce la condivisione file.

## <a name="clean-up-deployment-script-resources"></a>Pulire le risorse dello script di distribuzione

Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Le due risorse create automaticamente vengono eliminate dal servizio script quando l'esecuzione dello script di distribuzione raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per informazioni sui prezzi, vedere [Prezzi di Istanze di Container](https://azure.microsoft.com/pricing/details/container-instances/) e [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Il ciclo di vita di queste risorse è controllato dalle proprietà seguenti nel modello:

- **cleanupPreference**: Pulire le preferenze quando l'esecuzione dello script si trova in uno stato terminale. I valori supportati sono:

  - **Sempre**: Eliminare le risorse create automaticamente una volta che l'esecuzione dello script si trova in uno stato terminale. Se viene usato un account di archiviazione esistente, il servizio script elimina la condivisione file creata nell'account di archiviazione. Poiché la risorsa deploymentScripts può essere ancora presente dopo la pulizia delle risorse, i servizi script salvano in modo permanente i risultati dell'esecuzione dello script, ad esempio stdout, output, valore restituito e così via, prima che le risorse vengano eliminate.
  - **OnSuccess**: Eliminare le risorse create automaticamente solo quando l'esecuzione dello script ha esito positivo. Se viene usato un account di archiviazione esistente, il servizio script rimuove la condivisione file solo quando l'esecuzione dello script ha esito positivo. È comunque possibile accedere alle risorse per trovare le informazioni di debug.
  - **OnExpiration**: Eliminare le risorse create automaticamente solo quando l'impostazione **retentionInterval** è scaduta. Se viene usato un account di archiviazione esistente, il servizio script rimuove la condivisione file, ma conserva l'account di archiviazione.

- **retentionInterval**: Specificare l'intervallo di tempo durante il quale una risorsa di script verrà conservata e dopo cui scadrà e verrà eliminata.

> [!NOTE]
> Non è consigliabile usare l'account di archiviazione e l'istanza di contenitore generati dal servizio script per altri scopi. Le due risorse potrebbero essere rimosse a seconda del ciclo di vita dello script.

## <a name="run-script-more-than-once"></a>Eseguire lo script più di una volta

L'esecuzione dello script di distribuzione è un'operazione idempotente. Se nessuna delle proprietà della risorsa deploymentScripts (incluso lo script inline) viene modificata, lo script non verrà eseguito quando si ridistribuisce il modello. Il servizio script di distribuzione confronta i nomi delle risorse nel modello con le risorse esistenti nello stesso gruppo di risorse. Se si vuole eseguire lo stesso script di distribuzione più volte, sono disponibili due opzioni:

- Modificare il nome della risorsa deploymentScripts. Usare, ad esempio, la funzione di modello [utcNow](./template-functions-date.md#utcnow) come nome della risorsa o come parte del nome della risorsa. Se si modifica il nome della risorsa, viene creata una nuova risorsa deploymentScripts. È utile per mantenere una cronologia dell'esecuzione dello script.

    > [!NOTE]
    > La funzione utcNow può essere usata solo nel valore predefinito per un parametro.

- Specificare un valore diverso nella proprietà del modello `forceUpdateTag`.  Ad esempio, usare utcNow come valore.

> [!NOTE]
> Scrivere script di distribuzione idempotenti. In questo modo si garantisce che, in caso di esecuzione accidentale, non provocheranno modifiche al sistema. Ad esempio, se si usa lo script di distribuzione per creare una risorsa di Azure, verificare che la risorsa non esista prima di crearla, in modo che lo script abbia esito positivo o la risorsa non venga creata nuovamente.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

È possibile usare un'immagine del contenitore Docker preconfigurata come ambiente di sviluppo dello script di distribuzione. La procedura seguente illustra come configurare l'immagine Docker in Windows. Per Linux e Mac, è possibile trovare le informazioni su Internet.

1. Installare [Docker Desktop](https://www.docker.com/products/docker-desktop) nel computer di sviluppo.
1. Aprire Docker Desktop.
1. Selezionare l'icona di Docker Desktop nella barra delle applicazioni e quindi selezionare **Impostazioni**.
1. Selezionare **Unità condivise**, selezionare un'unità locale che si vuole che sia disponibile per i contenitori, quindi selezionare **Applica**

    ![Script di distribuzione del modello di Resource Manager docker drive](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Immettere le credenziali di Windows al prompt.
1. Aprire una finestra del terminale, ovvero il prompt dei comandi o Windows PowerShell (non usare PowerShell ISE).
1. Eseguire il pull dell'immagine del contenitore dello script di distribuzione nel computer locale:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Nell'esempio viene usata la versione di PowerShell 2.7.0.

    Per eseguire il pull di un'immagine dell'interfaccia della riga di comando da un Registro Microsoft Container (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Questo esempio usa la versione 2.0.80. Lo script di distribuzione usa le immagini predefinite dei contenitori CLI trovate [qui](https://hub.docker.com/_/microsoft-azure-cli).

1. Eseguire l'immagine di Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Sostituire **&lt;lettera unità host>** e **&lt;nome directory host>** con una cartella esistente nell'unità condivisa.  Esegue il mapping della cartella nella cartella **/data** del contenitore. Per esempi per eseguire il mapping di D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-it** indica di mantenere attiva l'immagine del contenitore.

    Esempio di interfaccia della riga di comando:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selezionare **Condividi** quando si riceve una richiesta.
1. Lo screenshot seguente illustra come eseguire uno script di PowerShell, dato che è presente un file helloworld.ps1 nella cartella d:\docker.

    ![Script di distribuzione del modello di Resource Manager docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Una volta testato correttamente lo script, è possibile usarlo come script di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare gli script di distribuzione. Per esaminare un'esercitazione relativa a uno script di distribuzione:

> [!div class="nextstepaction"]
> [Esercitazione: Usare gli script di distribuzione nei modelli di Azure Resource Manager](./template-tutorial-deployment-script.md)