---
title: Usare gli script di distribuzione nei modelli | Microsoft Docs
description: usare gli script di distribuzione in modelli Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: 99db4ec61a515301224691d7c2e4e3c905fee1c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188910"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Usare gli script di distribuzione nei modelli (anteprima)

Informazioni su come usare gli script di distribuzione nei modelli di risorse di Azure. Con un nuovo tipo di risorsa `Microsoft.Resources/deploymentScripts`denominato, gli utenti possono eseguire script di distribuzione nelle distribuzioni di modelli ed esaminare i risultati dell'esecuzione. Questi script possono essere usati per eseguire passaggi personalizzati, ad esempio:

- aggiungere utenti a una directory
- creare una registrazione per l'app
- eseguire operazioni sul piano dati, ad esempio, copiare i BLOB o il database di inizializzazione
- cercare e convalidare un codice di licenza
- Creare un certificato autofirmato
- creazione di un oggetto in Azure AD
- cercare i blocchi di indirizzi IP dal sistema personalizzato

I vantaggi dello script di distribuzione:

- Facile da codificare, utilizzare ed eseguire il debug. È possibile sviluppare script di distribuzione negli ambienti di sviluppo preferiti. Gli script possono essere incorporati in modelli o in file di script esterni.
- È possibile specificare la piattaforma e il linguaggio di scripting. Attualmente sono supportati gli script di distribuzione Azure PowerShell e dell'interfaccia della riga di comando di Azure nell'ambiente Linux.
- Consente di specificare le identità utilizzate per eseguire gli script. Attualmente è supportata solo l' [identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Consente di passare gli argomenti della riga di comando allo script.
- Può specificare gli output dello script e passarli nuovamente alla distribuzione.

La risorsa script di distribuzione è disponibile solo nelle aree in cui è disponibile l'istanza di contenitore di Azure.  Vedere [disponibilità delle risorse per istanze di contenitore di Azure in aree di Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Due risorse dello script di distribuzione, un account di archiviazione e un'istanza del contenitore, vengono create nello stesso gruppo di risorse per l'esecuzione dello script e la risoluzione dei problemi. Queste risorse vengono in genere eliminate dal servizio script quando l'esecuzione dello script di distribuzione si trova in uno stato terminale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per altre informazioni, vedere [Pulisci risorse script di distribuzione](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Prerequisiti

- **Identità gestita assegnata dall'utente con il ruolo di collaboratore al gruppo di risorse di destinazione**. Questa identità viene usata per eseguire gli script di distribuzione. Per eseguire operazioni all'esterno del gruppo di risorse, è necessario concedere autorizzazioni aggiuntive. Ad esempio, assegnare l'identità al livello della sottoscrizione se si vuole creare un nuovo gruppo di risorse.

  > [!NOTE]
  > Il motore di script di distribuzione crea un account di archiviazione e un'istanza del contenitore in background.  Un'identità gestita assegnata dall'utente con il ruolo di collaboratore a livello di sottoscrizione è obbligatoria se la sottoscrizione non ha registrato i provider di risorse dell'account di archiviazione di Azure (Microsoft. Storage) e dell'istanza di contenitore di Azure (Microsoft. ContainerInstance).

  Per creare un'identità, vedere [creare un'identità gestita assegnata dall'utente usando il portale di Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)o l' [interfaccia](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)della riga di comando di Azure oppure [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). È necessario l'ID identità per distribuire il modello. Il formato dell'identità è:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Usare l'interfaccia della riga di comando o lo script di PowerShell seguente per ottenere l'ID fornendo il nome del gruppo di risorse e il nome dell'identità.

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

- **Azure PowerShell** o l'interfaccia della riga di comando di **Azure**. Per un elenco delle versioni di Azure PowerShell supportate, vedere [qui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). per un elenco delle versioni supportate dell'interfaccia della riga di comando di Azure, vedere [qui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Lo script di distribuzione usa le immagini CLI disponibili di Microsoft Container Registry. La certificazione di un'immagine dell'interfaccia della riga di comando per uno script di distribuzione richiede circa un mese. Non usare le versioni dell'interfaccia della riga di comando rilasciate entro 30 giorni. Per trovare le date di rilascio per le immagini, vedere [Note sulla versione dell'interfaccia](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)della riga di comando di Azure. Se viene utilizzata una versione non supportata, nel messaggio di errore vengono elencate le versioni supportate.

    Queste versioni non sono necessarie per la distribuzione dei modelli. Queste versioni sono tuttavia necessarie per testare localmente gli script di distribuzione. Vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). È possibile usare un'immagine Docker preconfigurata.  Vedere [configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="sample-templates"></a>Modelli di esempio

