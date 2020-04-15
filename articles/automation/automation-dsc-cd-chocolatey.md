---
title: Azure Automation State Configuration continuous deployment with Chocolatey
description: Descrive la distribuzione continua DevOps usando la configurazione dello stato di Automazione di Azure con il gestore di pacchetti Chocolatey.Describes DevOps continuous deployment using Azure Automation state configuration with the Chocolatey package manager. Include un esempio con modello JSON Resource Manager completo e origine PowerShell.Includes an example with full JSON Resource Manager template and PowerShell source.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 706ab128af4379a56223ff65fb12f29d37b524f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383267"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Fornire una distribuzione continua alle macchine virtuali usando la configurazione dello stato di automazione e ChocolateyProvide continuous deployment to virtual machines using Automation State Configuration and Chocolatey

In un mondo DevOps, sono disponibili molti strumenti per facilitare la gestione di vari punti nella pipeline di integrazione continua. Azure Automation [State Configuration](automation-dsc-overview.md) è una nuova aggiunta gradita alle opzioni che i team DevOps possono usare. 

Automazione di Azure è un servizio gestito in Microsoft Azure che consente di automatizzare varie attività usando runbook, nodi e risorse condivise, ad esempio credenziali, pianificazioni e variabili globali. Azure Automation State Configuration estende questa funzionalità di automazione per includere gli strumenti DSC (Desired State Configuration) di PowerShell.Azure Automation State Configuration extends this automation capability to include PowerShell Desired State Configuration (DSC) tools. Ecco un'interessante [panoramica](automation-dsc-overview.md).

In questo articolo viene illustrato come configurare la distribuzione continua (CD) per un computer Windows. È possibile estendere facilmente la tecnica per includere tutti i computer Windows necessari nel ruolo, ad esempio, un sito Web e passare da lì a ruoli aggiuntivi.

