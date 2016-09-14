<properties
	pageTitle="Domande frequenti su DevTest Labs | Microsoft Azure"
	description="Trovare le risposte alle domande comuni su DevTest Labs."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="tarcher"/>

# Domande frequenti su DevTest Labs

Questo articolo risponde ad alcune delle domande più frequenti relative a DevTest Labs.

## Cosa fare se non è disponibile una risposta alla domanda?
Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto.

- Pubblicare una domanda nel [thread Disqus](#comments) alla fine del presente documento di FAQ e interagire con il team di Cache di Azure e altri membri della community in merito a questo articolo.
- Per raggiungere un pubblico maggiore, pubblicare una domanda sul [Forum MSDN di Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs) e interagire con il team di Azure DevTest Labs e gli altri membri della community.
- Per eseguire una richiesta di funzionalità, inviare richieste e idee al servizio [Azure DevTest Labs UserVoice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## Introduzione
- [Come posso iniziare a usare DevTest Labs?](http://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## Generale
- [Perché usare DevTest Labs?](#why-should-i-use-devtest-labs)
- [Cosa significa "self-service senza preoccupazioni"?](#what-does-quotworry-free-self-servicequot-mean)
- [Come usare DevTest Labs?](#how-can-i-use-devtest-labs)
- [Come verranno addebitati i costi per DevTest Labs?](#how-will-i-be-charged-for-devtest-labs)
 
## Sicurezza 
- [Quali sono i diversi livelli di sicurezza in DevTest Labs?](#what-are-the-different-security-levels-in-devtest-labs)
- [Come si crea un ruolo specifico per consentire agli utenti di eseguire solo una singola attività?](#how-do-i-create-a-specific-role-to-allow-users-to-perform-only-a-single-task)
 
## Integrazione e automazione CI/CD 
 
- [DevTest Labs si integra con la toolchain CI/CD?](#does-devtest-labs-integrate-with-my-cicd-toolchain)
 
## Macchine virtuali 
 
- [Perché nel pannello Macchine virtuali di Azure non è possibile visualizzare alcune VM che vengono visualizzate in DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-devtest-labs)
- [Qual è la differenza tra le immagini e le formule personalizzate?](#what-is-the-difference-between-custom-images-and-formulas)
- [Come si creano più VM dallo stesso modello contemporaneamente?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
- [Come si spostano le VM di Azure esistenti nel lab DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab)
- [È possibile collegare più dischi alle VM?](#can-i-attach-multiple-disks-to-my-vms)
- [Come si automatizza il processo di caricamento dei file VHD per creare immagini personalizzate?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
 
## Elementi 
 
- [Cosa sono gli elementi?](#what-are-artifacts)
 
## Configurazione del lab 
 
- [Come si crea un lab da un modello di Azure Resource Manager?](#how-do-i-create-a-lab-from-an-arm-template)
- [Perché tutte le VM vengono create in gruppi di risorse diversi con nomi arbitrari? È possibile rinominare o modificare questi gruppi di risorse?](#why-are-all-of-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
- [Quanti lab è possibile creare nella stessa sottoscrizione?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Quante VM è possibile creare per ogni lab?](#how-many-vms-can-i-create-per-lab)
 
## Risoluzione dei problemi 
 
- [Si è verificato un errore per l'elemento durante la creazione della VM. Come si risolve il problema?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
- [Perché la rete virtuale esistente non viene salvata correttamente?](#why-isnt-my-existing-virtual-network-saving-properly)

### Perché usare DevTest Labs? 
DevTest Labs può far risparmiare tempo e denaro al team. Gli sviluppatori possono creare gli ambienti usando alcune basi diverse e usare gli elementi per distribuire e configurare rapidamente le applicazioni. Con le immagini e le formule personalizzate, le macchine virtuali possono essere salvate come modelli e riprodotte facilmente. I lab offrono anche diversi criteri configurabili, ad esempio arresto automatico, soglia di costo, numero massimo di VM per utente e dimensioni massime delle VM, che consentono agli amministratori di lab di ridurre gli sprechi e di gestire gli ambienti di un team. Per una spiegazione più approfondita di DevTest Labs, vedere la [panoramica](devtest-lab-overview.md) o guardare il [video introduttivo](/documentation/videos/videos/what-is-azure-devtest-labs).

### Cosa significa "self-service senza preoccupazioni"?
"Self-service senza preoccupazioni" significa che gli sviluppatori e i tester possono creare gli ambienti necessari e gli amministratori hanno la certezza che DevTest Labs consentirà di ridurre al minimo gli sprechi e di controllare i costi. Gli amministratori possono specificare quali sono le dimensioni consentite per le VM e il numero massimo di VM e quando le VM vengono avviate e arrestate. DevTest Labs consente anche di monitorare facilmente i costi e di impostare avvisi per sapere quante risorse sono in uso nel lab.

### Come usare DevTest Labs? 
DevTest Labs è utile quando sono necessari ambienti di sviluppo o di testing e si vuole riprodurli rapidamente e/o gestirli con criteri di risparmio sui costi.

Ecco alcuni scenari per cui i clienti usano DevTest Labs:

- Gestione centralizzata degli ambienti di sviluppo e di testing, utilizzando criteri per ridurre i costi e immagini personalizzate per condividere le build con il team.
- Sviluppo di un'applicazione con le immagini personalizzate per salvare lo stato del disco durante le fasi di sviluppo.
- Verifica dei costi in relazione alla stato.
- Creazione di ambienti di testing di massa per i test di controllo di qualità.
- Uso di elementi e formule per configurare e riprodurre facilmente un'applicazione in svariati ambienti.
- Distribuzione di VM per gli hackathon (attività di test o sviluppo collaborative) e facile deprovisioning al termine dell'evento.

### Come verranno addebitati i costi per DevTest Labs? 
DevTest Labs è un servizio gratuito e quindi la creazione dei lab e la configurazione di criteri, modelli ed elementi sono gratuite. Si pagano solo le risorse di Azure usate nei lab, ad esempio macchine virtuali, account di archiviazione e reti virtuali. Per altre informazioni sui costi delle risorse dei lab, vedere [Prezzi di Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### Quali sono i diversi livelli di sicurezza in DevTest Labs?  
L'accesso sicuro è determinato dal [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md). Per comprendere il funzionamento dell'accesso, è utile conoscere le differenze tra un'autorizzazione, un ruolo e un ambito definiti dal Controllo degli accessi in base al ruolo. Un'autorizzazione è un accesso definito a un'azione specifica (ad esempio, un accesso in lettura a tutte le macchine virtuali). Un ruolo è un set di autorizzazioni che possono essere raggruppate e assegnate a un utente (ad esempio, il "proprietario della sottoscrizione" ha accesso a tutte le risorse in una sottoscrizione). Un ambito è un livello nella gerarchia della risorsa di Azure (ad esempio, un gruppo di risorse o un singolo lab nell'intera sottoscrizione).
 
Nell'ambito di DevTest Labs, esistono due tipi di ruoli per definire le autorizzazioni utente: proprietario del lab e utente del lab.

- Proprietario del lab: ha accesso a qualsiasi risorsa del lab, quindi può modificare i criteri, leggere e scrivere nelle VM, modificare la rete virtuale e così via.
- Utente del lab: può visualizzare tutte le risorse del lab, ad esempio VM, criteri e reti virtuali, ma non può modificare i criteri o le VM create da altri utenti. È anche possibile creare ruoli personalizzati in DevTest Labs, come illustrato nell'articolo [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).
 
Poiché gli ambiti sono gerarchici, quando un utente ha le autorizzazioni per un determinato ambito, gli vengono automaticamente concesse tali autorizzazioni per ogni ambito di livello inferiore incluso. Ad esempio, se un utente è assegnato al ruolo di proprietario della sottoscrizione, ha accesso a tutte le risorse in una sottoscrizione, che includono tutte le macchine virtuali, tutte le reti virtuali e tutti i lab. Il proprietario di una sottoscrizione quindi eredita automaticamente il ruolo di proprietario del lab, ma non il contrario. Il proprietario di un lab ha accesso a un lab, che è un ambito più basso del livello della sottoscrizione. Il proprietario di un lab quindi non potrà visualizzare le macchine virtuali, le reti virtuali o nessuna altra risorsa esterna al lab.

### Come si crea un ruolo per consentire agli utenti di eseguire un'attività specifica?
Un articolo completo su come creare i ruoli personalizzati e assegnare le autorizzazioni è disponibile qui. Ecco un esempio di script che crea il ruolo "DevTest Labs Advanced User", con l'autorizzazione per avviare e arrestare tutte le VM del lab:
 
	$policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User") 
	$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
	$policyRoleDef.Id = $null 
	$policyRoleDef.Name = "DevTest Labs Advance User" 
	$policyRoleDef.IsCustom = $true 
	$policyRoleDef.AssignableScopes.Clear() 
	$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
	$policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)  
 
### DevTest Labs si integra con la toolchain CI/CD? 
Se si usa VSTS, è disponibile un'[estensione Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) che consente di automatizzare la pipeline di tipo Versione in DevTest Labs. Alcuni degli usi di questa estensione includono:

- Creazione e distribuzione automatica di una VM e configurazione con la build più recente usando la copia dei file di Azure o le attività VSTS di PowerShell.
- Acquisizione automatica dello stato di una VM dopo il test per riprodurre un bug nella stessa VM per altre indagini.
- Eliminazione della VM alla fine della pipeline di tipo Versione quando non è più necessaria.

I post di blog seguenti forniscono indicazioni e informazioni sull'uso dell'estensione VSTS:
 
- [Azure DevTest Labs – VSTS extension (Azure DevTest Labs: estensione VSTS)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
- [Deploying a new VM in an existing AzureDevTestLab from VSTS (Distribuzione di una nuova VM in un'istanza di Azure DevTest Labs da VSTS)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (Uso di Release Management per VSTS per le distribuzioni continue in Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)
 
Per le altre toolchain CI/CD, tutti gli scenari sopra indicati che possono essere realizzati tramite le estensioni per VSTS Tasks possono essere realizzati in modo simile tramite la distribuzione di [modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) con i [cmdlet di Azure PowerShell](../resource-group-template-deploy.md) e [gli SDK di .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). È anche possibile usare le [API REST per DevTest Labs](http://aka.ms/dtlrestapis) per l'integrazione con la toolchain.

### Perché nel pannello Macchine virtuali di Azure non è possibile visualizzare alcune VM che vengono visualizzate in DevTest Labs?
Quando una VM viene creata in DevTest Labs, viene concessa l'autorizzazione per accedere a tale VM e sarà possibile visualizzarla sia nel pannello dei lab che nel pannello **Macchine virtuali**. Gli utenti di DevTest Labs possono quindi visualizzare tutte le macchine virtuali create nel lab tramite il pannello **All Virtual Machines** (Tutte le macchine virtuali). Tuttavia agli utenti di DevTest Labs non viene automaticamente concesso l'accesso in lettura alle risorse delle VM create da altri, che quindi non verranno visualizzate nel pannello **Macchine virtuali**.

### Qual è la differenza tra le immagini e le formule personalizzate? 
Un'immagine personalizzata è un disco rigido virtuale, mentre una formula è un'immagine che è possibile configurare con impostazioni aggiuntive che si possono salvare e riprodurre. Un'immagine personalizzata può essere preferibile se si vogliono creare rapidamente più ambienti con la stessa immagine di base non modificabile. Una formula può essere migliore se si vuole riprodurre la configurazione della VM con i bit più recenti, una rete virtuale/subnet o una dimensione specifica. Per una spiegazione più approfondita, vedere l'articolo [Confronto tra immagini e formule personalizzate in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
 
### Come si creano più VM dallo stesso modello contemporaneamente? 
È possibile usare l'[estensione VSTS Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) o [generare un modello di Resource Manager](devtest-lab-add-vm-with-artifacts.md#save-arm-template) durante la creazione di una VM e [distribuire il modello di Azure Resource Manager da Windows PowerShell](../resource-group-template-deploy.md).
 
### Come si spostano le VM di Azure esistenti nel lab DevTest Labs? 
È in fase di progettazione una soluzione per spostare direttamente le VM in DevTest Labs, ma attualmente è possibile copiare le VM esistenti in DevTest Labs come segue:

1. Copiare il file VHD della VM esistente usando questo [script di Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
1. [Creare l'immagine personalizzata](devtest-lab-create-template.md) nel lab di DevTest Labs.
1. Creare una VM nel lab dall'immagine personalizzata.
 
### È possibile collegare più dischi alle VM? 
Il collegamento di più dischi alle VM è supportato.
 
### Come si automatizza il processo di caricamento dei file VHD per creare immagini personalizzate? 
Sono disponibili due opzioni:

- È possibile usare [AzCopy per Azure](../storage/storage-use-azcopy.md#blob-upload) per copiare o caricare i file VHD nell'account di archiviazione associato al lab.
- [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che esegue Windows, OSX e Linux.
 
Per trovare l'account di archiviazione di destinazione associato al lab:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selezionare **Gruppi di risorse** dal pannello a sinistra.
1. Trovare e selezionare il gruppo di risorse associato al lab.
1. Nel pannello **Panoramica** selezionare uno degli account di archiviazione.
1. Selezionare **BLOB**.
1. Cercare i caricamenti nell'elenco. Se non ne esistono, tornare al passaggio n. 4 e provare con un altro account di archiviazione.
1. Usare l'**URL** come destinazione nel comando AzCopy.

### Cosa sono gli elementi? 
Gli elementi sono componenti personalizzabili che possono essere usati per distribuire i bit più recenti o gli strumenti di sviluppo in una VM. Vengono collegati alla VM durante la creazione con pochi semplici clic e, una volta effettuato il provisioning della VM, gli elementi distribuiscono e configurano la VM. Nel [repository GitHub pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) sono disponibili alcuni elementi preesistenti, ma è anche possibile [creare i propri elementi](devtest-lab-artifact-author.md) facilmente.

### Come si crea un lab da un modello di Azure Resource Manager? 
Esiste un [repository GitHub di modelli di Azure Resource Manager di lab](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates). Ogni modello ha un collegamento su cui è possibile fare clic per distribuire il lab di DevTest Labs nella sottoscrizione di Azure.
 
### Perché tutte le VM vengono create in gruppi di risorse diversi con nomi arbitrari? È possibile rinominare o modificare questi gruppi di risorse? 
I gruppi di risorse vengono creati in questo modo per consentire a DevTest Labs di gestire le autorizzazioni utente e di accedere alle macchine virtuali. Sebbene sia possibile spostare la VM in un altro gruppo di risorse con il nome desiderato, non è consigliabile eseguire questa operazione. Stiamo lavorando per migliorare questa esperienza e garantire maggiore flessibilità.
 
### Quanti lab è possibile creare nella stessa sottoscrizione? 
Non esiste un limite specifico al numero di lab che possono essere creati per ogni sottoscrizione, ma le risorse usate sono limitate per ogni sottoscrizione. Sono disponibili informazioni sui [limiti e sulle quote imposte alle sottoscrizioni di Azure](../azure-subscription-service-limits.md) e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).
 
### Quante VM è possibile creare per ogni lab? 
Non esiste un limite specifico al numero di VM che è possibile creare per ogni lab, ma attualmente il lab supporta solo circa 40 VM in esecuzione contemporaneamente nell'archiviazione Standard e 25 VM in esecuzione simultaneamente nell'archiviazione Premium. Microsoft sta lavorando all'aumento di queste limitazioni.
 
### Si è verificato un errore per l'elemento durante la creazione della VM. Come si risolve il problema? 
Vedere il post di blog [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Come risolvere gli errori degli elementi in Azure DevTest Labs), scritto da un MVP, per informazioni su come ottenere i log relativi all'elemento che ha generato l'errore.
 
### Perché la rete virtuale esistente non viene salvata correttamente?  
È possibile che il nome della rete virtuale contenga dei punti. In questo caso, provare a rimuovere i punti o a sostituirli con trattini e quindi provare a salvare di nuovo la rete virtuale.

<!---HONumber=AcomDC_0831_2016-->