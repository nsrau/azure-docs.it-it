---
title: Come creare i criteri di configurazione Guest
description: Informazioni su come creare criteri di configurazione Guest di criteri di Azure per macchine virtuali Windows o Linux.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 0c1c3470ae18b2a600af0d5e930b6fc114123728
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161940"
---
# <a name="how-to-create-guest-configuration-policies"></a>Come creare i criteri di configurazione Guest

La configurazione Guest usa un modulo di risorse DSC ( [desired state Configuration](/powershell/dsc) ) per creare la configurazione per il controllo delle macchine Azure. La configurazione DSC definisce la condizione in cui deve trovarsi il computer. Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto del criterio **auditIfNotExists** e il computer viene considerato **non conforme**.

La [configurazione Guest di criteri di Azure](/azure/governance/policy/concepts/guest-configuration) può essere usata solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di una macchina di Azure.

> [!IMPORTANT]
> I criteri personalizzati con la configurazione Guest sono una funzionalità in anteprima.

## <a name="add-the-guestconfiguration-resource-module"></a>Aggiungere il modulo della risorsa GuestConfiguration

Per creare un criterio di configurazione Guest, è necessario aggiungere il modulo della risorsa. Questo modulo di risorse può essere usato con PowerShell installato localmente, con [Azure cloud Shell](https://shell.azure.com)o con l' [immagine Azure PowerShell Docker](https://hub.docker.com/rsdk-powershell/).

### <a name="base-requirements"></a>Requisiti di base

Il modulo della risorsa di configurazione Guest richiede il seguente software:

- PowerShell. Se non è ancora installato, seguire [queste istruzioni](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Installare il modulo

La configurazione Guest usa il modulo della risorsa **GuestConfiguration** per la creazione di configurazioni DSC e la relativa pubblicazione in criteri di Azure:

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

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Creazione di risorse e configurazione personalizzate per la configurazione Guest

Il primo passaggio per la creazione di un criterio personalizzato per la configurazione Guest consiste nel creare la configurazione DSC. Per una panoramica dei concetti e della terminologia DSC, vedere [Panoramica di PowerShell DSC](/powershell/dsc/overview/overview).

Se la configurazione richiede solo risorse compilate con l'installazione dell'agente di configurazione Guest, è sufficiente creare un file MOF di configurazione. Se è necessario eseguire script aggiuntivi, sarà necessario creare un modulo di risorse personalizzato.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Requisiti per le risorse personalizzate di configurazione Guest

Quando la configurazione Guest controlla una macchina, viene eseguita `Test-TargetResource` prima per determinare se è nello stato corretto.  Il valore booleano restituito dalla funzione determina se lo stato del Azure Resource Manager per l'assegnazione Guest deve essere conforme/non conforme.  Se il valore booleano è `$false` per qualsiasi risorsa nella configurazione, il provider verrà eseguito. `Get-TargetResource`
Se il valore `$true` booleano `Get-TargetResource` non viene chiamato.

La funzione `Get-TargetResource` presenta requisiti speciali per la configurazione Guest che non sono stati necessari per la configurazione dello stato desiderato di Windows.

- La tabella hash restituita deve includere una proprietà denominata **reasons**.
- La proprietà reasons deve essere una matrice.
- Ogni elemento nella matrice deve essere una tabella hash con chiavi denominate **Code** e **Phrase**.

La proprietà reasons viene usata dal servizio per standardizzare il modo in cui le informazioni vengono presentate quando un computer non è conforme.
È possibile considerare ogni elemento come un "motivo" che la risorsa non è conforme. La proprietà è una matrice perché una risorsa potrebbe non essere conforme per più di un motivo.

Il **codice** e la **frase** delle proprietà sono previsti dal servizio. Quando si crea una risorsa personalizzata, impostare il testo (in genere stdout) da mostrare come il motivo per cui la risorsa non è conforme come valore per la **frase**.  Il **codice** presenta requisiti di formattazione specifici, quindi la creazione di report consente di visualizzare chiaramente le informazioni sulla risorsa utilizzata per eseguire il controllo. Questa soluzione rende estendibile la configurazione Guest. È possibile eseguire qualsiasi comando per controllare un computer purché l'output possa essere acquisito e restituito come valore stringa per la proprietà **Phrase** .

- **Codice** (stringa): Nome della risorsa, ripetuto, quindi nome breve senza spazi come identificatore per il motivo.  Questi tre valori devono essere delimitati da due punti senza spazi.
    - Un esempio è' Registry: Registry: keynotpresent '.
- **Frase** (stringa): Testo leggibile per spiegare il motivo per cui l'impostazione non è conforme.
    - Un esempio è' la chiave del registro di sistema $key non è presente nel computer '.

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

#### <a name="scaffolding-a-guest-configuration-project"></a>Impalcatura di un progetto di configurazione Guest

Per gli sviluppatori che desiderano accelerare il processo di introduzione e di utilizzo del codice di esempio, un progetto community denominato **progetto di configurazione Guest** esiste come modello per il modulo di PowerShell per [gesso](https://github.com/powershell/plaster) .  Questo strumento può essere usato per eseguire il patibolo di un progetto, tra cui una configurazione funzionante e una risorsa di esempio, e un set di test [Pester](https://github.com/pester/pester) per convalidare il progetto.  Il modello include anche gli esecutori di attività per Visual Studio Code per automatizzare la compilazione e la convalida del pacchetto di configurazione Guest. Per altre informazioni, vedere il progetto di [configurazione Guest](https://github.com/microsoft/guestconfigurationproject)di GitHub Project.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configurazione personalizzata della configurazione Guest in Linux

La configurazione DSC per la configurazione Guest in Linux usa `ChefInSpecResource` la risorsa per fornire al motore il nome della definizione di [chef INSPEC](https://www.chef.io/inspec/) . Il **nome** è l'unica proprietà obbligatoria della risorsa.

L'esempio seguente crea una configurazione denominata **baseline**, importa il modulo della risorsa **GuestConfiguration** e usa la `ChefInSpecResource` risorsa impostare il nome della definizione di INSPEC su **linux-patch-Baseline**:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Per altre informazioni, vedere [scrivere, compilare e applicare una configurazione](/powershell/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Configurazione personalizzata della configurazione Guest in Windows

La configurazione DSC per la configurazione Guest di criteri di Azure viene usata solo dall'agente di configurazione Guest e non è in conflitto con la configurazione dello stato desiderato di Windows PowerShell.

Nell'esempio seguente viene creata una configurazione denominata **AuditBitLocker**, viene importato il modulo della risorsa `Service` **GuestConfiguration** e viene utilizzata la risorsa da controllare per un servizio in esecuzione:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Per altre informazioni, vedere [scrivere, compilare e applicare una configurazione](/powershell/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Creare un pacchetto di criteri personalizzato per la configurazione Guest

Una volta compilato il file MOF, i file di supporto devono essere inclusi nel pacchetto. Il pacchetto completato viene usato dalla configurazione Guest per creare le definizioni di criteri di Azure. Il pacchetto è costituito da:

- Configurazione DSC compilata come MOF
- Cartella moduli
  - Modulo GuestConfiguration
  - Modulo DscNativeResources
  - Linux Una cartella con la definizione di chef INSPEC e contenuti aggiuntivi
  - Windows Moduli di risorse DSC che non sono incorporati

Tramite `New-GuestConfigurationPackage` il cmdlet viene creato il pacchetto. Il formato seguente viene usato per la creazione di un pacchetto personalizzato:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Parametri del `New-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome del pacchetto di configurazione Guest.
- **Configurazione**: Percorso completo del documento di configurazione DSC compilato.
- **Percorso**: Percorso della cartella di output. Questo parametro è facoltativo e, Se non è specificato, il pacchetto viene creato nella directory corrente.
- **ChefProfilePath**: Percorso completo del profilo INSPEC. Questo parametro è supportato solo quando si crea contenuto per il controllo di Linux.

Il pacchetto completato deve essere archiviato in un percorso accessibile dalle macchine virtuali gestite. Gli esempi includono repository GitHub, un repository di Azure o archiviazione di Azure. Se si preferisce non rendere pubblico il pacchetto, è possibile includere un [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) nell'URL. È anche possibile implementare l' [endpoint di servizio](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) per i computer in una rete privata, anche se questa configurazione si applica solo all'accesso al pacchetto e non alla comunicazione con il servizio.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Uso dei segreti nei pacchetti di configurazione Guest

Nella configurazione Guest di criteri di Azure, il modo migliore per gestire i segreti usati in fase di esecuzione è archiviarli in Azure Key Vault. Questa progettazione è implementata all'interno di risorse DSC personalizzate.

Per prima cosa, creare un'identità gestita assegnata dall'utente in Azure. L'identità viene usata dai computer per accedere ai segreti archiviati in Key Vault. Per i passaggi dettagliati, vedere [creare, elencare o eliminare un'identità gestita assegnata dall'utente con Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

Creare un'istanza di Key Vault. Per i passaggi dettagliati, vedere [impostare e recuperare un segreto-PowerShell](../../../key-vault/quick-create-powershell.md).
Assegnare le autorizzazioni all'istanza per concedere all'identità assegnata dall'utente l'accesso ai segreti archiviati in Key Vault. Per i passaggi dettagliati, vedere [impostare e recuperare un segreto-.NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

Assegnare l'identità assegnata dall'utente al computer. Per i passaggi dettagliati, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure con PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
A livello di scala, assegnare questa identità usando Azure Resource Manager tramite criteri di Azure. Per i passaggi dettagliati, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure usando un modello](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

Infine, all'interno della risorsa personalizzata usare l'ID client generato in precedenza per accedere Key Vault usando il token disponibile dal computer. L' `client_id` URL e per l'istanza di Key Vault può essere passato alla risorsa come [proprietà](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema) in modo che la risorsa non debba essere aggiornata per più ambienti o se è necessario modificare i valori.

L'esempio di codice seguente può essere usato in una risorsa personalizzata per recuperare i segreti da Key Vault usando un'identità assegnata dall'utente. Il valore restituito dalla richiesta a Key Vault è testo normale. Come procedura consigliata, archiviarlo in un oggetto credenziale.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Testare un pacchetto di configurazione Guest

Dopo aver creato il pacchetto di configurazione, ma prima di pubblicarlo in Azure, è possibile testare la funzionalità del pacchetto dalla workstation o dall'ambiente CI/CD. Il modulo GuestConfiguration include un cmdlet `Test-GuestConfigurationPackage` che carica lo stesso agente nell'ambiente di sviluppo usato in macchine virtuali di Azure. Con questa soluzione, è possibile eseguire test di integrazione in locale prima di rilasciare gli ambienti di test/QA/produzione fatturati.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Parametri del `Test-GuestConfigurationPackage` cmdlet:

- **Nome**: Nome dei criteri di configurazione Guest.
- **Parametro**: Parametri dei criteri forniti nel formato Hashtable.
- **Percorso**: Percorso completo del pacchetto di configurazione Guest.

Il cmdlet supporta anche l'input dalla pipeline di PowerShell. Inviare tramite pipe l' `New-GuestConfigurationPackage` output del cmdlet `Test-GuestConfigurationPackage` al cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Per ulteriori informazioni su come eseguire il test con i parametri, vedere la sezione seguente [utilizzo di parametri nei criteri di configurazione Guest personalizzati](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Creare i file di definizione dei criteri e di distribuzione delle iniziative di Azure

Dopo la creazione e il caricamento di un pacchetto di criteri personalizzato per la configurazione Guest in un percorso accessibile dai computer, creare la definizione dei criteri di configurazione Guest per criteri di Azure. Il `New-GuestConfigurationPolicy` cmdlet accetta un pacchetto di criteri personalizzato per la configurazione Guest accessibile pubblicamente e crea una definizione dei criteri **auditIfNotExists** e **deployIfNotExists** . Viene anche creata una definizione di iniziativa di criteri che include entrambe le definizioni dei criteri.

Nell'esempio seguente vengono create le definizioni di criteri e iniziative in un percorso specificato da un pacchetto di criteri personalizzato per la configurazione Guest per Windows e vengono forniti un nome, una descrizione e una versione:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Parametri del `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: URI http (s) pubblico del pacchetto del contenuto di configurazione Guest.
- **DisplayName**: Nome visualizzato del criterio.
- **Descrizione**: Descrizione dei criteri.
- **Parametro**: Parametri dei criteri forniti nel formato Hashtable.
- **Versione**: Versione dei criteri.
- **Percorso**: Percorso di destinazione in cui vengono create le definizioni dei criteri.
- **Piattaforma**: Piattaforma di destinazione (Windows/Linux) per i criteri di configurazione Guest e il pacchetto di contenuto.

I file seguenti vengono creati da `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato e il percorso dei file di criteri di Initiative.

Se si desidera utilizzare questo comando per creare un'impalcatura per un progetto di criteri personalizzato, è possibile apportare modifiche a tali file. Un esempio potrebbe modificare la sezione "if" per valutare se è presente un tag specifico per i computer. Per informazioni dettagliate sulla creazione di criteri, vedere [creare criteri a livello di codice](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Utilizzo di parametri nei criteri di configurazione Guest personalizzati

La configurazione Guest supporta l'override delle proprietà di una configurazione in fase di esecuzione. Questa funzionalità significa che i valori nel file MOF nel pacchetto non devono essere considerati statici. I valori di sostituzione vengono forniti tramite criteri di Azure e non influiscano sul modo in cui le configurazioni vengono create o compilate.

I cmdlet `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` includono un parametro denominato **Parameters**.
Questo parametro accetta una definizione Hashtable che include tutti i dettagli su ogni parametro e crea automaticamente tutte le sezioni necessarie dei file usati per creare ogni definizione di criteri di Azure.

L'esempio seguente crea un criterio di Azure per controllare un servizio, in cui l'utente seleziona da un elenco di servizi al momento dell'assegnazione dei criteri.

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Per i criteri di Linux, includere `AttributesYmlContent` la proprietà nella configurazione e sovrascrivere i valori di conseguenza. L'agente di configurazione Guest crea automaticamente il file YaML usato da INSPEC per archiviare gli attributi. Vedere l'esempio seguente.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Per ogni parametro aggiuntivo, aggiungere una tabella hash alla matrice. Nei file dei criteri verranno visualizzate le proprietà aggiunte alla configurazione Guest configurationName che identificano il tipo di risorsa, il nome, la proprietà e il valore.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Criteri di pubblicazione in Azure

Il modulo della risorsa **GuestConfiguration** offre un modo per creare le definizioni dei criteri e la definizione di iniziativa in Azure con un `Publish-GuestConfigurationPolicy` solo passaggio tramite il cmdlet.
Il cmdlet ha solo il parametro **path** che punta al percorso dei tre file JSON creati da `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

Il `Publish-GuestConfigurationPolicy` cmdlet accetta il percorso dalla pipeline di PowerShell. Questa funzionalità significa che è possibile creare i file di criteri e pubblicarli in un unico set di comandi inviati tramite pipe.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Con le definizioni di criteri e iniziative create in Azure, l'ultimo passaggio consiste nell'assegnazione dell'iniziativa. Vedere come assegnare l'iniziativa con il [portale](../assign-policy-portal.md), l'interfaccia della riga di comando di [Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> I criteri di configurazione Guest devono **sempre** essere assegnati usando l'iniziativa che combina i criteri _AuditIfNotExists_ e _DeployIfNotExists_ . Se viene assegnato solo il criterio _AuditIfNotExists_ , i prerequisiti non vengono distribuiti e il criterio indica sempre che i server ' 0' sono conformi.

## <a name="policy-lifecycle"></a>Ciclo di vita dei criteri

Dopo aver pubblicato un criterio personalizzato di Azure usando il pacchetto di contenuto personalizzato, è necessario aggiornare due campi se si vuole pubblicare una nuova versione.

- **Versione**: Quando si esegue il `New-GuestConfigurationPolicy` cmdlet, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato.  Tramite la proprietà viene aggiornata la versione dell'assegnazione di configurazione Guest nel nuovo file dei criteri in modo che l'estensione riconosca che il pacchetto è stato aggiornato.
- **contentHash**: Questa proprietà viene aggiornata automaticamente dal `New-GuestConfigurationPolicy` cmdlet.  Si tratta di un valore hash del pacchetto creato da `New-GuestConfigurationPackage`.  La proprietà deve essere corretta per il `.zip` file pubblicato.  Se viene aggiornata `contentUri` solo la proprietà, ad esempio nel caso in cui un utente possa apportare una modifica manuale alla definizione dei criteri dal portale, l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato.
Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Conversione del contenuto di Windows Criteri di gruppo nella configurazione Guest di criteri di Azure

La configurazione Guest, quando si controllano i computer Windows, è un'implementazione della sintassi di PowerShell DSC (Desired state Configuration).
La community DSC ha pubblicato strumenti per convertire i modelli esportati Criteri di gruppo in formato DSC.
Usando questo strumento con i cmdlet di configurazione Guest descritti in precedenza, è possibile convertire il contenuto di Windows Criteri di gruppo e il pacchetto/pubblicarlo per il controllo di criteri di Azure.
Per informazioni dettagliate sull'uso dello strumento, vedere l' [articolo Guida introduttiva: Convertire Criteri di gruppo in DSC](/powershell/dsc/quickstarts/gpo-quickstart).
Una volta che il contenuto è stato convertito, i passaggi precedenti per creare un pacchetto e pubblicarlo come criterio di Azure saranno identici a quelli per qualsiasi contenuto DSC.

## <a name="optional-signing-guest-configuration-packages"></a>FACOLTATIVO: Firma dei pacchetti di configurazione Guest

Per impostazione predefinita, i criteri personalizzati di configurazione Guest usano l'hash SHA256 per convalidare che il pacchetto di criteri non è stato modificato da quando è stato pubblicato in quando viene letto dal server sottoposto a controllo.
Facoltativamente, i clienti possono anche usare un certificato per firmare i pacchetti e forzare l'estensione di configurazione Guest in modo che consenta solo il contenuto firmato.

Per abilitare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e aggiungere un tag ai computer che devono richiedere la firma del codice.

Per utilizzare la funzionalità di convalida della firma, `Protect-GuestConfigurationPackage` eseguire il cmdlet per firmare il pacchetto prima di pubblicarlo. Questo cmdlet richiede un certificato di firma codice.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametri del `Protect-GuestConfigurationPackage` cmdlet:

- **Percorso**: Percorso completo del pacchetto di configurazione Guest.
- **Certificato**: Certificato di firma del codice per la firma del pacchetto. Questo parametro è supportato solo per la firma di contenuto per Windows.
- **PrivateGpgKeyPath**: Percorso della chiave GPG privata. Questo parametro è supportato solo quando si firma il contenuto per Linux.
- **PublicGpgKeyPath**: Percorso della chiave GPG pubblica. Questo parametro è supportato solo quando si firma il contenuto per Linux.

GuestConfiguration Agent prevede che la chiave pubblica del certificato sia presente in "autorità di certificazione radice attendibili" nei computer Windows e nel `/usr/local/share/ca-certificates/extra` percorso nei computer Linux. Per consentire al nodo di verificare il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare il criterio personalizzato. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando criteri di Azure. Un modello di esempio è [disponibile qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Il criterio di accesso Key Vault deve consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per i passaggi dettagliati, vedere [configurare Key Vault per le macchine virtuali in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Di seguito è riportato un esempio di esportazione della chiave pubblica da un certificato di firma per l'importazione nel computer.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Un utile riferimento per la creazione di chiavi GPG da usare con i computer Linux è fornito da un articolo su GitHub, che [genera una nuova chiave GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Dopo la pubblicazione del contenuto, aggiungere un tag con il `GuestConfigPolicyCertificateValidation` nome e `enabled` il valore a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Questo tag può essere distribuito su vasta scala usando criteri di Azure. Vedere l'esempio [Applica tag e il relativo valore predefinito](../samples/apply-tag-default-value.md) .
Una volta che questo tag è presente, la definizione dei criteri generata `New-GuestConfigurationPolicy` tramite il cmdlet Abilita il requisito tramite l'estensione di configurazione Guest.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>ANTEPRIMA Risoluzione dei problemi relativi alle assegnazioni dei criteri di configurazione Guest

Uno strumento è disponibile in anteprima per facilitare la risoluzione dei problemi relativi alle assegnazioni di configurazione Guest di criteri di Azure.
Lo strumento è in anteprima ed è stato pubblicato nel PowerShell Gallery come nome modulo [risoluzione dei problemi di configurazione Guest](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Per ulteriori informazioni sui cmdlet in questo strumento, utilizzare il comando Get-Help in PowerShell per visualizzare le linee guida predefinite.  Poiché lo strumento sta ottenendo aggiornamenti frequenti, questo è il modo migliore per ottenere le informazioni più recenti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul controllo delle VM con la [configurazione Guest](../concepts/guest-configuration.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](getting-compliance-data.md).
