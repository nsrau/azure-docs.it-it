---
title: Come creare criteri di configurazione guest per WindowsHow to create Guest Configuration policies for Windows
description: Informazioni su come creare criteri di configurazione guest dei criteri di Azure per Windows.Learn how to create an Azure Policy Guest Configuration policy for Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24069ff6518c4244026378e48216d4568fffeb8a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365473"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Come creare criteri di configurazione guest per WindowsHow to create Guest Configuration policies for Windows

Prima di creare criteri personalizzati, è consigliabile leggere le informazioni di panoramica concettuale nella pagina [Configurazione guest dei criteri di Azure](../concepts/guest-configuration.md).
 
Per informazioni sulla creazione di criteri di configurazione guest per Linux, vedere la pagina Come creare criteri di [configurazione guest per LinuxTo](./guest-configuration-create-linux.md) learn about creating Guest Configuration policies for Linux, see the page How to create Guest Configuration policies for Linux

Durante il controllo di Windows, Configurazione guest utilizza un modulo di risorse DSC [(Desired State Configuration)](/powershell/scripting/dsc/overview/overview) e un file di configurazione. La configurazione DSC definisce la condizione in cui deve trovarsi la macchina.
Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto criterio **auditIfNotExists** e il computer viene considerato **non conforme.**

[La configurazione Guest di Criteri di Azure](../concepts/guest-configuration.md) può essere usata solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di un computer Azure o non Azure.Use the following actions to create your own configuration for validating the state of an Azure or non-Azure machine.

> [!IMPORTANT]
> I criteri personalizzati con Configurazione ospite sono una funzionalità di anteprima.

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

Creating a Guest Configuration artifact, automated testing of the artifact, creating a policy definition, and publishing the policy, is entirely automatable using the Guest Configuration module in PowerShell. Il modulo può essere installato in un computer che esegue Windows, macOS o Linux con PowerShell 6.2 o versione successiva in esecuzione in locale oppure con [Azure Cloud Shell](https://shell.azure.com)oppure con [l'immagine Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core)principale di Azure PowerShell.

> [!NOTE]
> La compilazione delle configurazioni non è ancora supportata su Linux.Compilation of configurations is not yet supported on Linux.

### <a name="base-requirements"></a>Requisiti di base

Sistemi operativi in cui è installato il modulo:

- Linux
- macOS
- WINDOWS

Il modulo delle risorse Configurazione ospite richiede il software seguente:The Guest Configuration resource module requires the following software:

- PowerShell 6.2 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).
  - Sono necessari solo i moduli A 'Az.Accounts' e 'Az.Resources'.

### <a name="install-the-module"></a>Installare il modulo

Per installare il modulo **GuestConfiguration** in PowerShell:

1. Al prompt di PowerShell, eseguire il comando seguente:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Verificare che il modulo sia stato importato:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Guest Configuration artifacts and policy for Windows

Guest Configuration uses PowerShell Desired State Configuration as a language abstraction for writing what to audit in Windows. L'agente carica un'istanza autonoma di PowerShell 6.2, quindi non vi è alcun conflitto con l'utilizzo di PowerShell DSC in Windows PowerShell 5.1 e non è necessario preinstallare PowerShell 6.2 o versione successiva.

