---
title: Distribuzione continua della configurazione dello stato di automazione di Azure con cioccolato
description: Descrive la distribuzione continua di DevOps usando la configurazione dello stato di automazione di Azure con gestione pacchetti di cioccolato. Include un esempio con il modello di Gestione risorse JSON completo e l'origine PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 0c61a431b985e494148500ed0a7aeb106534ed2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392111"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Fornire la distribuzione continua alle macchine virtuali usando la configurazione dello stato di automazione e la cioccolata

In un mondo DevOps sono disponibili molti strumenti che assistono ai vari punti della pipeline di integrazione continua. La [configurazione dello stato](automation-dsc-overview.md) di automazione di Azure è una nuova introduzione alle opzioni che i team DevOps possono usare. 

Automazione di Azure è un servizio gestito in Microsoft Azure che consente di automatizzare varie attività usando manuali operativi, nodi e risorse condivise, ad esempio credenziali, pianificazioni e variabili globali. La configurazione dello stato di automazione di Azure estende questa funzionalità di automazione per includere gli strumenti DSC (Desired state Configuration) di PowerShell. Ecco un'interessante [panoramica](automation-dsc-overview.md).

Questo articolo illustra come configurare la distribuzione continua (CD) per un computer Windows. È possibile estendere facilmente la tecnica in modo da includere tutti i computer Windows necessari per il ruolo, ad esempio un sito Web, e passare da tale posizione a ruoli aggiuntivi.

