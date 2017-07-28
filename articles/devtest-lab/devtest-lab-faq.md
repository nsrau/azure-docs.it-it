---
title: Domande frequenti su Azure DevTest Labs | Documentazione Microsoft
description: Consultare le risposte alle domande comuni su Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: e0adac5193ae412845c8413dfee6b5557096359a
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---
# <a name="azure-devtest-labs-faq"></a>Domande frequenti su Azure DevTest Labs
Questo articolo risponde ad alcune delle domande più frequenti relative ad Azure DevTest Labs.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Generale
* [Cosa fare se non è disponibile una risposta alla domanda?](#what-if-my-question-isnt-answered-here)
* [Perché usare Azure DevTest Labs?](#why-should-i-use-azure-devtest-labs)
* [Cosa significa "self-service senza preoccupazioni"?](#what-does-worry-free-self-service-mean)
* [Come si usa Azure DevTest Labs?](#how-can-i-use-azure-devtest-labs)
* [Come avviene la fatturazione di Azure DevTest Labs?](#how-am-i-billed-for-azure-devtest-labs)

## <a name="security"></a>Sicurezza
* [Quali sono i diversi livelli di sicurezza in Azure DevTest Labs?](#what-are-the-different-security-levels-in-azure-devtest-labs)
* [Come si crea un ruolo per consentire agli utenti di eseguire un'attività specifica?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task)

## <a name="cicd-integration--automation"></a>Integrazione e automazione CI/CD
* [Azure DevTest Labs si integra con la toolchain CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain)

## <a name="virtual-machines"></a>Macchine virtuali
* [Perché nel pannello Macchine virtuali di Azure non è possibile visualizzare alcune VM che vengono visualizzate in Azure DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs)
* [Qual è la differenza tra le immagini e le formule personalizzate?](#what-is-the-difference-between-custom-images-and-formulas)
* [Come si creano più VM dallo stesso modello contemporaneamente?](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
* [Come si spostano le VM di Azure esistenti nel lab Azure DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab)
* [È possibile collegare più dischi alle VM?](#can-i-attach-multiple-disks-to-my-vms)
* [Per usare un'immagine del sistema operativo per le operazioni di test è necessario acquistare un abbonamento a MSDN?](#if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription)
* [Come si automatizza il processo di caricamento dei file VHD per creare immagini personalizzate?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
* [Come è possibile automatizzare il processo di eliminazione di tutte le macchine virtuali nel lab?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)

## <a name="artifacts"></a>Elementi
* [Cosa sono gli elementi?](#what-are-artifacts)

## <a name="lab-configuration"></a>Configurazione del lab
* [Come si crea un lab da un modello di Azure Resource Manager?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template)
* [Perché le VM vengono create in gruppi di risorse diversi con nomi arbitrari? È possibile rinominare o modificare questi gruppi di risorse?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
* [Quanti lab è possibile creare nella stessa sottoscrizione?](#how-many-labs-can-i-create-under-the-same-subscription)
* [Quante VM è possibile creare per ogni lab?](#how-many-vms-can-i-create-per-lab)
* [Come è possibile condividere un collegamento diretto al mio lab?](#how-do-i-share-a-direct-link-to-my-lab)
* [Che cos'è un account Microsoft?](#what-is-a-microsoft-account)

## <a name="troubleshooting"></a>Risoluzione dei problemi
* [Si è verificato un errore per l'elemento durante la creazione della VM. Come si risolve il problema?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
* [Perché la rete virtuale esistente non viene salvata correttamente?](#why-isnt-my-existing-virtual-network-saving-properly)
* [Perché quando si esegue il provisioning da PowerShell viene visualizzato un errore del tipo "Risorsa padre non trovata"?](#why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell)  
* [Se la distribuzione di una VM ha esito negativo, dove è possibile trovare maggiori informazioni sul tipo di errore?](#where-can-i-find-more-error-information-if-a-vm-deployment-fails)  

### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto.

* Pubblicare una domanda nel [thread Disqus](#comments) alla fine del presente documento di FAQ e interagire con il team di Cache di Azure e altri membri della community in merito a questo articolo.
* Per raggiungere un pubblico maggiore, pubblicare una domanda sul [Forum MSDN di Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)e interagire con il team di Azure DevTest Labs e gli altri membri della community.
* Per eseguire una richiesta di funzionalità, inviare richieste e idee al servizio [Azure DevTest Labs UserVoice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Perché usare Azure DevTest Labs?
Azure DevTest Labs può far risparmiare tempo e denaro al team. Gli sviluppatori possono creare gli ambienti usando svariate basi diverse e usare gli elementi per distribuire e configurare rapidamente le applicazioni. Con le immagini e le formule personalizzate, le macchine virtuali possono essere salvate come modelli e riprodotte facilmente. I lab offrono anche diversi criteri configurabili che consentono agli amministratori di lab di ridurre gli sprechi e di gestire gli ambienti di un team. Questi criteri sono arresto automatico, soglia di costo, numero massimo di VM per utente e dimensioni massime delle VM. Per una spiegazione più approfondita di Azure DevTest Labs, vedere la [panoramica](devtest-lab-overview.md) o guardare il [video introduttivo](/documentation/videos/videos/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Cosa significa "self-service senza preoccupazioni"?
"Self-service senza preoccupazioni" significa che gli sviluppatori e i tester possono creare gli ambienti necessari e gli amministratori hanno la certezza che Azure DevTest Labs consente di ridurre al minimo gli sprechi e di controllare i costi. Gli amministratori possono specificare quali sono le dimensioni consentite per le VM e il numero massimo di VM e quando le VM vengono avviate e arrestate. Azure DevTest Labs consente anche di monitorare facilmente i costi e di impostare avvisi per sapere quante risorse sono in uso nel lab.

### <a name="how-can-i-use-azure-devtest-labs"></a>Come si usa Azure DevTest Labs?
Azure DevTest Labs è utile quando sono necessari ambienti di sviluppo o di testing e si vuole riprodurli rapidamente e/o gestirli con criteri di risparmio sui costi.

Ecco alcuni scenari per cui i clienti usano Azure DevTest Labs:

* Gestione centralizzata degli ambienti di sviluppo e di testing, utilizzando criteri per ridurre i costi e immagini personalizzate per condividere le build con il team.
* Sviluppo di un'applicazione con le immagini personalizzate per salvare lo stato del disco durante le fasi di sviluppo.
* Verifica dei costi in relazione alla stato.
* Creazione di ambienti di testing di massa per i test di controllo di qualità.
* Uso di elementi e formule per configurare e riprodurre facilmente un'applicazione in svariati ambienti.
* Distribuzione di VM per gli hackathon (attività di test o sviluppo collaborative) e facile deprovisioning al termine dell'evento.

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Come avviene la fatturazione di Azure DevTest Labs?
Azure DevTest Labs è un servizio gratuito e quindi la creazione dei lab e la configurazione di criteri, modelli ed elementi sono gratuite. Si pagano solo le risorse di Azure usate nei lab, ad esempio macchine virtuali, account di archiviazione e reti virtuali. Per altre informazioni sui costi delle risorse dei lab, vedere [Prezzi di Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quali sono i diversi livelli di sicurezza in Azure DevTest Labs?
L'accesso sicuro è determinato dal [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-built-in-roles.md). Per comprendere il funzionamento dell'accesso, è utile conoscere le differenze tra un'autorizzazione, un ruolo e un ambito definiti dal Controllo degli accessi in base al ruolo.

* **Autorizzazione** : un'autorizzazione è un accesso definito a un'azione specifica. Ad esempio, un'autorizzazione può essere l'accesso in lettura a tutte le macchine virtuali.
* **Ruolo** : un ruolo è un set di autorizzazioni che possono essere raggruppate e assegnate a un utente. Ad esempio, un "proprietario della sottoscrizione" ha accesso a tutte le risorse all'interno di una sottoscrizione.
* **Ambito** : un ambito è un livello nella gerarchia di risorse di Azure. Ad esempio, un ambito può essere un gruppo di risorse, un unico lab oppure l'intera sottoscrizione.

Nell'ambito di Azure DevTest Labs, esistono due tipi di ruoli per definire le autorizzazioni utente: proprietario del lab e utente del lab.

* **Proprietario del lab** : un proprietario del lab ha accesso a tutte le risorse all'interno del lab. Può quindi modificare i criteri, leggere e scrivere nelle VM, modificare la rete virtuale e così via.
* **Utente del lab** : può visualizzare tutte le risorse del lab, ad esempio VM, criteri e reti virtuali, ma non può modificare i criteri o le VM create da altri utenti. È anche possibile creare ruoli personalizzati in Azure DevTest Labs, come illustrato nell'articolo [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Poiché gli ambiti sono gerarchici, quando un utente ha le autorizzazioni per un determinato ambito, gli vengono automaticamente concesse tali autorizzazioni per ogni ambito di livello inferiore incluso. Ad esempio, se un utente è assegnato al ruolo di proprietario della sottoscrizione, ha accesso a tutte le risorse in una sottoscrizione. Le risorse includono tutte le macchine virtuali, tutte le reti virtuali e tutti i lab. Il proprietario di una sottoscrizione quindi eredita automaticamente il ruolo di proprietario del lab, ma non il contrario. Il proprietario di un lab ha accesso a un lab, che è un ambito più basso del livello della sottoscrizione. Il proprietario di un lab quindi non può visualizzare le macchine virtuali, le reti virtuali o nessuna altra risorsa esterna al lab.

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Come si crea un ruolo per consentire agli utenti di eseguire un'attività specifica?
Un articolo completo su come creare i ruoli personalizzati e assegnare le autorizzazioni è disponibile qui. Ecco un esempio di script che crea il ruolo "DevTest Labs Advanced User", con l'autorizzazione per avviare e arrestare tutte le VM del lab:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advance User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  

### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs si integra con la toolchain CI/CD?
Se si usa VSTS, è disponibile un' [estensione Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) che consente di automatizzare la pipeline di tipo Versione in Azure DevTest Labs. Alcuni degli usi di questa estensione includono:

* Creazione e distribuzione automatica di una VM e configurazione con la build più recente usando la copia dei file di Azure o le attività VSTS di PowerShell.
* Acquisizione automatica dello stato di una VM dopo il test per riprodurre un bug nella stessa VM per altre indagini.
* Eliminazione della VM alla fine della pipeline di tipo Versione quando non è più necessaria.

I post di blog seguenti forniscono indicazioni e informazioni sull'uso dell'estensione VSTS:

* [Azure DevTest Labs – VSTS extension (Azure DevTest Labs: estensione VSTS)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Deploying a new VM in an existing AzureDevTestLab from VSTS (Distribuzione di una nuova VM in un'istanza di Azure DevTest Labs da VSTS)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (Uso di Release Management per VSTS per le distribuzioni continue in Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Per le altre toolchain CI/CD, tutti gli scenari sopra indicati che possono essere realizzati tramite le estensioni per VSTS Tasks possono essere realizzati in modo simile tramite la distribuzione di [modelli di Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) con i [cmdlet di Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e gli [SDK di .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). È anche possibile usare le [API REST per DevTest Labs](http://aka.ms/dtlrestapis) per l'integrazione con la toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Perché nel pannello Macchine virtuali di Azure non è possibile visualizzare alcune VM che vengono visualizzate in Azure DevTest Labs?
Quando si crea una VM in Azure DevTest Labs, viene fornita l'autorizzazione ad accedervi, visualizzabile nei pannelli dei lab e delle **Virtual Machines** (Macchine virtuali). Gli utenti con ruolo DevTest Labs possono visualizzare tutte le macchine virtuali create nel lab tramite il pannello **All Virtual Machines** (Tutte le macchine virtuali) del lab. Tuttavia, agli utenti con ruolo DevTest Labs non viene concesso automaticamente l'accesso in lettura a risorse delle VM create da altri. Pertanto, queste VM non vengono visualizzate nel pannello **Virtual machines** (Macchine virtuali).

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Qual è la differenza tra le immagini e le formule personalizzate?
Un'immagine personalizzata è un disco rigido virtuale, mentre una formula è un'immagine che è possibile configurare con impostazioni aggiuntive che si possono salvare e riprodurre. Un'immagine personalizzata può essere preferibile se si vogliono creare rapidamente più ambienti con la stessa immagine di base non modificabile. Una formula può essere migliore se si vuole riprodurre la configurazione della VM con i bit più recenti, una rete virtuale/subnet o una dimensione specifica. Per una spiegazione più approfondita, vedere l'articolo [Confronto tra immagini e formule personalizzate in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Come si creano più VM dallo stesso modello contemporaneamente?
È possibile usare l'[estensione per VSTS Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) o [generare un modello di Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) durante la creazione di una VM e [distribuire il modello di Azure Resource Manager da Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Come si spostano le VM di Azure esistenti nel lab Azure DevTest Labs?
Per copiare le VM esistenti in Azure DevTest Labs, seguire questa procedura:

1. Copiare il file VHD della VM esistente usando questo [script di Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)
2. [Creare l'immagine personalizzata](devtest-lab-create-template.md) nel lab di Azure DevTest Labs.
3. Creare una VM nel lab dall'immagine personalizzata.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>È possibile collegare più dischi alle VM?
Il collegamento di più dischi alle VM è supportato.  

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Per usare un'immagine del sistema operativo per le operazioni di test è necessario acquistare un abbonamento a MSDN?
Sì, se si vuole usare immagini del sistema operativo client (Windows 7 o versioni successive) per operazioni di sviluppo o test in Azure. In questo caso, è necessario:

- [Acquistare un abbonamento a MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Creare una sottoscrizione di Azure con l'[offerta Sviluppo/test Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0148p) (se si possiede un contratto Enterprise).

Per altre informazioni sui crediti Azure per ogni offerta MSDN, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Come si automatizza il processo di caricamento dei file VHD per creare immagini personalizzate?
Sono disponibili due opzioni:

* [AzCopy per Azure](../storage/storage-use-azcopy.md#blob-upload) per copiare o caricare i file VHD nell'account di archiviazione associato al lab.
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che esegue Windows, OSX e Linux.   

Per trovare l'account di archiviazione di destinazione associato al lab, seguire questa procedura:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Gruppi di risorse** dal pannello a sinistra.
3. Trovare e selezionare il gruppo di risorse associato al lab.
4. Nel pannello **Panoramica** selezionare uno degli account di archiviazione.
5. Selezionare **BLOB**.
6. Cercare i caricamenti nell'elenco. Se non ne esistono, tornare al passaggio n. 4 e provare con un altro account di archiviazione.
7. Usare l' **URL** come destinazione nel comando AzCopy.

### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Come è possibile automatizzare il processo di eliminazione di tutte le macchine virtuali nel lab?
Per eliminare le macchine virtuali presenti nel lab, è possibile usare sia il portale di Azure sia uno script di PowerShell. Nell'esempio seguente, modificare i valori dei parametri nel commento **Values to change** (Valori da modificare). È possibile recuperare i valori `subscriptionId`, `labResourceGroup` e `labName` dal pannello Lab nel portale di Azure.

    # Delete all the VMs in a lab

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>Cosa sono gli elementi?
Gli elementi sono componenti personalizzabili che possono essere usati per distribuire i bit più recenti o gli strumenti di sviluppo in una VM. Vengono collegati alla VM durante la creazione con pochi semplici clic e, una volta effettuato il provisioning della VM, gli elementi distribuiscono e configurano la VM. Nell'[archivio GitHub pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) sono disponibili svariati elementi preesistenti, ma è anche possibile [creare i propri elementi](devtest-lab-artifact-author.md) facilmente.

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Come si crea un lab da un modello di Azure Resource Manager?
Microsoft ha fornito un [archivio GitHub di modelli di Azure Resource Manager per lab](https://aka.ms/dtlquickstarttemplate) che è possibile distribuire così com'è oppure modificare per creare modelli personalizzati per i propri lab. Ciascuno di questi modelli offre un collegamento su cui è possibile fare clic per distribuire il lab così com'è nella propria sottoscrizione di Azure o per personalizzare il modello e [distribuirlo tramite PowerShell o l'interfaccia della riga di comando di Aure](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Perché le VM vengono create in gruppi di risorse diversi con nomi arbitrari? È possibile rinominare o modificare questi gruppi di risorse?
I gruppi di risorse vengono creati in questo modo per consentire ad Azure DevTest Labs di gestire le autorizzazioni utente e di accedere alle macchine virtuali. Sebbene sia possibile spostare la VM in un altro gruppo di risorse con il nome desiderato, non è consigliabile farlo. Stiamo lavorando per migliorare questa esperienza e garantire maggiore flessibilità.   

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quanti lab è possibile creare nella stessa sottoscrizione?
Non esiste un limite specifico al numero di lab che possono essere creati per ogni sottoscrizione. Tuttavia, le risorse usate sono limitate per ogni sottoscrizione. Sono disponibili informazioni sui [limiti e sulle quote imposte alle sottoscrizioni](../azure-subscription-service-limits.md) di Azure e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-many-vms-can-i-create-per-lab"></a>Quante VM è possibile creare per ogni lab?
Non esiste un limite specifico al numero di VM che possono essere create per ogni lab. Tuttavia, le risorse usate sono limitate per ogni sottoscrizione, ad esempio, core della macchina virtuale, indirizzi IP pubblici e così via. Sono disponibili informazioni sui [limiti e sulle quote imposte alle sottoscrizioni](../azure-subscription-service-limits.md) di Azure e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Come è possibile condividere un collegamento diretto al mio lab?
Per condividere un collegamento diretto con gli utenti del lab, è possibile seguire questa procedura:

1. Cercare il lab nel portale di Azure.
2. Copiare l'URL del lab dal browser e condividerlo con gli utenti del lab.

> [!NOTE]
> Se gli utenti del lab sono esterni e dispongono di un [account Microsoft](#what-is-a-microsoft-account), ma non appartengono ad Active Directory aziendale, è possibile che ricevano un errore durante la navigazione verso il collegamento specificato. In questo caso, indicare loro di fare clic sul proprio nome nell'angolo in alto a destra del portale di Azure e selezionare la directory in cui è presente il lab dalla sezione **Directory** del menu.
>
>

### <a name="what-is-a-microsoft-account"></a>Che cos'è un account Microsoft?
Un account Microsoft è ciò che si usa per quasi tutte le operazioni eseguite con servizi e dispositivi di Microsoft. È composto da un indirizzo di posta elettronica e una password usati per accedere a Skype, Outlook.com, OneDrive, Windows Phone e Xbox LIVE. Grazie a questo account, file, foto, contatti e impostazioni possono seguire l'utente su qualsiasi dispositivo.

> [!NOTE]
> In precedenza, l'account Microsoft veniva denominato "Windows Live ID".
>
>

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Si è verificato un errore per l'elemento durante la creazione della VM. Come si risolve il problema?
Fare riferimento al post di blog [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Come risolvere gli errori degli elementi in Azure DevTest Labs), scritto da un MVP, per informazioni su come ottenere i log relativi all'elemento che ha generato l'errore.

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Perché la rete virtuale esistente non viene salvata correttamente?
È possibile che il nome della rete virtuale contenga dei punti. In questo caso, provare a rimuovere i punti o a sostituirli con trattini e quindi provare a salvare di nuovo la rete virtuale.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-provisioning-a-vm-from-powershell"></a>Perché quando si esegue il provisioning di una macchina virtuale da PowerShell viene visualizzato un errore del tipo "Risorsa padre non trovata"?
Quando una risorsa è l'elemento padre di un'altra risorsa, deve già esistere prima di creare la risorsa figlio. Se non esiste ancora, viene visualizzato l'errore **ParentResourceNotFound**. Se non si specifica una dipendenza dalla risorsa padre, la risorsa figlio può essere distribuita prima dell'elemento padre.

Le macchine virtuali sono risorse figlio di un lab in un gruppo di risorse. Quando si usano modelli di Azure Resource Manager per eseguire una distribuzione tramite PowerShell, il nome del gruppo di risorse specificato nello script di PowerShell deve coincidere con quello del gruppo di risorse del lab. Per altre informazioni, vedere [Risolvere errori comuni durante la distribuzione in Azure ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Se la distribuzione di una macchina virtuale ha esito negativo, dove è possibile trovare maggiori informazioni sul tipo di errore?
Gli errori che si verificano durante la distribuzione di una macchina virtuale vengono acquisiti nei log attività. È possibile trovare i log attività delle macchine virtuali del lab tramite la voce **Log di controllo** o **Diagnostica macchina virtuale** del menu di risorse presente nel pannello della macchina virtuale del lab (il pannello viene visualizzato dopo aver selezionato la macchina virtuale dall'elenco **Macchine virtuali**).

In alcuni casi, l'errore di distribuzione si verifica prima dell'avvio della distribuzione della macchina virtuale, ad esempio se viene superato il limite di sottoscrizioni per una risorsa creata con la macchina virtuale. In questo caso, i dettagli dell'errore vengono acquisiti nei **Log attività** a livello di lab, disponibili nella parte inferiore delle impostazioni **Configuration and policies** (Configurazione e criteri). Per altre informazioni sull'utilizzo dei log attività in Azure, vedere [Visualizzare i log attività per controllare le azioni sulle risorse](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit).

