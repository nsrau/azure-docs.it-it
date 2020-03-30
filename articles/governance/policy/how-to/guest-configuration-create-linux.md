---
title: Come creare criteri di configurazione guest per LinuxHow to create Guest Configuration policies for Linux
description: Informazioni su come creare criteri di configurazione guest dei criteri di Azure per Linux.Learn how to create an Azure Policy Guest Configuration policy for Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f93aafc8f2c016218b1b7fea82558ea6ba4b4ff8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365402"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Come creare criteri di configurazione guest per LinuxHow to create Guest Configuration policies for Linux

Prima di creare criteri personalizzati, leggere le informazioni generali in [Configurazione guest di Criteri di Azure.](../concepts/guest-configuration.md)
 
Per informazioni sulla creazione di criteri di configurazione guest per Windows, vedere la pagina Come creare criteri di [configurazione guest per Windows](./guest-configuration-create.md)

Quando si esegue il controllo di Linux, Configurazione ospite utilizza [Chef InSpec](https://www.inspec.io/). Il profilo InSpec definisce la condizione in cui deve trovarsi la macchina. Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto criterio **auditIfNotExists** e il computer viene considerato **non conforme.**

[La configurazione Guest di Criteri di Azure](../concepts/guest-configuration.md) può essere usata solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di un computer Azure o non Azure.Use the following actions to create your own configuration for validating the state of an Azure or non-Azure machine.

> [!IMPORTANT]
> I criteri personalizzati con Configurazione ospite sono una funzionalità di anteprima.

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

Creating a Guest Configuration artifact, automated testing of the artifact, creating a policy definition, and publishing the policy, is entirely automatable using the Guest Configuration module in PowerShell. Il modulo può essere installato in un computer che esegue Windows, macOS o Linux con PowerShell 6.2 o versione successiva in esecuzione in locale oppure con [Azure Cloud Shell](https://shell.azure.com)oppure con [l'immagine Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core)principale di Azure PowerShell.

> [!NOTE]
> La compilazione delle configurazioni non è supportata in Linux.Compilation of configurations isn't supported on Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Guest Configuration artifacts and policy for Linux

Anche negli ambienti Linux, Configurazione ospite utilizza desired State Configuration come astrazione del linguaggio. L'implementazione è basata sul codice nativo (C ) in modo che non richiede il caricamento di PowerShell.The implementation is based in native code (C'è) so it't require loading PowerShell. Tuttavia, richiede un MOF di configurazione che descrive i dettagli sull'ambiente. DSC funge da wrapper per InSpec per standardizzare come viene eseguito, come vengono forniti i parametri e come l'output viene restituito al servizio. Quando si lavora con contenuto InSpec personalizzato, è necessaria una scarsa conoscenza di DSC.

#### <a name="configuration-requirements"></a>Requisiti di configurazione

Il nome della configurazione personalizzata deve essere coerente ovunque. Il nome del file .zip per il pacchetto di contenuto, il nome della configurazione nel file MOF e il nome dell'assegnazione guest nel modello di Resource Manager devono essere uguali.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configurazione della configurazione guest personalizzata su LinuxCustom Guest Configuration configuration on Linux

Configurazione guest su `ChefInSpecResource` Linux utilizza la risorsa per fornire al motore il nome del [profilo InSpec](https://www.inspec.io/docs/reference/profiles/). **Name** è l'unica proprietà obbligatoria della risorsa. Creare un file YaML e un file di script Ruby, come descritto di seguito.

Creare innanzitutto il file YaML utilizzato da InSpec. Il file fornisce informazioni di base sull'ambiente. Di seguito è riportato un esempio:An example is given below:

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

Salvare il file in `linux-path` una cartella denominata nella directory del progetto.

Successivamente, creare il file Ruby con l'astrazione del linguaggio InSpec utilizzata per controllare il computer.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Salvare il file in `controls` una `linux-path` nuova cartella denominata all'interno della directory.

Infine, creare una configurazione, importare il modulo `ChefInSpecResource` di risorsa **GuestConfiguration** e utilizzare la risorsa per impostare il nome del profilo InSpec.

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
AuditFilePathExists -out ./Config
```

Il `Node AuditFilePathExists` comando non è tecnicamente richiesto, `AuditFilePathExists.mof` ma produce un `localhost.mof`file denominato anziché il valore predefinito, . Avere il nome del file .mof seguire la configurazione rende facile organizzare molti file quando si opera su larga scala.

A questo punto dovrebbe essere una struttura di progetto come indicato di seguito:You should now have a project structure as below:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

I file di supporto devono essere inclusi nel pacchetto. Il pacchetto completato viene usato da Configurazione ospite per creare le definizioni dei criteri di Azure.The completed package is used by Guest Configuration to create the Azure Policy definitions.

Il `New-GuestConfigurationPackage` cmdlet crea il pacchetto. Parametri del `New-GuestConfigurationPackage` cmdlet durante la creazione di contenuto Linux:

- **Nome**: Nome pacchetto di configurazione guest.
- **Configurazione**: Percorso completo del documento di configurazione compilato.
- **Percorso**: Percorso cartella di output. Questo parametro è facoltativo e, Se non specificato, il pacchetto viene creato nella directory corrente.
- **ChefProfilePath:** percorso completo del profilo InSpec. Questo parametro è supportato solo quando si crea contenuto per il controllo Linux.This parameter is supported only when creating content to audit Linux.

Eseguire il comando seguente per creare un pacchetto usando la configurazione specificata nel passaggio precedente:Run the following command to create a package using the configuration given in the previous step:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof'
  -ChefProfilePath './'
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
  -Path ./AuditFilePathExists.zip
```

Il cmdlet supporta anche l'input dalla pipeline di PowerShell.The cmdlet also supports input from the PowerShell pipeline. Eseguire il `New-GuestConfigurationPackage` pipamento `Test-GuestConfigurationPackage` dell'output del cmdlet al cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

I seguenti file `New-GuestConfigurationPolicy`sono creati da:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato dell'iniziativa e il percorso dei file di criteri.

Infine, pubblicare le `Publish-GuestConfigurationPolicy` definizioni dei criteri utilizzando il cmdlet.
Il cmdlet dispone solo del parametro **Path** che punta `New-GuestConfigurationPolicy`al percorso dei file JSON creati da .

To run the Publish command, you need access to create Policies in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica dei criteri di Azure.The](../overview.md) specific authorization requirements are documented in the Azure Policy Overview page. Il ruolo predefinito migliore è **Contributore criteri risorse**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Il `Publish-GuestConfigurationPolicy` cmdlet accetta il percorso dalla pipeline di PowerShell. Questa funzionalità consente di creare i file di criteri e pubblicarli in un unico set di comandi reindirizzati.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Con InSpec, i parametri vengono in genere gestiti come input in fase di esecuzione o come codice tramite attributi. La configurazione guest offusca questo processo in modo che l'input possa essere fornito quando viene assegnato il criterio. Un file di attributi viene creato automaticamente all'interno della macchina. Non è necessario creare e aggiungere un file nel progetto. Esistono due passaggi per aggiungere parametri al progetto di controllo Linux.There are two steps to adding parameters to your Linux audit project.

Definire l'input nel file Ruby in cui si esegue lo script degli elementi da controllare nel computer. Di seguito è riportato un esempio.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

I cmdlet `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` e includono un parametro denominato **Parameters**. Questo parametro accetta una tabella hash che include tutti i dettagli su ogni parametro e crea automaticamente tutte le sezioni necessarie dei file usati per creare ogni definizione di Criteri di Azure.This parameter takes a hashtable includes all details about each parameter and automatically creates all the required sections of the files used to create each Azure Policy definition.

Nell'esempio seguente viene creata una definizione dei criteri per controllare un percorso di file, in cui l'utente fornisce il percorso al momento dell'assegnazione dei criteri.

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

Per i criteri Linux, includere la proprietà **AttributesYmlContent** nella configurazione e sovrascrivere i valori in base alle esigenze. L'agente di configurazione guest crea automaticamente il file YAML utilizzato da InSpec per archiviare gli attributi. Vedere l'esempio seguente.

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

Per rilasciare un aggiornamento della definizione dei criteri, sono disponibili due campi che richiedono attenzione.

- **Versione**: quando `New-GuestConfigurationPolicy` si esegue il cmdlet, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato. La proprietà aggiorna la versione dell'assegnazione Configurazione ospite in modo che l'agente riconosca il pacchetto aggiornato.
- **contentHash**: questa proprietà viene `New-GuestConfigurationPolicy` aggiornata automaticamente dal cmdlet. Si tratta di un valore hash `New-GuestConfigurationPackage`del pacchetto creato da . La proprietà deve essere `.zip` corretta per il file pubblicato. Se viene aggiornata solo la proprietà **contentUri,** l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato. Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.

## <a name="optional-signing-guest-configuration-packages"></a>Facoltativo: Firma dei pacchetti di configurazione guestOptional: Signing Guest Configuration packages

I criteri personalizzati di configurazione guest usano l'hash SHA256 per convalidare che il pacchetto di criteri non sia stato modificato.
Facoltativamente, i clienti possono anche utilizzare un certificato per firmare i pacchetti e forzare l'estensione Configurazione guest per consentire solo il contenuto firmato.

Per abilitare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e aggiungere un tag ai computer che devono richiedere la firma del codice.

Per utilizzare la funzionalità di `Protect-GuestConfigurationPackage` convalida della firma, eseguire il cmdlet per firmare il pacchetto prima della pubblicazione. Questo cmdlet richiede un certificato 'Firma codice'.

Parametri del `Protect-GuestConfigurationPackage` cmdlet:

- Path ( **Percorso):** percorso completo del pacchetto di configurazione guest.
- **PublicGpgKeyPath:** percorso della chiave GPG pubblica. Questo parametro è supportato solo quando si firma il contenuto per Linux.This parameter is only supported when signing content for Linux.

Un buon riferimento per la creazione di chiavi GPG da utilizzare con macchine Linux è fornito da un articolo su GitHub, [Generazione di una nuova chiave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

L'agente GuestConfiguration prevede che la chiave `/usr/local/share/ca-certificates/extra` pubblica del certificato sia presente nel percorso nei computer Linux.GuestConfiguration agent expects the certificate public key to be present in the path on Linux machines. Affinché il nodo verifichi il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare il criterio personalizzato. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando Criteri di Azure.This process can be done using any technique inside the VM, or by using Azure Policy. Un modello di esempio viene [fornito qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
I criteri di accesso dell'insieme di credenziali delle chiavi devono consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per la procedura dettagliata, vedere [Configurare l'insieme](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)di credenziali delle chiavi per le macchine virtuali in Azure Resource Manager.

Dopo la pubblicazione del contenuto, `GuestConfigPolicyCertificateValidation` aggiungere `enabled` un tag con nome e valore a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Vedere gli esempi di tag per informazioni su come i tag possono essere recapitati su larga scala usando Criteri di Azure.See the [Tag samples](../samples/built-in-policies.md#tags) for how tags can be delivered at scale using Azure Policy. Una volta che questo tag è sul `New-GuestConfigurationPolicy` posto, la definizione dei criteri generata utilizzando il cmdlet abilita il requisito tramite l'estensione Configurazione ospite.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Risoluzione dei problemi relativi alle assegnazioni dei criteri di configurazione guest (anteprima)Troubleshooting Guest Configuration policy assignments (Preview)

Uno strumento è disponibile in anteprima per facilitare la risoluzione dei problemi delle assegnazioni di configurazione guest di Criteri di Azure.A tool is available in preview to assist in troubleshooting Azure Policy Guest Configuration assignments. Lo strumento è in anteprima ed è stato pubblicato in PowerShell Gallery come nome del modulo [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Per ulteriori informazioni sui cmdlet in questo strumento, usare il comando Get-Help in PowerShell per visualizzare le indicazioni predefinite. Come lo strumento sta ottenendo aggiornamenti frequenti, che è il modo migliore per ottenere le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul controllo delle macchine virtuali con [Configurazione guest](../concepts/guest-configuration.md).
- Comprendere come creare criteri a livello di [codice.](programmatically-create.md)
- Scopri come ottenere i dati di [conformità](get-compliance-data.md).