![Distribuzione continua con VM IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="at-a-high-level"></a>A livello generale

C'è molto da fare, ma Fortunatamente può essere suddiviso in due processi principali:

- Scrittura di codice e relativo test, quindi creazione e pubblicazione dei pacchetti di installazione per le versioni principale e secondaria del sistema.
- Creazione e gestione di macchine virtuali che installano ed eseguono il codice nei pacchetti.  

Una volta creati entrambi questi processi di base, è facile aggiornare automaticamente il pacchetto nelle VM quando vengono create e distribuite nuove versioni.

## <a name="component-overview"></a>Panoramica dei componenti

Gli strumenti di gestione dei pacchetti, ad esempio [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , sono ben noti nel mondo Linux, ma non molto nel mondo di Windows.
La [cioccolata](https://chocolatey.org/) è un elemento di questo tipo e il [Blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) di Scott hansely nell'argomento è un'introduzione eccezionale. In breve, Chocolate consente di usare la riga di comando per installare i pacchetti da un repository centrale in un sistema operativo Windows. È possibile creare e gestire un repository personalizzato e Chocolatey può installare pacchetti da tutti i repository designati dall'utente.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) è uno strumento di PowerShell che consente di dichiarare la configurazione desiderata per un computer. Ad esempio, se si vuole che sia installato cioccolato, viene installato IIS, la porta 80 aperta e la versione 1.0.0 del sito Web, il Configuration Manager locale DSC (Gestione configurazione locale) implementa tale configurazione. Un server di pull DSC include un repository di configurazioni per i computer. Gestione configurazione locale in ogni computer controlla periodicamente se la configurazione esistente corrisponde a quella archiviata. Può segnalare lo stato o provare a riallineare il computer con la configurazione archiviata. È possibile modificare la configurazione archiviata nel server di pull per fare in modo che il computer o un set di computer venga allineato con la configurazione modificata.

Una risorsa DSC è un modulo di codice con funzionalità specifiche, ad esempio la gestione di rete, Active Directory o SQL Server. La risorsa DSC di Chocolatey riconosce in che modo accedere, tra gli altri, a un server NuGet, scaricare pacchetti, installare pacchetti e così via. In [PowerShell Gallery](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)sono disponibili numerose altre risorse DSC. Installare questi moduli nel server di pull della configurazione dello stato di automazione di Azure per l'uso in base alle configurazioni.

Gestione risorse modelli forniscono un modo dichiarativo per la generazione dell'infrastruttura, ad esempio reti, subnet, sicurezza e routing di rete, bilanciamenti del carico, NIC, VM e così via. Ecco un [articolo](../azure-resource-manager/management/deployment-models.md) che confronta il modello di distribuzione gestione risorse (dichiarativo) con il modello di distribuzione di gestione dei servizi di Azure (ASM o classica) (imperativo). Questo articolo include una descrizione dei principali provider di risorse: calcolo, archiviazione e rete.

Una funzionalità chiave di un modello di Gestione risorse è la possibilità di installare un'estensione VM nella macchina virtuale durante il provisioning. Un'estensione di macchina virtuale ha funzionalità specifiche, ad esempio l'esecuzione di uno script personalizzato, l'installazione di software antivirus e l'esecuzione di uno script di configurazione DSC. Sono disponibili molti altri tipi di estensioni VM.

## <a name="quick-trip-around-the-diagram"></a>Descrizione rapida del diagramma

Iniziando dalla parte superiore, è possibile scrivere il codice, compilarlo, testarlo, quindi creare un pacchetto di installazione. Chocolatey può gestire diversi tipi di pacchetti di installazione, ad esempio MSI, MSU, ZIP. Ed è possibile sfruttare tutte le potenzialità di PowerShell per eseguire l'installazione effettiva se le funzionalità native di Chocolate non sono al corrente. Inserire il pacchetto in una posizione raggiungibile, ovvero un repository di pacchetti. Questo esempio di utilizzo usa una cartella pubblica in un account di archiviazione BLOB di Azure, ma può trovarsi anche in un'altra posizione. Chocolatey funziona in modalità nativa con i server NuGet e alcuni altri per la gestione dei metadati dei pacchetti. [Questo articolo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descrive le opzioni. L'esempio di utilizzo USA NuGet. Nuspec sono i metadati relativi ai pacchetti. Le informazioni NuSpec vengono compilate in un NuPkg e archiviate in un server NuGet. Quando la configurazione richiede un pacchetto in base al nome e fa riferimento a un server NuGet, la risorsa DSC di cioccolato nella macchina virtuale estrae il pacchetto e lo installa. È anche possibile richiedere una versione specifica di un pacchetto.

Nella parte inferiore sinistra dell'immagine è presente un modello di Azure Resource Manager. In questo esempio di utilizzo l'estensione della macchina virtuale registra la macchina virtuale con il server di pull della configurazione dello stato di automazione di Azure come nodo. La configurazione viene archiviata due volte nel server di pull: una volta come testo normale e una volta compilata come file MOF. Nel portale di Azure il file MOF rappresenta una configurazione di nodo, anziché una semplice configurazione. Si tratta dell'elemento associato a un nodo in modo che il nodo conosca la configurazione. I dettagli che seguono illustrano come assegnare la configurazione di nodi al nodo.

Creare il NuSpec, compilarlo e archiviarlo in un server NuGet è un piccolo elemento. e si stanno già gestendo le VM. 

Per eseguire la distribuzione continua, è necessario configurare il server di pull una volta, registrare i nodi in una sola volta e creare e archiviare la configurazione iniziale nel server. Quando i pacchetti vengono aggiornati e distribuiti nel repository, è necessario aggiornare solo la configurazione e la configurazione del nodo nel server di pull in base alle esigenze.

Se non si inizia con un modello di Gestione risorse, questo è un problema. Sono disponibili comandi di PowerShell che consentono di registrare le VM con il server di pull. Per altre informazioni, vedere Caricamento [di computer per la gestione mediante la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Informazioni sull'esempio di utilizzo

L'esempio di utilizzo in questo articolo inizia con una VM da un'immagine di Windows Server 2012 R2 generica della raccolta di Azure. È possibile iniziare da qualsiasi immagine archiviata e usarla come base per modificare la configurazione DSC.
La modifica della configurazione basata su un'immagine è tuttavia molto più complessa rispetto all'aggiornamento dinamico della configurazione mediante DSC.

Non è necessario usare un modello di Resource Manager e l'estensione VM per usare questa tecnica con le proprie VM. Inoltre, le VM non devono trovarsi necessariamente in Azure per la gestione della distribuzione continua. È solo necessario che Chocolatey sia installato e che Gestione configurazione locale sia configurato nella VM perché riconosca la posizione del server di pull.

Quando si aggiorna un pacchetto in una macchina virtuale in produzione, è necessario disattivarla dalla rotazione durante l'installazione dell'aggiornamento. La procedura per eseguire questa operazione varia ampiamente. Ad esempio, per una VM con bilanciamento del carico di Azure è possibile aggiungere un probe personalizzato. Durante l'aggiornamento della VM, l'endpoint del probe dovrà restituire un messaggio 400. La modifica necessaria per ottenere questo cambiamento può essere eseguita nella configurazione, così come può esserlo la modifica per ripristinare la restituzione di un messaggio 200 una volta completato l'aggiornamento.

Il codice sorgente completo per questo esempio di utilizzo si trova in [questo progetto di Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) su GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Passaggio 1: configurare il server di pull e l'account di automazione

Da una riga di comando di PowerShell (`Connect-AzAccount`) autenticata (la configurazione del server di pull può richiedere alcuni minuti):

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

È possibile inserire l'account di automazione in una delle aree seguenti (anche note come località): Stati Uniti orientali 2, Stati Uniti centro-meridionali, US Gov Virginia, Europa occidentale, Asia sudorientale, Giappone orientale, India centrale e Australia sudorientale, Canada centrale ed Europa settentrionale.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Passaggio 2: apportare modifiche all'estensione della macchina virtuale nel modello di Gestione risorse

I dettagli per la registrazione della VM, usando l'estensione VM di PowerShell DSC, sono disponibili in questo [modello di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Questo passaggio registra la nuova VM con il server di pull nell'elenco di nodi DSC. Parte di tale registrazione consiste nella specifica della configurazione da applicare ai nodi. Questa configurazione del nodo non deve ancora esistere nel server di pull, quindi è bene che il passaggio 4 sia il momento in cui questa operazione viene eseguita per la prima volta. In questo passaggio 2 è tuttavia necessario definire il nome del nodo e della configurazione. In questo esempio di utilizzo, il nodo è 'isvbox' e la configurazione è 'ISVBoxConfig'. Pertanto il nome della configurazione di nodi da specificare in DeploymentTemplate.json è 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Passaggio 3: aggiungere le risorse DSC necessarie al server di pull

PowerShell Gallery è instrumentata per l'installazione delle risorse DSC nell'account di Automazione di Azure.
Passare alla risorsa desiderata e fare clic sul pulsante "Distribuisci in automazione di Azure".

![Esempio di PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Un'altra tecnica aggiunta recentemente al portale di Azure consente di eseguire il pull dei nuovi moduli o aggiornare i moduli esistenti. Fare clic sulla risorsa dell'account di automazione, sul riquadro asset e infine sul riquadro moduli. L'icona Esplora raccolta consente di visualizzare l'elenco dei moduli nella raccolta, eseguire il drill-down nei dettagli e infine importare nell'account di automazione. Questo è un ottimo modo per mantenere aggiornati i moduli periodicamente. La funzionalità di importazione verifica le dipendenze con altri moduli per garantire che rimangano sincronizzati.

È anche disponibile un approccio manuale, usato solo una volta per ogni risorsa, a meno che non si desideri aggiornarlo in un secondo momento. Per altre informazioni sulla creazione di moduli di integrazione PowerShell, vedere [creazione di moduli di integrazione per automazione di Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>La struttura di cartelle di un modulo di integrazione PowerShell per un computer Windows è leggermente diversa dalla struttura di cartelle prevista da automazione di Azure. 

1. Installare [Windows Management Framework V5](https://aka.ms/wmf5latest) (non necessario per Windows 10).

2. Installare il modulo di integrazione.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copiare la cartella del modulo da **C:\Program Files\WindowsPowerShell\Modules\MODULE-Name** a una cartella temporanea.

4. Eliminare gli esempi e la documentazione dalla cartella principale.

5. Comprimere la cartella principale, assegnando un nome al file ZIP con il nome della cartella.

6. Inserire il file ZIP in un percorso HTTP raggiungibile, ad esempio l'archiviazione BLOB in un account di archiviazione di Azure.

7. Eseguire il comando seguente.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

L'esempio incluso implementa questi passaggi per cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Passaggio 4: aggiungere la configurazione del nodo al server di pull

Non è necessario eseguire operazioni speciali la prima volta che si importa la configurazione nel server di pull e si procede alla compilazione. Tutte le importazioni o compilazioni successive della stessa configurazione sono identiche. Ogni volta che si aggiorna il pacchetto ed è necessario effettuarne il push all'ambiente di produzione, si eseguirà questo passaggio dopo avere verificato che il file di configurazione è corretto, inclusa la nuova versione del pacchetto. Ecco il file di configurazione **ISVBoxConfig. ps1**:

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

Ecco lo script **New-ConfigurationScript. ps1** (modificato per usare il modulo AZ):

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

Questa procedura comporta la creazione di una nuova configurazione di nodo denominata **ISVBoxConfig. isvbox** nel server di pull. Il nome della configurazione del nodo è `configurationName.nodeName`compilato come.

## <a name="step-5-create-and-maintain-package-metadata"></a>Passaggio 5: creare e gestire i metadati del pacchetto

Per ogni pacchetto inserito nel repository del pacchetto, è necessario un NuSpec che lo descrive. Deve essere compilato e archiviato nel server NuGet. Questo processo è descritto [qui](https://docs.nuget.org/create/creating-and-publishing-a-package). 

È possibile usare **MyGet.org** come server NuGet. È possibile acquistare questo servizio, ma è uno SKU di avvio gratuito. In [NuGet](https://www.nuget.org/)sono disponibili istruzioni per l'installazione del server NuGet per i pacchetti privati.

## <a name="step-6-tie-it-all-together"></a>Passaggio 6: combinare tutto

Ogni volta che una versione supera il controllo di qualità e viene approvato per la distribuzione, viene creato il pacchetto e NuSpec e nupkg vengono aggiornati e distribuiti nel server NuGet. È necessario aggiornare anche la configurazione (passaggio 4) per concordare con il nuovo numero di versione. Deve quindi essere inviata al server di pull e compilata.

A questo punto, le macchine virtuali che dipendono da tale configurazione devono effettuare l'aggiornamento e installarlo. Ognuno di questi aggiornamenti è semplice: solo una riga o due di PowerShell. Per Azure DevOps, alcune di esse sono incapsulate in attività di compilazione che possono essere concatenate in una compilazione. Per altre informazioni dettagliate, vedere questo [articolo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery). Questo [repository GitHub](https://github.com/Microsoft/vso-agent-tasks) illustra le attività di compilazione disponibili.

## <a name="related-articles"></a>Articoli correlati
* [Panoramica di Azure Automation DSC](automation-dsc-overview.md)
* [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [configurazione dello stato di automazione di Azure](automation-dsc-overview.md).
- Per iniziare, vedere [Guida introduttiva alla configurazione dello stato di automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione delle configurazioni DSC per poterle assegnare ai nodi di destinazione, vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere [prezzi di configurazione dello stato di automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio di come usare la configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [distribuzione continua con la configurazione dello stato di automazione di Azure e cioccolato](automation-dsc-cd-chocolatey.md).
