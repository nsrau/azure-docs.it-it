---
title: Creare ambienti multi-VM e risorse PaaS con i modelliCreate multi-VM environments and PaaS resources with templates
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169643"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager

Gli ambienti Azure DevTest Labs consentono agli utenti di distribuire facilmente infrastrutture complesse in modo coerente entro i limiti del lab. È possibile usare i modelli di Azure Resource Manager per creare ambienti con set di risorse in DevTest Labs.You can use [Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md) to create environments with sets of resources in DevTest Labs. Questi ambienti possono contenere tutte le risorse di Azure che i modelli di Resource Manager possono creare.

È possibile aggiungere facilmente [una macchina virtuale (VM) alla volta](devtest-lab-add-vm.md) a un lab usando il portale di Azure.You can easily add one virtual machine (VM) at a time to a lab by using the Azure [portal](https://portal.azure.com). Tuttavia, scenari come le app Web a più livelli o una farm di SharePoint richiedono un meccanismo per creare più macchine virtuali in un unico passaggio. Usando i modelli di Azure Resource Manager, è possibile definire l'infrastruttura e la configurazione della soluzione Azure e distribuire ripetutamente più macchine virtuali in uno stato coerente.

Anche i modelli di Azure Resource Manager offrono i vantaggi seguenti:Azure Resource Manager templates also provide the following benefits:

- I modelli di Azure Resource Manager vengono caricati direttamente dal repository del controllo del codice sorgente GitHub o Azure Repos.Azure Resource Manager templates are loaded directly from your GitHub or Azure Repos source control repository.
- Gli utenti possono creare un ambiente selezionando un modello di Azure Resource Manager configurato dal portale di Azure, proprio come fanno con altri tipi di basi di [VM.](devtest-lab-comparing-vm-base-image-types.md)
- È possibile eseguire il provisioning delle risorse di Azure PaaS e delle macchine virtuali IaaS in un ambiente da un modello di Azure Resource Manager.You can provision Azure PaaS resources as well as IaaS VMs in an environment from an Azure Resource Manager template.
- È possibile tenere traccia del costo degli ambienti nel lab, oltre alle singole macchine virtuali create da altri tipi di basi. Le risorse PaaS vengono create e verranno visualizzate nella tracciabilità dei costi. Tuttavia, l'arresto automatico della macchina virtuale non si applica alle risorse PaaS.

Per altre informazioni sui vantaggi dell'uso dei modelli di Resource Manager per distribuire, aggiornare o eliminare molte risorse lab in un'unica operazione, vedere [Vantaggi dell'utilizzo dei modelli di Resource Manager.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Quando si usa un modello di Resource Manager come base per creare macchine virtuali lab, esistono alcune differenze tra la creazione di più macchine virtuali o di una singola macchina virtuale. Per altre informazioni, vedere Usare il modello di Azure Resource Manager di [una macchina virtuale.](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Usare gli ambienti pubblici DevTest LabsUse DevTest Labs public environments
Azure DevTest Labs include un [repository pubblico di modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) che è possibile usare per creare ambienti senza doversi connettere manualmente a un'origine GitHub esterna. Questo repository pubblico è simile al repository pubblico degli elementi disponibile nel portale di Azure per ogni lab creato. Il repository dell'ambiente consente di iniziare rapidamente a utilizzare modelli di ambiente creati in modo pre-autore con pochi parametri di input. Questi modelli offrono un'esperienza introduttiva semplice per le risorse PaaS all'interno dei laboratori.

Nel repository pubblico, il team di DevTest Labs e altri hanno creato e condiviso modelli usati di frequente, ad esempio App Web di Azure, Cluster di infrastruttura di servizi e un ambiente farm di SharePoint di sviluppo. È possibile utilizzare questi modelli direttamente o personalizzarli in base alle proprie esigenze. Per altre informazioni, vedere [Configurare e usare ambienti pubblici in DevTest Labs](devtest-lab-configure-use-public-environments.md). Dopo aver creato i propri modelli, è possibile archiviarli in questo repository per condividerli con altri utenti o impostare il proprio repository Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Creare repository di modelli personalizzati

Come una delle procedure consigliate con infrastructure-as-code e configuration-as-code, è necessario gestire i modelli di ambiente nel controllo del codice sorgente. Azure DevTest Labs segue questa procedura e carica tutti i modelli di Azure Resource Manager direttamente dai repository GitHub o Azure Repos. Di conseguenza, è possibile usare i modelli di Resource Manager nell'intero ciclo di rilascio, dall'ambiente di test all'ambiente di produzione.

Esistono diverse regole da seguire per organizzare i modelli di Azure Resource Manager in un repository:There are several rules to follow to organize your Azure Resource Manager templates in a repository:

- È necessario assegnare al file modello master il nome *azuredeploy.json*.

- Se si desidera utilizzare i valori dei parametri definiti in un file di parametri, il file dei parametri deve essere denominato *azuredeploy.parameters.json*.

  È possibile usare i parametri `_artifactsLocation` e `_artifactsLocationSasToken` per costruire il valori URI parametersLink e consentire a DevTest Labs di gestire automaticamente i modelli nidificati. Per altre informazioni, vedere [Distribuire modelli di Azure Resource Manager annidati per gli ambienti](deploy-nested-template-environments.md)di test.

- È possibile definire i metadati per specificare il nome visualizzato e la descrizione del modello in un file denominato *metadata.json,* come indicato di seguito:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![File principali del modello di Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Aggiungere archivi modelli al labAdd template repositories to the lab

Dopo aver creato e configurato il repository, è possibile aggiungerlo al lab usando il portale di Azure:After you create and configure your repository, you can add it to your lab by using the Azure portal:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato.
1. Nel riquadro **Panoramica** del lab selezionare **Configurazione e criteri**.

   ![Configurazione e criteri](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Nell'elenco **Impostazioni configurazione e criteri** selezionare **Repository**. Il repository **Public Artifact Repo** viene generato automaticamente per tutti i laboratori e si connette al repository pubblico GitHub di [DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Per aggiungere il repository dei modelli di Azure Resource Manager, selezionare **Aggiungi**.

   ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Nel riquadro **Repository** immettere le informazioni seguenti:

   - **Nome**: Immettere un nome di repository da utilizzare nel lab.
   - **URL clone Git:** immettere l'URL del clone HTTPS Git da GitHub o Azure Repos.
   - **Ramo** (facoltativo): immettere il nome del ramo per accedere alle definizioni di modello di Azure Resource Manager.Branch (optional): Enter the branch name to access your Azure Resource Manager template definitions.
   - **Token di accesso personale**: Immettere il token di accesso personale utilizzato per accedere in modo sicuro al repository.
     - Per ottenere il token da Azure Repos, in Profilo selezionare **Impostazioni** > utente token di**accesso personali****di sicurezza** > .
     - Per ottenere il token da GitHub, in profilo selezionare **Impostazioni** > **impostazioni impostazioni** > **personali token di accesso**.
   - **Percorsi cartella:** immettere il percorso della cartella relativo all'URI del clone Git per le definizioni degli elementi o per le definizioni di modello di Azure Resource Manager.Folder paths : Enter the folder path that is relative to your Git clone URI for either your artifact definitions or your Azure Resource Manager template definitions.

1. Selezionare **Salva**.

   ![Aggiungi nuovo repository](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Dopo aver aggiunto un modello di Azure Resource Manager al lab, gli utenti del lab possono creare ambienti usando il modello.

## <a name="configure-access-rights-for-lab-users"></a>Configurare i diritti di accesso per gli utenti del labConfigure access rights for lab users

Gli utenti del lab hanno il ruolo **Lettore** per impostazione predefinita, pertanto non possono modificare le risorse in un gruppo di risorse dell'ambiente. Ad esempio, non possono arrestare o avviare le risorse.

Per assegnare agli utenti del lab il ruolo **di collaboratore** in modo che possano modificare le risorse nei propri ambienti, attenersi alla seguente procedura:

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro **Panoramica** del lab selezionare **Configurazione e criteri**e quindi Impostazioni **lab**.

1. Nel riquadro **Impostazioni lab** selezionare **Collaboratore**e quindi **Salva** per concedere autorizzazioni di scrittura agli utenti del lab.

   ![Configurare i diritti di accesso per l'utente del lab](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

La sezione successiva illustra la creazione di ambienti da un modello di Azure Resource Manager.The next section walks through creating environments from an Azure Resource Manager template.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Creare ambienti da modelli nel portale di AzureCreate environments from templates in the Azure portal

Dopo aver aggiunto un modello di Azure Resource Manager al lab, gli utenti del lab possono creare ambienti nel portale di Azure eseguendo la procedura seguente:Once you add an Azure Resource Manager template to the lab, your lab users can create environments in the Azure portal by following these steps:

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nella pagina del lab selezionare **Aggiungi**.

1. Nel riquadro **Scegli una base** vengono visualizzate le immagini di base che è possibile usare, con i modelli di Azure Resource Manager elencati per primi. Selezionare il modello di Azure Resource Manager desiderato.

   ![Scegli una base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Nel riquadro **Aggiungi** immettere un valore **Nome ambiente** da visualizzare agli utenti dell'ambiente.

   Il modello di Azure Resource Manager definisce il resto dei campi di input. Se il file *modello azuredeploy.parameter.json* definisce i valori predefiniti, i campi di input mostrano tali valori.

   Per i parametri di tipo *stringa sicura,* è possibile usare i segreti dall'insieme di credenziali delle chiavi di Azure.For parameters of type secure string , you can use secrets from your Azure Key Vault. Per informazioni sull'archiviazione di segreti in un insieme di credenziali delle chiavi e sull'uso durante la creazione di risorse lab, vedere [Archiviare i segreti nell'insieme](devtest-lab-store-secrets-in-key-vault.md)di credenziali delle chiavi di Azure.To learn about storing secrets in a key vault and using them when creating lab resources, see Store secrets in Azure Key Vault .  

   ![Riquadro Aggiungi](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > I valori dei parametri seguenti non vengono visualizzati nei campi di input, anche se il modello li specifica. Al contrario, il modulo mostra campi di input vuoti in cui gli utenti del lab devono immettere valori durante la creazione dell'ambiente.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Selezionare **Aggiungi** per creare l'ambiente.

   L'ambiente viene avviato immediatamente il provisioning, con lo stato visualizzato nell'elenco **Macchine virtuali personali.** Il lab crea automaticamente un nuovo gruppo di risorse per eseguire il provisioning di tutte le risorse definite nel modello di Azure Resource Manager.The lab automatically creates a new resource group to provision all the resources defined in the Azure Resource Manager template.

1. Dopo aver creato l'ambiente, selezionare l'ambiente nell'elenco **Macchine virtuali** personali per aprire il riquadro del gruppo di risorse ed esplorare tutte le risorse di cui è stato eseguito il provisioning dell'ambiente.

   ![Risorse per l'ambiente](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   È anche possibile espandere l'ambiente per visualizzare solo l'elenco delle macchine virtuali di cui è stato eseguito il provisioning dell'ambiente.

   ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selezionare uno degli ambienti per visualizzare le azioni disponibili, ad esempio l'applicazione di elementi, il collegamento di dischi dati, la modifica del tempo di arresto automatico e altro ancora.

   ![Azioni dell'ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizzare la creazione dell'ambiente con PowerShellAutomate environment creation with PowerShell

È possibile usare il portale di Azure per aggiungere un singolo ambiente a un lab, ma quando uno scenario di sviluppo o test deve creare più ambienti, la distribuzione automatizzata è un'esperienza migliore.

Prima di procedere, assicurarsi di disporre di un modello di Azure Resource Manager che definisce le risorse da creare. [Aggiungere e configurare il modello in un repository Git](#configure-your-own-template-repositories)e [aggiungerlo al lab](#add-template-repositories-to-the-lab).

Lo script di esempio seguente crea un ambiente nel lab. I commenti consentono di comprendere meglio lo script.

1. Salvare lo script di PowerShell di esempio seguente nel disco rigido come *deployenv.ps1*.

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

1. Eseguire lo script come segue, usando i valori specifici per SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (cartella nel repository Git) e EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

È anche possibile usare l'interfaccia della riga di comando di Azure per distribuire le risorse con i modelli di Resource Manager.You can also use Azure CLI to deploy resources with Resource Manager templates. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Solo un utente con autorizzazioni di proprietario lab può creare macchine virtuali da un modello di Resource Manager usando Azure PowerShell. Se si vuole automatizzare la creazione di macchine virtuali usando un modello di Resource Manager e si dispone solo delle autorizzazioni utente, è possibile usare il comando CLI [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Limitazioni del modello di Resource Manager in DevTest Labs

Considerare queste limitazioni quando si usano i modelli di Resource Manager in DevTest Labs:Consider these limitations when using Resource Manager templates in DevTest Labs:

- I modelli di Resource Manager non possono fare riferimento alla maggior parte delle risorse esistenti. Possono creare solo nuove risorse. Se si dispone di modelli di Resource Manager utilizzati all'esterno di DevTest Labs che fanno riferimento a risorse esistenti, non è possibile utilizzarli in DevTest Labs. L'unica eccezione è che è possibile fare riferimento a una rete virtuale esistente.

- Non è possibile creare formule o immagini personalizzate da macchine virtuali lab create da un modello di Resource Manager.You can't create formulas or custom images from lab VMs that were created from a Resource Manager template.

- La maggior parte dei criteri non viene valutata quando si distribuiscono modelli di Resource Manager.Most policies aren't evaluated when you deploy Resource Manager templates.

  Ad esempio, si potrebbe avere un criterio lab che un utente può creare solo cinque macchine virtuali. Tuttavia un utente può distribuire un modello di Resource Manager per la creazione di varie macchine virtuali. I criteri non valutati includono:Policies that aren't evaluated include:

  - Numero di macchine virtuali per utente

  - Numero di macchine virtuali premium per utente del lab

  - Numero di dischi premium per utente del lab

## <a name="next-steps"></a>Passaggi successivi
- Dopo aver creato una macchina virtuale, è possibile connettersi alla macchina virtuale selezionando **Connetti** nel riquadro di gestione della macchina virtuale.
- Visualizzare e gestire le risorse in ambiente selezionando l'ambiente nell'elenco **My virtual machines (Macchine virtuali personali)** nel lab.
- Esplorare i modelli di Azure Resource Manager dalla raccolta di [modelli di Avvio rapido](https://github.com/Azure/azure-quickstart-templates)di Azure.