Il codice JSON seguente è un esempio.  Lo schema del modello più recente è disponibile [qui](/azure/templates/microsoft.resources/deploymentscripts).

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

- **Identity**: il servizio script di distribuzione usa un'identità gestita assegnata dall'utente per eseguire gli script. Attualmente è supportata solo l'identità gestita assegnata dall'utente.
- **kind**: specificare il tipo di script. Attualmente, Azure PowerShell e gli script dell'interfaccia della riga di comando di Azure sono supportati. I valori sono **AzurePowerShell** e **AzureCLI**.
- **Proprietà forceupdatetag**: la modifica di questo valore tra le distribuzioni di modelli forza la ripetizione dell'esecuzione dello script di distribuzione. Usare la funzione newGuid () o utcNow () che deve essere impostata come defaultValue di un parametro. Per altre informazioni, vedere [Eseguire lo script più di una volta](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: specificare la versione del modulo da usare. Per un elenco delle versioni supportate di PowerShell e dell'interfaccia della riga di comando, vedere [prerequisiti](#prerequisites).
- **arguments**: Specificare i valori del parametro. I valori sono separati da uno spazio.
- **EnvironmentVariables**: specificare le variabili di ambiente da passare allo script. Per altre informazioni, vedere [sviluppare script di distribuzione](#develop-deployment-scripts).
- **scriptContent**: specificare il contenuto dello script. Per eseguire uno script esterno, usare `primaryScriptUri` invece. Per esempi, vedere [usare script inline](#use-inline-scripts) e [usare uno script esterno](#use-external-scripts).
- **primaryScriptUri**: specificare un URL accessibile pubblicamente per lo script di distribuzione primario con le estensioni di file supportate.
- **supportingScriptUris**: specificare una matrice di URL accessibili pubblicamente per supportare i file che vengono chiamati `ScriptContent` in `PrimaryScriptUri`o.
- **timeout**: specificare il tempo di esecuzione dello script massimo consentito nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
- **cleanupPreference**. Specificare la preferenza per la pulizia delle risorse di distribuzione quando l'esecuzione dello script si trova in uno stato terminale. L'impostazione predefinita è **sempre**, il che significa eliminare le risorse nonostante lo stato del terminale (riuscito, non riuscito, annullato). Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).
- **retentionInterval**: specificare l'intervallo per il quale il servizio conserva le risorse dello script di distribuzione dopo che l'esecuzione dello script di distribuzione raggiunge uno stato terminale. Le risorse dello script di distribuzione verranno eliminate alla scadenza della durata. La durata è basata sul [modello ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**, che significa sette giorni. Questa proprietà viene usata quando l'opzione cleanupPreference è impostata su *OnExpiration*. La proprietà *Onexpirement* non è attualmente abilitata. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Altri esempi

- [creare e assegnare un certificato a un insieme di credenziali delle chiavi](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [creare e assegnare un'identità gestita assegnata dall'utente a un gruppo di risorse ed eseguire uno script di distribuzione](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> È consigliabile creare un'identità assegnata dall'utente e concedere in anticipo le autorizzazioni. È possibile che vengano restituiti errori di accesso e di autorizzazione se si crea l'identità e si concedono le autorizzazioni nello stesso modello in cui vengono eseguiti gli script di distribuzione. Il tempo necessario prima che le autorizzazioni diventino effettive.

## <a name="use-inline-scripts"></a>Usa script inline

Il modello seguente include una risorsa definita con il `Microsoft.Resources/deploymentScripts` tipo. La parte evidenziata è lo script inline.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Poiché gli script di distribuzione inline sono racchiusi tra virgolette doppie, le stringhe all'interno degli script di distribuzione devono essere racchiuse tra virgolette singole. Il carattere di escape per PowerShell è **&#92;** . È anche possibile prendere in considerazione l'uso della sostituzione di stringhe come illustrato nell'esempio JSON precedente. Vedere il valore predefinito del parametro Name.

Lo script accetta un parametro e genera l'output del valore del parametro. **DeploymentScriptOutputs** viene usato per archiviare gli output.  Nella sezione Outputs (output) la riga **valore** Mostra come accedere ai valori archiviati. `Write-Output`viene utilizzato a scopo di debug. Per informazioni su come accedere al file di output, vedere [debug degli script di distribuzione](#debug-deployment-scripts).  Per le descrizioni delle proprietà, vedere [modelli di esempio](#sample-templates).

Per eseguire lo script, selezionare **prova** per aprire il cloud Shell e quindi incollare il codice seguente nel riquadro della shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

L'output è simile al seguente:

![Script di distribuzione del modello di Gestione risorse Hello World output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usare script esterni

Oltre agli script inline, è anche possibile usare file di script esterni. Sono supportati solo gli script di PowerShell primari con l'estensione di file **ps1** . Per gli script dell'interfaccia della riga di comando, gli script primari possono avere estensioni (o senza estensione), purché gli script siano script bash validi. Per usare file di script esterni, `scriptContent` sostituire `primaryScriptUri`con. Ad esempio:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Per visualizzare un esempio, fare clic [qui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

I file di script esterni devono essere accessibili.  Per proteggere i file di script archiviati negli account di archiviazione di Azure, vedere [distribuire un modello ARM privato con token SAS](./secure-template-with-sas-token.md).

L'utente è responsabile di garantire l'integrità degli script a cui viene fatto riferimento dallo script di distribuzione, ovvero **PrimaryScriptUri** o **SupportingScriptUris**.  Fare riferimento solo a script attendibili.

## <a name="use-supporting-scripts"></a>Usare gli script di supporto

È possibile separare le logiche complesse in uno o più file di script di supporto. La `supportingScriptURI` proprietà consente di fornire una matrice di URI ai file di script di supporto, se necessario:

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

I file script di supporto possono essere chiamati sia da script inline che da file script primari. Il supporto dei file di script non prevede alcuna restrizione sull'estensione di file.

I file di supporto vengono copiati in azscripts/azscriptinput in fase di esecuzione. Utilizzare il percorso relativo per fare riferimento ai file di supporto da script inline e file script primari.

## <a name="work-with-outputs-from-powershell-script"></a>Usare gli output dello script di PowerShell

Il modello seguente mostra come passare i valori tra due risorse di deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

Nella prima risorsa definire una variabile denominata **$DeploymentScriptOutputs**e usarla per archiviare i valori di output. Per accedere al valore di output da un'altra risorsa all'interno del modello, usare:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Usare gli output dello script dell'interfaccia della riga di comando

Diverso dallo script di distribuzione di PowerShell, il supporto CLI/bash non espone una variabile comune per archiviare gli output di script, ma è presente una variabile di ambiente denominata **AZ_SCRIPTS_OUTPUT_PATH** che archivia il percorso in cui risiede il file di output dello script. Se uno script di distribuzione viene eseguito da un modello di Gestione risorse, questa variabile di ambiente viene impostata automaticamente dalla shell bash.

Gli output dello script di distribuzione devono essere salvati nel percorso AZ_SCRIPTS_OUTPUT_PATH e gli output devono essere un oggetto stringa JSON valido. Il contenuto del file deve essere salvato come coppia chiave-valore. Una matrice di stringhe, ad esempio, viene archiviata come {"risultato": ["foo", "bar"]}.  Archiviare solo i risultati della matrice, ad esempio ["foo", "bar"], non è valido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) viene usato nell'esempio precedente. Viene fornita con le immagini del contenitore. Vedere [configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Sviluppare script di distribuzione

### <a name="handle-non-terminating-errors"></a>Gestione degli errori non fatali

È possibile controllare il modo in cui PowerShell risponde agli errori non fatali usando la variabile [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) nello script di distribuzione. Il motore di script di distribuzione non imposta o modifica il valore.  Nonostante il valore impostato per $ErrorActionPreference, lo script di distribuzione imposta lo stato di provisioning delle risorse su *non riuscito* quando si verifica un errore nello script.

### <a name="pass-secured-strings-to-deployment-script"></a>Passa stringhe protette allo script di distribuzione

L'impostazione delle variabili di ambiente (Metodo EnvironmentVariable) nelle istanze del contenitore consente di fornire la configurazione dinamica dell'applicazione o dello script eseguito dal contenitore. Lo script di distribuzione gestisce le variabili di ambiente non protette e protette in modo analogo all'istanza di contenitore di Azure. Per altre informazioni, vedere [impostare le variabili di ambiente nelle istanze di contenitore](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Debug degli script di distribuzione

Il servizio script crea un [account di archiviazione](../../storage/common/storage-account-overview.md) e un' [istanza del contenitore](../../container-instances/container-instances-overview.md) per l'esecuzione dello script. Entrambe le risorse hanno il suffisso **azscripts** nei nomi delle risorse.

![Nomi delle risorse dello script di distribuzione del modello Gestione risorse](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Lo script utente, i risultati dell'esecuzione e il file stdout vengono archiviati nelle condivisioni file dell'account di archiviazione. È presente una cartella denominata **azscripts**. Nella cartella sono disponibili altre due cartelle per i file di input e di output: **azscriptinput** e **azscriptoutput**.

La cartella di output contiene un file **executionresult.json** e il file di output dello script. È possibile visualizzare il messaggio di errore di esecuzione dello script in **ExecutionResult. JSON**. Il file di output viene creato solo quando lo script viene eseguito correttamente. La cartella di input contiene un file di script di sistema di PowerShell e i file di script di distribuzione dell'utente. È possibile sostituire il file script di distribuzione dell'utente con uno modificato e rieseguire lo script di distribuzione dall'istanza di contenitore di Azure.

È possibile ottenere le informazioni sulla distribuzione delle risorse dello script di distribuzione a livello di gruppo di risorse e a livello di sottoscrizione usando l'API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

L'esempio seguente usa [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

L'output è simile a:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

L'output Mostra lo stato di distribuzione e gli ID di risorsa dello script di distribuzione.

La seguente API REST restituisce il log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Funziona solo prima che vengano eliminate le risorse dello script di distribuzione.

Per visualizzare la risorsa deploymentScripts nel portale, selezionare **Mostra tipi nascosti**:

![Gestione risorse script di distribuzione del modello, Mostra tipi nascosti, portale](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Pulisci risorse script di distribuzione

Lo script di distribuzione crea un account di archiviazione e un'istanza del contenitore usati per l'esecuzione degli script di distribuzione e l'archiviazione delle informazioni di debug. Queste due risorse vengono create nello stesso gruppo di risorse delle risorse di cui è stato effettuato il provisioning e verranno eliminate dal servizio script al termine dello script. È possibile controllare il ciclo di vita di queste risorse.  Fino a quando non vengono eliminati, verranno addebitate entrambe le risorse. Per informazioni sui prezzi, vedere prezzi delle [istanze di contenitore](https://azure.microsoft.com/pricing/details/container-instances/) e prezzi di [archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Il ciclo di vita di queste risorse è controllato dalle seguenti proprietà nel modello:

- **cleanupPreference**: consente di pulire le preferenze quando l'esecuzione dello script si trova in uno stato terminale.  I valori supportati sono:

  - **Sempre**: eliminare le risorse dopo che l'esecuzione dello script si trova in uno stato terminale. Poiché la risorsa deploymentScripts potrebbe essere ancora presente dopo la pulizia delle risorse, lo script del sistema copia i risultati dell'esecuzione dello script, ad esempio stdout, gli output, il valore restituito e così via, nel database prima che le risorse vengano eliminate.
  - **OnSuccess**: Elimina le risorse solo quando l'esecuzione dello script ha esito positivo. È comunque possibile accedere alle risorse per trovare le informazioni di debug.
  - **Onexpireation**: Elimina le risorse solo quando l'impostazione **retentionInterval** è scaduta. Questa proprietà è attualmente disabilitata.

- **retentionInterval**: specificare l'intervallo di tempo durante il quale verrà mantenuta una risorsa di script e dopo la scadenza e l'eliminazione.

> [!NOTE]
> Non è consigliabile usare le risorse dello script di distribuzione per altri scopi.

## <a name="run-script-more-than-once"></a>Esegui script più di una volta

L'esecuzione dello script di distribuzione è un'operazione idempotente. Se nessuna delle proprietà della risorsa deploymentScripts (incluso lo script inline) viene modificata, lo script non verrà eseguito quando si ridistribuisce il modello. Il servizio script di distribuzione Confronta i nomi delle risorse nel modello con le risorse esistenti nello stesso gruppo di risorse. Se si vuole eseguire lo stesso script di distribuzione più volte, sono disponibili due opzioni:

- Modificare il nome della risorsa deploymentScripts. Ad esempio, usare la funzione di modello [UtcNow](./template-functions-date.md#utcnow) come nome della risorsa o come parte del nome della risorsa. Se si modifica il nome della risorsa, viene creata una nuova risorsa deploymentScripts. È opportuno mantenere una cronologia dell'esecuzione dello script.

    > [!NOTE]
    > La funzione utcNow può essere usata solo nel valore predefinito per un parametro.

- Specificare un valore diverso nella proprietà `forceUpdateTag` template.  Ad esempio, usare utcNow come valore.

> [!NOTE]
> Scrivere gli script di distribuzione idempotente. In questo modo si garantisce che, in caso di esecuzione accidentale, non provocherà modifiche al sistema. Ad esempio, se si usa lo script di distribuzione per creare una risorsa di Azure, verificare che la risorsa non esista prima di crearla, in modo che lo script abbia esito positivo o non venga ricreata la risorsa.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

È possibile usare un'immagine del contenitore Docker preconfigurata come ambiente di sviluppo dello script di distribuzione. La procedura seguente illustra come configurare l'immagine Docker in Windows. Per Linux e Mac, è possibile trovare le informazioni su Internet.

1. Installare [Docker desktop](https://www.docker.com/products/docker-desktop) nel computer di sviluppo.
1. Aprire Docker desktop.
1. Selezionare l'icona del desktop Docker nelle barra delle applicazioni e quindi selezionare **Impostazioni**.
1. Selezionare **unità condivise**, selezionare un'unità locale che si desidera sia disponibile per i contenitori, quindi selezionare **applica** .

    ![Script di distribuzione del modello di Gestione risorse unità Docker](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Immettere le credenziali di Windows al prompt.
1. Aprire una finestra del terminale, ovvero il prompt dei comandi o Windows PowerShell (non usare PowerShell ISE).
1. Eseguire il pull dell'immagine del contenitore dello script di distribuzione nel computer locale:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Nell'esempio viene usata la versione di PowerShell 2.7.0.

    Per eseguire il pull di un'immagine dell'interfaccia della riga di comando da un Container Registry Microsoft ():

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Questo esempio usa l'interfaccia della riga di comando 2.0.80. Lo script di distribuzione usa le immagini dei contenitori CLI predefinite disponibili [qui](https://hub.docker.com/_/microsoft-azure-cli).

1. Eseguire l'immagine Docker in locale.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Sostituire ** &lt;la lettera del driver host>** e ** &lt;il nome della directory host>** con una cartella esistente nell'unità condivisa.  Esegue il mapping della cartella alla cartella **/Data** nel contenitore. Per esempi, per eseguire il mapping di D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-** significa mantenere attivo l'immagine del contenitore.

    Esempio di interfaccia della riga di comando:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selezionare **Condividi** quando si riceve un messaggio di richiesta.
1. Lo screenshot seguente illustra come eseguire uno script di PowerShell, dato che è presente un file HelloWorld. ps1 nella cartella d:\docker.

    ![Script di distribuzione del modello di Gestione risorse Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Una volta testato correttamente lo script, è possibile utilizzarlo come script di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare gli script di distribuzione. Per esaminare un'esercitazione relativa a uno script di distribuzione:

> [!div class="nextstepaction"]
> [Esercitazione: usare gli script di distribuzione nei modelli di Azure Resource Manager](./template-tutorial-deployment-script.md)