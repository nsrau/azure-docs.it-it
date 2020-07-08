---
title: Configurare la distribuzione continua di Automazione di Azure con Chocolatey
description: Questo articolo descrive come configurare la distribuzione continua con State Configuration e lo strumento di gestione pacchetti Chocolatey.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 1bab503004876a2680286204de28631ce26b9069
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84197112"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Configurare la distribuzione continua con Chocolatey

In un ambiente DevOps sono disponibili molti strumenti utili in diversi punti della pipeline di integrazione continua. [State Configuration](automation-dsc-overview.md) per Automazione di Azure è una nuova aggiunta alle opzioni disponibili per i team DevOps. 

Automazione di Azure è un servizio gestito di Microsoft Azure che consente di automatizzare diverse attività tramite runbook, nodi e risorse condivise, ad esempio credenziali, pianificazioni e variabili globali. State Configuration per Automazione di Azure estende questa funzionalità di automazione includendo strumenti Desired State Configuration (DSC) di PowerShell. Ecco un'interessante [panoramica](automation-dsc-overview.md).

Questo articolo illustra come configurare la distribuzione continua per un computer Windows. La tecnica può essere facilmente estesa per includere nel ruolo, ad esempio un sito Web, tutti i computer Windows necessari, estendendola quindi anche ad altri ruoli.

