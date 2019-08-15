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
ms.date: 08/07/2019
ms.author: spelluru
ms.openlocfilehash: 51c699f9b392be5f2e2bc16b5729d6567ace7f17
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016180"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager

Gli ambienti Azure DevTest Labs consentono agli utenti di distribuire prontamente infrastrutture complesse in modo coerente entro i confini del Lab. È possibile usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per creare ambienti con set di risorse in DevTest Labs. Questi ambienti possono contenere tutte le risorse di Azure che possono essere create da Gestione risorse modelli. 

È possibile [aggiungere facilmente una macchina virtuale (VM) alla volta](devtest-lab-add-vm.md) a un Lab usando il [portale di Azure](https://portal.azure.com). Tuttavia, scenari come app Web a più livelli o una farm di SharePoint necessitano di un meccanismo per creare più macchine virtuali in un unico passaggio. Usando i modelli di Azure Resource Manager, è possibile definire l'infrastruttura e la configurazione della soluzione di Azure e distribuire ripetutamente più macchine virtuali in uno stato coerente. 

I modelli di Azure Resource Manager offrono inoltre i vantaggi seguenti:

- I modelli di Azure Resource Manager vengono caricati direttamente dal repository GitHub o Azure Repos controllo del codice sorgente.
- Gli utenti possono creare un ambiente selezionando un modello di Azure Resource Manager configurato dall'portale di Azure, come avviene con altri tipi di basi di [VM](devtest-lab-comparing-vm-base-image-types.md).
- È possibile eseguire il provisioning di risorse PaaS di Azure e di macchine virtuali IaaS in un ambiente da un modello di Azure Resource Manager.
- È possibile tenere traccia del costo degli ambienti nel Lab, oltre alle singole macchine virtuali create da altri tipi di basi. Le risorse di PaaS vengono create e verranno visualizzate nella verifica dei costi. Tuttavia, l'arresto automatico della macchina virtuale non si applica alle risorse PaaS.

Per ulteriori informazioni sui vantaggi derivanti dall'utilizzo di modelli di Gestione risorse per distribuire, aggiornare o eliminare molte risorse lab in un'unica operazione, vedere l'articolo relativo ai [vantaggi dell'utilizzo di modelli gestione risorse](../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Quando si usa un modello di Gestione risorse come base per creare macchine virtuali del Lab, esistono alcune differenze tra la creazione di più macchine virtuali o una singola macchina virtuale. Per altre informazioni, vedere [usare il modello di Azure Resource Manager di una macchina virtuale](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Usare gli ambienti pubblici di DevTest Labs
Azure DevTest Labs dispone [di un repository pubblico di modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) che è possibile usare per creare ambienti senza dover connettersi a un'origine GitHub esterna. Questo repository pubblico è simile al repository pubblico degli elementi disponibili nella portale di Azure per ogni Lab creato. Il repository dell'ambiente consente di iniziare rapidamente a usare modelli di ambiente precreati con pochi parametri di input. Questi modelli offrono un'esperienza introduttiva semplice per le risorse PaaS all'interno dei Lab. 

Nel repository pubblico, il team di DevTest Labs e altri hanno creato e condiviso modelli usati di frequente come app Web di Azure, Service Fabric cluster e un ambiente di farm di SharePoint di sviluppo. Questi modelli possono essere usati direttamente o personalizzati in base alle proprie esigenze. Per altre informazioni, vedere [Configurare e usare ambienti pubblici in DevTest Labs](devtest-lab-configure-use-public-environments.md). Dopo aver creato i modelli personalizzati, è possibile archiviarli in questo repository per condividerli con altri utenti o impostare un repository git personalizzato.

<a name="configure-your-own-template-repositories"></a> 
## <a name="create-your-own-template-repositories"></a>Creare repository di modelli personalizzati

Come una delle procedure consigliate per l'infrastruttura come il codice e la configurazione come codice, è necessario gestire i modelli di ambiente nel controllo del codice sorgente. Azure DevTest Labs segue questa procedura e carica tutti i modelli di Azure Resource Manager direttamente dai repository GitHub o Azure Repos. Di conseguenza, è possibile usare Gestione risorse modelli nell'intero ciclo di rilascio, dall'ambiente di test all'ambiente di produzione.

Esistono diverse regole da seguire per organizzare i modelli di Azure Resource Manager in un repository:

- È necessario denominare il file del modello master *file azuredeploy. JSON*. 
  
- Se si vuole usare i valori dei parametri definiti in un file di parametri, il file dei parametri deve essere denominato *file azuredeploy. Parameters. JSON*.
  
  È possibile usare i parametri `_artifactsLocation` e `_artifactsLocationSasToken` per costruire il valori URI parametersLink e consentire a DevTest Labs di gestire automaticamente i modelli nidificati. Per altre informazioni, vedere [distribuire modelli annidati di Azure Resource Manager per ambienti di testing](deploy-nested-template-environments.md).
  
- È possibile definire i metadati per specificare il nome visualizzato e la descrizione del modello in un file denominato *Metadata. JSON*, come indicato di seguito:
  
  ```json
  { 
    "itemDisplayName": "<your template name>", 
    "description": "<description of the template>" 
  }
  ```

![File principali del modello di Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Aggiungere repository di modelli al Lab

Dopo aver creato e configurato il repository, è possibile aggiungerlo al Lab usando il portale di Azure: 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Dall'elenco dei Lab selezionare il Lab desiderato. 
1. Nel riquadro **Panoramica** del Lab selezionare **configurazione e criteri**.
   
   ![Configurazione e criteri](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)
   
1. Nell'elenco impostazioni di **configurazione e criteri** selezionare **repository**. Il repository del repository di **artefatti pubblico** viene generato automaticamente per tutti i Lab e si connette al [repository GitHub pubblico di DevTest Labs](https://github.com/Azure/azure-devtestlab).
   
1. Per aggiungere il repository del modello di Azure Resource Manager, selezionare **Aggiungi**.
   
   ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/public-repo.png)
   
1. Nel riquadro **repository** immettere le informazioni seguenti:
   
   - **Nome**: Immettere un nome di repository da usare nel Lab.
   - **URL clone git**: Immettere l'URL del clone di git HTTPS da GitHub o Azure Repos. 
   - **Ramo** di (facoltativo): Immettere il nome del ramo per accedere alle definizioni del modello di Azure Resource Manager.
   - **Token di accesso personale**: Immettere il token di accesso personale usato per accedere in modo sicuro al repository.
     - Per ottenere il token da Azure Repos, nel profilo selezionare **Impostazioni** > utente**sicurezza** > **token di accesso personale**.
     - Per ottenere il token da GitHub, nel profilo selezionare **Impostazioni** > **sviluppatore impostazioni** > per i**token di accesso personali**.
   - **Percorsi cartella**: Immettere il percorso della cartella relativo all'URI del clone git per le definizioni di artefatto o per le definizioni di modello di Azure Resource Manager. 
   
1. Selezionare **Salva**.
   
   ![Aggiungi nuovo repository](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Una volta aggiunto un modello di Azure Resource Manager al Lab, gli utenti del Lab possono creare gli ambienti usando il modello. 

## <a name="configure-access-rights-for-lab-users"></a>Configurare i diritti di accesso per gli utenti del Lab

Per impostazione predefinita, gli utenti del Lab hanno il ruolo **lettore** , quindi non possono modificare le risorse in un gruppo di risorse dell'ambiente. Ad esempio, non possono arrestare o avviare le risorse. 

Per assegnare il ruolo **collaboratore** agli utenti del Lab in modo che possano modificare le risorse nei rispettivi ambienti, attenersi alla procedura seguente:

1. Nella finestra di [portale di Azure](https://portal.azure.com), nel riquadro **Panoramica** del Lab selezionare **configurazione e criteri**e quindi selezionare **impostazioni Lab**.
   
1. Nel riquadro **impostazioni Lab** selezionare collaboratoree quindi fare clic su **Salva** per concedere autorizzazioni di scrittura agli utenti del Lab.
   
   ![Configurare i diritti di accesso per l'utente del lab](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Nella sezione successiva vengono illustrati i passaggi per la creazione di ambienti da un modello di Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Creare ambienti da modelli nel portale di Azure

Una volta aggiunto un modello di Azure Resource Manager al Lab, gli utenti del Lab possono creare ambienti nel portale di Azure attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
   
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
   
1. Dall'elenco dei Lab selezionare il Lab desiderato. 
   
1. Nella pagina del Lab selezionare **Aggiungi**.
   
1. Nel riquadro **Scegli una base** vengono visualizzate le immagini di base che è possibile utilizzare con i modelli Azure Resource Manager elencati per primi. Selezionare il modello di Azure Resource Manager desiderato.
   
   ![Scegli una base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
   
1. Nel riquadro **Aggiungi** immettere un valore per **nome ambiente** da visualizzare agli utenti dell'ambiente. 
   
   Il modello di Azure Resource Manager definisce il resto dei campi di input. Se il file del modello *file azuredeploy. Parameter. JSON* definisce i valori predefiniti, i campi di input mostrano tali valori. 
   
   Per i parametri di tipo *Secure String*, è possibile usare i segreti del Azure Key Vault. Per informazioni su come archiviare i segreti in un insieme di credenziali delle chiavi e usarli durante la creazione di risorse Lab, vedere [archiviare i segreti in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  
   
   ![Riquadro Aggiungi](./media/devtest-lab-create-environment-from-arm/add.png)
   
   > [!NOTE]
   > I valori dei parametri seguenti non vengono visualizzati nei campi di input, anche se specificati dal modello. Al contrario, il form Mostra campi di input vuoti in cui gli utenti del lab devono immettere i valori durante la creazione dell'ambiente.
   > 
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD 
   
1. Selezionare **Aggiungi** per creare l'ambiente. 
   
   L'ambiente avvia il provisioning immediatamente, con lo stato visualizzato nell'elenco **macchine virtuali personali** . Il Lab crea automaticamente un nuovo gruppo di risorse per il provisioning di tutte le risorse definite nel modello di Azure Resource Manager.
   
1. Dopo aver creato l'ambiente, selezionare l'ambiente nell'elenco **macchine virtuali personali** per aprire il riquadro del gruppo di risorse ed esplorare tutte le risorse di cui è stato effettuato il provisioning nell'ambiente.
   
   ![Risorse dell'ambiente](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   È anche possibile espandere l'ambiente per visualizzare solo l'elenco di macchine virtuali di cui è stato effettuato il provisioning nell'ambiente.
   
   ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)
   
1. Selezionare uno degli ambienti per visualizzare le azioni disponibili, ad esempio l'applicazione di elementi, il fissaggio di dischi dati, la modifica dell'ora di arresto automatico e altro ancora.
   
   ![Azioni dell'ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a> 
## <a name="automate-environment-creation-with-powershell"></a>Automatizzare la creazione dell'ambiente con PowerShell

È possibile utilizzare il portale di Azure per aggiungere un singolo ambiente a un Lab, ma quando uno scenario di sviluppo o test deve creare più ambienti, la distribuzione automatizzata è un'esperienza migliore. 

Prima di procedere, assicurarsi di disporre di un modello di Azure Resource Manager che definisce le risorse da creare. [Aggiungere e configurare il modello in un repository git](#configure-your-own-template-repositories)e [aggiungere il repository al Lab](#add-template-repositories-to-the-lab).

Lo script di esempio seguente crea un ambiente nel Lab. I commenti consentono di comprendere meglio lo script. 

1. Salvare il seguente script di PowerShell di esempio nel disco rigido come *deployenv. ps1*. 
  
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   ```powershell
   #Requires -Module Az.Resources
   
   [CmdletBinding()]
   
   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,
   
   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,
   
   # Name of the connected repository in the lab 
   [string] [Parameter(Mandatory=$true)] $RepositoryName,
   
   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,
   
   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,
   
   # The parameters to be passed to the template. Each parameter is prefixed with "-param_". 
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName", 
   # the string in $Params will have the form: -param_TestVMName MyVMName. 
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )
   
   # Sign in to Azure. 
   # Comment out the following statement to completely automate the environment creation. 
   Connect-AzAccount
   
   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
   
   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
           
   # Get information about the lab, such as lab location. 
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
   
   # Get information about the Resource Manager template base for the environment. 
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
   
   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }
   
   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
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
   
1. Eseguire lo script come segue, usando i valori specifici per SubscriptionId, LabName, ResourceGroupName, RepositoryName, templateName (cartella nel repository git) e EnvironmentName.
   
   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv" 
   ```

È anche possibile usare l'interfaccia della riga di comando di Azure per distribuire le risorse con i modelli Gestione risorse. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

> [!NOTE]
> Solo un utente con autorizzazioni di proprietario lab può creare macchine virtuali da un modello di Resource Manager usando Azure PowerShell. Se si vuole automatizzare la creazione di VM usando un modello di Gestione risorse e si dispone solo delle autorizzazioni utente, è possibile usare il comando dell'interfaccia della riga di comando [AZ Lab VM create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Limitazioni del modello di Gestione risorse in DevTest Labs 

Prendere in considerazione queste limitazioni quando si usano modelli Gestione risorse in DevTest Labs:

- I modelli di Gestione risorse non possono fare riferimento alla maggior parte delle risorse esistenti. Possono creare solo nuove risorse. Se sono presenti Gestione risorse modelli usati al di fuori di DevTest Labs che fanno riferimento a risorse esistenti, non è possibile usarli in DevTest Labs. L'unica eccezione è che è possibile fare riferimento a una rete virtuale esistente. 
  
- Non è possibile creare formule o immagini personalizzate da macchine virtuali del Lab create da un modello di Gestione risorse. 
  
- La maggior parte dei criteri non viene valutata quando si distribuiscono Gestione risorse modelli.
  
  Ad esempio, si potrebbe avere un criterio Lab che un utente può creare solo cinque macchine virtuali. Tuttavia un utente può distribuire un modello di Resource Manager per la creazione di varie macchine virtuali. I criteri che non vengono valutati includono:
  
  - Numero di macchine virtuali per utente
    
  - Numero di macchine virtuali premium per utente del lab
    
  - Numero di dischi premium per utente del lab

## <a name="next-steps"></a>Passaggi successivi
- Dopo aver creato una macchina virtuale, è possibile connettersi alla macchina virtuale selezionando **Connetti** nel riquadro di gestione della macchina virtuale.
- Visualizzare e gestire le risorse in ambiente selezionando l'ambiente nell'elenco **My virtual machines (Macchine virtuali personali)** nel lab. 
- Esplorare i [modelli di Azure Resource Manager dalla raccolta di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).
