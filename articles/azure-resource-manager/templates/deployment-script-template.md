---
title: Utilizzo degli script di distribuzione nei modelli . Documenti Microsoft
description: usare gli script di distribuzione nei modelli di Azure Resource Manager.Use deployment scripts in Azure Resource Manager templates.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: aa49b313f0fb10175dc6c0003f1a919f61731269
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743308"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Usare gli script di distribuzione nei modelli (anteprima)Use deployment scripts in templates (Preview)

Informazioni su come usare gli script di distribuzione nei modelli di risorse di Azure.Learn how to use deployment scripts in Azure Resource templates. Con un nuovo `Microsoft.Resources/deploymentScripts`tipo di risorsa denominato , gli utenti possono eseguire script di distribuzione nelle distribuzioni di modelli ed esaminare i risultati dell'esecuzione. Questi script possono essere utilizzati per eseguire passaggi personalizzati, ad esempio:These scripts can be used for performing custom steps such as:

- aggiungere utenti a una directory
- creare una registrazione dell'app
- eseguire operazioni del piano dati, ad esempio copiare BLOB o database di seeded
- cercare e convalidare una chiave di licenza
- Creare un certificato autofirmato
- creare un oggetto in Azure ADCreate an object in Azure AD
- cercare blocchi di indirizzi IP dal sistema personalizzato

I vantaggi dello script di distribuzione:

- Facile da codificare, utilizzare ed eseguire il debug. È possibile sviluppare script di distribuzione negli ambienti di sviluppo preferiti. Gli script possono essere incorporati nei modelli o nei file di script esterni.
- È possibile specificare il linguaggio di script e la piattaforma. Attualmente sono supportati gli script di distribuzione di Azure PowerShell e dell'interfaccia della riga di comando di Azure nell'ambiente Linux.Currently, Azure PowerShell and Azure CLI deployment scripts on the Linux environment are supported.
- Consentire di specificare le identità utilizzate per eseguire gli script. Attualmente è supportata solo [l'identità gestita assegnata dall'utente di Azure.Currently, only Azure user-assigned managed identity](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) is supported.
- Consentire il passaggio di argomenti della riga di comando allo script.
- Può specificare gli output dello script e passarli alla distribuzione.

La risorsa script di distribuzione è disponibile solo nelle aree in cui è disponibile l'istanza del contenitore di Azure.The deployment script resource is only available in the regions where Azure Container Instance is available.  Vedere [Disponibilità delle risorse per le istanze del contenitore](../../container-instances/container-instances-region-availability.md)di Azure nelle aree di Azure.See Resource availability for Azure Container Instances in Azure regions.

> [!IMPORTANT]
> Due risorse dello script di distribuzione, un account di archiviazione e un'istanza del contenitore, vengono create nello stesso gruppo di risorse per l'esecuzione dello script e la risoluzione dei problemi. Queste risorse vengono in genere eliminate dal servizio script quando l'esecuzione dello script di distribuzione entra in uno stato terminale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per ulteriori informazioni, vedere [Risorse di script di distribuzione di pulizione](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Prerequisiti

- **Identità gestita assegnata dall'utente con il ruolo del collaboratore al gruppo di risorse**di destinazione. Questa identità viene usata per eseguire gli script di distribuzione. Per eseguire operazioni all'esterno del gruppo di risorse, è necessario concedere autorizzazioni aggiuntive. Ad esempio, assegnare l'identità al livello di sottoscrizione se si vuole creare un nuovo gruppo di risorse.

  > [!NOTE]
  > Il motore di script di distribuzione crea un account di archiviazione e un'istanza del contenitore in background.  Un'identità gestita assegnata dall'utente con il ruolo del collaboratore a livello di sottoscrizione è necessaria se la sottoscrizione non ha registrato i provider di risorse dell'account di archiviazione di Azure (Microsoft.Storage) e dell'istanza del contenitore di Azure (Microsoft.ContainerInstance).

  Per creare un'identità, vedere [Creare un'identità gestita assegnata dall'utente tramite il portale](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)di Azure oppure [l'interfaccia della riga](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)di comando di Azure oppure Azure [PowerShell.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) È necessario l'ID identità per distribuire il modello. Il formato dell'identità è:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Usare lo script dell'interfaccia della riga di comando o di PowerShell seguente per ottenere l'ID specificando il nome del gruppo di risorse e il nome dell'identità.

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