![Distribuzione continua con VM IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="at-a-high-level"></a>A livello generale

C'è un bel po 'in corso qui, ma per fortuna può essere suddiviso in due processi principali:

- Scrittura di codice e relativo test, quindi creazione e pubblicazione dei pacchetti di installazione per le versioni principale e secondaria del sistema.
- Creazione e gestione di macchine virtuali che installano ed eseguono il codice nei pacchetti.  

Una volta creati entrambi questi processi principali, è facile aggiornare automaticamente il pacchetto nelle macchine virtuali man mano che vengono create e distribuite nuove versioni.

## <a name="component-overview"></a>Panoramica dei componenti

I gestori di pacchetti come [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) sono ben noti nel mondo Linux, ma non tanto nel mondo di Windows.
[Chocolatey](https://chocolatey.org/) è una cosa del genere, e il [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) di Scott Hanselman sull'argomento è una grande introduzione. In poche parole, Chocolatey consente di utilizzare la riga di comando per installare i pacchetti da un repository centrale su un sistema operativo Windows. È possibile creare e gestire un repository personalizzato e Chocolatey può installare pacchetti da tutti i repository designati dall'utente.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) è uno strumento di PowerShell che consente di dichiarare la configurazione desiderata per un computer. Ad esempio, se si desidera che Chocolatey installato, IIS installato, porta 80 aperta e versione 1.0.0 del sito Web installato, Gestione configurazione locale DSC implementa tale configurazione. Un server di pull DSC contiene un repository di configurazioni per i computer. Gestione configurazione locale in ogni computer controlla periodicamente se la configurazione esistente corrisponde a quella archiviata. Può segnalare lo stato o provare a riallineare il computer con la configurazione archiviata. È possibile modificare la configurazione archiviata nel server di pull per fare in modo che il computer o un set di computer venga allineato con la configurazione modificata.

A DSC resource is a module of code that has specific capabilities, such as managing networking, Active Directory, or SQL Server. La risorsa DSC di Chocolatey riconosce in che modo accedere, tra gli altri, a un server NuGet, scaricare pacchetti, installare pacchetti e così via. In [PowerShell Gallery](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)sono disponibili numerose altre risorse DSC. Questi moduli vengono installati nel server di pull della configurazione dello stato di automazione di Azure per l'uso da parte delle configurazioni.

I modelli di Resource Manager forniscono un modo dichiarativo per generare l'infrastruttura, ad esempio reti, subnet, sicurezza e routing di rete, servizi di bilanciamento del carico, schede di interfaccia di rete, macchine virtuali e così via. Ecco un [articolo](../azure-resource-manager/management/deployment-models.md) che confronta il modello di distribuzione di Resource Manager (dichiarativo) con il modello di distribuzione di Azure Service Management (ASM o classico) (imperativo). Questo articolo include una descrizione dei provider di risorse di base: calcolo, archiviazione e rete.

Una caratteristica chiave di un modello di Resource Manager è la possibilità di installare un'estensione di macchina virtuale nella macchina virtuale durante il provisioning. Un'estensione di macchina virtuale dispone di funzionalità specifiche, ad esempio l'esecuzione di uno script personalizzato, l'installazione di software antivirus e l'esecuzione di uno script di configurazione DSC. Sono disponibili molti altri tipi di estensioni VM.

## <a name="quick-trip-around-the-diagram"></a>Descrizione rapida del diagramma

Partendo dall'alto, si scrive il codice, lo si compila, lo si testa, quindi si crea un pacchetto di installazione. Chocolatey può gestire diversi tipi di pacchetti di installazione, ad esempio MSI, MSU, ZIP. E si ha tutta la potenza di PowerShell per fare l'installazione effettiva se le funzionalità native di Chocolatey non sono all'ora. Mettere il pacchetto in un luogo raggiungibile - un repository di pacchetti. Questo esempio di utilizzo usa una cartella pubblica in un account di archiviazione BLOB di Azure, ma può trovarsi anche in un'altra posizione. Chocolatey funziona in modalità nativa con i server NuGet e alcuni altri per la gestione dei metadati dei pacchetti. [Questo articolo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descrive le opzioni. Nell'esempio di utilizzo viene utilizzato NuGet.The usage example uses NuGet. Nuspec sono i metadati relativi ai pacchetti. Le informazioni Nuspec vengono compilate in un NuPkg e archiviate in un server NuGet.The Nuspec information is compiled into a NuPkg and stored on a NuGet server. Quando la configurazione richiede un pacchetto in base al nome e fa riferimento a un server NuGet, la risorsa DSC Chocolatey nella macchina virtuale acquisisce il pacchetto e lo installa. È anche possibile richiedere una versione specifica di un pacchetto.

Nella parte inferiore sinistra dell'immagine è presente un modello di Azure Resource Manager.In the bottom left of the picture, there's an Azure Resource Manager template. In questo esempio di utilizzo, l'estensione della macchina virtuale registra la macchina virtuale con il server di pull della configurazione dello stato di automazione di Azure come nodo. La configurazione viene archiviata due volte nel server di pull: una volta come testo normale e una volta compilata come file MOF. Nel portale di Azure il file MOF rappresenta una configurazione di nodo, anziché una configurazione semplice. È l'elemento che è associato a un nodo in modo che il nodo ne conosce la configurazione. I dettagli che seguono illustrano come assegnare la configurazione di nodi al nodo.

Creare il Nuspec, compilarlo e archiviarlo in un server NuGet è una piccola cosa. e si stanno già gestendo le VM. 

Per eseguire il passaggio successivo alla distribuzione continua, è necessario configurare il server di pull una sola volta, registrare i nodi con esso una sola volta e creare e archiviare la configurazione iniziale nel server. Quando i pacchetti vengono aggiornati e distribuiti nel repository, è sufficiente aggiornare la configurazione e la configurazione del nodo nel server di pull in base alle esigenze.

Se non si inizia con un modello di Resource Manager, va bene. Sono disponibili comandi di PowerShell che consentono di registrare le macchine virtuali con il server di pull. Per altre informazioni, vedere [Onboarding di macchine per la gestione in base alla configurazione dello stato](automation-dsc-onboarding.md)di automazione di Azure.For more information, see Onboarding machines for management by Azure Automation State Configuration .

## <a name="about-the-usage-example"></a>Informazioni sull'esempio di utilizzo

L'esempio di utilizzo in questo articolo inizia con una macchina virtuale da un'immagine generica di Windows Server 2012 R2 dalla raccolta di Azure.The usage example in this article starts with a VM from a generic Windows Server 2012 R2 image from the Azure gallery. È possibile iniziare da qualsiasi immagine archiviata e usarla come base per modificare la configurazione DSC.
La modifica della configurazione basata su un'immagine è tuttavia molto più complessa rispetto all'aggiornamento dinamico della configurazione mediante DSC.

Non è necessario usare un modello di Resource Manager e l'estensione VM per usare questa tecnica con le proprie VM. Inoltre, le VM non devono trovarsi necessariamente in Azure per la gestione della distribuzione continua. È solo necessario che Chocolatey sia installato e che Gestione configurazione locale sia configurato nella VM perché riconosca la posizione del server di pull.

Quando si aggiorna un pacchetto in una macchina virtuale in produzione, è necessario disconnetterla durante l'installazione dell'aggiornamento. La procedura per eseguire questa operazione varia ampiamente. Ad esempio, per una VM con bilanciamento del carico di Azure è possibile aggiungere un probe personalizzato. Durante l'aggiornamento della VM, l'endpoint del probe dovrà restituire un messaggio 400. La modifica necessaria per ottenere questo cambiamento può essere eseguita nella configurazione, così come può esserlo la modifica per ripristinare la restituzione di un messaggio 200 una volta completato l'aggiornamento.

Il codice sorgente completo per questo esempio di utilizzo si trova in [questo progetto di Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) su GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Passaggio 1: Configurare il server di pull e l'account di automazioneStep 1: Set up the pull server and Automation account

Da una riga di comando di PowerShell (`Connect-AzAccount`) autenticata (la configurazione del server di pull può richiedere alcuni minuti):

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Puoi inserire il tuo account Automation in una delle seguenti aree geografiche (note anche come località): Stati Uniti orientali 2, Stati Uniti centro-meridionali, Stati Uniti , Europa occidentale, Asia sudorientale, Giappone orientale, India centrale e Australia a sud-est, Canada centrale, Europa settentrionale.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Passaggio 2: Apportare modifiche alle modifiche dell'estensione della macchina virtuale al modello di Resource ManagerStep 2: Make VM extension tweaks to the Resource Manager template

I dettagli per la registrazione della VM, usando l'estensione VM di PowerShell DSC, sono disponibili in questo [modello di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Questo passaggio registra la nuova VM con il server di pull nell'elenco di nodi DSC. Parte di tale registrazione consiste nella specifica della configurazione da applicare ai nodi. Questa configurazione del nodo non deve ancora esistere nel server di pull, quindi va bene che il passaggio 4 è dove questa operazione viene eseguita per la prima volta. In questo passaggio 2 è tuttavia necessario definire il nome del nodo e della configurazione. In questo esempio di utilizzo, il nodo è 'isvbox' e la configurazione è 'ISVBoxConfig'. Pertanto il nome della configurazione di nodi da specificare in DeploymentTemplate.json è 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Passaggio 3: Aggiungere le risorse DSC necessarie al server di pullStep 3: Add required DSC resources to the pull server

PowerShell Gallery è instrumentata per l'installazione delle risorse DSC nell'account di Automazione di Azure.
Passare alla risorsa desiderata e fare clic sul pulsante "Distribuisci in automazione di Azure".

![Esempio di PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Un'altra tecnica aggiunta di recente al portale di Azure consente di estrarre nuovi moduli o aggiornare i moduli esistenti. Fare clic sulla risorsa account di automazione, sul riquadro Asset e infine nel riquadro Moduli. L'icona Sfoglia galleria consente di visualizzare l'elenco dei moduli nella raccolta, eseguire il drill-down nei dettagli e infine importare nell'account di automazione. Questo è un ottimo modo per mantenere aggiornati i moduli periodicamente. La funzionalità di importazione verifica le dipendenze con altri moduli per garantire che rimangano sincronizzati.

In alternativa, è disponibile l'approccio manuale. Questo approccio viene usato una sola volta per risorsa, a meno che non si desideri aggiornarlo in un secondo momento. Per altre informazioni sulla creazione di moduli di integrazione di PowerShell, vedere Creazione di moduli di [integrazione per l'automazione](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)di Azure.For more information on authoring PowerShell integration modules, see Authoring Integration Modules for Azure Automation.

>[!NOTE]
>The folder structure of a PowerShell integration module for a Windows computer is a little different from the folder structure expected by the Azure Automation. 

1. Installare [Windows Management Framework v5](https://aka.ms/wmf5latest) (non necessario per Windows 10).

2. Installare il modulo di integrazione.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copiare la cartella del modulo da c: , **Programmi , WindowsPowerShell , Moduli , MODULE-NAME** in una cartella temporanea.

4. Eliminare campioni e documentazione dalla cartella principale.

5. Comprimere la cartella principale, assegnando al file zip il nome della cartella.

6. Inserire il file zip in un percorso HTTP raggiungibile, ad esempio l'archiviazione BLOB in un account di archiviazione di Azure.Put the zip file into a reachable HTTP location, such as blob storage in an Azure Storage account.

7. Eseguire il comando seguente.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

L'esempio incluso implementa questi passaggi per cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Passaggio 4: Aggiungere la configurazione del nodo al server di pullStep 4: Add the node configuration to the pull server

Non è necessario eseguire operazioni speciali la prima volta che si importa la configurazione nel server di pull e si procede alla compilazione. Tutte le importazioni o le compilazioni successive della stessa configurazione hanno esattamente lo stesso aspetto. Ogni volta che si aggiorna il pacchetto ed è necessario effettuarne il push all'ambiente di produzione, si eseguirà questo passaggio dopo avere verificato che il file di configurazione è corretto, inclusa la nuova versione del pacchetto. Di seguito è riportato il file di configurazione **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Di seguito è riportato lo script **New-ConfigurationScript.ps1** (modificato per utilizzare il modulo Az):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Questi passaggi comportano l'inserimento di una nuova configurazione di nodo denominata **ISVBoxConfig.isvbox** nel server di pull. Il nome della configurazione `configurationName.nodeName`del nodo viene compilato come .

## <a name="step-5-create-and-maintain-package-metadata"></a>Passaggio 5: Creare e gestire i metadati del pacchettoStep 5: Create and maintain package metadata

Per ogni pacchetto inserito nel repository di pacchetti, è necessario un Nuspec che lo descriva.
Questo Nuspec deve essere compilato e archiviato nel server NuGet. Questo processo è descritto [qui](https://docs.nuget.org/create/creating-and-publishing-a-package). È possibile usare MyGet.org come server NuGet. Il servizio è a pagamento, ma viene offerto gratuitamente uno SKU di avvio. In NuGet.org, troverai le istruzioni per installare il tuo server NuGet per i tuoi pacchetti privati.

## <a name="step-6-tie-it-all-together"></a>Fase 6: Legare tutto insieme

Ogni volta che una versione supera il QA e viene approvata per la distribuzione, il pacchetto viene creato e nuspec e nupkg vengono aggiornati e distribuiti al server NuGet. Anche la configurazione (passaggio 4 precedente) deve essere aggiornata per concordare con il nuovo numero di versione. Deve quindi essere inviato al server di pull e compilato.

A questo punto, le macchine virtuali che dipendono da tale configurazione devono effettuare l'aggiornamento e installarlo. Ognuno di questi aggiornamenti è semplice: solo una riga o due di PowerShell.Each of these updates is simple - just a line or two of PowerShell. Per DevOps di Azure, alcuni di essi sono incapsulati in attività di compilazione che possono essere concatenate in una compilazione. Per altre informazioni dettagliate, vedere questo [articolo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery). Questo [repository GitHub](https://github.com/Microsoft/vso-agent-tasks) descrive in dettaglio le attività di compilazione disponibili.

## <a name="related-articles"></a>Articoli correlati
* [Panoramica della piattaforma DSC di Automazione di Azure](automation-dsc-overview.md)
* [Cmdlet di Automation DSC per Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere Configurazione dello stato di [automazione](automation-dsc-overview.md)di Azure .
- Per iniziare, vedere Introduzione alla configurazione dello stato di automazione di [Azure.](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compilazioning configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua tramite Configurazione dello stato](automation-dsc-cd-chocolatey.md)di automazione di Azure e Chocolatey .