![Distribuzione continua con VM IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>A livello generale

Gli aspetti da considerare sono molteplici, ma possono comunque essere suddivisi in due processi principali:

- Scrittura di codice e relativo test, quindi creazione e pubblicazione dei pacchetti di installazione per le versioni principale e secondaria del sistema.
- Creazione e gestione delle VM che installeranno ed eseguiranno il codice nei pacchetti.  

Dopo l'implementazione di entrambi questi processi di base, si potrà aggiornare automaticamente il pacchetto in qualsiasi VM specifica, man mano che vengono create e distribuite nuove versioni.

## <a name="component-overview"></a>Panoramica dei componenti

Gli strumenti di gestione dei pacchetti, ad esempio [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool), sono ben noti nell'ambiente Linux, ma non lo sono altrettanto nell'ambiente Windows.
[Chocolatey](https://chocolatey.org/) è uno di questi e il [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) di Scott Hanselman sull'argomento costituisce un'introduzione molto interessante. In breve, Chocolatey consente di installare pacchetti da un repository centrale in un sistema Windows usando la riga di comando. È possibile creare e gestire un repository personalizzato e Chocolatey può installare pacchetti da tutti i repository designati dall'utente.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) è uno strumento di PowerShell che consente di dichiarare la configurazione che si vuole applicare a un computer. Se si vuole ad esempio installare Chocolatey, installare IIS, aprire la porta 80 e installare la versione 1.0.0 di un sito Web, Gestione configurazione locale di DSC consente di implementare tale configurazione. Un server di pull DSC mantiene un repository di configurazioni per i computer dell'utente. Gestione configurazione locale in ogni computer controlla periodicamente se la configurazione esistente corrisponde a quella archiviata. Può segnalare lo stato o provare a riallineare il computer con la configurazione archiviata. È possibile modificare la configurazione archiviata nel server di pull per fare in modo che il computer o un set di computer venga allineato con la configurazione modificata.

Una risorsa DSC è un modulo di codice con funzionalità specifiche, ad esempio per la gestione della rete, di Active Directory o SQL Server. La risorsa DSC di Chocolatey riconosce in che modo accedere, tra gli altri, a un server NuGet, scaricare pacchetti, installare pacchetti e così via. In [PowerShell Gallery](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)sono disponibili numerose altre risorse DSC. Questi moduli vengono installati dall'utente nel server di pull di State Configuration di Automazione di Azure per poter essere usati nelle configurazioni personalizzate.

I modelli di Resource Manager offrono un modo dichiarativo per la generazione dell'infrastruttura, ad esempio reti, subnet, routing e sicurezza di rete, servizi di bilanciamento del carico, NIC, VM e così via. Ecco un [articolo](../azure-resource-manager/management/deployment-models.md) che confronta il modello di distribuzione di Resource Manager (dichiarativo) con il modello di distribuzione di Gestione dei servizi di Azure (ASM o classico) (imperativo). Questo articolo include una discussione sui provider di risorse di base: calcolo, archiviazione e rete.

Una delle funzionalità chiave di un modello di Resource Manager è la capacità di installare un'estensione VM nella macchina virtuale durante il provisioning. Un'estensione VM include funzionalità specifiche, come l'esecuzione di uno script personalizzato, l'installazione di software antivirus e l'esecuzione di uno script di configurazione DSC. Sono disponibili molti altri tipi di estensioni VM.

## <a name="quick-trip-around-the-diagram"></a>Descrizione rapida del diagramma

A partire dall'alto, si scrive il codice, si compila e si eseguono i test e quindi si crea un pacchetto di installazione. Chocolatey può gestire diversi tipi di pacchetti di installazione, ad esempio MSI, MSU, ZIP. Se le funzionalità native di Chocolatey non sono del tutto soddisfacenti, si può sempre ricorrere alle potenzialità complete di PowerShell per eseguire l'installazione effettiva. Inserire il pacchetto in una posizione raggiungibile, come un repository di pacchetti. Questo esempio di utilizzo usa una cartella pubblica in un account di archiviazione BLOB di Azure, ma può trovarsi anche in un'altra posizione. Chocolatey funziona in modalità nativa con i server NuGet e alcuni altri per la gestione dei metadati dei pacchetti. [Questo articolo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descrive le opzioni. Questo esempio di utilizzo usa NuGet. Nuspec sono i metadati relativi ai pacchetti. Le informazioni NuSpec vengono compilate in un NuPkg e archiviate in un server NuGet. Quando la configurazione richiede un pacchetto in base al nome e fa riferimento a un server NuGet, la risorsa DSC di Chocolatey nella VM estrae il pacchetto e lo installa. È anche possibile richiedere una versione specifica di un pacchetto.

Nella parte in basso dell'immagine è presente un modello di Azure Resource Manager. In questo esempio di utilizzo l'estensione VM registra la macchina virtuale con il server di pull di State Configuration di Automazione di Azure come nodo. La configurazione viene archiviata due volte nel server di pull: una volta come testo normale e una volta compilata come file MOF. Nel portale di Azure il file MOF rappresenta una configurazione di nodi invece di una semplice configurazione. Poiché l'artefatto è associato a un nodo, quest'ultimo riconoscerà la propria configurazione. I dettagli che seguono illustrano come assegnare la configurazione di nodi al nodo.

La creazione del Nuspec, la relativa compilazione e archiviazione in un server NuGet non è un'operazione impegnativa e si stanno già gestendo le VM. 

L'esecuzione del passaggio successivo della distribuzione continua richiede la configurazione del server di pull (una volta), la registrazione dei nodi con il server (una volta) e la creazione e archiviazione della configurazione iniziale nel server. In seguito, man mano che i pacchetti vengono aggiornati e distribuiti nel repository, è sufficiente aggiornare la configurazione e la configurazione dei nodi nel server di pull secondo necessità.

Il procedimento funziona anche se non si inizia con un modello di Resource Manager. Sono disponibili comandi di PowerShell per facilitare la registrazione delle VM con il server di pull. Per altre informazioni, vedere [Onboarding di computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Informazioni sull'esempio di utilizzo

L'esempio di utilizzo contenuto in questo articolo inizia con una VM da un'immagine generica di Windows Server 2012 R2 disponibile nella raccolta di Azure. È possibile iniziare da qualsiasi immagine archiviata e usarla come base per modificare la configurazione DSC.
La modifica della configurazione basata su un'immagine è tuttavia molto più complessa rispetto all'aggiornamento dinamico della configurazione mediante DSC.

Non è necessario usare un modello di Resource Manager e l'estensione VM per usare questa tecnica con le proprie VM. Inoltre, le VM non devono trovarsi necessariamente in Azure per la gestione della distribuzione continua. È solo necessario che Chocolatey sia installato e che Gestione configurazione locale sia configurato nella VM perché riconosca la posizione del server di pull.

Quando si aggiorna un pacchetto in una VM in produzione, è necessario escluderla dalla rotazione mentre viene installato l'aggiornamento. La procedura per eseguire questa operazione varia ampiamente. Ad esempio, per una VM con bilanciamento del carico di Azure è possibile aggiungere un probe personalizzato. Durante l'aggiornamento della VM, l'endpoint del probe dovrà restituire un messaggio 400. La modifica necessaria per ottenere questo cambiamento può essere eseguita nella configurazione, così come può esserlo la modifica per ripristinare la restituzione di un messaggio 200 una volta completato l'aggiornamento.

Il codice sorgente completo per questo esempio di utilizzo si trova in [questo progetto di Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) su GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Passaggio 1: configurare il server di pull e l'account di Automazione

Da una riga di comando di PowerShell (`Connect-AzAccount`) autenticata (la configurazione del server di pull può richiedere alcuni minuti):

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

È possibile inserire l'account di Automazione in una qualsiasi delle aree seguenti (dette anche località): Stati Uniti orientali 2, Stati Uniti centro-meridionali, US Gov Virginia, Europa occidentale, Asia sud-orientale, Giappone orientale, India centrale e Australia sud-orientale, Canada centrale ed Europa settentrionale.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Passaggio 2: apportare modifiche all'estensione della macchina virtuale nel modello di Resource Manager

I dettagli per la registrazione della VM, usando l'estensione VM di PowerShell DSC, sono disponibili in questo [modello di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Questo passaggio registra la nuova VM con il server di pull nell'elenco di nodi DSC. Parte di tale registrazione consiste nella specifica della configurazione da applicare ai nodi. Questa configurazione di nodi non deve essere già presente nel server di pull e questa operazione può quindi essere eseguita per la prima volta nel passaggio 4. In questo passaggio 2 è tuttavia necessario definire il nome del nodo e della configurazione. In questo esempio di utilizzo, il nodo è 'isvbox' e la configurazione è 'ISVBoxConfig'. Pertanto il nome della configurazione di nodi da specificare in DeploymentTemplate.json è 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Passaggio 3: aggiungere le risorse DSC necessarie al server di pull

PowerShell Gallery è instrumentata per l'installazione delle risorse DSC nell'account di Automazione di Azure.
Passare alla risorsa desiderata e fare clic sul pulsante "Distribuisci in Automazione di Azure".

![Esempio di PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Un'altra tecnica aggiunta di recente al portale di Azure consente di inserire nuovi moduli o aggiornare quelli esistenti. Fare clic sulla risorsa account di Automazione, sul riquadro Asset e infine sul riquadro Moduli. L'icona Esplora raccolta consente di visualizzare l'elenco dei moduli nella raccolta, eseguire il drill-down dei dettagli e infine di eseguire l'importazione nell'account di Automazione. Questo è un ottimo modo per mantenere aggiornati i moduli periodicamente. La funzionalità di importazione verifica le dipendenze con altri moduli per garantire che rimangano sincronizzati.

È disponibile anche un approccio manuale, usato solo una volta per ogni risorsa, a meno che non si voglia eseguire l'aggiornamento in un secondo momento. Per altre informazioni sulla creazione di moduli di integrazione di PowerShell, vedere [Creazione di moduli di integrazione per Automazione di Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>La struttura delle cartelle di un modulo di integrazione di PowerShell per un computer Windows è leggermente diversa da quella prevista da Automazione di Azure. 

1. Installare [Windows Management Framework versione 5](https://aka.ms/wmf5latest) (non necessario per Windows 10).

2. Installare il modulo di integrazione.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Copiare la cartella del modulo da **c:\Programmi\WindowsPowerShell\Moduli\NOME-MODULO** in una cartella temporanea.

4. Eliminare esempi e documentazione dalla cartella principale.

5. Comprimere la cartella principale e denominare il file ZIP come la cartella.

6. Inserire il file ZIP in una posizione HTTP raggiungibile, ad esempio l'archivio BLOB in un account di archiviazione di Azure.

7. Eseguire il comando seguente.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

L'esempio incluso esegue questi passaggi per cChoco e xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Passaggio 4: aggiungere la configurazione di nodi al server di pull

Non è necessario eseguire operazioni speciali la prima volta che si importa la configurazione nel server di pull e si procede alla compilazione. Tutte le importazioni o compilazioni successive della stessa configurazione avranno esattamente lo stesso aspetto. Ogni volta che si aggiorna il pacchetto ed è necessario effettuarne il push all'ambiente di produzione, si eseguirà questo passaggio dopo avere verificato che il file di configurazione è corretto, inclusa la nuova versione del pacchetto. Ecco il file di configurazione **ISVBoxConfig.ps1**:

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

Ecco lo script **New-ConfigurationScript.ps1** (modificato per usare il modulo Az):

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

Questi passaggi generano una nuova configurazione di nodi denominata **ISVBoxConfig.isvbox** che viene posizionata nel server di pull. Il nome della configurazione del nodo viene compilato come `configurationName.nodeName`.

## <a name="step-5-create-and-maintain-package-metadata"></a>Passaggio 5: creare e gestire i metadati dei pacchetti

Per ogni pacchetto inserito nel repository di pacchetti è necessario un Nuspec che lo descriva. Deve essere compilato e archiviato nel server NuGet. Questo processo è descritto [qui](https://docs.nuget.org/create/creating-and-publishing-a-package). 

È possibile usare **MyGet.org** come server NuGet. È possibile acquistare questo servizio, ma è disponibile uno SKU iniziale gratuito. In [NuGet](https://www.nuget.org/) sono disponibili istruzioni sull'installazione di un server NuGet personalizzato per i pacchetti privati.

## <a name="step-6-tie-it-all-together"></a>Passaggio 6: verificare tutti gli elementi

Ogni volta che una versione passa il controllo di qualità e viene approvata per la distribuzione, viene creato il pacchetto e Nuspec e Nupkg vengono aggiornati e distribuiti nel server NuGet. La configurazione (passaggio 4) deve essere aggiornata per essere conforme al nuovo numero di versione. Deve essere inviata al server di pull per la compilazione.

A questo punto, le macchine virtuali che dipendono da tale configurazione devono effettuare l'aggiornamento e installarlo. Questi aggiornamenti sono semplici: solo una o due righe di PowerShell. Nel caso di Azure DevOps, alcuni aggiornamenti vengono incapsulati in attività di compilazione che possono essere concatenate in una compilazione. Per altre informazioni dettagliate, vedere questo [articolo](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery). Questo [repository GitHub](https://github.com/Microsoft/vso-agent-tasks) descrive le attività di compilazione disponibili.

## <a name="related-articles"></a>Articoli correlati
* [Panoramica di Automation DSC per Azure](automation-dsc-overview.md)
* [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Panoramica di State Configuration di Automazione di Azure](automation-dsc-overview.md).
- Per iniziare a usare questa funzionalità, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).