- **Azure PowerShell** o interfaccia della riga di comando di **Azure.** Per un elenco delle versioni di Azure PowerShell supportate, vedere [qui;](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) Per un elenco delle versioni dell'interfaccia della riga di comando di Azure supportate, vedere [qui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Lo script di distribuzione utilizza le immagini dell'interfaccia della riga di comando disponibili da Microsoft Container Registry (MCR) . Richiede circa un mese per certificare un'immagine dell'interfaccia della riga di comando per lo script di distribuzione. Non utilizzare le versioni dell'interfaccia della riga di comando rilasciate entro 30 giorni. Per trovare le date di rilascio per le immagini, vedere Note sulla [versione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)di Azure.To find the release dates for the images, see Azure CLI release notes . Se viene utilizzata una versione non supportata, il messaggio di errore elenca le versioni supportate.

    Queste versioni non sono necessarie per la distribuzione dei modelli. Ma queste versioni sono necessarie per testare gli script di distribuzione in locale. Vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). È possibile utilizzare un'immagine Docker preconfigurata.  Vedere [Configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="sample-templates"></a>Modelli di esempio

Il codice json seguente è un esempio.  Lo schema di modello più recente è disponibile [qui](/azure/templates/microsoft.resources/deploymentscripts).

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
> L'esempio è a scopo dimostrativo.  **scriptContent** e **primaryScriptUris** non possono coesistere in un modello.

Dettagli valore proprietà:

- **Identity**: il servizio script di distribuzione utilizza un'identità gestita assegnata dall'utente per eseguire gli script. Attualmente è supportata solo l'identità gestita assegnata dall'utente.
- **kind**: specificare il tipo di script. Attualmente, gli script di Azure PowerShell e dell'interfaccia della riga di comando di Azure sono supportati. I valori sono **AzurePowerShell** e **AzureCLI**.
- **forceUpdateTag**: la modifica di questo valore tra le distribuzioni di modelli forza la riesecuzione dello script di distribuzione. Utilizzare la funzione newGuid() o utcNow() che deve essere impostata come defaultValue di un parametro. Per altre informazioni, vedere [Eseguire lo script più di una volta](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: specifica la versione del modulo da usare. Per un elenco delle versioni supportate di PowerShell e dell'interfaccia della riga di comando, vedere [Prerequisiti](#prerequisites).
- **arguments**: Specificare i valori dei parametri. I valori sono separati da uno spazio.
- **environmentVariables**: Specificare le variabili di ambiente da passare allo script. Per ulteriori informazioni, vedere [Sviluppare script di distribuzione](#develop-deployment-scripts).
- **scriptContent**: specifica il contenuto dello script. Per eseguire uno script `primaryScriptUri` esterno, utilizzare invece. Per alcuni esempi, consultate [Utilizzo di script inline](#use-inline-scripts) e [Utilizzo di script esterni.](#use-external-scripts)
- **primaryScriptUri**: specifica un URL accessibile pubblicamente allo script di distribuzione primario con le estensioni di file supportate.primaryScriptUri : Specify a publicly accessible Url to the primary deployment script with supported file extensions.
- **supportingScriptUris**: specifica una matrice di URL accessibili pubblicamente `ScriptContent` `PrimaryScriptUri`ai file di supporto chiamati in o .
- **timeout**: specificare il tempo massimo di esecuzione dello script consentito specificato nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
- **cleanupPreference**. Specificare la preferenza per la pulizia delle risorse di distribuzione quando l'esecuzione dello script passa allo stato terminale. L'impostazione predefinita è **Sempre**, che indica l'eliminazione delle risorse nonostante lo stato del terminale (Operazione completata, Operazione non riuscita, Annullata). Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).
- **retentionInterval**: specifica l'intervallo per il quale il servizio conserva le risorse dello script di distribuzione dopo che l'esecuzione dello script di distribuzione raggiunge uno stato del terminale. Le risorse dello script di distribuzione verranno eliminate alla scadenza di questa durata. La durata si basa sul [modello ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**, ovvero sette giorni. Questa proprietà viene usata quando l'opzione cleanupPreference è impostata su *OnExpiration*. La proprietà *OnExpiration* non è attualmente abilitata. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Altri esempi

- [creare e assegnare un certificato a un insieme di credenziali delle chiavi](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [creare e assegnare un'identità gestita assegnata dall'utente a un gruppo di risorse ed eseguire uno script di distribuzione.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)

> [!NOTE]
> È consigliabile creare un'identità assegnata dall'utente e concedere le autorizzazioni in anticipo. Se si creano l'identità e si concedono autorizzazioni nello stesso modello in cui si eseguono script di distribuzione, è possibile che vengano visualizzati errori correlati all'accesso e alle autorizzazioni. Ci vuole un po 'di tempo prima che le autorizzazioni diventino effettive.

## <a name="use-inline-scripts"></a>Utilizzare script inline

Il modello seguente ha una `Microsoft.Resources/deploymentScripts` risorsa definita con il tipo. La parte evidenziata è lo script in linea.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Poiché gli script di distribuzione inline sono racchiusi tra virgolette doppie, le stringhe all'interno degli script di distribuzione devono essere racchiuse tra virgolette singole. Il carattere di escape per PowerShell è **&#92;**. È anche possibile usare la sostituzione di stringhe come illustrato nell'esempio JSON precedente. Vedere il valore predefinito del parametro name.

Lo script accetta un parametro e restituisce il valore del parametro. **DeploymentScriptOutputs** viene utilizzato per archiviare gli output.  Nella sezione outputs, la linea **di valori** mostra come accedere ai valori memorizzati. `Write-Output`viene utilizzato a scopo di debug. Per informazioni su come accedere al file di output, vedere Debug di script di [distribuzione](#debug-deployment-scripts).  Per le descrizioni delle proprietà, consultate [Modelli di esempio.](#sample-templates)

Per eseguire lo script, selezionare **Prova** per aprire Cloud Shell, quindi incollare il codice seguente nel riquadro della shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

L'output è simile al seguente:

![Lo script di distribuzione del modello di Resource Manager hello world output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Utilizzare script esterni

Oltre agli script inline, è anche possibile utilizzare file di script esterni. Sono supportati solo gli script PowerShell primari con estensione **ps1.** Per gli script CLI, gli script principali possono avere estensioni (o senza estensione), purché gli script siano script bash validi. Per utilizzare file di `scriptContent` `primaryScriptUri`script esterni, sostituire con . Ad esempio:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Per visualizzare un esempio, selezionare [qui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

I file di script esterni devono essere accessibili.  Per proteggere i file di script archiviati negli account di archiviazione di Azure, vedere Esercitazione: Elementi sicuri nelle distribuzioni di modelli di Azure Resource Manager.To secure your script files that are stored in Azure storage accounts, see [Tutorial: Secure artifacts in Azure Resource Manager template deployments.](./template-tutorial-secure-artifacts.md)

L'utente è responsabile di garantire l'integrità degli script a cui fa riferimento lo script di distribuzione, **PrimaryScriptUri** o **SupportingScriptUris**.  Fare riferimento solo agli script considerati attendibili.

## <a name="use-supporting-scripts"></a>Utilizzare script di supporto

È possibile separare logiche complesse in uno o più file di script di supporto. La `supportingScriptURI` proprietà consente di fornire una matrice di URI ai file di script di supporto, se necessario:

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

I file di script di supporto possono essere chiamati sia da script inline che da file script primari. I file di script di supporto non hanno restrizioni sull'estensione del file.

I file di supporto vengono copiati in azscripts/azscriptinput in fase di esecuzione. Utilizzare il percorso relativo per fare riferimento ai file di supporto da script inline e file di script primari.

## <a name="work-with-outputs-from-powershell-script"></a>Usare gli output dallo script di PowerShellWork with outputs from PowerShell script

Il modello seguente mostra come passare i valori tra due risorse deploymentScripts:The following template shows how to pass values between two deploymentScripts resources:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

Nella prima risorsa si definisce una variabile denominata **$DeploymentScriptOutputs**e la si utilizza per archiviare i valori di output. Per accedere al valore di output da un'altra risorsa all'interno del modello, usare:To access the output value from another resource within the template, use:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Utilizzo degli output da script CLI

A differenza dello script di distribuzione di PowerShell, il supporto CLI/bash non espone una variabile comune per archiviare gli output di script, esiste invece una variabile di ambiente denominata **AZ_SCRIPTS_OUTPUT_PATH** che archivia il percorso in cui si trova il file degli output dello script. Se uno script di distribuzione viene eseguito da un modello di Resource Manager, questa variabile di ambiente viene impostata automaticamente dalla shell Bash.If a deployment script is run from a Resource Manager template, this environment variable is set automatically for you by the Bash shell.

Gli output dello script di distribuzione devono essere salvati nel percorso AZ_SCRIPTS_OUTPUT_PATH e gli output devono essere un oggetto stringa JSON valido. Il contenuto del file deve essere salvato come coppia chiave-valore. Ad esempio, una matrice di stringhe viene archiviata come : "MyResult": [ "foo", "bar"] .  L'archiviazione solo dei risultati della matrice, ad esempio [ "foo", "bar" ], non è valida.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) viene utilizzato nell'esempio precedente. Viene fornito con le immagini del contenitore. Vedere [Configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Sviluppare script di distribuzioneDevelop deployment scripts

### <a name="handle-non-terminating-errors"></a>Gestire gli errori non terminati

È possibile controllare il modo in cui PowerShell risponde agli errori non di terminazione usando la variabile [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) nello script di distribuzione. Il motore di script di distribuzione non imposta/modifica il valore.  Nonostante il valore impostato per $ErrorActionPreference, lo script di distribuzione imposta lo stato di provisioning delle risorse su *Non riuscito* quando lo script rileva un errore.

### <a name="pass-secured-strings-to-deployment-script"></a>Passare stringhe protette allo script di distribuzionePass secured strings to deployment script

L'impostazione delle variabili di ambiente (EnvironmentVariable) nelle istanze del contenitore consente di fornire la configurazione dinamica dell'applicazione o dello script eseguito dal contenitore. Lo script di distribuzione gestisce le variabili di ambiente non protette e protette nello stesso modo dell'istanza del contenitore di Azure.Deployment script handles non-secured and secured environment variables in the same way as Azure Container Instance. Per ulteriori informazioni, vedere [Impostare le variabili di ambiente nelle istanze del contenitore.](../../container-instances/container-instances-environment-variables.md#secure-values)

## <a name="debug-deployment-scripts"></a>Eseguire il debug degli script di distribuzioneDebug deployment scripts

Il servizio di script crea un [account di archiviazione](../../storage/common/storage-account-overview.md) e [un'istanza del contenitore](../../container-instances/container-instances-overview.md) per l'esecuzione dello script. Entrambe le risorse hanno il suffisso **azscripts** nei nomi delle risorse.

![Nomi delle risorse di script di distribuzione dei modelli di Resource ManagerResource Manager template deployment script resource names](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Lo script utente, i risultati dell'esecuzione e il file stdout vengono archiviati nelle condivisioni file dell'account di archiviazione. C'è una cartella chiamata **azscripts**. Nella cartella sono presenti altre due cartelle per i file di input e di output: **azscriptinput** e **azscriptoutput**.

La cartella di output contiene un file **executionresult.json** e il file di output dello script. È possibile visualizzare il messaggio di errore di esecuzione dello script in **executionresult.json**. Il file di output viene creato solo quando lo script viene eseguito correttamente. La cartella di input contiene un file di script di sistema di PowerShell e i file di script di distribuzione dell'utente. È possibile sostituire il file di script di distribuzione utente con uno rivisto ed eseguire nuovamente lo script di distribuzione dall'istanza del contenitore di Azure.You can replace the user deployment script file with a revised one, and rerun the deployment script from the Azure container instance.

È possibile ottenere le informazioni sulla distribuzione delle risorse script di distribuzione a livello di gruppo di risorse e a livello di sottoscrizione usando l'API REST:You can get the deployment script resource deployment information at the resource group and the subscription level by using REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

Nell'esempio seguente viene utilizzato [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

L'output è simile a:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

L'output mostra lo stato della distribuzione e gli ID delle risorse dello script di distribuzione.

L'API REST seguente restituisce il log:The following REST API returns the log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Funziona solo prima dell'eliminazione delle risorse dello script di distribuzione.

Per visualizzare la risorsa deploymentScripts nel portale, selezionare **Mostra tipi nascosti:**

![Script di distribuzione del modello di Resource Manager, mostra tipi nascosti, portale](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Pulire le risorse dello script di distribuzione

Lo script di distribuzione crea un account di archiviazione e un'istanza del contenitore usati per l'esecuzione di script di distribuzione e l'archiviazione delle informazioni di debug. Queste due risorse vengono create nello stesso gruppo di risorse delle risorse di cui è stato eseguito il provisioning e verranno eliminate dal servizio script alla scadenza dello script. È possibile controllare il ciclo di vita di queste risorse.  Finché non vengono eliminate, vengono fatturate entrambe le risorse. Per informazioni sui prezzi, vedere [Prezzi delle istanze del contenitore](https://azure.microsoft.com/pricing/details/container-instances/) e Prezzi di [Archiviazione di Azure.For](https://azure.microsoft.com/pricing/details/storage/)the price information, see Container Instances pricing and Azure Storage pricing .

Il ciclo di vita di queste risorse è controllato dalle seguenti proprietà nel modello:

- **cleanupPreference**: preferenza di pulizia quando l'esecuzione dello script entra in uno stato terminale.  I valori supportati sono:

  - **Sempre**: Elimina le risorse una volta che l'esecuzione dello script entra in uno stato terminale. Poiché la risorsa deploymentScripts potrebbe essere ancora presente dopo la pulizia delle risorse, lo script di sistema copierà i risultati dell'esecuzione dello script, ad esempio stdout, output, valore restituito e così via in DB prima che le risorse vengano eliminate.
  - **OnSuccess**: Elimina le risorse solo quando l'esecuzione dello script ha esito positivo. È comunque possibile accedere alle risorse per trovare le informazioni di debug.
  - **OnExpiration**: elimina le risorse solo quando l'impostazione **retentionInterval** è scaduta. Questa proprietà è attualmente disabilitata.

- **retentionInterval**: Specificare l'intervallo di tempo per cui una risorsa script verrà mantenuta e dopo di che verrà scaduta ed eliminata.

> [!NOTE]
> Non è consigliabile utilizzare le risorse dello script di distribuzione per altri scopi.

## <a name="run-script-more-than-once"></a>Esegui script più di una volta

L'esecuzione dello script di distribuzione è un'operazione idempotente. Se nessuna delle proprietà della risorsa deploymentScripts (incluso lo script inline) viene modificata, lo script non verrà eseguito quando si ridistribuisce il modello. Il servizio script di distribuzione confronta i nomi delle risorse nel modello con le risorse esistenti nello stesso gruppo di risorse. Sono disponibili due opzioni se si desidera eseguire lo stesso script di distribuzione più volte:There are two options if you want to execute the same deployment script multiple times:

- Modificare il nome della risorsa deploymentScripts. Ad esempio, utilizzare la funzione di modello [utcNow](./template-functions-string.md#utcnow) come nome della risorsa o come parte del nome della risorsa. La modifica del nome della risorsa crea una nuova risorsa deploymentScripts.Changing the resource name creates a new deploymentScripts resource. È bene per mantenere una cronologia di esecuzione dello script.

    > [!NOTE]
    > La funzione utcNow può essere utilizzata solo nel valore predefinito per un parametro.

- Specificare un valore `forceUpdateTag` diverso nella proprietà del modello.  Ad esempio, utilizzare utcNow come valore.

> [!NOTE]
> Scrivere gli script di distribuzione idempotenti. Ciò garantisce che se vengono eseguiti di nuovo accidentalmente, non causerà modifiche di sistema. Ad esempio, se lo script di distribuzione viene usato per creare una risorsa di Azure, verificare che la risorsa non esista prima di crearla, in modo che lo script abbia esito positivo o non la crei di nuovo.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

È possibile usare un'immagine del contenitore docker preconfigurata come ambiente di sviluppo di script di distribuzione. La procedura seguente mostra come configurare l'immagine docker in Windows. Per Linux e Mac, è possibile trovare le informazioni su Internet.

1. Installare [Docker Desktop](https://www.docker.com/products/docker-desktop) nel computer di sviluppo.
1. Aprire Docker Desktop.
1. Seleziona l'icona Docker Desktop dalle barre delle applicazioni, quindi seleziona **Impostazioni**.
1. Selezionare **Unità condivise**, selezionare un'unità locale che si desidera sia disponibile per i contenitori e quindi selezionare **Applica**

    ![Unità docker script di distribuzione modello di Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Immettere le credenziali di Windows al prompt.
1. Aprire una finestra del terminale, prompt dei comandi o Windows PowerShell (non utilizzare PowerShell ISE).
1. Eseguire il pull dell'immagine del contenitore di script di distribuzione nel computer locale:Pull the deployment script container image to the local computer:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    L'esempio usa la versione PowerShell 2.7.0.The example uses version PowerShell 2.7.0.

    Per estrarre un'immagine dell'interfaccia della riga di comando da un registro contenitori Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    In questo esempio viene utilizzata la versione CLI 2.0.80.This example uses version CLI 2.0.80. Lo script di distribuzione utilizza le immagini predefinite dei contenitori dell'interfaccia della riga [di comando](https://hub.docker.com/_/microsoft-azure-cli)disponibili qui .

1. Eseguire l'immagine docker in locale.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Sostituire ** &lt;** la lettera>del driver host e ** &lt;** il nome della directory host>con una cartella esistente nell'unità condivisa.  Esegue il mapping della cartella alla cartella **/data** nel contenitore. Ad esempio, per eseguire il mapping di D:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-significa** mantenere attiva l'immagine del contenitore.

    Un esempio CLI:A CLI example:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selezionare **Condividi** quando viene visualizzato un prompt.
1. Nella schermata seguente viene illustrato come eseguire uno script di PowerShell, dato che si dispone di un file helloworld.ps1 nella cartella d:.

    ![Resource Manager modello di distribuzione script docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Dopo che lo script è stato testato correttamente, è possibile utilizzarlo come script di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come utilizzare gli script di distribuzione. Per eseguire un'esercitazione su uno script di distribuzione:To walk through a deployment script tutorial:

> [!div class="nextstepaction"]
> [Esercitazione: Usare gli script di distribuzione nei modelli di Azure Resource ManagerTutorial: Use deployment scripts in Azure Resource Manager templates](./template-tutorial-deployment-script.md)