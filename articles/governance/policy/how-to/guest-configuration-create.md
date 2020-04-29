---
title: Come creare criteri di configurazione Guest per Windows
description: Informazioni su come creare criteri di configurazione Guest di criteri di Azure per Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f09bb543f73e37bd211a55e2238808f57585bb18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024898"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Come creare criteri di configurazione Guest per Windows

Prima di creare criteri personalizzati, è consigliabile leggere le informazioni generali concettuali nella pagina [configurazione Guest di criteri di Azure](../concepts/guest-configuration.md).
 
Per informazioni sulla creazione di criteri di configurazione Guest per Linux, vedere la pagina [come creare criteri di configurazione Guest per Linux](./guest-configuration-create-linux.md)

Quando si controlla Windows, la configurazione Guest usa un modulo di risorse DSC ( [desired state Configuration](/powershell/scripting/dsc/overview/overview) ) per e il file di configurazione. La configurazione DSC definisce la condizione in cui deve trovarsi il computer.
Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto del criterio **auditIfNotExists** e il computer viene considerato **non conforme**.

La [configurazione Guest di criteri di Azure](../concepts/guest-configuration.md) può essere usata solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di un computer Azure o non Azure.

> [!IMPORTANT]
> I criteri personalizzati con la configurazione Guest sono una funzionalità in anteprima.
>
> L'estensione di configurazione Guest è necessaria per eseguire controlli in macchine virtuali di Azure.
> Per distribuire l'estensione su larga scala in tutti i computer Windows, assegnare le definizioni dei criteri seguenti:
>   - [Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

La creazione di un elemento di configurazione Guest, il test automatizzato dell'artefatto, la creazione di una definizione dei criteri e la pubblicazione del criterio sono completamente automatizzabili usando il modulo di configurazione Guest in PowerShell. Il modulo può essere installato in un computer che esegue Windows, macOS o Linux con PowerShell 6,2 o versioni successive in esecuzione in locale o con [Azure cloud Shell](https://shell.azure.com)o con l' [immagine Docker Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> La compilazione di configurazioni non è ancora supportata in Linux.

### <a name="base-requirements"></a>Requisiti di base

Sistemi operativi in cui è possibile installare il modulo:

- Linux
- macOS
- Windows

Il modulo della risorsa di configurazione Guest richiede il seguente software:

- PowerShell 6,2 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).
  - Sono necessari solo AZ modules ' AZ. Accounts ' è AZ. resources '.

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

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Criteri e artefatti di configurazione Guest per Windows

La configurazione Guest usa PowerShell DSC (Desired state Configuration) come un'astrazione del linguaggio per la scrittura di elementi da controllare in Windows. L'agente carica un'istanza autonoma di PowerShell 6,2, pertanto non si verifica alcun conflitto con l'utilizzo di PowerShell DSC in Windows PowerShell 5,1 e non è necessario preinstallare PowerShell 6,2 o versione successiva.

Per una panoramica dei concetti e della terminologia DSC, vedere [Panoramica di PowerShell DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Differenze tra i moduli di configurazione Guest e i moduli DSC di Windows PowerShell

Quando la configurazione Guest controlla un computer:

1. L'agente viene eseguito `Test-TargetResource` per la prima volta per determinare se la configurazione è nello stato corretto.
1. Il valore booleano restituito dalla funzione determina se lo stato del Azure Resource Manager per l'assegnazione Guest deve essere conforme/non conforme.
1. Il provider viene `Get-TargetResource` eseguito per restituire lo stato corrente di ogni impostazione, in modo che i dettagli siano disponibili per il motivo per cui un computer non è conforme e per confermare che lo stato corrente è conforme.

### <a name="get-targetresource-requirements"></a>Requisiti di Get-TargetResource

La funzione `Get-TargetResource` presenta requisiti speciali per la configurazione Guest che non sono stati necessari per la configurazione dello stato desiderato di Windows.

- La tabella hash restituita deve includere una proprietà denominata **reasons**.
- La proprietà reasons deve essere una matrice.
- Ogni elemento nella matrice deve essere una tabella hash con chiavi denominate **Code** e **Phrase**.

La proprietà reasons viene usata dal servizio per standardizzare il modo in cui le informazioni vengono presentate quando un computer non è conforme. È possibile considerare ogni elemento come un "motivo" che la risorsa non è conforme. La proprietà è una matrice perché una risorsa potrebbe non essere conforme per più di un motivo.

Il **codice** e la **frase** delle proprietà sono previsti dal servizio. Quando si crea una risorsa personalizzata, impostare il testo (in genere stdout) da mostrare come motivo per cui la risorsa non è conforme come valore per la **frase**. Il **codice** presenta requisiti di formattazione specifici, quindi la creazione di report consente di visualizzare chiaramente le informazioni sulla risorsa utilizzata per eseguire il controllo. Questa soluzione rende estendibile la configurazione Guest. Qualsiasi comando può essere eseguito finché l'output può essere restituito come valore stringa per la proprietà **Phrase** .

- **Code** (String): nome della risorsa, ripetuto, quindi nome breve senza spazi come identificatore per il motivo. Questi tre valori devono essere delimitati da due punti senza spazi.
  - Un esempio è`registry:registry:keynotpresent`
- **Phrase** (String): testo leggibile per spiegare il motivo per cui l'impostazione non è conforme.
  - Un esempio è`The registry key $key is not present on the machine.`

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

È necessario aggiungere la proprietà reasons anche al file MOF dello schema per la risorsa come classe incorporata.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Requisiti di configurazione

Il nome della configurazione personalizzata deve essere coerente ovunque. Il nome del file con estensione zip per il pacchetto di contenuto, il nome della configurazione nel file MOF e il nome dell'assegnazione Guest nel modello di Gestione risorse devono essere uguali.

### <a name="scaffolding-a-guest-configuration-project"></a>Impalcatura di un progetto di configurazione Guest

Gli sviluppatori che desiderano accelerare il processo di introduzione e utilizzo del codice di esempio possono installare un progetto community denominato **progetto di configurazione Guest**. Il progetto installa un modello per il modulo di PowerShell di [gesso](https://github.com/powershell/plaster) . Questo strumento può essere usato per eseguire il patibolo di un progetto, tra cui una configurazione funzionante e una risorsa di esempio, e un set di test [Pester](https://github.com/pester/pester) per convalidare il progetto. Il modello include anche gli esecutori di attività per Visual Studio Code per automatizzare la compilazione e la convalida del pacchetto di configurazione Guest. Per altre informazioni, vedere il progetto di [configurazione Guest](https://github.com/microsoft/guestconfigurationproject)di GitHub Project.

Per ulteriori informazioni sull'utilizzo delle configurazioni in generale, vedere [scrivere, compilare e applicare una configurazione](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Contenuto previsto di un elemento di configurazione Guest

Il pacchetto completato viene usato dalla configurazione Guest per creare le definizioni di criteri di Azure. Il pacchetto è costituito da:

- Configurazione DSC compilata come MOF
- Cartella moduli
  - Modulo GuestConfiguration
  - Modulo DscNativeResources
  - Windows Moduli di risorse DSC richiesti dal file MOF

I cmdlet di PowerShell facilitano la creazione del pacchetto.
Non è necessaria alcuna cartella di livello radice o cartella della versione.
Il formato del pacchetto deve essere un file con estensione zip.

### <a name="storing-guest-configuration-artifacts"></a>Archiviazione degli elementi di configurazione Guest

Il pacchetto zip deve essere archiviato in un percorso accessibile dalle macchine virtuali gestite.
Gli esempi includono repository GitHub, un repository di Azure o archiviazione di Azure. Se si preferisce non rendere pubblico il pacchetto, è possibile includere un [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) nell'URL.
È anche possibile implementare l' [endpoint di servizio](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) per i computer in una rete privata, anche se questa configurazione si applica solo all'accesso al pacchetto e non alla comunicazione con il servizio.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Procedura dettagliata per la creazione di criteri di controllo della configurazione Guest personalizzati per Windows

Creare una configurazione DSC per controllare le impostazioni. Nell'esempio di script di PowerShell seguente viene creata una configurazione denominata **AuditBitLocker**, viene importato il modulo `Service` della risorsa **PsDscResources** e viene utilizzata la risorsa da controllare per un servizio in esecuzione. Lo script di configurazione può essere eseguito da un computer Windows o macOS.

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
AuditBitLocker ./Config
```

Salvare il file con il `config.ps1` nome nella cartella del progetto. Eseguirlo in PowerShell eseguendo `./config.ps1` nel terminale. Verrà creato un nuovo file MOF.

Il `Node AuditBitlocker` comando non è tecnicamente necessario, ma produce un file `AuditBitlocker.mof` denominato anziché quello predefinito, `localhost.mof`. Il nome del file con estensione MOF che segue la configurazione consente di organizzare in modo semplice molti file quando si opera su larga scala.

Una volta compilato il file MOF, i file di supporto devono essere inclusi nel pacchetto. Il pacchetto completato viene usato dalla configurazione Guest per creare le definizioni di criteri di Azure.

Tramite `New-GuestConfigurationPackage` il cmdlet viene creato il pacchetto. I moduli necessari per la configurazione devono essere disponibili in `$Env:PSModulePath`. Parametri del cmdlet `New-GuestConfigurationPackage` durante la creazione del contenuto di Windows:

- **Nome**: nome del pacchetto di configurazione Guest.
- **Configurazione**: percorso completo del documento di configurazione DSC compilato.
- **Path**: percorso della cartella di output. Questo parametro è facoltativo e, Se non è specificato, il pacchetto viene creato nella directory corrente.

Eseguire il comando seguente per creare un pacchetto usando la configurazione fornita nel passaggio precedente:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Dopo aver creato il pacchetto di configurazione, ma prima di pubblicarlo in Azure, è possibile testare il pacchetto dalla workstation o dall'ambiente CI/CD. Il cmdlet `Test-GuestConfigurationPackage` GuestConfiguration include lo stesso agente nell'ambiente di sviluppo usato in macchine virtuali di Azure. Con questa soluzione è possibile eseguire test di integrazione in locale prima di rilasciare gli ambienti cloud fatturati.

Poiché l'agente sta effettivamente valutando l'ambiente locale, nella maggior parte dei casi è necessario eseguire il cmdlet di test sulla stessa piattaforma del sistema operativo che si prevede di controllare. Il test userà solo i moduli inclusi nel pacchetto di contenuto.

Parametri del `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: nome dei criteri di configurazione Guest.
- **Parametro**: parametri dei criteri forniti nel formato Hashtable.
- **Percorso**: percorso completo del pacchetto di configurazione Guest.

Eseguire il comando seguente per testare il pacchetto creato dal passaggio precedente:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Il cmdlet supporta anche l'input dalla pipeline di PowerShell. Inviare tramite pipe l' `New-GuestConfigurationPackage` output del cmdlet `Test-GuestConfigurationPackage` al cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Il passaggio successivo consiste nel pubblicare il file nell'archivio BLOB. Lo script seguente contiene una funzione che è possibile usare per automatizzare questa attività. I comandi usati nella `publish` funzione richiedono il `Az.Storage` modulo.

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

Una volta creato e caricato un pacchetto di criteri personalizzato per la configurazione Guest, creare la definizione dei criteri di configurazione Guest. Il `New-GuestConfigurationPolicy` cmdlet accetta un pacchetto di criteri personalizzato e crea una definizione di criteri.

Parametri del `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: URI http (s) pubblico del pacchetto del contenuto di configurazione Guest.
- **DisplayName**: nome visualizzato del criterio.
- **Descrizione**: Descrizione del criterio.
- **Parametro**: parametri dei criteri forniti nel formato Hashtable.
- **Versione**: versione dei criteri.
- **Path**: percorso di destinazione in cui vengono create le definizioni dei criteri.
- **Piattaforma**: piattaforma di destinazione (Windows/Linux) per i criteri di configurazione Guest e il pacchetto di contenuto.

Nell'esempio seguente vengono create le definizioni dei criteri in un percorso specificato da un pacchetto di criteri personalizzato:

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

I file seguenti vengono creati da `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato e il percorso dei file di criteri di Initiative.

Infine, pubblicare le definizioni dei criteri usando `Publish-GuestConfigurationPolicy` il cmdlet. Il cmdlet ha solo il parametro **path** che punta al percorso dei file JSON creati da `New-GuestConfigurationPolicy`.

Per eseguire il comando Publish, è necessario l'accesso per creare criteri in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica di criteri di Azure](../overview.md) . Il ruolo predefinito migliore è **collaboratore criteri risorse**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Il `Publish-GuestConfigurationPolicy` cmdlet accetta il percorso dalla pipeline di PowerShell. Questa funzionalità significa che è possibile creare i file di criteri e pubblicarli in un unico set di comandi inviati tramite pipe.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Con i criteri creati in Azure, l'ultimo passaggio consiste nell'assegnazione dell'iniziativa. Vedere come assegnare l'iniziativa con il [portale](../assign-policy-portal.md), l'interfaccia della riga di comando di [Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> I criteri di configurazione Guest devono **sempre** essere assegnati usando l'iniziativa che combina i criteri _AuditIfNotExists_ e _DeployIfNotExists_ . Se viene assegnato solo il criterio _AuditIfNotExists_ , i prerequisiti non vengono distribuiti e il criterio indica sempre che i server ' 0' sono conformi.

Per assegnare una definizione di criteri con effetto _DeployIfNotExists_ è necessario un livello di accesso aggiuntivo. Per concedere il privilegio minimo, è possibile creare una definizione di ruolo personalizzata che estende il **collaboratore ai criteri delle risorse**. Nell'esempio seguente viene creato un ruolo denominato **collaboratore criteri risorse cena** con l'autorizzazione aggiuntiva _Microsoft. Authorization/roleAssignments/Write_.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilizzo di parametri nei criteri di configurazione Guest personalizzati

La configurazione Guest supporta l'override delle proprietà di una configurazione in fase di esecuzione. Questa funzionalità significa che i valori nel file MOF nel pacchetto non devono essere considerati statici. I valori di sostituzione vengono forniti tramite criteri di Azure e non influiscano sul modo in cui le configurazioni vengono create o compilate.

I cmdlet `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` includono un parametro denominato **Parameters**. Questo parametro accetta una definizione Hashtable che include tutti i dettagli su ogni parametro e crea le sezioni obbligatorie di ogni file usato per la definizione di criteri di Azure.

Nell'esempio seguente viene creata una definizione di criteri per controllare un servizio, dove l'utente seleziona da un elenco al momento dell'assegnazione dei criteri.

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

Per rilasciare un aggiornamento dei criteri, è necessario prestare attenzione a due campi.

- **Versione**: quando si esegue il `New-GuestConfigurationPolicy` cmdlet, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato. La proprietà aggiorna la versione dell'assegnazione di configurazione Guest in modo che l'agente riconosca il pacchetto aggiornato.
- **contentHash**: questa proprietà viene aggiornata automaticamente dal `New-GuestConfigurationPolicy` cmdlet. Si tratta di un valore hash del pacchetto creato da `New-GuestConfigurationPackage`. La proprietà deve essere corretta per il `.zip` file pubblicato. Se viene aggiornata solo la proprietà **contentUri** , l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato. Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Conversione del contenuto di Windows Criteri di gruppo nella configurazione Guest di criteri di Azure

La configurazione Guest, quando si controllano i computer Windows, è un'implementazione della sintassi di PowerShell DSC (Desired state Configuration). La community DSC ha pubblicato strumenti per convertire i modelli esportati Criteri di gruppo in formato DSC. Usando questo strumento con i cmdlet di configurazione Guest descritti in precedenza, è possibile convertire il contenuto di Windows Criteri di gruppo e il pacchetto/pubblicarlo per il controllo di criteri di Azure. Per informazioni dettagliate sull'uso dello strumento, vedere l'articolo [Guida introduttiva: convertire criteri di gruppo in DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Una volta che il contenuto è stato convertito, i passaggi precedenti per creare un pacchetto e pubblicarlo come criteri di Azure sono gli stessi di tutti i contenuti DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Facoltativo: firma dei pacchetti di configurazione Guest

I criteri personalizzati di configurazione Guest usano l'hash SHA256 per convalidare il pacchetto dei criteri non è stato modificato.
Facoltativamente, i clienti possono anche usare un certificato per firmare i pacchetti e forzare l'estensione di configurazione Guest in modo che consenta solo il contenuto firmato.

Per abilitare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e aggiungere un tag ai computer che devono richiedere la firma del codice.

Per utilizzare la funzionalità di convalida della firma, `Protect-GuestConfigurationPackage` eseguire il cmdlet per firmare il pacchetto prima di pubblicarlo. Questo cmdlet richiede un certificato di firma codice.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametri del `Protect-GuestConfigurationPackage` cmdlet:

- **Percorso**: percorso completo del pacchetto di configurazione Guest.
- **Certificato**: certificato di firma del codice per la firma del pacchetto. Questo parametro è supportato solo per la firma di contenuto per Windows.

GuestConfiguration Agent prevede che la chiave pubblica del certificato sia presente in "autorità di certificazione radice attendibili" nei computer Windows e nel `/usr/local/share/ca-certificates/extra` percorso nei computer Linux. Per consentire al nodo di verificare il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare il criterio personalizzato. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando criteri di Azure. Un modello di esempio è [disponibile qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Il criterio di accesso Key Vault deve consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per i passaggi dettagliati, vedere [configurare Key Vault per le macchine virtuali in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Di seguito è riportato un esempio di esportazione della chiave pubblica da un certificato di firma per l'importazione nel computer.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Dopo la pubblicazione del contenuto, aggiungere un tag con il `GuestConfigPolicyCertificateValidation` nome e `enabled` il valore a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Vedere gli [esempi di tag](../samples/built-in-policies.md#tags) per il modo in cui i tag possono essere distribuiti su larga scala usando criteri di Azure. Una volta che questo tag è presente, la definizione dei criteri generata `New-GuestConfigurationPolicy` tramite il cmdlet Abilita il requisito tramite l'estensione di configurazione Guest.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Risoluzione dei problemi relativi alle assegnazioni dei criteri di configurazione Guest (anteprima)

Uno strumento è disponibile in anteprima per facilitare la risoluzione dei problemi relativi alle assegnazioni di configurazione Guest di criteri di Azure. Lo strumento è in anteprima ed è stato pubblicato nel PowerShell Gallery come nome modulo [risoluzione dei problemi di configurazione Guest](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Per ulteriori informazioni sui cmdlet in questo strumento, utilizzare il comando Get-Help in PowerShell per visualizzare le linee guida predefinite. Poiché lo strumento sta ottenendo aggiornamenti frequenti, questo è il modo migliore per ottenere le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul controllo delle VM con la [configurazione Guest](../concepts/guest-configuration.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](get-compliance-data.md).
