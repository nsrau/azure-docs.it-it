---
title: Usare gli script di distribuzione nei modelli | Microsoft Docs
description: usare gli script di distribuzione in modelli Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: jgao
ms.openlocfilehash: 125fefbb1d83db8b6114b2d09f5bd6da885159ba
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547643"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Usare gli script di distribuzione nei modelli (anteprima)

Informazioni su come usare gli script di distribuzione nei modelli di risorse di Azure. Con un nuovo tipo di risorsa denominato `Microsoft.Resources/deploymentScripts`, gli utenti possono eseguire script di distribuzione nelle distribuzioni di modelli ed esaminare i risultati dell'esecuzione. Questi script possono essere usati per eseguire passaggi personalizzati, ad esempio:

- aggiungere utenti a una directory
- creare una registrazione per l'app
- eseguire operazioni sul piano dati, ad esempio, copiare i BLOB o il database di inizializzazione
- cercare e convalidare un codice di licenza
- Creare un certificato autofirmato
- creazione di un oggetto in Azure AD
- cercare i blocchi di indirizzi IP dal sistema personalizzato

I vantaggi dello script di distribuzione:

- Facile da codificare, utilizzare ed eseguire il debug. È possibile sviluppare script di distribuzione negli ambienti di sviluppo preferiti. Gli script possono essere incorporati in modelli o in file di script esterni.
- È possibile specificare la piattaforma e il linguaggio di scripting. Attualmente, sono supportati solo gli script di distribuzione Azure PowerShell nell'ambiente Linux.
- Consente di specificare le identità utilizzate per eseguire gli script. Attualmente è supportata solo l' [identità gestita assegnata dall'utente di Azure](../../active-directory/managed-identities-azure-resources/overview.md) .
- Consente di passare gli argomenti della riga di comando allo script.
- Può specificare gli output dello script e passarli nuovamente alla distribuzione.

> [!NOTE]
> Lo script di distribuzione è attualmente in anteprima. Per usarlo, è necessario [iscriversi all'anteprima](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Due risorse dello script di distribuzione, un account di archiviazione e un'istanza del contenitore, vengono create nello stesso gruppo di risorse per l'esecuzione di script e la risoluzione dei problemi. Queste risorse vengono in genere eliminate dal servizio script quando l'esecuzione dello script di distribuzione si trova in uno stato terminale. Verranno addebitate le risorse fino a quando le risorse non verranno eliminate. Per altre informazioni, vedere [Pulisci risorse script di distribuzione](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Prerequisiti

- **Identità gestita assegnata dall'utente con il ruolo di collaboratore a livello di sottoscrizione**. Questa identità viene utilizzata per eseguire gli script di distribuzione. Per crearne uno, vedere [creare un'identità gestita assegnata dall'utente usando il portale di Azure o l'interfaccia della](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)riga di comando di [Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)oppure [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). È necessario l'ID identità quando si distribuisce il modello. Il formato dell'identità è:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Usare lo script di PowerShell seguente per ottenere l'ID fornendo il nome del gruppo di risorse e il nome dell'identità.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell versione 2.7.0, 2.8.0 o 3.0.0**. Queste versioni non sono necessarie per la distribuzione dei modelli. Queste versioni sono tuttavia necessarie per testare localmente gli script di distribuzione. Vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). È possibile usare un'immagine Docker preconfigurata.  Vedere [configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="resource-schema"></a>Schema delle risorse