Per una panoramica dei concetti e della terminologia di DSC, vedere Panoramica di [PowerShell DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Differenze tra i moduli di configurazione guest e i moduli DSC di Windows PowerShell

Quando Configurazione ospite controlla un computer, viene prima eseguito `Test-TargetResource` per determinare se si trova nello stato corretto. Il valore booleano restituito dalla funzione determina se lo stato di Azure Resource Manager per l'assegnazione Guest deve essere conforme/non conforme. Successivamente, il `Get-TargetResource` provider viene eseguito per restituire lo stato corrente di ogni impostazione in modo che siano disponibili sia i dettagli sul motivo per cui un computer non è conforme, sia per verificare che lo stato corrente sia conforme.

### <a name="get-targetresource-requirements"></a>Requisiti di Get-TargetResource

La `Get-TargetResource` funzione ha requisiti speciali per la configurazione Guest che non sono stati necessari per la configurazione dello stato desiderato di Windows.The function has special requirements for Guest Configuration that haven't been needed for Windows Desired State Configuration.

- La tabella hash restituita deve includere una proprietà denominata **Reasons**.
- Il Reasons proprietà deve essere una matrice.
- Ogni elemento della matrice deve essere una tabella hash con chiavi denominate **Code** e **Phrase**.

La proprietà Reasons viene utilizzata dal servizio per standardizzare il modo in cui le informazioni vengono presentate quando una macchina non è conforme. È possibile considerare ogni elemento in Motivi come un "motivo" che la risorsa non è conforme. La proprietà è una matrice perché una risorsa potrebbe non essere conforme per più di un motivo.

Le proprietà **Code** e **Phrase** sono previste dal servizio. Quando si crea una risorsa personalizzata, impostare il testo (in genere stdout) che si desidera visualizzare come motivo per cui la risorsa non è conforme come valore per **Frase**. **Il codice** ha requisiti di formattazione specifici in modo che i report possano visualizzare chiaramente informazioni sulla risorsa utilizzata per eseguire il controllo. Questa soluzione rende la configurazione Guest estensibile. Qualsiasi comando può essere eseguito finché l'output può essere restituito come valore stringa per la proprietà **Phrase.**

- **Codice** (stringa): il nome della risorsa, ripetuto e quindi un nome breve senza spazi come identificatore del motivo. Questi tre valori devono essere delimitati da due punti senza spazi.
  - Un esempio potrebbe essere`registry:registry:keynotpresent`
- **Frase** (stringa): testo leggibile per spiegare perché l'impostazione non è conforme.
  - Un esempio potrebbe essere`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```
### <a name="configuration-requirements"></a>Requisiti di configurazione

Il nome della configurazione personalizzata deve essere coerente ovunque. Il nome del file .zip per il pacchetto di contenuto, il nome della configurazione nel file MOF e il nome dell'assegnazione guest nel modello di Resource Manager devono essere uguali.

### <a name="scaffolding-a-guest-configuration-project"></a>Scaffolding di un progetto di configurazione guestScaffolding a Guest Configuration project

Gli sviluppatori che desiderano accelerare il processo di introduzione e di lavoro dal codice di esempio possono installare un progetto comunitario denominato **Guest Configuration Project**. Il progetto installa un modello per il modulo [PowerShell Plaster.](https://github.com/powershell/plaster) Questo strumento può essere utilizzato per eseguire lo scaffolding di un progetto, inclusa una configurazione funzionante e una risorsa di esempio e un set di test [Pester](https://github.com/pester/pester) per convalidare il progetto. Il modello include anche i task runner per Visual Studio Code per automatizzare la compilazione e la convalida del pacchetto di configurazione guest. Per ulteriori informazioni, vedere il progetto GitHub [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

Per ulteriori informazioni sull'utilizzo delle configurazioni in generale, vedere [Scrittura, compilazione e applicazione di una configurazione](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Contenuto previsto di un elemento di configurazione guest

Il pacchetto completato viene usato da Configurazione ospite per creare le definizioni dei criteri di Azure.The completed package is used by Guest Configuration to create the Azure Policy definitions. Il pacchetto è costituito da:

- La configurazione DSC compilata come MOF
- Cartella Moduli
  - Modulo GuestConfiguration
  - Modulo DscNativeResources
  - (Windows) Moduli di risorse DSC richiesti dal mOF

I cmdlet di PowerShell facilitano la creazione del pacchetto.
Non è richiesta alcuna cartella di livello principale o di versione.
Il formato del pacchetto deve essere un file .zip.

### <a name="storing-guest-configuration-artifacts"></a>Archiviazione degli elementi di configurazione guest

Il pacchetto .zip deve essere archiviato in un percorso accessibile dalle macchine virtuali gestite.
Gli esempi includono i repository GitHub, un repository di Azure o l'archiviazione di Azure.Examples include GitHub repositories, an Azure Repo, or Azure storage. Se si preferisce non rendere pubblico il pacchetto, è possibile includere un [token di firma di accesso](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) condiviso nell'URL.
È anche possibile implementare [l'endpoint](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) del servizio per i computer in una rete privata, anche se questa configurazione si applica solo all'accesso al pacchetto e non alla comunicazione con il servizio.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Procedura dettagliata, creazione di un criterio di controllo della configurazione Guest personalizzato per Windows

Creare una configurazione DSC. Nell'esempio di script di PowerShell seguente viene creata una configurazione denominata **AuditBitLocker**, viene importato il modulo di risorsa **PsDscResources** e viene utilizzata la `Service` risorsa per il controllo di un servizio in esecuzione. Lo script di configurazione può essere eseguito da un computer Windows o macOS.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker -out ./Config
```

Il `Node AuditBitlocker` comando non è tecnicamente richiesto, `AuditBitlocker.mof` ma produce un `localhost.mof`file denominato anziché il valore predefinito, . Avere il nome del file .mof seguire la configurazione rende facile organizzare molti file quando si opera su larga scala.

Una volta compilato il file MOF, i file di supporto devono essere inclusi nel pacchetto. Il pacchetto completato viene usato da Configurazione ospite per creare le definizioni dei criteri di Azure.The completed package is used by Guest Configuration to create the Azure Policy definitions.

Il `New-GuestConfigurationPackage` cmdlet crea il pacchetto. Parametri del `New-GuestConfigurationPackage` cmdlet durante la creazione di contenuto di Windows:

- **Nome**: Nome pacchetto di configurazione guest.
- **Configurazione**: percorso completo del documento di configurazione DSC compilato.
- **Percorso**: Percorso cartella di output. Questo parametro è facoltativo e, Se non specificato, il pacchetto viene creato nella directory corrente.

Eseguire il comando seguente per creare un pacchetto usando la configurazione specificata nel passaggio precedente:Run the following command to create a package using the configuration given in the previous step:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Dopo aver creato il pacchetto di configurazione ma prima di pubblicarlo in Azure, è possibile testare il pacchetto dalla workstation o dall'ambiente CI/CD. Il cmdlet `Test-GuestConfigurationPackage` GuestConfiguration include nell'ambiente di sviluppo lo stesso agente usato nei computer di Azure.The GuestConfiguration cmdlet includes the same agent in your development environment as is used inside Azure machines. Utilizzando questa soluzione, è possibile eseguire il test di integrazione in locale prima del rilascio in ambienti cloud fatturati.

Poiché l'agente sta effettivamente valutando l'ambiente locale, nella maggior parte dei casi è necessario eseguire il cmdlet Test- sulla stessa piattaforma del sistema operativo che si prevede di controllare.

Parametri del `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome del criterio di configurazione guest.
- **Parametro**: Parametri dei criteri forniti in formato hashtable.
- Path ( **Percorso):** percorso completo del pacchetto di configurazione guest.

Eseguire il comando seguente per testare il pacchetto creato nel passaggio precedente:Run the following command to test the package created by the previous step:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Il cmdlet supporta anche l'input dalla pipeline di PowerShell.The cmdlet also supports input from the PowerShell pipeline. Eseguire il `New-GuestConfigurationPackage` pipamento `Test-GuestConfigurationPackage` dell'output del cmdlet al cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Il passaggio successivo consiste nel pubblicare il file nell'archiviazione BLOB. Lo script seguente contiene una funzione che è possibile utilizzare per automatizzare questa attività. I comandi utilizzati `publish` nella `Az.Storage` funzione richiedono il modulo.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Dopo aver creato e caricato un pacchetto di criteri personalizzati di configurazione guest, creare la definizione dei criteri di configurazione guest. Il `New-GuestConfigurationPolicy` cmdlet accetta un pacchetto di criteri personalizzato e crea una definizione dei criteri.

Parametri del `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri:** URI pubblico del pacchetto di contenuto Configurazione ospite.
- **DisplayName**: Nome visualizzato criterio.
- **Descrizione**: Descrizione del criterio.
- **Parametro**: Parametri dei criteri forniti in formato hashtable.
- **Versione**: Versione politica.
- **Percorso:** percorso di destinazione in cui vengono create le definizioni dei criteri.
- **Piattaforma**: Piattaforma di destinazione (Windows/Linux) per i criteri di configurazione guest e il pacchetto di contenuto.

Nell'esempio seguente vengono create le definizioni dei criteri in un percorso specificato da un pacchetto di criteri personalizzato:The following example creates the policy definitions in a specified path from a custom policy package:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

I seguenti file `New-GuestConfigurationPolicy`sono creati da:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato dell'iniziativa e il percorso dei file di criteri.

Infine, pubblicare le `Publish-GuestConfigurationPolicy` definizioni dei criteri utilizzando il cmdlet. Il cmdlet dispone solo del parametro **Path** che punta `New-GuestConfigurationPolicy`al percorso dei file JSON creati da .

To run the Publish command, you need access to create policies in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica dei criteri di Azure.The](../overview.md) specific authorization requirements are documented in the Azure Policy Overview page. Il ruolo predefinito migliore è **Contributore criteri risorse**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Il `Publish-GuestConfigurationPolicy` cmdlet accetta il percorso dalla pipeline di PowerShell. Questa funzionalità consente di creare i file di criteri e pubblicarli in un unico set di comandi reindirizzati.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Con i criteri creati in Azure, l'ultimo passaggio consiste nell'assegnare l'iniziativa. Informazioni su come assegnare l'iniziativa con [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> I criteri di configurazione guest devono **essere sempre** assegnati utilizzando l'iniziativa che combina i criteri _AuditIfNotExists_ e _DeployIfNotExists._ Se viene assegnato solo il criterio _AuditIfNotExists,_ i prerequisiti non vengono distribuiti e il criterio mostra sempre che i server '0' sono conformi.

L'assegnazione di una definizione di criteri con l'effetto _DeployIfNotExists_ richiede un ulteriore livello di accesso. Per concedere il privilegio minimo, è possibile creare una definizione di ruolo personalizzata che estenda **Contributori criteri risorse**. Nell'esempio seguente viene creato un ruolo denominato **Collaboratore criteri risorse DINE** con l'autorizzazione aggiuntiva _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilizzo di parametri nei criteri di configurazione guest personalizzatiUsing parameters in custom Guest Configuration policies

Configurazione guest supporta l'override delle proprietà di una configurazione in fase di esecuzione. Questa funzionalità significa che i valori nel file MOF nel pacchetto non devono essere considerati statici. I valori di override vengono forniti tramite Criteri di Azure e non influiscono sul modo in cui le configurazioni vengono create o compilate.

I cmdlet `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` e includono un parametro denominato **Parameters**. Questo parametro accetta una definizione di tabella hash che include tutti i dettagli su ogni parametro e crea le sezioni obbligatorie di ogni file usato per la definizione di Criteri di Azure.This parameter takes a hashtable definition including all details about each parameter and creates the required sections of each file used for the Azure Policy definition.

Nell'esempio seguente viene creata una definizione di criteri per controllare un servizio, in cui l'utente seleziona da un elenco al momento dell'assegnazione dei criteri.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Ciclo di vita dei criteri

Se si desidera rilasciare un aggiornamento del criterio, sono disponibili due campi che richiedono attenzione.

- **Versione**: quando `New-GuestConfigurationPolicy` si esegue il cmdlet, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato. La proprietà aggiorna la versione dell'assegnazione Configurazione ospite in modo che l'agente riconosca il pacchetto aggiornato.
- **contentHash**: questa proprietà viene `New-GuestConfigurationPolicy` aggiornata automaticamente dal cmdlet. Si tratta di un valore hash `New-GuestConfigurationPackage`del pacchetto creato da . La proprietà deve essere `.zip` corretta per il file pubblicato. Se viene aggiornata solo la proprietà **contentUri,** l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato. Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Conversione del contenuto di Criteri di gruppo di Windows nella configurazione guest di Criteri di AzureConverting Windows Group Policy content to Azure Policy Guest Configuration

Configurazione guest, quando si esegue il controllo di computer Windows, è un'implementazione della sintassi di configurazione dello stato desiderato di PowerShell.Guest Configuration, when auditing Windows machines, is an implementation of the PowerShell Desired State Configuration syntax. La comunità DSC ha pubblicato strumenti per convertire i modelli di criteri di gruppo esportati in formato DSC. Utilizzando questo strumento con i cmdlet di configurazione ospite descritti in precedenza, è possibile convertire il contenuto di Criteri di gruppo di Windows e crearne/pubblicarlo per i criteri di Azure da controllare. Per informazioni dettagliate sull'utilizzo dello strumento, vedere l'articolo [Guida introduttiva: Convertire Criteri di gruppo in DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Dopo la conversione del contenuto, i passaggi precedenti per creare un pacchetto e pubblicarlo come criteri di Azure sono gli stessi di qualsiasi contenuto DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Facoltativo: Firma dei pacchetti di configurazione guestOptional: Signing Guest Configuration packages

I criteri personalizzati di configurazione guest usano l'hash SHA256 per convalidare che il pacchetto di criteri non sia stato modificato.
Facoltativamente, i clienti possono anche utilizzare un certificato per firmare i pacchetti e forzare l'estensione Configurazione guest per consentire solo il contenuto firmato.

Per abilitare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e aggiungere un tag ai computer che devono richiedere la firma del codice.

Per utilizzare la funzionalità di `Protect-GuestConfigurationPackage` convalida della firma, eseguire il cmdlet per firmare il pacchetto prima della pubblicazione. Questo cmdlet richiede un certificato 'Firma codice'.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametri del `Protect-GuestConfigurationPackage` cmdlet:

- Path ( **Percorso):** percorso completo del pacchetto di configurazione guest.
- **Certificato**: Certificato di firma del codice per firmare il pacchetto. Questo parametro è supportato solo quando si firma contenuto per Windows.

Agente GuestConfiguration prevede che la chiave pubblica del certificato sia presente in "Autorità di certificazione radice attendibili" nei computer Windows e nel percorso nei computer Linux.GuestConfiguration agent expects the certificate public key to be present in "Trusted Root Certificate Authorities" on Windows machines and in the path `/usr/local/share/ca-certificates/extra` on Linux machines. Affinché il nodo verifichi il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare il criterio personalizzato. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando Criteri di Azure.This process can be done using any technique inside the VM or by using Azure Policy. Un modello di esempio viene [fornito qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
I criteri di accesso dell'insieme di credenziali delle chiavi devono consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per la procedura dettagliata, vedere [Configurare l'insieme](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager.

Di seguito è riportato un esempio per esportare la chiave pubblica da un certificato di firma, da importare nel computer.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Dopo la pubblicazione del contenuto, `GuestConfigPolicyCertificateValidation` aggiungere `enabled` un tag con nome e valore a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Vedere gli esempi di tag per informazioni su come i tag possono essere recapitati su larga scala usando Criteri di Azure.See the [Tag samples](../samples/built-in-policies.md#tags) for how tags can be delivered at scale using Azure Policy. Una volta che questo tag è sul `New-GuestConfigurationPolicy` posto, la definizione dei criteri generata utilizzando il cmdlet abilita il requisito tramite l'estensione Configurazione ospite.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Risoluzione dei problemi relativi alle assegnazioni dei criteri di configurazione guest (anteprima)Troubleshooting Guest Configuration policy assignments (Preview)

Uno strumento è disponibile in anteprima per facilitare la risoluzione dei problemi delle assegnazioni di configurazione guest di Criteri di Azure.A tool is available in preview to assist in troubleshooting Azure Policy Guest Configuration assignments. Lo strumento è in anteprima ed è stato pubblicato in PowerShell Gallery come nome del modulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Per ulteriori informazioni sui cmdlet in questo strumento, usare il comando Get-Help in PowerShell per visualizzare le indicazioni predefinite. Come lo strumento sta ottenendo aggiornamenti frequenti, che è il modo migliore per ottenere le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul controllo delle macchine virtuali con [Configurazione guest](../concepts/guest-configuration.md).
- Comprendere come creare criteri a livello di [codice.](programmatically-create.md)
- Scopri come ottenere i dati di [conformità](get-compliance-data.md).
