---
title: Distribuzione continua di Automation DSC per Azure
description: Distribuzione continua in DevOps con Gestione pacchetti di Automation DSC per Azure e Chocolatey.  Esempio con modello ARM JSON completo e codice sorgente di PowerShell.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e92bb8b4078bc5c85a639d3b5b38c124152576f7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Esempio di utilizzo: Distribuzione continua in macchine virtuali tramite Automation DSC e Chocolatey
In un ambiente DevOps sono disponibili molti strumenti utili in diversi punti della pipeline di integrazione continua.  Automation DSC (Desired State Configuration) per Azure è una nuova aggiunta alle opzioni disponibili per i team DevOps.  Questo articolo illustra come configurare la distribuzione continua per un computer Windows.  La tecnica può essere facilmente estesa per includere nel ruolo, ad esempio un sito Web, tutti i computer Windows necessari, estendendola quindi anche ad altri ruoli.

![Distribuzione continua con VM IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>A livello generale
Gli aspetti da considerare sono molteplici, ma possono comunque essere suddivisi in due processi principali: 

* Scrittura di codice e relativo test, quindi creazione e pubblicazione dei pacchetti di installazione per le versioni principale e secondaria del sistema. 
* Creazione e gestione delle VM che installeranno ed eseguiranno il codice nei pacchetti.  

Dopo l'implementazione di entrambi questi processi di base, si potrà aggiornare automaticamente e in modo rapido il pacchetto in esecuzione in qualsiasi VM specifica, man mano che vengono create e distribuite nuove versioni.

## <a name="component-overview"></a>Panoramica dei componenti
Gli strumenti di gestione dei pacchetti, ad esempio [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , sono ben noti nell'ambiente Linux, ma non lo sono altrettanto nell'ambiente Windows.  [Chocolatey](https://chocolatey.org/) è uno di questi e il [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) di Scott Hanselman sull'argomento costituisce un'introduzione molto interessante.  In breve, Chocolatey consente di installare pacchetti da un repository di pacchetti centrale in un sistema Windows usando la riga di comando.  È possibile creare e gestire un repository personalizzato e Chocolatey può installare pacchetti da tutti i repository designati dall'utente.

DSC (Desired State Configuration) ([panoramica](https://technet.microsoft.com/library/dn249912.aspx)) è uno strumento di PowerShell che consente di dichiarare la configurazione che si desidera applicare a un computer.  Si può ad esempio dichiarare di voler installare Chocolatey o IIS, di voler aprire la porta 80 e di voler installare la versione 1.0.0 del proprio sito Web.  Gestione configurazione locale per DSC implementa tale configurazione. Un server di pull DSC mantiene un repository di configurazioni per i computer dell'utente. Gestione configurazione locale in ogni computer controlla periodicamente se la configurazione esistente corrisponde a quella archiviata. Può segnalare lo stato o provare a riallineare il computer con la configurazione archiviata. È possibile modificare la configurazione archiviata nel server di pull per fare in modo che il computer o un set di computer venga allineato con la configurazione modificata.

Automazione di Azure è un servizio gestito di Microsoft Azure che consente di automatizzare diverse attività tramite runbook, nodi, credenziali, risorse e asset, ad esempio pianificazioni e variabili globali. Automation DSC per Azure estende questa funzionalità di automazione includendo strumenti DSC di PowerShell.  Ecco un'interessante [panoramica](automation-dsc-overview.md).

Una risorsa DSC è un modulo di codice con funzionalità specifiche, ad esempio per la gestione della rete, di Active Directory o SQL Server.  La risorsa DSC di Chocolatey riconosce in che modo accedere, tra gli altri, a un server NuGet, scaricare pacchetti, installare pacchetti e così via.  In [PowerShell Gallery](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)sono disponibili numerose altre risorse DSC.  Questi moduli vengono installati dall'utente nel server di pull di Automation DSC per Azure, per poter essere usati nelle configurazioni personalizzate.

I modelli di Resource Manager offrono un modo dichiarativo per la generazione dell'infrastruttura, ad esempio reti, subnet, routing e sicurezza di rete, servizi di bilanciamento del carico, NIC, VM e così via.  Ecco un [articolo](../azure-resource-manager/resource-manager-deployment-model.md) che confronta il modello di distribuzione (dichiarativo) di Resource Manager con il modello di distribuzione ASM o classica (imperativo) e illustra i principali provider di risorse, calcolo, archiviazione e rete.

Una funzionalità chiave di un modello di Resource Manager è la capacità di installare un'estensione VM nella macchina virtuale durante il provisioning.  Un'estensione VM include funzionalità specifiche, come l'esecuzione di uno script personalizzato, l'installazione di software antivirus o l'esecuzione di uno script di configurazione DSC.  Sono disponibili molti altri tipi di estensioni VM.

## <a name="quick-trip-around-the-diagram"></a>Descrizione rapida del diagramma
A partire dall'alto, si scrive il codice, si compila e si eseguono i test e quindi si crea un pacchetto di installazione.  Chocolatey può gestire diversi tipi di pacchetti di installazione, ad esempio MSI, MSU, ZIP.  Se le funzionalità native di Chocolatey non sono del tutto soddisfacenti, si può sempre ricorrere alle potenzialità complete di PowerShell per eseguire l'installazione effettiva.  Inserire il pacchetto in una posizione raggiungibile, come un repository di pacchetti.  Questo esempio di utilizzo usa una cartella pubblica in un account di archiviazione BLOB di Azure, ma può trovarsi anche in un'altra posizione.  Chocolatey funziona in modalità nativa con i server NuGet e alcuni altri per la gestione dei metadati dei pacchetti.  [Questo articolo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descrive le opzioni.  Questo esempio di utilizzo usa NuGet.  Nuspec sono i metadati relativi ai pacchetti.  I Nuspec vengono "compilati" in NuPkg e archiviati in un server NuGet.  Quando la configurazione richiede un pacchetto in base al nome e fa riferimento a un server NuGet, la risorsa DSC di Chocolatey, che ora si trova nella VM, estrae il pacchetto e lo installa automaticamente.  È anche possibile richiedere una versione specifica di un pacchetto.

Nella parte in basso dell'immagine è presente un modello di Gestione risorse di Azure.  In questo esempio di utilizzo, l'estensione VM registra la macchina virtuale con il server di pull di Automation DSC per Azure come nodo.  La configurazione viene archiviata nel server di pull.  In realtà viene archiviata due volte, cioè una volta come testo normale e una volta compilata come file MOF, un'indicazione per coloro che hanno familiarità con questi elementi.  Nel portale il file MOF è una "configurazione di nodi", invece di una semplice "configurazione".  Poiché l'artefatto è associato a un nodo, quest'ultimo riconoscerà la propria configurazione.  I dettagli che seguono illustrano come assegnare la configurazione di nodi al nodo.

È probabile che ci si stia già occupando della sezione iniziale o la maggior parte di essa.  La creazione del Nuspec, la relativa compilazione e archiviazione in un server NuGet non è un'operazione impegnativa  e si stanno già gestendo le VM.  L'esecuzione del passaggio successivo della distribuzione continua richiede la configurazione del server di pull (una volta), la registrazione dei nodi con il server (una volta) e la creazione e archiviazione della configurazione nel server (inizialmente).  In seguito, man mano che i pacchetti vengono aggiornati e distribuiti nel repository, aggiornare la configurazione e la configurazione di nodi nel server di pull, ripetendola se necessario.

Il procedimento funziona anche se non si inizia con un modello ARM.  Sono disponibili cmdlet di PowerShell progettati per facilitare la registrazione delle VM con il server di pull e tutto il resto. Per altre informazioni dettagliate, vedere l'articolo [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Passaggio 1: Configurazione del server di pull e dell'account di automazione
Da una riga di comando di PowerShell (Add-AzureRmAccount) autenticata (la configurazione del server di pull può richiedere alcuni minuti):

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

È possibile inserire l'account di automazione in una qualsiasi delle aree seguenti (dette anche località): Stati Uniti orientali 2, Stati Uniti centro-meridionali, US Gov Virginia, Europa occidentale, Asia sud-orientale, Giappone orientale, India centrale e Australia sud-orientale, Canada centrale ed Europa settentrionale.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Passaggio 2: Modifiche dell'estensione VM nel modello ARM
I dettagli per la registrazione della VM, usando l'estensione VM di PowerShell DSC, sono disponibili in questo [modello di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Questo passaggio registra la nuova VM con il server di pull nell'elenco di nodi DSC.  Parte di tale registrazione consiste nella specifica della configurazione da applicare ai nodi.  Questa configurazione di nodi non deve essere già presente nel server di pull e questa operazione può quindi essere eseguita per la prima volta nel passaggio 4.  In questo passaggio 2 è tuttavia necessario definire il nome del nodo e della configurazione.  In questo esempio di utilizzo, il nodo è 'isvbox' e la configurazione è 'ISVBoxConfig'.  Pertanto il nome della configurazione di nodi da specificare in DeploymentTemplate.json è 'ISVBoxConfig.isvbox'.  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Passaggio 3: Aggiunta delle risorse DSC necessarie al server di pull
PowerShell Gallery è instrumentata per l'installazione delle risorse DSC nell'account di Automazione di Azure.  Passare alla risorsa desiderata e fare clic sul pulsante "Deploy to Azure Automation".

![Esempio di PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Un'altra tecnica aggiunta di recente al portale di Azure consente di inserire nuovi moduli o aggiornare quelli esistenti. Fare clic sulla risorsa Account di automazione, sul riquadro Asset e infine sul riquadro Moduli.  L'icona Esplora raccolta consente di visualizzare l'elenco dei moduli nella raccolta, eseguire il drill-down dei dettagli e infine di eseguire l'importazione in Account di automazione. Questo è un ottimo modo per mantenere aggiornati i moduli periodicamente. La funzionalità di importazione verifica le dipendenze con altri moduli per garantire che rimangano sincronizzati.

In alternativa, è disponibile l'approccio manuale.  La struttura di cartelle di un modulo di integrazione di PowerShell per un computer Windows è leggermente diversa da quella prevista da Automation DSC per Azure,  perciò richiede alcune modifiche da parte dell'utente.  Non è un'operazione difficile e viene eseguita una sola volta per risorsa, a meno che non si voglia aggiornarla in futuro.  Per altre informazioni sulla creazione di moduli di integrazione di PowerShell, vedere l'articolo relativo alla [creazione di moduli di integrazione per Automazione di Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Installare il modulo necessario nella workstation, come indicato di seguito:
  * Installare [Windows Management Framework versione 5](http://aka.ms/wmf5latest) (non necessario per Windows 10).
  * `Install-Module –Name MODULE-NAME`    &lt;—estrae il modulo da PowerShell Gallery 
* Copiare la cartella del modulo da `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` in una cartella temporanea. 
* Eliminare esempi e documentazione dalla cartella principale. 
* Comprimere la cartella principale e denominare il file ZIP esattamente come la cartella. 
* Inserire il file ZIP in una posizione HTTP raggiungibile, ad esempio l'archivio BLOB in un account di archiviazione di Azure.
* Eseguire questo comando di PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

L'esempio incluso esegue questi passaggi per cChoco e xNetworking. Per informazioni sulla gestione specifica di cChoco, vedere le [note](#notes) .

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Passaggio 4: Aggiunta della configurazione di nodi al server di pull
Non è necessario eseguire operazioni speciali la prima volta che si importa la configurazione nel server di pull e si procede alla compilazione.  Tutte le importazioni/compilazioni successive della stessa configurazione avranno esattamente lo stesso aspetto.  Ogni volta che si aggiorna il pacchetto ed è necessario effettuarne il push all'ambiente di produzione, si eseguirà questo passaggio dopo avere verificato che il file di configurazione è corretto, inclusa la nuova versione del pacchetto.  Ecco il file di configurazione e PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

    Import-AzureRmAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force

    $jobData = Start-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 

    $compilationJobId = $jobData.Id

    Get-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Questi passaggi generano una nuova configurazione di nodi denominata "ISVBoxConfig.isvbox" che viene posizionata nel server di pull.  Il nome della configurazione di nodi viene compilato come "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Passaggio 5: Creazione e gestione dei metadati del pacchetto
Per ogni pacchetto inserito nel repository di pacchetti, è necessario un Nuspec che lo descriva.  Questo Nuspec deve essere compilato e archiviato nel server NuGet. Questo processo è descritto [qui](http://docs.nuget.org/create/creating-and-publishing-a-package).  È possibile usare MyGet.org come server NuGet.  Il servizio è a pagamento, ma viene offerto gratuitamente uno SKU di avvio.  In NuGet.org sono disponibili istruzioni sull'installazione di un server NuGet personalizzato per i pacchetti privati.

## <a name="step-6-tying-it-all-together"></a>Passaggio 6: Verifica di tutti gli elementi
Ogni volta che una versione passa il controllo di qualità e viene approvata per la distribuzione, viene creato il pacchetto e Nuspec e Nupkg vengono aggiornati e distribuiti nel server NuGet.  Inoltre, la configurazione illustrata nel passaggio 4 deve essere aggiornata, per essere conforme al nuovo numero di versione,  e deve essere inviata al server di pull per la compilazione.  A questo punto, le macchine virtuali che dipendono da tale configurazione devono effettuare l'aggiornamento e installarlo.  Questi aggiornamenti sono semplici: solo una o due righe di PowerShell.  Nel caso di Visual Studio Team Services, alcuni aggiornamenti vengono incapsulati in attività di compilazione che possono essere concatenate in una compilazione.  Per altre informazioni dettagliate, vedere questo [articolo](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery).  Questo [repository GitHub](https://github.com/Microsoft/vso-agent-tasks) descrive le varie attività di compilazione disponibili.

## <a name="notes"></a>Note
Questo esempio di uso inizia con una VM da un'immagine generica di Windows 2012 R2 disponibile nella raccolta di Azure.  È possibile iniziare da qualsiasi immagine archiviata e usarla come base per modificare la configurazione DSC.  La modifica della configurazione basata su un'immagine è tuttavia molto più complessa rispetto all'aggiornamento dinamico della configurazione mediante DSC.

Non è necessario usare un modello ARM e l'estensione VM per usare questa tecnica con le proprie VM.  Inoltre, le VM non devono trovarsi necessariamente in Azure per la gestione della distribuzione continua.  È solo necessario che Chocolatey sia installato e che Gestione configurazione locale sia configurato nella VM perché riconosca la posizione del server di pull.  

Naturalmente, quando si aggiorna un pacchetto in una VM in produzione, è necessario escluderla dalla rotazione mentre viene installato l'aggiornamento.  La procedura per eseguire questa operazione varia ampiamente.  Ad esempio, per una VM con bilanciamento del carico di Azure è possibile aggiungere un probe personalizzato.  Durante l'aggiornamento della VM, l'endpoint del probe dovrà restituire un messaggio 400.  La modifica necessaria per ottenere questo cambiamento può essere eseguita nella configurazione, così come può esserlo la modifica per ripristinare la restituzione di un messaggio 200 una volta completato l'aggiornamento.

Il codice sorgente completo per questo esempio di utilizzo si trova in [questo progetto di Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) su GitHub.

## <a name="related-articles"></a>Articoli correlati
* [Panoramica della piattaforma DSC di Automazione di Azure](automation-dsc-overview.md)
* [Cmdlet di Automation DSC per Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)

