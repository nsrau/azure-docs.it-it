<properties
   pageTitle="Distribuzione continua di Automation DSC per Azure con Chocolatey | Microsoft Azure"
   description="Distribuzione continua in DevOps con Gestione pacchetti di Automation DSC per Azure e Chocolatey. Esempio con modello ARM JSON completo e codice sorgente di PowerShell."
   services="automation"
   documentationCenter=""
   authors="sebastus"
   manager="stevenka"
   editor=""/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/07/2015"
   ms.author="golive"/>

# Distribuzione continua in VM IaaS con Automation DSC per Azure e Chocolatey

In un ambiente DevOps sono disponibili molti strumenti utili in diversi punti della pipeline di integrazione continua. Automation DSC (Desired State Configuration) per Azure è una nuova aggiunta alle opzioni disponibili per i team DevOps. Questo articolo illustra come configurare la distribuzione continua per un computer Windows. La tecnica può essere facilmente estesa per includere nel ruolo, ad esempio un sito Web, tutti i computer Windows necessari, estendendola quindi anche ad altri ruoli.

Meta: Questo articolo è stato scritto nel settembre 2015. La versione di Visual studio è la 2015. [PowerShell Tools for Visual Studio](http://adamdriscoll.github.io/poshtools/) è installato e facilita l'utilizzo di PowerShell con l'aggiunta di evidenziazione della sintassi e Intellisense. Attualmente, Automation DSC per Azure è disponibile nella versione di anteprima pubblica, quindi è possibile che vengano apportate modifiche nel passaggio allo stato di disponibilità generale. [Il codice sorgente completo](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) per l'esempio è disponibile su GitHub.

![Distribuzione continua con VM IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## A livello generale

Gli aspetti da considerare sono molteplici, ma possono comunque essere suddivisi un due processi principali:

  - Scrittura di codice e relativo test, quindi creazione e pubblicazione dei pacchetti di installazione per le versioni principale e secondaria del sistema. 
  - Creazione e gestione delle VM che installeranno ed eseguiranno il codice nei pacchetti.  

Dopo l'implementazione di entrambi questi processi di base, si potrà aggiornare automaticamente e in modo rapido il pacchetto in esecuzione in qualsiasi VM specifica, man mano che vengono create e distribuite nuove versioni.

## Panoramica dei componenti

Gli strumenti di gestione dei pacchetti, ad esempio [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool), sono ben noti nell'ambiente Linux, ma non lo sono altrettanto nell'ambiente Windows. [Chocolatey](https://chocolatey.org/) è uno di questi e il [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) di Scott Hanselman sull'argomento costituisce un'introduzione molto interessante. In breve, Chocolatey consente di installare pacchetti da un repository di pacchetti centrale in un sistema Windows usando la riga di comando. È possibile creare e gestire un repository personalizzato e Chocolatey può installare pacchetti da tutti i repository designati dall'utente.

DSC (Desired State Configuration) ([panoramica](https://technet.microsoft.com/it-IT/library/dn249912.aspx)) è uno strumento di PowerShell che consente di dichiarare la configurazione che si vuole applicare a un computer. Si può ad esempio dichiarare di voler installare Chocolatey o IIS, di voler aprire la porta 80 e di voler installare la versione 1.0.0 del proprio sito Web. Gestione configurazione locale per DSC implementa tale configurazione. Un server di pull DSC mantiene un repository di configurazioni per i computer dell'utente. Gestione configurazione locale in ogni computer controlla periodicamente se la configurazione esistente corrisponde a quella archiviata. Può segnalare lo stato o provare a riallineare il computer con la configurazione archiviata. È possibile modificare la configurazione archiviata nel server di pull per fare in modo che il computer o un set di computer venga allineato con la configurazione modificata.

Automazione di Azure è un servizio gestito di Microsoft Azure che consente di automatizzare diverse attività tramite runbook e altri asset, come nodi, credenziali e risorse. Automation DSC per Azure estende questa funzionalità di automazione includendo strumenti DSC di PowerShell. Ecco una interessante [panoramica](automation-dsc-overview.md).

Una risorsa DSC è un modulo di codice con funzionalità specifiche, ad esempio per la gestione della rete, di Active Directory o SQL Server. La risorsa DSC di Chocolatey riconosce in che modo accedere, tra gli altri, a un server NuGet, scaricare pacchetti, installare pacchetti e così via. In [PowerShell Gallery](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) sono disponibili molte altre risorse DSC. Questi moduli vengono installati dall'utente nel server di pull di Automation DSC per Azure, per poter essere usati nelle configurazioni personalizzate.

I modelli ARM forniscono un modo dichiarativo per la generazione dell'infrastruttura, ad esempio reti, subnet, routing e sicurezza di rete, servizi di bilanciamento del carico, NIC, VM e così via. Ecco un [articolo](../resource-manager-deployment-model.md) che confronta il modello di distribuzione ARM, ovvero dichiarativo, con il modello di distribuzione di Gestione servizi di Azure (classico), o imperativo. È disponibile anche un altro [articolo](../virtual-machines\virtual-machines-azurerm-versus-azuresm.md) relativo ai provider di risorse di base, come calcolo, archiviazione e rete.

Una funzionalità chiave di un modello ARM è la capacità di installate un'estensione VM nella macchina virtuale durante il provisioning. Un'estensione VM include funzionalità specifiche, come l'esecuzione di uno script personalizzato, l'installazione di software antivirus o l'esecuzione di uno script di configurazione DSC. Sono disponibili molti altri tipi di estensioni VM.

## Descrizione rapida del diagramma

A partire dall'alto, si scrive il codice, si compila e si eseguono i test e quindi si crea un pacchetto di installazione. Chocolatey può gestire diversi tipi di pacchetti di installazione, ad esempio MSI, MSU, ZIP. Se le funzionalità native di Chocolatey non sono del tutto soddisfacenti, si può sempre ricorrere alle potenzialità complete di PowerShell per eseguire l'installazione effettiva. Inserire il pacchetto in una posizione raggiungibile, come un repository di pacchetti. Si può usare una cartella pubblica nel proprio account di archiviazione BLOB di Azure, ma può essere una posizione qualsiasi. Chocolatey funziona in modalità nativa con i server NuGet e alcuni altri per la gestione dei metadati dei pacchetti. [Questo articolo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) descrive le opzioni. Questo esempio usa NuGet. Nuspec sono i metadati relativi ai pacchetti. I Nuspec vengono "compilati" in NuPkg e archiviati in un server NuGet. Quando la configurazione richiede un pacchetto in base al nome e fa riferimento a un server NuGet, la risorsa DSC di Chocolatey, che ora si trova nella VM, estrae il pacchetto e lo installa automaticamente. È anche possibile richiedere una versione specifica di un pacchetto.

Nella parte in basso dell'immagine è presente un modello di Gestione risorse di Azure. In questo esempio l'estensione VM registra la macchina virtuale con il server di pull di Automation DSC per Azure come nodo. La configurazione viene archiviata nel server di pull. In realtà viene archiviata due volte, cioè una volta come testo normale e una volta compilata come file MOF, un'indicazione per coloro che hanno familiarità con questi elementi. Nel portale il file MOF è una "configurazione di nodi", invece di una semplice "configurazione". Poiché l'artefatto è associato a un nodo, quest'ultimo riconoscerà la propria configurazione. I dettagli che seguono illustrano come assegnare la configurazione di nodi al nodo.

È probabile che ci si stia già occupando della sezione iniziale o la maggior parte di essa. La creazione del Nuspec, la relativa compilazione e archiviazione in un server NuGet non è un'operazione impegnativa e si stanno già gestendo le VM. L'esecuzione del passaggio successivo della distribuzione continua richiede la configurazione del server di pull (una volta), la registrazione dei nodi con il server (una volta) e la creazione e archiviazione della configurazione nel server (inizialmente). Poi, man mano che i pacchetti vengono aggiornati e distribuiti nel repository, aggiornare la configurazione nel server di pull, ripetendola se necessario.

Il procedimento funziona anche se non si inizia con un modello ARM. Sono disponibili cmdlet di PowerShell progettati per facilitare la registrazione delle VM con il server di pull e tutto il resto.


## Passaggio 1: Configurazione del server di pull e dell'accont di automazione

Da una riga di comando di PowerShell autenticata (Add-AzureAccount), la configurazione del server di pull può richiedere alcuni minuti:

    Switch-AzureMode -Name AzureResourceManager 
    Register-AzureProvider –ProviderNamespace Microsoft.Automation 
    Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation 
    New-AzureResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

È possibile inserire l'account di automazione in una qualsiasi delle aree seguenti (dette anche località): Giappone orientale, Stati Uniti orientali 2, Europa occidentale, Asia sudorientale, Stati Uniti centro-meridionali. Naturalmente, assicurarsi che il gruppo di risorse di automazione si trovi nella stessa area dell'account di automazione.

## Passaggio 2: Modifiche dell'estensione VM nel modello ARM

I dettagli per la registrazione della VM, usando l'estensione VM di PowerShell DSC, sono disponibili in questo [modello di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver). Questo passaggio registra la nuova VM con il server di pull nell'elenco di nodi DSC.

## Passaggio 3: Aggiunta delle risorse DSC necessarie al server di pull

PowerShell Gallery è instrumentata per l'installazione delle risorse DSC nell'account di Automazione di Azure. Passare alla risorsa desiderata e fare clic sul pulsante "Deploy to Azure Automation".

![Esempio di PowerShell Gallery](./media/automation-dsc-cd-chocolatey/cchoco.png)

In alternativa, è disponibile l'approccio manuale. La struttura di cartelle di un modulo di integrazione di PowerShell per un computer Windows è leggermente diversa da quella prevista per il server di pull di Automation DSC per Azure, perciò richiede alcune modifiche da parte dell'utente. Non è un'operazione difficile e viene eseguita una sola volta per risorsa, a meno che non si voglia aggiornarla in futuro.

-   Installare il modulo necessario nella workstation, come indicato di seguito:
    -   Installare [Windows Management Framework versione 5](http://aka.ms/wmf5latest). 
    -   `Install-Module  –ModuleName MODULENAME` <—estrae il modulo da PowerShell Gallery. 
-   Copiare la cartella del modulo da `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` in una cartella temporanea. 
-   Eliminare esempi e documentazione dalla cartella principale. 
-   Comprimere la cartella principale e denominare il file ZIP esattamente come la cartella. 
-   Inserire il file ZIP in un percorso HTTP raggiungibile. 
-   Eseguire questo comando di PowerShell:

        New-AzureAutomationModule ``
            -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ``
            -Name MODULE-NAME –ContentLink "https://STORAGE-URI/public/MODULE-NAME.zip"
        

L'esempio incluso esegue questi passaggi per cChoco e xNetworking.

## Passaggio 4: Aggiunta della configurazione di nodi al server di pull

Non è necessario eseguire operazioni speciali la prima volta che si importa la configurazione nel server di pull e si procede alla compilazione. Tutte le importazioni/compilazioni successive della stessa configurazione avranno esattamente lo stesso aspetto. Ogni volta che si aggiorna il pacchetto ed è necessario effettuarne il push all'ambiente di produzione, si eseguirà questo passaggio dopo avere verificato che il file di configurazione è corretto, inclusa la nuova versione del pacchetto. Ecco il file di configurazione e PowerShell:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking
    
        Node "isvbox" {   <— node must be named, $AllNodes.NodeName won’t work.
    
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
                Source = “MY-NUGET-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

    Import-AzureAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force
    
    $jobData = Start-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 
    
    $compilationJobId = $jobData.Id
    
    Get-AzureAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Questi passaggi generano una nuova configurazione di nodi denominata "ISVBoxConfig.isvbox". Il nome viene compilato come "configurationName.nodeName".

## Passaggio 5: Assegnare la configurazione di nodi al nodo

Per questo passaggio è necessario eseguire una query sul server di pull per ottenere l'ID della nuova VM dopo che è stata registrata. Il cmdlet per eseguire questa operazione è Get-AzureAutomationDscNode. Include alcuni filtri che potrebbero funzionare, ma in generale si vorrà inviare tramite pipe l'output di questa operazione tramite un filtro che estragga le VM con un modello del nome. Se si creano solo VM con un singolo ruolo, si potranno estrarre tutte quelle in cui NodeConfigurationName è ConfigureLCMforAAPull.isvbox. Seguendo questo esempio, questo è il nome di configurazione di qualsiasi VM appena registrata. Una volta ottenuto l'elenco di ID; ecco il cmdlet:

    Set-AzureAutomationDscNode ` 
        -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -NodeConfigurationName ISVBoxConfig.isvbox -Id $theId

## Passaggio 6: Dove inserire tutto il codice di PowerShell

Quando si scrivono modelli ARM personalizzati, tramite Visual Studio è disponibile uno script di PowerShell che richiama il modello. I cmdlet che importano la configurazione, la compilano, ottengono gli ID delle VM e così via possono essere aggiunti a questo script. Vedere il codice sorgente per un esempio su GitHub. Per le parti che vengono eseguite ogni volta che si aggiorna il pacchetto, integrare i comandi necessari nella pipeline di integrazione continua.

## Passaggio 7: Creazione e gestione dei metadati del pacchetto

Per ogni pacchetto inserito nel repository di pacchetti, è necessario un Nuspec che lo descriva. Questo Nuspec deve essere compilato e archiviato nel server NuGet. Questo processo è descritto [qui](http://docs.nuget.org/create/creating-and-publishing-a-package). È possibile usare MyGet.org come server NuGet. Il servizio è a pagamento, ma viene offerto gratuitamente uno SKU di avvio. In NuGet.org sono disponibili istruzioni sull'installazione di un server NuGet personalizzato per i pacchetti privati.

## Note

Questo esempio inizia con una VM da un'immagine generica di Windows 2012 R2 disponibile nella Raccolta di Azure. È possibile iniziare da qualsiasi immagine archiviata e quindi usarla come base per procedere alla modifica con la configurazione DSC.

Non è necessario usare un modello ARM e l'estensione VM per usare questa tecnica con le proprie VM. Inoltre, le VM non devono trovarsi necessariamente in Azure per la gestione della distribuzione continua. È solo necessario che Chocolatey sia installato e che Gestione configurazione locale sia configurato nella VM perché riconosca la posizione del server di pull.

Naturalmente, quando si aggiorna un pacchetto in una VM in produzione, è necessario escluderla dalla rotazione mentre viene installato l'aggiornamento. La procedura per eseguire questa operazione varia ampiamente. Ad esempio, per una VM con bilanciamento del carico di Azure è possibile aggiungere un probe personalizzato. Durante l'aggiornamento della VM, l'endpoint del probe dovrà restituire un messaggio 400. La modifica necessaria per ottenere questo cambiamento può essere eseguita nella configurazione, così come può esserlo la modifica per ripristinare la restituzione di un messaggio 200 una volta completato l'aggiornamento.

Il codice sorgente completo per questo esempio si trova in [questo progetto di Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) su GitHub.

<!---HONumber=Oct15_HO3-->