Il codice JSON seguente è un esempio.  Lo schema del modello più recente è disponibile [qui](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
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
- **Kind**: specificare il tipo di script. Attualmente, solo Azure PowerShell script è supportato. Il valore è **AzurePowerShell**.
- **Proprietà forceupdatetag**: la modifica di questo valore tra le distribuzioni di modelli forza la ripetizione dell'esecuzione dello script di distribuzione. Usare la funzione newGuid () o utcNow () che deve essere impostata come defaultValue di un parametro. Per altre informazioni, vedere [eseguire lo script più di una volta](#run-script-more-than-once).
- **azPowerShellVersion**: specificare la versione del modulo Azure PowerShell da usare. Lo script di distribuzione supporta attualmente la versione 2.7.0, 2.8.0 e 3.0.0.
- **argomenti**: specificare i valori dei parametri. I valori sono separati da spazi.
- **scriptContent**: specificare il contenuto dello script. Per eseguire uno script esterno, usare invece `primaryScriptUri`. Per esempi, vedere [usare script inline](#use-inline-scripts) e [usare uno script esterno](#use-external-scripts).
- **primaryScriptUri**: specificare un URL accessibile pubblicamente per lo script di PowerShell primario con l'estensione di file di PowerShell supportata.
- **supportingScriptUris**: specificare una matrice di URL accessibili pubblicamente per supportare i file di PowerShell che verranno chiamati in `ScriptContent` o `PrimaryScriptUri`.
- **timeout**: specificare il tempo di esecuzione dello script massimo consentito specificato nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
- **cleanupPreference**. Specificare la preferenza per la pulizia delle risorse di distribuzione quando l'esecuzione dello script si trova in uno stato terminale. L'impostazione predefinita è **sempre**, il che significa eliminare le risorse nonostante lo stato del terminale (riuscito, non riuscito, annullato). Per altre informazioni, vedere [pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).
- **retentionInterval**: specificare l'intervallo per il quale il servizio conserva le risorse dello script di distribuzione dopo che l'esecuzione dello script di distribuzione raggiunge uno stato terminale. Le risorse dello script di distribuzione verranno eliminate alla scadenza della durata. La durata è basata sul [modello ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**, che significa sette giorni. Questa proprietà viene utilizzata quando cleanupPreference è impostato su *Onexpirement*. La proprietà *Onexpirement* non è attualmente abilitata. Per altre informazioni, vedere [pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Usa script inline

Il modello seguente include una risorsa definita con il tipo di `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Poiché gli script di distribuzione inline sono racchiusi tra virgolette doppie, le stringhe all'interno degli script di distribuzione devono essere racchiuse tra virgolette singole. Il carattere di escape per PowerShell **&#92;** è. È anche possibile prendere in considerazione l'uso della sostituzione di stringhe come illustrato nell'esempio JSON precedente. Vedere il valore predefinito del parametro Name.

Lo script accetta un parametro e genera l'output del valore del parametro. **DeploymentScriptOutputs** viene usato per archiviare gli output.  Nella sezione Outputs (output) la riga **valore** Mostra come accedere ai valori archiviati. `Write-Output` viene utilizzato a scopo di debug. Per informazioni su come accedere al file di output, vedere [debug degli script di distribuzione](#debug-deployment-scripts).  Per le descrizioni delle proprietà, vedere [schema di risorse](#resource-schema).

Per eseguire lo script, selezionare **prova** per aprire cloud Shell e quindi incollare il codice seguente nel riquadro della shell.

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

Oltre agli script inline, è anche possibile usare file di script esterni. Attualmente sono supportati solo gli script di PowerShell con l'estensione di file **ps1** . Per usare file di script esterni, sostituire `scriptContent` con `primaryScriptUri`. Ad esempio:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Per visualizzare un esempio, fare clic [qui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

I file di script esterni devono essere accessibili.  Per proteggere i file di script archiviati negli account di archiviazione di Azure, vedere [esercitazione: proteggere gli artefatti nelle distribuzioni di modelli Azure Resource Manager](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Usare gli script di supporto

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

I file script di supporto possono essere chiamati sia da script inline che da file script primari.

I file di supporto vengono copiati in azscripts/azscriptinput in fase di esecuzione. Utilizzare il percorso relativo per fare riferimento ai file di supporto da script inline e file script primari.

## <a name="work-with-outputs-from-deployment-scripts"></a>Usare gli output degli script di distribuzione

Il modello seguente mostra come passare i valori tra due risorse di deploymentScripts:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

Nella prima risorsa definire una variabile denominata **$DeploymentScriptOutputs**e usarla per archiviare i valori di output. Per accedere al valore di output da un'altra risorsa all'interno del modello, usare:

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>Debug degli script di distribuzione

Il servizio script crea un [account di archiviazione](../../storage/common/storage-account-overview.md) e un' [istanza del contenitore](../../container-instances/container-instances-overview.md) per l'esecuzione dello script. Entrambe le risorse hanno il suffisso **azscripts** nei nomi delle risorse.

![Nomi delle risorse dello script di distribuzione del modello Gestione risorse](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Lo script utente, i risultati dell'esecuzione e il file stdout vengono archiviati nelle condivisioni file dell'account di archiviazione. È presente una cartella denominata **azscripts**. Nella cartella sono disponibili altre due cartelle per i file di input e di output: **azscriptinput** e **azscriptoutput**.

La cartella di output contiene un file **ExecutionResult. JSON** e il file di output dello script. È possibile visualizzare il messaggio di errore di esecuzione dello script in **ExecutionResult. JSON**. Il file di output viene creato solo quando lo script viene eseguito correttamente. La cartella di input contiene un file di script di System PowerShell e i file script di distribuzione dell'utente. È possibile sostituire il file script di distribuzione dell'utente con uno modificato e rieseguire lo script di distribuzione dall'istanza di contenitore di Azure.

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

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

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

- Modificare il nome della risorsa deploymentScripts. Ad esempio, usare la funzione di modello [UtcNow](./template-functions-string.md#utcnow) come nome della risorsa o come parte del nome della risorsa. Se si modifica il nome della risorsa, viene creata una nuova risorsa deploymentScripts. È opportuno mantenere una cronologia dell'esecuzione dello script.

    > [!NOTE]
    > La funzione utcNow può essere usata solo nel valore predefinito per un parametro.

- Specificare un valore diverso nella proprietà del modello `forceUpdateTag`.  Ad esempio, usare utcNow come valore.

> [!NOTE]
> Scrivere gli script di distribuzione idempotente. In questo modo si garantisce che, in caso di esecuzione accidentale, non provocherà modifiche al sistema. Ad esempio, se si usa lo script di distribuzione per creare una risorsa di Azure, verificare che la risorsa non esista prima di crearla, in modo che lo script abbia esito positivo o non venga ricreata la risorsa.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

Attualmente, lo script di distribuzione supporta Azure PowerShell versione 2.7.0, 2.8.0 e 3.0.0.  Se si dispone di un computer Windows, è possibile installare una delle versioni di Azure PowerShell supportate e iniziare a sviluppare e testare gli script di distribuzione.  Se non si dispone di un computer Windows o se non si ha una di queste versioni Azure PowerShell installate, è possibile usare un'immagine del contenitore Docker preconfigurata. La procedura seguente illustra come configurare l'immagine Docker in Windows. Per Linux e Mac, è possibile trovare le informazioni su Internet.

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

    Nell'esempio viene utilizzata la versione 2.7.0.

1. Eseguire l'immagine Docker in locale.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Sostituire **&lt;lettera driver host >** e **&lt;nome directory host >** con una cartella esistente nell'unità condivisa.  Esegue il mapping della cartella alla cartella **/Data** nel contenitore. Per esempi, per eseguire il mapping di D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-** significa mantenere attivo l'immagine del contenitore.

1. Selezionare **Condividi** quando si riceve un messaggio di richiesta.
1. Eseguire uno script di PowerShell come illustrato nello screenshot seguente (dato che è presente un file HelloWorld. ps1 nella cartella d:\docker).

    ![Script di distribuzione del modello di Gestione risorse Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Una volta testato correttamente lo script PowerShell, è possibile usarlo come script di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare gli script di distribuzione. Per esaminare un'esercitazione relativa a uno script di distribuzione:

> [!div class="nextstepaction"]
> [Esercitazione: usare gli script di distribuzione nei modelli di Azure Resource Manager](./template-tutorial-deployment-script.md)