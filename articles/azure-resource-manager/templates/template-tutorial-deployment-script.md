---
title: Usare gli script di distribuzione del modello | Microsoft Docs
description: Informazioni su come usare gli script di distribuzione nei modelli di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/09/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e52a859c86ff451293ac6ff795c7fe427a383b9d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843508"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Esercitazione: Usare gli script di distribuzione per creare un certificato autofirmato (anteprima)

Informazioni su come usare gli script di distribuzione nei modelli di Azure Resource Manager. Gli script di distribuzione possono essere usati per eseguire passaggi personalizzati che non possono essere eseguiti dai modelli di Resource Manager. Ad esempio, la creazione di un certificato autofirmato.  In questa esercitazione viene creato un modello per distribuire un insieme di credenziali delle chiavi di Azure e quindi viene usata una risorsa `Microsoft.Resources/deploymentScripts` nello stesso modello per creare un certificato e infine viene aggiunto il certificato all'insieme di credenziali delle chiavi. Per altre informazioni sullo script di distribuzione, vedere [Usare gli script di distribuzione nei modelli di Azure Resource Manager](./deployment-script-template.md).

> [!NOTE]
> Lo script di distribuzione è attualmente in anteprima. Per usarlo, è necessario [iscriversi per l'anteprima](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Due risorse dello script di distribuzione, un account di archiviazione e un'istanza del contenitore, vengono create nello stesso gruppo di risorse per l'esecuzione dello script e la risoluzione dei problemi. Queste risorse vengono in genere eliminate dal servizio di script quando l'esecuzione dello script raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](./deployment-script-template.md#clean-up-deployment-script-resources).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Aprire un modello di avvio rapido
> * Modificare il modello
> * Distribuire il modello
> * Eseguire il debug dello script non riuscito
> * Pulire le risorse

## <a name="prerequisites"></a>Prerequisites

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* **[Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Resource Manager**. Vedere [Usare Visual Studio Code per creare modelli di Azure Resource Manager](./use-vs-code-to-create-template.md).

* **Identità gestita assegnata dall'utente con il ruolo di collaboratore a livello di sottoscrizione**. Questa identità viene usata per eseguire gli script di distribuzione. Per crearne una, vedere [Identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). È necessario l'ID identità per distribuire il modello. Il formato dell'identità è:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Usare lo script di PowerShell seguente per ottenere l'ID fornendo il nome del gruppo di risorse e il nome dell'identità.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Invece di creare un modello da zero, aprire un modello da [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/). I modelli di avvio rapido di Azure costituiscono un repository di modelli di Resource Manager.

Il modello usato in questo avvio rapido è denominato [Creare un insieme di credenziali delle chiavi e un segreto di Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Il modello crea un'insieme di credenziali delle chiavi e quindi aggiunge un segreto all'insieme di credenziali delle chiavi.

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selezionare **Apri** per aprire il file.
4. Selezionare **File**>**Salva con nome** per salvare il file con il nome **azuredeploy.json** nel computer locale.

## <a name="edit-the-template"></a>Modificare il modello

Apportare le modifiche seguenti al modello:

### <a name="clean-up-the-template-optional"></a>Pulire il modello (facoltativo)

Il modello originale aggiunge un segreto all'insieme di credenziali delle chiavi.  Per semplificare l'esercitazione, rimuovere la risorsa seguente:

* **Microsoft.KeyVault/vaults/secrets**

Rimuovere le due definizioni di parametro seguenti:

* **secretName**
* **secretValue**

Se si sceglie di non rimuovere queste definizioni, è necessario specificare i valori dei parametri durante la distribuzione.

### <a name="configure-the-key-vault-access-policies"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Lo script di distribuzione aggiunge un certificato all'insieme di credenziali delle chiavi. Configurare i criteri di accesso dell'insieme di credenziali delle chiavi per concedere l'autorizzazione all'identità gestita:

1. Aggiungere un parametro per ottenere l'ID identità gestita:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > L'estensione del modello di Resource Manager di Visual Studio Code non è ancora in grado di formattare gli script di distribuzione. Non usare [MAIUSC] + [ALT] + F per formattare le risorse deploymentScripts, come quella riportata di seguito.

1. Aggiungere un parametro per la configurazione dei criteri di accesso dell'insieme di credenziali delle chiavi in modo che l'identità gestita possa aggiungere certificati all'insieme di credenziali delle chiavi.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Aggiornare i criteri di accesso dell'insieme di credenziali delle chiavi esistenti come segue:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Sono definiti due criteri, uno per l'utente connesso e l'altro per l'identità gestita.  Per verificare la distribuzione, l'utente connesso necessita solo dell'autorizzazione *list*.  Per semplificare l'esercitazione, lo stesso certificato viene assegnato sia all'identità gestita che agli utenti connessi.

### <a name="add-the-deployment-script"></a>Aggiungere lo script di distribuzione

1. Aggiungere tre parametri usati dallo script di distribuzione.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }

1. Add a deploymentScripts resource:

    > [!NOTE]
    > Because the inline deployment scripts are enclosed in double quotes, the strings inside the deployment scripts need to be enclosed in single quotes instead. The escape character for PowerShell is **&#92;**.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "2.8",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an arguement string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    La risorsa `deploymentScripts` dipende dalla risorsa dell'insieme di credenziali delle chiavi e dalla risorsa di assegnazione di ruolo.  Ha queste proprietà:

    * **identity**: lo script di distribuzione usa un'identità gestita assegnata dall'utente per eseguire gli script.
    * **kind**: specificare il tipo di script. Attualmente, solo lo script di PowerShell è supportato.
    * **forceUpdateTag**: determinare se lo script di distribuzione deve essere eseguito anche se l'origine dello script non è stata modificata. Può essere il timestamp corrente o un GUID. Per altre informazioni, vedere [Eseguire lo script più di una volta](./deployment-script-template.md#run-script-more-than-once).
    * **azPowerShellVersion**: specifica la versione del modulo di Azure PowerShell da usare. Attualmente, lo script di distribuzione supporta la versione 2.7.0, 2.8.0 e 3.0.0.
    * **timeout**: specificare il tempo di esecuzione dello script massimo consentito nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
    * **arguments**: Specificare i valori del parametro. I valori sono separati da uno spazio.
    * **scriptContent**: specificare il contenuto dello script. Per eseguire uno script esterno, usare **primaryScriptURI**. Per altre informazioni, vedere [Usare uno script esterno](./deployment-script-template.md#use-external-scripts).
        La dichiarazione di **$DeploymentScriptOutputs** è necessaria solo quando si esegue il test dello script in un computer locale. La dichiarazione della variabile consente di eseguire lo script in un computer locale e in una risorsa deploymentScript senza dover apportare modifiche. Il valore assegnato a $DeploymentScriptOutputs è disponibile come output nelle distribuzioni. Per altre informazioni, vedere [Usare gli output degli script di distribuzione](./deployment-script-template.md#work-with-outputs-from-deployment-scripts).
    * **cleanupPreference**: specificare la preferenza che determina quando eliminare le risorse dello script di distribuzione.  Il valore predefinito è **Always**, che significa che le risorse dello script di distribuzione vengono eliminate nonostante lo stato finale (Succeeded, Failed, Canceled). In questa esercitazione viene usato **OnSuccess** in modo da potere visualizzare i risultati dell'esecuzione dello script.
    * **retentionInterval**: specificare l'intervallo per cui il servizio mantiene le risorse dello script dopo il raggiungimento di uno stato finale. Le risorse verranno eliminate alla scadenza di tale durata. La durata è basata sul modello ISO 8601. Questa esercitazione usa P1D, che significa un giorno.  Questa proprietà viene usata quando **retentionInterval** è impostato su **OnExpiration**. Tale proprietà non è attualmente abilitata.

    Lo script di distribuzione accetta tre parametri: il nome dell'insieme di credenziali delle chiavi, il nome del certificato e il nome del soggetto.  Crea un certificato e quindi aggiunge il certificato all'insieme di credenziali delle chiavi.

    **$DeploymentScriptOutputs** viene usato per archiviare il valore di output.  Per altre informazioni, vedere [Usare gli output degli script di distribuzione](./deployment-script-template.md#work-with-outputs-from-deployment-scripts).

    Il modello completato è disponibile [qui](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Per visualizzare il processo di debug, inserire un errore nel codice aggiungendo la riga seguente allo script di distribuzione:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Il comando corretto è **Write-Output** invece di **Write-Output1**.

1. Selezionare **File**>**Salva** per salvare il file.

## <a name="deploy-the-template"></a>Distribuire il modello

Vedere la sezione [Distribuire il modello](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) nell'argomento di avvio rapido di Visual Studio Code per aprire Cloud Shell e caricare il file di modello nella shell. Eseguire quindi lo script di PowerShell seguente:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

Il servizio dello script di distribuzione deve creare altre risorse dello script di distribuzione per l'esecuzione dello script. Il completamento della preparazione e del processo di pulizia può richiedere fino a un minuto oltre al tempo di esecuzione effettivo dello script.

La distribuzione non è riuscita perché nello script è stato usato il comando **Write-Output1** non valido. Si riceverà un errore simile al seguente:

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

Il risultato dell'esecuzione dello script di distribuzione viene archiviato nelle risorse dello script di distribuzione per la risoluzione dei problemi.

## <a name="debug-the-failed-script"></a>Eseguire il debug dello script non riuscito

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire il gruppo di risorse. È il nome del progetto seguito da **rg**. Nel gruppo di risorse verranno visualizzate due risorse aggiuntive. Queste risorse vengono definite *risorse dello script di distribuzione*.

    ![Risorse dello script di distribuzione del modello di Resource Manager](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Entrambi i file hanno il suffisso **azscripts**. Uno è un account di archiviazione e l'altro è un'istanza di contenitore.

    Selezionare **Mostra tipi nascosti** per elencare la risorsa deploymentScripts.

1. Selezionare l'account di archiviazione con il suffisso **azscripts**.
1. Selezionare il riquadro **Condivisioni file**. Verrà visualizzata una cartella **azscripts**.  La cartella contiene i file di esecuzione dello script di distribuzione.
1. Selezionare **azscripts**. Verranno visualizzate due cartelle **azscriptinput** e **azscriptoutput**.  La cartella di input contiene un file di script di sistema di PowerShell e i file di script di distribuzione dell'utente. La cartella di output contiene un file **executionresult.json** e il file di output dello script. È possibile visualizzare il messaggio di errore in **executionresult.json**. Il file di output non è presente perché l'esecuzione non è riuscita.

Rimuovere la riga **Write-Output1** e ridistribuire il modello.

Dopo l'esecuzione corretta della seconda distribuzione, le risorse dello script di distribuzione verranno rimosse dal servizio di script, perché la proprietà **cleanupPreference** è impostata su **OnSuccess**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come usare lo script di distribuzione nei modelli di Azure Resource Manager. Per informazioni su come distribuire risorse di Azure in base a condizioni, vedere:

> [!div class="nextstepaction"]
> [Usare le condizioni](./template-tutorial-use-conditions.md)