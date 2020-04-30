---
title: Come creare criteri di configurazione Guest per Linux
description: Informazioni su come creare criteri di configurazione Guest di criteri di Azure per Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024983"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Come creare criteri di configurazione Guest per Linux

Prima di creare criteri personalizzati, leggere le informazioni generali in [configurazione Guest di criteri di Azure](../concepts/guest-configuration.md).
 
Per informazioni sulla creazione di criteri di configurazione Guest per Windows, vedere la pagina [come creare i criteri di configurazione Guest per Windows](./guest-configuration-create.md)

Quando si controlla Linux, la configurazione Guest usa [chef INSPEC](https://www.inspec.io/). Il profilo INSPEC definisce la condizione in cui deve trovarsi il computer. Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto del criterio **auditIfNotExists** e il computer viene considerato **non conforme**.

La [configurazione Guest di criteri di Azure](../concepts/guest-configuration.md) può essere usata solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di un computer Azure o non Azure.

> [!IMPORTANT]
> I criteri personalizzati con la configurazione Guest sono una funzionalità in anteprima.
>
> L'estensione di configurazione Guest è necessaria per eseguire controlli in macchine virtuali di Azure.
> Per distribuire l'estensione su larga scala in tutti i computer Linux, assegnare la definizione dei criteri seguente:
>   - [Distribuisci i prerequisiti per abilitare i criteri di configurazione guest nelle macchine virtuali Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

La creazione di un elemento di configurazione Guest, il test automatizzato dell'artefatto, la creazione di una definizione dei criteri e la pubblicazione del criterio sono completamente automatizzabili usando il modulo di configurazione Guest in PowerShell. Il modulo può essere installato in un computer che esegue Windows, macOS o Linux con PowerShell 6,2 o versioni successive in esecuzione in locale o con [Azure cloud Shell](https://shell.azure.com)o con l' [immagine Docker Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> La compilazione di configurazioni non è supportata in Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Criteri e artefatti di configurazione Guest per Linux

Anche negli ambienti Linux, la configurazione Guest usa la configurazione dello stato desiderato come astrazione del linguaggio. L'implementazione è basata sul codice nativo (C++), quindi non richiede il caricamento di PowerShell. Tuttavia, richiede un file MOF di configurazione che descrive i dettagli sull'ambiente. DSC funge da wrapper per INSPEC per standardizzare la modalità di esecuzione, come vengono forniti i parametri e come viene restituito il risultato al servizio. Quando si lavora con contenuto personalizzato INSPEC, è necessaria una conoscenza minima di DSC.

#### <a name="configuration-requirements"></a>Requisiti di configurazione

Il nome della configurazione personalizzata deve essere coerente ovunque. Il nome del file con estensione zip per il pacchetto di contenuto, il nome della configurazione nel file MOF e il nome dell'assegnazione Guest nel modello di Gestione risorse devono essere uguali.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configurazione personalizzata della configurazione Guest in Linux

La configurazione Guest in Linux usa `ChefInSpecResource` la risorsa per fornire al motore il nome del [profilo INSPEC](https://www.inspec.io/docs/reference/profiles/). Il **nome** è l'unica proprietà obbligatoria della risorsa. Creare un file YaML e un file di script Ruby, come descritto di seguito.

Per prima cosa, creare il file YaML usato da INSPEC. Il file fornisce informazioni di base sull'ambiente. Di seguito è riportato un esempio:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Salvare il file con il `inspec.yml` nome in una cartella `linux-path` denominata nella directory del progetto.

Successivamente, creare il file Ruby con l'astrazione del linguaggio INSPEC usato per controllare il computer.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Salvare il file con nome `linux-path.rb` in una nuova cartella denominata `controls` all'interno `linux-path` della directory.

Infine, creare una configurazione, importare il modulo della risorsa **PSDesiredStateConfiguration** e compilare la configurazione.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Salvare il file con il `config.ps1` nome nella cartella del progetto. Eseguirlo in PowerShell eseguendo `./config.ps1` nel terminale. Verrà creato un nuovo file MOF.

Il `Node AuditFilePathExists` comando non è tecnicamente necessario, ma produce un file `AuditFilePathExists.mof` denominato anziché quello predefinito, `localhost.mof`. Il nome del file con estensione MOF che segue la configurazione consente di organizzare in modo semplice molti file quando si opera su larga scala.



A questo punto dovrebbe essere disponibile una struttura di progetto come indicato di seguito:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

I file di supporto devono essere inclusi nel pacchetto. Il pacchetto completato viene usato dalla configurazione Guest per creare le definizioni di criteri di Azure.

Tramite `New-GuestConfigurationPackage` il cmdlet viene creato il pacchetto. Parametri del cmdlet `New-GuestConfigurationPackage` durante la creazione del contenuto Linux:

- **Nome**: nome del pacchetto di configurazione Guest.
- **Configurazione**: percorso completo del documento di configurazione compilato.
- **Path**: percorso della cartella di output. Questo parametro è facoltativo e, Se non è specificato, il pacchetto viene creato nella directory corrente.
- **ChefProfilePath**: percorso completo del profilo INSPEC. Questo parametro è supportato solo quando si crea contenuto per il controllo di Linux.

Eseguire il comando seguente per creare un pacchetto usando la configurazione fornita nel passaggio precedente:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Dopo aver creato il pacchetto di configurazione, ma prima di pubblicarlo in Azure, è possibile testare il pacchetto dalla workstation o dall'ambiente CI/CD. Il cmdlet `Test-GuestConfigurationPackage` GuestConfiguration include lo stesso agente nell'ambiente di sviluppo usato in macchine virtuali di Azure. Con questa soluzione, è possibile eseguire test di integrazione in locale prima di rilasciare gli ambienti cloud fatturati.

Poiché l'agente sta effettivamente valutando l'ambiente locale, nella maggior parte dei casi è necessario eseguire il cmdlet di test sulla stessa piattaforma del sistema operativo che si prevede di controllare.

Parametri del `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: nome dei criteri di configurazione Guest.
- **Parametro**: parametri dei criteri forniti nel formato Hashtable.
- **Percorso**: percorso completo del pacchetto di configurazione Guest.

Eseguire il comando seguente per testare il pacchetto creato dal passaggio precedente:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Il cmdlet supporta anche l'input dalla pipeline di PowerShell. Inviare tramite pipe l' `New-GuestConfigurationPackage` output del cmdlet `Test-GuestConfigurationPackage` al cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

I file seguenti vengono creati da `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato e il percorso dei file di criteri di Initiative.

Infine, pubblicare le definizioni dei criteri usando `Publish-GuestConfigurationPolicy` il cmdlet.
Il cmdlet ha solo il parametro **path** che punta al percorso dei file JSON creati da `New-GuestConfigurationPolicy`.

Per eseguire il comando Publish, è necessario l'accesso per creare criteri in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica di criteri di Azure](../overview.md) . Il ruolo predefinito migliore è **collaboratore criteri risorse**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Il `Publish-GuestConfigurationPolicy` cmdlet accetta il percorso dalla pipeline di PowerShell. Questa funzionalità significa che è possibile creare i file di criteri e pubblicarli in un unico set di comandi inviati tramite pipe.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Con INSPEC, i parametri vengono in genere gestiti come input in fase di esecuzione o come codice usando gli attributi. La configurazione Guest offusca questo processo, in modo che sia possibile fornire l'input quando viene assegnato un criterio. Un file degli attributi viene creato automaticamente all'interno del computer. Non è necessario creare e aggiungere un file nel progetto. Sono disponibili due passaggi per aggiungere parametri al progetto di controllo di Linux.

Definire l'input nel file Ruby in cui si esegue lo script di elementi da controllare nel computer. Di seguito viene fornito un esempio.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

I cmdlet `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` includono un parametro denominato **Parameters**. Questo parametro accetta una tabella hash che include tutti i dettagli su ogni parametro e crea automaticamente tutte le sezioni necessarie dei file usati per creare ogni definizione di criteri di Azure.

Nell'esempio seguente viene creata una definizione di criteri per controllare un percorso di file, in cui l'utente fornisce il percorso al momento dell'assegnazione dei criteri.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Per i criteri di Linux, includere la proprietà **AttributesYmlContent** nella configurazione e sovrascrivere i valori in base alle esigenze. L'agente di configurazione Guest crea automaticamente il file YAML usato da INSPEC per archiviare gli attributi. Vedere l'esempio seguente.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Ciclo di vita dei criteri

Per rilasciare un aggiornamento alla definizione dei criteri, sono disponibili due campi che richiedono attenzione.

- **Versione**: quando si esegue il `New-GuestConfigurationPolicy` cmdlet, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato. La proprietà aggiorna la versione dell'assegnazione di configurazione Guest in modo che l'agente riconosca il pacchetto aggiornato.
- **contentHash**: questa proprietà viene aggiornata automaticamente dal `New-GuestConfigurationPolicy` cmdlet. Si tratta di un valore hash del pacchetto creato da `New-GuestConfigurationPackage`. La proprietà deve essere corretta per il `.zip` file pubblicato. Se viene aggiornata solo la proprietà **contentUri** , l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato. Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.

## <a name="optional-signing-guest-configuration-packages"></a>Facoltativo: firma dei pacchetti di configurazione Guest

I criteri personalizzati di configurazione Guest usano l'hash SHA256 per convalidare il pacchetto dei criteri non è stato modificato.
Facoltativamente, i clienti possono anche usare un certificato per firmare i pacchetti e forzare l'estensione di configurazione Guest in modo che consenta solo il contenuto firmato.

Per abilitare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e aggiungere un tag ai computer che devono richiedere la firma del codice.

Per utilizzare la funzionalità di convalida della firma, `Protect-GuestConfigurationPackage` eseguire il cmdlet per firmare il pacchetto prima di pubblicarlo. Questo cmdlet richiede un certificato di firma codice.

Parametri del `Protect-GuestConfigurationPackage` cmdlet:

- **Percorso**: percorso completo del pacchetto di configurazione Guest.
- **PublicGpgKeyPath**: percorso della chiave GPG pubblico. Questo parametro è supportato solo quando si firma il contenuto per Linux.

Un utile riferimento per la creazione di chiavi GPG da usare con i computer Linux è fornito da un articolo su GitHub, che [genera una nuova chiave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

GuestConfiguration Agent prevede che la chiave pubblica del certificato sia presente nel percorso `/usr/local/share/ca-certificates/extra` nei computer Linux. Per consentire al nodo di verificare il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare il criterio personalizzato. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando criteri di Azure. Un modello di esempio è [disponibile qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Il criterio di accesso Key Vault deve consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per i passaggi dettagliati, vedere [configurare Key Vault per le macchine virtuali in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Dopo la pubblicazione del contenuto, aggiungere un tag con il `GuestConfigPolicyCertificateValidation` nome e `enabled` il valore a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Vedere gli [esempi di tag](../samples/built-in-policies.md#tags) per il modo in cui i tag possono essere distribuiti su larga scala usando criteri di Azure. Una volta che questo tag è presente, la definizione dei criteri generata `New-GuestConfigurationPolicy` tramite il cmdlet Abilita il requisito tramite l'estensione di configurazione Guest.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Risoluzione dei problemi relativi alle assegnazioni dei criteri di configurazione Guest (anteprima)

Uno strumento è disponibile in anteprima per facilitare la risoluzione dei problemi relativi alle assegnazioni di configurazione Guest di criteri di Azure. Lo strumento è in anteprima ed è stato pubblicato nel PowerShell Gallery come nome modulo [risoluzione dei problemi di configurazione Guest](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Per ulteriori informazioni sui cmdlet in questo strumento, utilizzare il comando Get-Help in PowerShell per visualizzare le linee guida predefinite. Poiché lo strumento sta ottenendo aggiornamenti frequenti, questo è il modo migliore per ottenere le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul controllo delle VM con la [configurazione Guest](../concepts/guest-configuration.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](get-compliance-data.md).
