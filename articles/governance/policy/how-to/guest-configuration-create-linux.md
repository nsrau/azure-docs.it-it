---
title: Come creare criteri di Configurazione guest per Linux
description: Informazioni su come creare criteri di Configurazione guest di Criteri di Azure per Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ecf798a18f28c490d95b28c6ea8f02c6f22eee8
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893238"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Come creare criteri di Configurazione guest per Linux

Prima di creare criteri personalizzati, leggere le informazioni generali in [Configurazione guest di Criteri di Azure](../concepts/guest-configuration.md).
 
Per informazioni sulla creazione di criteri di Configurazione guest per Windows, vedere la pagina [Come creare criteri di Configurazione guest per Windows](./guest-configuration-create.md)

Quando si esegue il controllo di Linux, Configurazione guest usa [Chef InSpec](https://www.inspec.io/). Il profilo InSpec definisce la condizione in cui deve trovarsi il computer. Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto del criterio **auditIfNotExists** e il computer viene considerato **non conforme**.

[Configurazione guest di Criteri di Azure](../concepts/guest-configuration.md) può essere usata solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di un computer Azure o non Azure.

> [!IMPORTANT]
> I criteri personalizzati con Configurazione guest sono una funzionalità di anteprima.
>
> L'estensione Configurazione guest è necessaria per eseguire controlli in macchine virtuali di Azure. Per distribuire l'estensione su larga scala in tutti i computer Linux, assegnare la definizione dei criteri seguente: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`

## <a name="install-the-powershell-module"></a>Installare il modulo PowerShell

Il modulo Configurazione guest automatizza il processo di creazione di contenuti personalizzati, tra cui:

- Creazione di un artefatto del contenuto di Configurazione guest (ZIP)
- Test automatizzato dell'artefatto
- Creazione di una definizione dei criteri
- Pubblicazione dei criteri

Il modulo può essere installato in un computer che esegue Windows, macOS o Linux con PowerShell 6.2 o versioni successive in esecuzione in locale oppure con [Azure Cloud Shell](https://shell.azure.com) o con l'[immagine Docker Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> La compilazione di configurazioni non è supportata in Linux.

### <a name="base-requirements"></a>Requisiti di base

Sistemi operativi in cui è possibile installare il modulo:

- Linux
- macOS
- Windows

> [!NOTE]
> Il cmdlet ' test-GuestConfigurationPackage ' richiede la versione OpenSSL 1,0, a causa di una dipendenza da OMI. Questo genera un errore in qualsiasi ambiente con OpenSSL 1,1 o versione successiva.

Il modulo risorse Configurazione guest richiede il software seguente:

- PowerShell 6.2 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/scripting/install/installing-powershell).
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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Criteri e artefatti di Configurazione guest per Linux

Anche negli ambienti Linux, Configurazione guest usa Desired State Configuration come astrazione del linguaggio. L'implementazione è basata sul codice nativo (C++) in modo da non richiedere il caricamento di PowerShell. Tuttavia, richiede un file MOF di configurazione che descrive i dettagli sull'ambiente.
DSC funge da wrapper per InSpec per standardizzare la modalità di esecuzione, il modo in cui vengono forniti i parametri e il modo in cui l’output viene restituito al servizio. Quando si lavora con contenuto InSpec personalizzato, è necessaria una conoscenza minima di DSC.

#### <a name="configuration-requirements"></a>Requisiti di configurazione

Il nome della configurazione personalizzata deve essere coerente ovunque. Il nome del file con estensione zip per il pacchetto di contenuto, il nome della configurazione nel file MOF e il nome dell'assegnazione Guest nel modello di Azure Resource Manager (modello ARM) devono essere uguali.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configurazione personalizzata di Configurazione guest in Linux

Configurazione guest in Linux usa la risorsa `ChefInSpecResource` per fornire al motore il nome del [profilo InSpec](https://www.inspec.io/docs/reference/profiles/). **Name** è l'unica proprietà della risorsa richiesta. Creare un file YaML e un file di script Ruby, come descritto di seguito.

Innanzitutto, creare il file YaML usato da InSpec. Il file fornisce informazioni di base sull'ambiente. Di seguito è illustrato un esempio:

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

Salvare questo file con il nome `inspec.yml` in una cartella denominata `linux-path` nella directory del progetto.

Successivamente, creare il file Ruby con l'astrazione del linguaggio InSpec usata per controllare il computer.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Salvare questo file con il nome `linux-path.rb` in una nuova cartella denominata `controls` all'interno della directory `linux-path`.

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

Salvare questo file con il nome `config.ps1` nella cartella del progetto. Eseguirlo in PowerShell eseguendo `./config.ps1` nel terminale. Verrà creato un nuovo file MOF.

Il comando `Node AuditFilePathExists` non è tecnicamente necessario, ma produce un file denominato `AuditFilePathExists.mof` anziché il file `localhost.mof` predefinito. Il nome del file MOF che segue la configurazione consente di organizzare in modo semplice molti file quando si opera su larga scala.

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

I file di supporto devono essere inclusi in un unico pacchetto. Il pacchetto completato viene usato da Configurazione guest per creare le definizioni di Criteri di Azure.

Il cmdlet `New-GuestConfigurationPackage` crea il pacchetto. Parametri del cmdlet `New-GuestConfigurationPackage` durante la creazione del contenuto Linux:

- **Name**: Nome del pacchetto di Configurazione guest.
- **Configuration**: percorso completo del documento di configurazione compilato.
- **Path**: percorso della cartella di output. Questo parametro è facoltativo e, se non è specificato, il pacchetto viene creato nella directory corrente.
- **ChefProfilePath**: Percorso completo del profilo InSpec. Questo parametro è supportato solo quando si crea contenuto per il controllo di Linux.

Eseguire il comando seguente per creare un pacchetto usando la configurazione fornita nel passaggio precedente:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Dopo aver creato il pacchetto di configurazione, ma prima di pubblicarlo in Azure, è possibile testare il pacchetto dalla workstation o dall'ambiente di integrazione continua e distribuzione continua (CI/CD). Il cmdlet GuestConfiguration `Test-GuestConfigurationPackage` include lo stesso agente nell'ambiente di sviluppo utilizzato all'interno dei computer Azure. Con questa soluzione, è possibile eseguire test di integrazione in locale prima del rilascio in ambienti cloud fatturati.

Poiché l'agente valuta effettivamente l'ambiente locale, nella maggior parte dei casi è necessario eseguire il cmdlet Test- sulla stessa piattaforma del sistema operativo che si prevede di controllare.

Parametri del cmdlet `Test-GuestConfigurationPackage`:

- **Name**: nome dei criteri di Configurazione guest.
- **Parameter**: parametri dei criteri forniti in formato di tabella hash.
- **Path**: percorso completo del pacchetto di Configurazione guest.

Eseguire il comando seguente per testare il pacchetto creato nel passaggio precedente:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Il cmdlet supporta anche l'input dalla pipeline di PowerShell. Inoltrare tramite pipe l'output del cmdlet `New-GuestConfigurationPackage` al cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

Il passaggio successivo consiste nel pubblicare il file nell'archivio BLOB di Azure. Lo script seguente contiene una funzione che è possibile usare per automatizzare questa attività. I comandi usati nella funzione `publish` richiedono il modulo `Az.Storage`.

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
Una volta creato e caricato un pacchetto di criteri personalizzati di Configurazione guest, creare la definizione dei criteri di Configurazione guest. Il cmdlet `New-GuestConfigurationPolicy` accetta un pacchetto di criteri personalizzati e crea una definizione dei criteri.

Parametri del cmdlet `New-GuestConfigurationPolicy`:

- **ContentUri**: URI http(s) pubblico del pacchetto di contenuto di Configurazione guest.
- **DisplayName**: nome visualizzato dei criteri.
- **Description**: descrizione dei criteri.
- **Parameter**: parametri dei criteri forniti in formato di tabella hash.
- **Version**: versione dei criteri.
- **Path**: percorso di destinazione in cui vengono create le definizioni dei criteri.
- **Platform**: piattaforma di destinazione (Windows/Linux) per i criteri e il pacchetto di contenuto di Configurazione guest.
- **Tag** aggiunge uno o più filtri di tag alla definizione dei criteri
- **Category** imposta il campo dei metadati della categoria nella definizione dei criteri

Nell'esempio seguente vengono create le definizioni dei criteri in un percorso specificato da un pacchetto di criteri personalizzati:

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

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato dell'iniziativa e il percorso dei file dei criteri.

Infine, pubblicare le definizioni dei criteri usando il cmdlet `Publish-GuestConfigurationPolicy`. Il cmdlet ha solo il parametro **Path** che punta al percorso dei file JSON creati da `New-GuestConfigurationPolicy`.

Per eseguire il comando Publish, è necessario l'accesso per creare criteri in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica di Criteri di Azure](../overview.md). Il ruolo predefinito migliore è **Collaboratore ai criteri delle risorse**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Il cmdlet `Publish-GuestConfigurationPolicy` accetta il percorso dalla pipeline di PowerShell. Con questa funzionalità è possibile creare i file dei criteri e pubblicarli in un unico set di comandi inoltrati tramite pipe.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Una volta creati i criteri in Azure, l'ultimo passaggio consiste nell'assegnazione dell'iniziativa. Vedere come assegnare l'iniziativa con il [portale](../assign-policy-portal.md), l'[interfaccia della riga di comando di Azure](../assign-policy-azurecli.md) e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> I criteri di Configurazione guest devono **sempre** essere assegnati usando l'iniziativa che combina i criteri _AuditIfNotExists_ e _DeployIfNotExists_. Se vengono assegnati solo i criteri _AuditIfNotExists_, i prerequisiti non vengono distribuiti e i criteri indicano sempre che "0" server sono conformi.

Per assegnare una definizione dei criteri con l'effetto _DeployIfNotExists_, è necessario un livello di accesso aggiuntivo. Per concedere privilegi minimi, è possibile creare una definizione del ruolo personalizzata che estende il ruolo **Collaboratore ai criteri delle risorse**. Nell'esempio riportato di seguito viene creato un ruolo denominato **Resource Policy Contributor DINE** con l'autorizzazione aggiuntiva _Microsoft.Authorization/roleAssignments/write_.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilizzo di parametri nei criteri di Configurazione guest personalizzati

Configurazione guest supporta l'override delle proprietà di una configurazione in fase di esecuzione. Ciò significa che i valori nel file MOF del pacchetto non devono essere considerati statici. I valori di sostituzione vengono forniti tramite Criteri di Azure e non influiscono sul modo in cui le configurazioni vengono create o compilate.

Con InSpec, i parametri vengono in genere gestiti come input in fase di esecuzione o come codice usando gli attributi. Configurazione guest offusca questo processo in modo che sia possibile fornire l'input quando viene assegnato un criterio. Un file degli attributi viene creato automaticamente all'interno del computer. Non è necessario creare e aggiungere un file nel progetto. Sono disponibili due passaggi per aggiungere parametri al progetto di controllo di Linux.

Definire l'input nel file Ruby in cui si esegue lo script degli elementi da controllare nel computer. Di seguito è illustrato un esempio.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

I cmdlet `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` includono un parametro denominato **Parameter**. Questo parametro prende una tabella hash che include tutti i dettagli su ogni parametro e crea automaticamente tutte le sezioni necessarie dei file usati per creare ogni definizione di Criteri di Azure.

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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

Per i criteri di Linux, includere la proprietà **AttributesYmlContent** nella configurazione e sovrascrivere i valori in base alle esigenze. L'agente di Configurazione guest crea automaticamente il file YAML usato da InSpec per archiviare gli attributi. Vedere l'esempio seguente.

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

Per rilasciare un aggiornamento della definizione dei criteri, è necessario prestare attenzione a due campi.

- **Versione**: Quando si esegue il cmdlet `New-GuestConfigurationPolicy`, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato. La proprietà aggiorna la versione dell'assegnazione di Configurazione guest in modo che l'agente riconosca il pacchetto aggiornato.
- **contentHash**: questa proprietà viene aggiornata automaticamente dal cmdlet `New-GuestConfigurationPolicy`. Si tratta di un valore hash del pacchetto creato da `New-GuestConfigurationPackage`. La proprietà deve essere corretta per il file `.zip` da pubblicare. Se viene aggiornata solo la proprietà **contentUri**, l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato. Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.


### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrare i criteri di Configurazione guest usando i tag

I criteri creati dai cmdlet nel modulo Configurazione guest possono includere facoltativamente un filtro per i tag. Il parametro **-Tag** di `New-GuestConfigurationPolicy` supporta un array di tabelle hash che contengono voci di tag singoli. I tag verranno aggiunti alla sezione `If` della definizione dei criteri e non possono essere modificati da un'assegnazione di criteri.

Di seguito è riportato un frammento di esempio di una definizione dei criteri che filtra in base ai tag.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Facoltativo: Firma dei pacchetti di Configurazione guest

I criteri personalizzati di Configurazione guest usano l'hash SHA256 per verificare che il pacchetto dei criteri non sia stato modificato.
Facoltativamente, i clienti possono anche usare un certificato per firmare i pacchetti e forzare l'estensione Configurazione guest in modo da consentire solo il contenuto firmato.

Per attuare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e accodare un tag ai computer che devono richiedere la firma del codice.

Per usare la funzionalità di convalida della firma, eseguire il cmdlet `Protect-GuestConfigurationPackage` per firmare il pacchetto prima di pubblicarlo. Questo cmdlet richiede un certificato di firma del codice.

Parametri del cmdlet `Protect-GuestConfigurationPackage`:

- **Percorso**: Percorso completo del pacchetto di Configurazione guest.
- **PublicGpgKeyPath**: Percorso della chiave GPG pubblica. Questo parametro è supportato solo quando si firma il contenuto per Linux.

Un utile riferimento per la creazione di chiavi GPG da usare con i computer Linux è fornito da un articolo in GitHub, [Generazione di una nuova chiave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

L’agente GuestConfiguration prevede che la chiave pubblica del certificato sia presente nel percorso `/usr/local/share/ca-certificates/extra` nei computer Linux. Per consentire al nodo di verificare il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare il criterio personalizzato. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando Criteri di Azure. Un modello di esempio è [disponibile qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
I criteri di accesso di Key Vault devono consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per i passaggi dettagliati, vedere [Configurare Key Vault per le macchine virtuali in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Una volta pubblicato il contenuto, accodare un tag con il nome `GuestConfigPolicyCertificateValidation` e il valore `enabled` a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Vedere gli [esempi di tag](../samples/built-in-policies.md#tags) per informazioni sulle modalità di distribuzione dei tag su larga scala usando Criteri di Azure. Una volta che il tag è stato inserito, la definizione dei criteri generata usando il cmdlet `New-GuestConfigurationPolicy` abilita il requisito tramite l'estensione Configurazione guest.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Risoluzione dei problemi relativi alle assegnazioni dei criteri di Configurazione guest (anteprima)

È disponibile uno strumento in anteprima per facilitare la risoluzione dei problemi relativi alle assegnazioni di Configurazione guest di Criteri di Azure. Lo strumento è in anteprima ed è stato pubblicato in PowerShell Gallery come modulo denominato [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Per altre informazioni sui cmdlet in questo strumento, usare il comando Get-Help in PowerShell per visualizzare la guida predefinita. Lo strumento è sottoposto ad aggiornamenti frequenti, quindi questo è il modo migliore per ottenere le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sul controllo delle macchine virtuali con [Configurazione guest](../concepts/guest-configuration.md).
- Vedere come [creare criteri a livello di codice](./programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](./get-compliance-data.md).
