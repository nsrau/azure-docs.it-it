---
title: Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come creare ambienti con più macchine virtuali e risorse PaaS in Azure DevTest Labs con un modello di Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: 96e3a24b0c9f9ab21652ffcd1b29deeb512581e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796934"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager

Il [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) consente di [aggiungere una macchina virtuale alla volta a un lab](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Tuttavia, se l'ambiente contiene più macchine virtuali, ogni macchina deve essere creata individualmente. Per scenari quali app Web multilivello o farm di SharePoint, è necessario un meccanismo che consenta la creazione di più macchine virtuali in un unico passaggio. I modelli di Azure Resource Manager consentono di definire l'infrastruttura e la configurazione della soluzione di Azure e di distribuire ripetutamente più macchine virtuali in modo coerente. Ciò offre i vantaggi seguenti:

- I modelli di Azure Resource Manager vengono caricati direttamente dal repository di controllo del codice sorgente (GitHub o Azure DevOps Services Git).
- Al termine della configurazione, gli utenti possono creare un ambiente scegliendo un modello di Azure Resource Manager dal portale di Azure, come avviene con altri tipi di [basi per macchine virtuali](./devtest-lab-comparing-vm-base-image-types.md).
- È possibile eseguire il provisioning delle risorse di Azure PaaS in un ambiente usando un modello di Azure Resource Manager oltre che le macchine virtuali IaaS.
- È possibile tenere traccia del costo dell'ambiente insieme alle singole VM create da altri tipi di base.
- Le risorse di PaaS vengono create e visualizzate nella verifica dei costi. Tuttavia, l'arresto automatico della macchina virtuale non si applica alle risorse di PaaS.

Altre informazioni sui numerosi [vantaggi offerti dai modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) per distribuire, aggiornare o eliminare tutte le risorse del lab con una sola operazione.

> [!NOTE]
> Quando si usa un modello di Resource Manager come base per creare più macchine virtuali lab, occorre tenere presente che ci sono alcune differenze tra la creazione di più macchine virtuali e una singola macchina virtuale. Nell'articolo [Use a virtual machine's Azure Resource Manager template](devtest-lab-use-resource-manager-template.md) (Usare un modello di Azure Resource Manager di una macchina virtuale) queste differenze sono illustrate con maggiori dettagli.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="devtest-labs-public-environments"></a>Ambienti pubblici in DevTest Labs
Azure DevTest Labs include un [repository pubblico di modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) che consente di creare ambienti senza connettersi a un'origine GitHub esterna. Questo repository include i modelli usati di frequente, ad esempio App Web di Azure, Cluster di Service Fabric e l'ambiente di sviluppo della Farm di SharePoint. Questa funzionalità è simile al repository pubblico di artefatti incluso in ogni lab creato. Il repository di ambienti consente di iniziare rapidamente usando modelli di ambiente predefiniti con parametri di input minimi per garantire un'agevole esperienza iniziale con le risorse PaaS all'interno dei lab. Per altre informazioni, vedere [Configurare e usare ambienti pubblici in DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Configurare i propri repository di modelli
Come procedura consigliata nei casi di infrastruttura come codice e configurazione come codice, i modelli di ambiente devono essere gestiti nel controllo del codice sorgente. Azure DevTest Labs applica questa procedura e carica tutti i modelli Azure Resource Manager direttamente dai repository GitHub o Azure DevOps Services Git. Di conseguenza, i modelli di Resource Manager possono essere usati in tutto il ciclo di rilascio, dall'ambiente di test a quello di produzione.

Esaminare i modelli creati dal team di DevTest Labs nel [repository GitHub pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Environments). Nel repository pubblico è possibile visualizzare i modelli condivisi da altri utenti che è possibile usare direttamente o personalizzare in base alle esigenze. Dopo aver creato il modello, archiviarlo nel repository per condividerlo con altri utenti. È anche possibile configurare un repository Git personale con i modelli che si usano per configurare gli ambienti nel cloud. 

Ci sono un paio di regole da seguire per organizzare i modelli di Azure Resource Manager in un repository:

- Il nome del file del modello master deve essere `azuredeploy.json`. 

    ![File principali del modello di Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Se si intende usare valori di parametro definiti in un file di parametri, il nome di tale file deve essere `azuredeploy.parameters.json`.
- È possibile usare i parametri `_artifactsLocation` e `_artifactsLocationSasToken` per costruire il valori URI parametersLink e consentire a DevTest Labs di gestire automaticamente i modelli nidificati. Per altre informazioni, vedere [How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments (Come Azure DevTest Labs semplifica le distribuzioni di modelli di Resource Manager nidificati negli ambienti di test)](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- È possibile definire metadati per specificare il nome e la descrizione visualizzati del modello. I metadati deve essere contenuti in un file denominato `metadata.json`. Il file di metadati di esempio seguente mostra come specificare il nome e la descrizione visualizzati: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

La procedura seguente consente di aggiungere un repository nel lab tramite il portale di Azure. 

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato.   
1. Nel riquadro **Panoramica** del lab selezionare **Configurazione e criteri**.

    ![Configurazione e criteri](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Nell'elenco delle impostazioni **Configuration and Policies** (Configurazione e criteri) **Repository**. Nel riquadro **Repository** sono elencati i repository aggiunti al lab. Per tutti i lab viene automaticamente generato un repository denominato `Public Repo`, che si connette al repository [DevTest Labs di GitHub](https://github.com/Azure/azure-devtestlab), che contiene diversi elementi di macchina virtuale utilizzabili.

    ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Selezionare **Aggiungi +** per aggiungere il repository di modelli di Azure Resource Manager.
1. Quando si apre il secondo riquadro **Repository**, immettere le informazioni necessarie, come indicato di seguito:
    - **Nome**: immettere il nome del repository usato nel lab.
    - **URL clone Git**: immettere l'URL del clone Git HTTPS copiato in precedenza da GitHub o da Azure DevOps Services.  
    - **Ramo**: immettere il nome del ramo per accedere alle definizioni dei modelli di Azure Resource Manager. 
    - **Token di accesso personale**: il token di accesso personale usato per accedere in modo sicuro al repository. Per ottenere il token da Azure DevOps Services, selezionare **&lt;YourName> > My profile > Security > Public access token** (NomeUtente > Profilo personale > Sicurezza > Token di accesso pubblico). Per ottenere il token da GitHub, selezionare l'avatar e quindi selezionare **Settings > Public access token** (Impostazioni > Token di accesso pubblico). 
    - **Percorsi delle cartelle**: usando uno dei due campi di input, immettere il percorso della cartella che inizia con la barra rovesciata - / - ed è relativo all'URI del clone Git delle definizioni di elemento (primo campo di input) o delle definizioni di modello di Azure Resource Manager.   
    
        ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Quando tutti i campi obbligatori sono stati immessi e hanno superato la verifica, selezionare **Salva**.

La sezione successiva illustra come creare ambienti da un modello di Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Creare un ambiente da un modello di Resource Manager usando il portale di Azure

Dopo aver configurato un repository di modelli di Azure Resource Manager nel laboratorio, gli utenti del laboratorio possono creare un ambiente usando il portale di Azure, seguendo questa procedura:

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato.   
1. Nel riquadro del lab selezionare **Aggiungi+**.
1. Nel riquadro **Scegli una base** vengono visualizzate le immagini di base che è possibile usare con i modelli di Azure Resource Manager prima elencati. Selezionare il modello di Azure Resource Manager desiderato.

    ![Scegli una base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Nel riquadro **Aggiungi** immettere un valore in **Nome ambiente**. Il nome dell'ambiente è quello visualizzato agli utenti nel laboratorio. I campi di input rimanenti vengono definiti nel modello di Azure Resource Manager. Se i valori predefiniti sono definiti nel modello o se è presente il file `azuredeploy.parameter.json`, i valori predefiniti vengono visualizzati in tali campi di input. Per i parametri di tipo *stringa protetta*, è possibile usare i segreti archiviati in Azure Key Vault. Per informazioni sul salvataggio dei segreti in un insieme di credenziali delle chiavi e sull'uso durante la creazione delle risorse di lab, vedere [Archiviare segreti in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

    ![Riquadro Aggiungi](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Esistono diversi valori di parametro che, anche se specificati, vengono visualizzati come valori vuoti. Se quindi tali valori vengono assegnati ai parametri in un modello di Azure Resource Manager, non vengono visualizzati in DevTest Labs. Vengono invece visualizzati i campi di input vuoti nell'area in cui è necessario immettere un valore quando si crea l'ambiente.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Selezionare **Aggiungi** per creare l'ambiente. L'ambiente avvia immediatamente il provisioning con lo stato visualizzato nell'elenco **Macchine virtuali**. Un nuovo gruppo di risorse viene creato automaticamente dal lab per eseguire il provisioning di tutte le risorse definite nel modello di Azure Resource Manager.
1. Dopo aver creato l'ambiente, selezionarlo nell'elenco **Macchine virtuali personali** per aprire il riquadro del gruppo di risorse e cercare tutte le risorse di cui è stato eseguito il provisioning nell'ambiente.
    
    ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   È possibile anche espandere l'ambiente per visualizzare solo l'elenco di macchine virtuali oggetto del provisioning nell'ambiente.
    
    ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Fare clic su uno degli ambienti per visualizzare le azioni disponibili, quali l'applicazione di elementi, il collegamento di dischi dati, la modifica dell'ora di arresto automatico e altro.

    ![Azioni dell'ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>Automatizzare la distribuzione degli ambienti
Azure DevTest Labs offre la possibilità di usare un [modello di gestione di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per creare un ambiente con un set di risorse nell'ambiente di laboratorio. Questi ambienti possono contenere tutte le risorse di Azure che possono essere create usando i modelli di Resource Manager. Gli ambienti DevTest Labs consentono agli utenti di distribuire facilmente infrastrutture complesse in modo coerente entro i confini del lab. Attualmente, l'aggiunta di un ambiente a un lab usando il portale di Azure è fattibile momento della creazione di una volta, ma in una soluzione di sviluppo o di una situazione di test, in cui si verificano più operazioni di creazione, una distribuzione automatizzata consente un'esperienza migliorata.

Completare i passaggi seguenti nel [configurare il proprio repository di modelli](#configure-your-own-template-repositories) sezione prima di procedere: 

1. Creare il modello di Resource Manager che definisce le risorse da creare. 
2. Consente di impostare il modello di Resource Manager in Git un repository. 
3. Connettere il repository Git al lab. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>Script di PowerShell per distribuire il modello di Resource Manager
Salvare lo script di PowerShell nella sezione successiva sul disco rigido (ad esempio: deployenv.ps1) ed eseguire lo script dopo aver specificato i valori per SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (cartella) nel repository Git, EnvironmentName.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>Script di esempio
Ecco lo script di esempio per creare un ambiente nel lab. I commenti nello script utile per comprendere meglio lo script. 

```powershell
#Requires -Module Az.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzAccount

# Select the subscription that has the lab.  
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzResource command. 
New-AzResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

È anche possibile usare CLI di Azure per distribuire le risorse con i modelli di Resource Manager. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Solo un utente con autorizzazioni di proprietario lab può creare macchine virtuali da un modello di Resource Manager usando Azure PowerShell. Se si desidera automatizzare la creazione di VM usando un modello di Resource Manager e si dispone solo delle autorizzazioni utente, è possibile usare il comando [**az lab vm create** nell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Limitazioni generali 

Quando si usa un modello di Resource Manager in DevTest Labs, tenere in considerazione le limitazioni seguenti:

- Un modello di Resource Manager non può fare riferimento alle risorse esistenti, ma solo a nuove risorse. Inoltre, se si dispone di un modello di Resource Manager esistente, in genere distribuito all'esterno di DevTest Labs e contenente riferimenti alle risorse esistenti, non è possibile usarlo nel lab.

   L'unica eccezione è **fare** riferimento a una rete virtuale esistente. 

- Non è possibile creare formule da macchine virtuali del lab create da un modello di Resource Manager. 

- Non è possibile creare immagini personalizzate da macchine virtuali del lab create da un modello di Resource Manager. 

- Non è possibile convalidare la maggior parte dei criteri quando si distribuiscono i modelli di Resource Manager.

   Ad esempio, un criterio del lab può specificare che un utente può creare solo cinque macchine virtuali. Tuttavia un utente può distribuire un modello di Resource Manager per la creazione di varie macchine virtuali. Tra i criteri che non vengono convalidati sono inclusi:

   - Numero di macchine virtuali per utente
   - Numero di macchine virtuali premium per utente del lab
   - Numero di dischi premium per utente del lab


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Configurare i diritti di accesso del gruppo di risorse dell'ambiente per gli utenti del lab

Gli utenti del lab possono distribuire un modello di Resource Manager. Tuttavia, per impostazione predefinita, dispongono di diritti di accesso in lettura. Ovvero, non possono modificare le risorse in un gruppo di risorse dell'ambiente. Ad esempio, non possono arrestare o avviare le proprie risorse.

Per concedere diritti di accesso di tipo Collaboratore, seguire questa procedura. In questo modo, quando si distribuisce un modello di Resource Manager, sarà possibile modificare le risorse nell'ambiente. 


1. Nel riquadro **Panoramica** del lab selezionare **Configurazione e criteri**.
1. Selezionare **Impostazioni lab**.
1. Nel riquadro Impostazioni lab selezionare **Collaboratore** per concedere le autorizzazioni di scrittura agli utenti del lab.

    ![Configurare i diritti di accesso per l'utente del lab](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* Dopo la creazione, è possibile connettersi alla macchina virtuale selezionando **Connetti** nel riquadro di gestione della macchina virtuale.
* Visualizzare e gestire le risorse in ambiente selezionando l'ambiente nell'elenco **My virtual machines (Macchine virtuali personali)** nel lab. 
* Esplorare i [modelli di Azure Resource Manager dalla raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).
