---
title: Domande frequenti su Azure DevTest Labs | Documentazione Microsoft
description: Questo articolo fornisce le risposte ad alcune delle domande frequenti su Azure DevTest Labs.This article provides answers to some of the frequently asked questions (FAQ) about Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270784"
---
# <a name="azure-devtest-labs-faq"></a>Domande frequenti su Azure DevTest Labs
Ottenere le risposte ad alcune delle domande più comuni relative ad Azure DevTest Labs.

## <a name="blog-post"></a>Post di blog
Il nostro blog del team DevTest Labs è stato ritirato a partire dal 20 marzo 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Dove posso tenere traccia degli aggiornamenti delle funzionalità d'ora in ora in tanto?
D'ora in poi pubblicheremo aggiornamenti delle funzionalità e post di blog informativi nel blog di Azure e negli aggiornamenti di Azure.From now on, we'll be posting feature updates and informative blog posts on the Azure blog and Azure updates. Questi post del blog verranno anche collegati alla nostra documentazione ovunque sia necessario.

Sottoscrivere gli aggiornamenti di [Azure](https://azure.microsoft.com/updates/?product=devtest-lab) [Di DevTest Labs di Azure](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) per essere sempre informati sulle nuove funzionalità nei laboratori DevTest.

### <a name="what-happens-to-the-existing-blog-posts"></a>Cosa succede ai post di blog esistenti?
Attualmente stiamo lavorando alla migrazione di post di blog esistenti (esclusi gli aggiornamenti di interruzione) alla documentazione di [DevTest Labs.](devtest-lab-overview.md) Quando il blog MSDN è deprecato, verrà reindirizzato alla documentazione fornita panoramica per DevTest Labs. Una volta reindirizzato, puoi cercare l'articolo che stai cercando nel titolo "Filtra per". Non abbiamo ancora eseguito la migrazione di tutti i post, ma dovrebbe essere fatto entro la fine di questo mese. 


### <a name="where-do-i-see-outage-updates"></a>Dove è possibile visualizzare gli aggiornamenti delle interruzioni?
Pubblicheremo aggiornamenti di interruzione utilizzando il nostro handle Twitter da ora in poi. Seguici su Twitter per ricevere gli ultimi aggiornamenti sulle interruzioni e i bug noti.

### <a name="twitter"></a>Twitter
Il nostro handle Twitter:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Generale
### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Se la tua domanda non è elencata qui, comunicacelo, in modo che possiamo aiutarti a trovare una risposta.

- Pubblicare una domanda alla fine di questo articolo di domande frequenti.
- Per raggiungere un gruppo di destinatari più ampio, pubblicare una domanda nel [forum MSDN di Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Interagire con il team di Azure DevTest Labs e altri membri della community.
- Per richiedere funzionalità, inviare le richieste e le idee al [sito User Voice per Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Che cos'è un account Microsoft?
Un account Microsoft è l'account che si usa per quasi tutte le attività eseguite con servizi e dispositivi Microsoft. Si tratta di un indirizzo di posta elettronica e di una password utilizzati per accedere a Skype, Outlook.com, OneDrive, Windows Phone, Azure e Xbox Live. Grazie a questo singolo account, file, foto, contatti e impostazioni possono seguire l'utente su qualsiasi dispositivo.

> [!NOTE]
> In precedenza, l'account Microsoft era detto Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Perché usare Azure DevTest Labs?
Azure DevTest Labs può far risparmiare tempo e denaro al team. Gli sviluppatori possono creare ambienti personalizzati usando svariate basi diverse. Possono anche usare gli elementi per distribuire e configurare rapidamente le applicazioni. Usando formule e immagini personalizzate, è possibile salvare le macchine virtuali come modelli e riprodurle facilmente nel team. DevTest Labs offre anche diversi criteri configurabili che consentono agli amministratori di lab di ridurre gli sprechi e di gestire gli ambienti di un team. Questi criteri includono l'arresto automatico, la soglia di costo, il numero massimo di macchine virtuali per utente e le dimensioni massime delle macchine virtuali. Per una spiegazione più approfondita di DevTest Labs, vedere la [panoramica](devtest-lab-overview.md) o guardare il [video introduttivo](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Cosa significa "self-service senza preoccupazioni"?
Self-service senza preoccupazioni significa che gli sviluppatori e i tester creano i propri ambienti in base alle esigenze. Gli amministratori hanno la sicurezza di sapere che DevTest Labs può aiutare a impostare l'accesso appropriato, ridurre al minimo gli sprechi e i costi di controllo. Gli amministratori possono specificare quali sono le dimensioni consentite per le VM e il numero massimo di VM e quando le VM vengono avviate e arrestate. DevTest Labs consente anche di monitorare facilmente i costi e di impostare avvisi per sapere come vengono usate le risorse del lab.

### <a name="how-can-i-use-devtest-labs"></a>Come si usa DevTest Labs?
DevTest Labs è utile ogni volta che sono necessari ambienti di sviluppo o test e si desidera riprodurli rapidamente o gestirli utilizzando criteri di riduzione dei costi.
Ecco alcuni scenari per cui i clienti usano DevTest Labs:

- Gestire ambienti di sviluppo e test da un'unica posizione. Usare criteri per ridurre i costi e creare immagini personalizzate per condividere le build tra i membri del team.
- Sviluppare un'applicazione usando immagini personalizzate per salvare lo stato del disco durante le fasi di sviluppo.
- Tenere traccia dei costi in relazione allo stato.
- Creare ambienti di test di massa per i test di controllo di qualità.
- Usare elementi e formule per configurare e riprodurre facilmente un'applicazione in diversi ambienti.
- Distribuire le macchine virtuali per gli hackathon (attività di sviluppo o test collaborative) e quindi effettuare facilmente il deprovisioning al termine dell'evento.

### <a name="how-am-i-billed-for-devtest-labs"></a>Come avviene la fatturazione di DevTest Labs?
DevTest Labs è un servizio gratuito. La creazione di lab e la configurazione di criteri, modelli ed elementi in DevTest Labs sono gratuite. Si pagano solo le risorse di Azure usate nei lab, ad esempio macchine virtuali, account di archiviazione e reti virtuali. Per altre informazioni sui costi delle risorse dei lab, vedere [Prezzi di Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Security

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quali sono i diversi livelli di sicurezza in DevTest Labs?
L'accesso sicuro è determinato dal controllo degli accessi in base al ruolo. Per comprendere il funzionamento dell'accesso, è utile conoscere le differenze tra un'autorizzazione, un ruolo e un ambito, come definiti dal controllo degli accessi in base al ruolo.

- **Autorizzazione**: un'autorizzazione è un accesso definito per un'azione specifica. Un'autorizzazione può ad esempio essere l'accesso in lettura a tutte le macchine virtuali.
- **Ruolo**: un ruolo è un set di autorizzazioni che possono essere raggruppate e assegnate a un utente. Un utente con il ruolo di proprietario della sottoscrizione ha ad esempio accesso a tutte le risorse all'interno di una sottoscrizione.
- **Ambito**: un ambito è un livello nella gerarchia di una risorsa di Azure. Un ambito può ad esempio essere un gruppo di risorse, un singolo lab oppure l'intera sottoscrizione.

Nell'ambito di DevTest Labs, ci sono due tipi di ruoli che definiscono le autorizzazioni utente:

- **Proprietario del lab**: un proprietario del lab ha accesso a tutte le risorse all'interno del lab. Un proprietario del lab può modificare i criteri, leggere e scrivere in qualsiasi macchina virtuale, modificare la rete virtuale e così via.
- **Utente del lab**: un utente del lab può visualizzare tutte le risorse del lab, ad esempio macchine virtuali, criteri e reti virtuali. Tuttavia, un utente del lab non può modificare i criteri o le macchine virtuali create da altri utenti.

È anche possibile creare ruoli personalizzati in DevTest Labs. Per informazioni su come creare ruoli personalizzati in DevTest Labs, vedere [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Poiché gli ambiti sono gerarchici, quando un utente ha le autorizzazioni per un determinato ambito, gli vengono automaticamente concesse tali autorizzazioni per ogni ambito di livello inferiore. Se ad esempio a un utente è assegnato il ruolo di proprietario della sottoscrizione, l'utente ha accesso a tutte le risorse in una sottoscrizione. Queste risorse includono macchine virtuali, reti virtuali e lab. Il proprietario di una sottoscrizione eredita automaticamente il ruolo di proprietario del lab, ma non il contrario. Il proprietario di un lab ha accesso a un lab, che è un ambito più basso del livello della sottoscrizione. Pertanto, il proprietario di un lab non può visualizzare macchine virtuali, reti virtuali o altre risorse esterne al lab.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Come definisco il controllo degli accessi in base al ruolo per gli ambienti DevTest Labs al fine di garantire che l'IT possa governare mentre gli sviluppatori/i test possono svolgere il proprio lavoro?
Esiste un criterio ampio, tuttavia i dettagli dipendono dalla propria organizzazione.

L'IT centrale deve essere proprietario solo di ciò che è necessario e consentire ai team di progetto e applicazione di avere il livello di controllo necessario. In genere significa che l'IT centrale possiede la sottoscrizione e gestisce le funzioni IT di base come le configurazioni di rete. Il set di **proprietari** per una sottoscrizione deve essere di piccole dimensioni. Questi proprietari possono nominare proprietari aggiuntivi in caso di necessità o applicare criteri a livello di sottoscrizione, ad esempio "Nessun IP pubblico".

Un sottoinsieme di utenti potrebbe richiedere l'accesso a una sottoscrizione, ad esempio il supporto di Livello 1 o 2. In questo caso, si consiglia di concedere a questi utenti l'accesso **collaboratore** in modo che possano gestire le risorse, ma non fornire l'accesso utente o modificare i criteri.

La risorsa di DevTest Labs deve appartenere a proprietari vicini al team di progetto/applicazione. È perché capiscono i loro requisiti per le macchine e il software richiesto. Nella maggior parte delle organizzazioni, il proprietario della risorsa di DevTest Labs è in genere il responsabile del progetto/sviluppo. Questo proprietario può gestire gli utenti e i criteri all'interno dell'ambiente lab, nonché tutte le macchine virtuali nell'ambiente di DevTest Labs.

I membri del team di progetto/applicazione devono essere aggiunti al ruolo **DevTest Labs Users.** Questi utenti possono creare macchine virtuali (in linea con i criteri a livello di sottoscrizione e lab). Possono anche gestire le proprie macchine virtuali. Non possono gestire le macchine virtuali che appartengono ad altri utenti.

Per altre informazioni, vedere [lo scaffold di Azure Enterprise e la documentazione sulla governance delle sottoscrizioni prescrittiva.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Come si crea un ruolo per consentire agli utenti di eseguire un'attività specifica?
Per un articolo completo su come creare ruoli personalizzati e assegnare le autorizzazioni a un ruolo, vedere [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Ecco un esempio di script che crea il ruolo di utente avanzato di DevTest Labs (**DevTest Labs Advanced User**) che ha l'autorizzazione per avviare e arrestare tutte le macchine virtuali del lab:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>In che modo un'organizzazione può garantire l'applicazione di criteri di sicurezza aziendali?
Un'organizzazione può ottenere questo risultato eseguendo queste azioni:

- Sviluppo e pubblicazione di criteri di sicurezza completi. I criteri enunciano le regole di utilizzo accettabile associate all'uso di software, asset cloud. Definiscono anche le condizioni che violano i criteri.
- Sviluppo di un'immagine personalizzata, di artefatti personalizzati e di un processo di distribuzione che consente l'orchestrazione nel campo della sicurezza definito con Active Directory. Questo approccio applica limiti aziendali e imposta un set comune di controlli nell'ambiente. Questi sono controlli nell'ambiente che uno sviluppatore può prendere in considerazione mentre sviluppa e segue un ciclo di vita di sviluppo sicuro come parte del processo complessivo. L'obiettivo è anche quello di fornire un ambiente che non è eccessivamente restrittivo che può ostacolare lo sviluppo, ma un insieme ragionevole di controlli. I criteri di gruppo nell'unità organizzativa che contiene macchine virtuali lab possono essere un sottoinsieme dei criteri di gruppo totali che si trovano nell'ambiente di produzione. Possono altrimenti rappresentare un set aggiuntivo per attenuare adeguatamente eventuali rischi identificati.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>In che modo un'organizzazione può garantire l'integrità dei dati per assicurare che gli sviluppatori in remoto non rimuovano codice o introducano malware o software non approvato?
Esistono diversi livelli di controllo per attenuare la minaccia che può sorgere da consulenti esterni, terzisti o dipendenti che collaborano in DevTest Labs in remoto.

Come indicato in precedenza, il primo passaggio deve avere criteri di utilizzo accettabile redatti e definiti che descrivono chiaramente le conseguenze quando un utente li viola.

Il primo livello di controlli per l'accesso remoto consiste nell'applicare un criterio di accesso remoto tramite una connessione VPN non connessa direttamente al lab.

Il secondo livello di controlli consiste nell'applicare un set di oggetti criteri di gruppo che impediscono le operazioni di copia e incolla tramite un desktop remoto. È possibile implementare criteri di rete per non consentire servizi in uscita dall'ambiente, ad esempio i servizi FTP e RDP esterni all'ambiente. Il routing definito dall'utente potrebbe forzare il traffico di rete di Azure in locale, ma il routing non è stato in grado di tenere conto di tutti gli URL che potrebbero consentire il caricamento dei dati, a meno che non sia controllato tramite un proxy per l'analisi del contenuto e delle sessioni. Gli indirizzi IP pubblici possono essere limitati all'interno della rete virtuale che supporta DevTest Labs per non consentire il bridging di una risorsa di rete esterna.

In definitiva, lo stesso tipo di restrizioni deve essere applicato all'interno dell'organizzazione, il che tiene conto di tutti i possibili metodi di supporti rimovibili o URL esterni che potrebbero accettare un post di contenuto. Consultare i propri esperti di sicurezza per esaminare e implementare i criteri di sicurezza. Per altre raccomandazioni, vedere il sito sulla [Sicurezza informatica di Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configurazione del lab

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Come si crea un lab da un modello di Resource Manager?
È disponibile un [repository GitHub di modelli di Azure Resource Manager per lab](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) che è possibile distribuire così come sono oppure modificare per creare modelli personalizzati per i propri lab. Ogni modello ha un collegamento per distribuire il lab così com'è nella propria sottoscrizione di Azure.Each template has a link to deploy the lab as it's in your own Azure subscription. In alternativa, è possibile personalizzare il modello ed [eseguire la distribuzione con PowerShell o l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>È possibile creare tutte le macchine virtuali in un gruppo di risorse comune invece che ogni macchina sia nel proprio gruppo di risorse?
Sì, in qualità di proprietario del lab, è possibile lasciare che il lab gestisca automaticamente l'allocazione del gruppo di risorse o creare tutte le macchine virtuali in un gruppo di risorse comune specificato.

Scenario di gruppo di risorse separato:Separate resource group scenario:
-   DevTest Labs crea un nuovo gruppo di risorse per ogni macchina virtuale IP pubblica/privata che si configura
-   DevTest Labs crea un gruppo di risorse per i computer IP condivisi che appartengono alla stessa dimensione.

Scenario di gruppo di risorse comune:Common resource group scenario:
-   Tutte le macchine virtuali vengono riunite nel gruppo di risorse comune specificato. Ulteriori informazioni [sull'allocazione](https://aka.ms/RGControl)dei gruppi di risorse per il lab .

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Come è possibile gestire una convenzione di denominazione nell'ambiente di DevTest Labs?
È possibile estendere le convenzioni di denominazione correnti dell'organizzazione alle operazioni di Azure e renderle coerenti in tutto l'ambiente DevTest Labs. In fase di distribuzione di DevTest Labs è consigliabile avere criteri iniziali specifici. Questi criteri vengono distribuiti in base a uno script centrale e a modelli JSON per garantire la coerenza. I criteri di denominazione possono essere implementati tramite i criteri di Azure applicati a livello di sottoscrizione. Per esempi JSON per Criteri di Azure, vedere [Esempi di Criteri di Azure](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quanti lab è possibile creare nella stessa sottoscrizione?
Non c'è un limite specifico al numero di lab che è possibile creare per ogni sottoscrizione. È tuttavia previsto un limite per la quantità di risorse usate per ogni sottoscrizione. Sono disponibili informazioni su [limiti e quote per le sottoscrizioni di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Quante VM è possibile creare per ogni lab?
Non esiste un limite specifico al numero di VM che possono essere create per ogni lab. È tuttavia previsto un limite per le risorse (core delle macchine virtuali, indirizzi IP pubblici e così via) usate per ogni sottoscrizione. Sono disponibili informazioni su [limiti e quote per le sottoscrizioni di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Come è possibile determinare il rapporto tra gli utenti per laboratorio e il numero complessivo di lab necessari in un'organizzazione?
È consigliabile che le business unit e i gruppi di sviluppo dello stesso progetto di sviluppo siano associati allo stesso lab. In questo modo è possibile applicare a entrambi i gruppi gli stessi tipi di criteri, immagini e criteri di arresto.

Potrebbe anche essere necessario considerare i limiti geografici. Ad esempio, gli sviluppatori negli Stati Uniti del Nord Est (Stati Uniti) possono utilizzare un laboratorio di cui è stato eseguito il provisioning negli Stati Uniti orientali2.For example, developers in the North East United States (US) may use a lab provisioned in East US2. E gli sviluppatori a Dallas, in Texas, e a Denver, in Colorado, potrebbero essere indirizzati all'uso di una risorsa negli Stati Uniti centro-meridionali. In caso di un'attività collaborativa con una terza parte esterna, quest'ultima potrebbe essere assegnata a un lab non usato dagli sviluppatori interni.

È anche possibile usare un lab per un progetto specifico all'interno di progetti DevOps di Azure.You may also use a lab for a specific project within Azure DevOps Projects. In questo caso, applicare la sicurezza tramite un gruppo di Azure Active Directory specificato, che consente l'accesso a entrambi i set di risorse. La rete virtuale assegnata al lab può essere un altro limite al consolidamento degli utenti.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Come è possibile impedire l'eliminazione delle risorse all'interno di un lab?
È consigliabile impostare le autorizzazioni appropriate a livello di lab in modo che solo gli utenti autorizzati possano eliminare le risorse o modificare i criteri del lab. Gli sviluppatori devono trovarsi all'interno del gruppo di **utenti DevTest Labs**. Il responsabile degli sviluppatori o dell'infrastruttura deve essere il **proprietario di DevTest Labs**. È consigliabile avere solo due proprietari di lab. Questi criteri si estendono al repository di codice per evitare il danneggiamento. Gli utenti del lab dispongono dei diritti per l'utilizzo delle risorse, ma non possono aggiornare i criteri lab. Vedere l'articolo seguente che elenca i ruoli e i diritti di ogni gruppo incorporato all'interno di un lab: [Aggiungere proprietari e utenti in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Come è possibile condividere un collegamento diretto al mio lab?

1. Nel [portale di Azure](https://portal.azure.com)passare al lab.
2. Copiare **l'URL** del lab dal browser e quindi condividerlo con gli utenti del lab.

> [!NOTE]
> Se un utente del lab è un utente esterno con un account Microsoft, ma che non è membro dell'istanza di Active Directory dell'organizzazione, potrebbe venire visualizzato un messaggio di errore quando l'utente cerca di accedere al collegamento condiviso. In questo caso, chiedere all'utente di selezionare prima di tutto il proprio nome nell'angolo superiore destro del portale di Azure. Quindi, nella sezione Directory del menu, l'utente può selezionare la directory in cui si trova il lab.

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Perché non è possibile visualizzare le macchine virtuali nella pagina Macchine virtuali visualizzata in DevTest Labs?
Quando si crea una macchina virtuale in DevTest Labs, viene concessa l'autorizzazione per accedere a tale macchina virtuale. È possibile visualizzare la macchina virtuale sia nella pagina dei lab che nella pagina **Macchine virtuali.** Gli utenti assegnati al ruolo **proprietario di DevTest Labs** possono visualizzare tutte le macchine virtuali create nel lab nella pagina Tutte le macchine **virtuali** del lab. Tuttavia, agli utenti che dispongono del ruolo **utente DevTest Labs** non viene concesso automaticamente l'accesso in lettura alle risorse della macchina virtuale create da altri utenti. Pertanto, tali macchine virtuali non vengono visualizzate nella pagina **Macchine virtuali.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Come si creano più VM dallo stesso modello contemporaneamente?
Ci sono due opzioni per creare simultaneamente più macchine virtuali dallo stesso modello:

- È possibile usare l'[estensione Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- È possibile [generare un modello](devtest-lab-add-vm.md#save-azure-resource-manager-template) di Resource Manager durante la creazione di una macchina virtuale e [distribuire il modello Gestione risorse da Windows PowerShell.](../azure-resource-manager/templates/deploy-powershell.md)
- È inoltre possibile specificare più istanze di una macchina da creare durante la creazione della macchina virtuale. Per altre informazioni sulla creazione di più istanze di macchine virtuali, vedere il documento sulla creazione di [una macchina virtuale lab.](devtest-lab-add-vm.md)

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Come si spostano le macchine virtuali di Azure esistenti nel lab di DevTest Labs?
Per copiare le macchine virtuali esistenti in DevTest Labs:

1.  Copiare il file VHD della macchina virtuale esistente usando uno [script di Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Creare [l'immagine personalizzata](devtest-lab-create-template.md) all'interno del lab DevTest Labs.Create the custom image inside your DevTest Labs lab.
3.  Creare una macchina virtuale nel lab dall'immagine personalizzata.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>È possibile collegare più dischi alle VM?

Sì, è possibile collegare più dischi alle macchine virtuali.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Per usare un'immagine del sistema operativo per le operazioni di test è necessario acquistare un abbonamento a MSDN?
Per usare le immagini del sistema operativo client Windows (Windows 7 o versione successiva) per lo sviluppo o il test in Azure, eseguire una delle operazioni seguenti:To use Windows client OS images (Windows 7 or a later version) for your development or testing in Azure, take one of the following steps:

- [Acquistare un abbonamento a MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se si ha un contratto Enterprise Agreement, creare una sottoscrizione di Azure con l'[offerta Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p).

Per altre informazioni sui crediti Azure per ogni offerta MSDN, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Come è possibile automatizzare il processo di eliminazione di tutte le macchine virtuali nel lab?
Il proprietario di un lab può eliminare le macchine virtuali dal lab nel portale di Azure.As a lab owner, you can delete VMs from your lab in the Azure portal. È anche possibile eliminare tutte le macchine virtuali del lab usando uno script di PowerShell. Nell'esempio seguente, sotto i **valori per modificare** il commento, modificare i valori dei parametri. È possibile recuperare i valori subscriptionId, labResourceGroup e labName dal riquadro lab nel portale di Azure.You can retrieve the subscriptionId, labResourceGroup, and labName values from the lab pane in the Azure portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Ambienti

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Come è possibile usare modelli di Resource Manager in un ambiente DevTest Labs?
Distribuire i modelli di Resource Manager in un ambiente DevTest Labs utilizzando i passaggi indicati nell'articolo [della funzionalità Ambienti in DevTest Labs.](devtest-lab-test-env.md) In sostanza, si esegue il check-in dei modelli di Resource Manager in un repository Git (Azure Repos o GitHub) e si aggiunge un [repository privato per i modelli](devtest-lab-test-env.md) al lab. Questo scenario potrebbe non essere utile se si utilizza DevTest Labs per ospitare i computer di sviluppo, ma può essere utile se si sta creando un ambiente di gestione temporanea, che è rappresentativo della produzione.

Vale anche la pena notare che il numero di macchine virtuali per lab o per opzione utente limita solo il numero di macchine create in modo nativo nel lab stesso e non da qualsiasi ambiente (modelli di Resource Manager).

## <a name="custom-images"></a>Immagini personalizzate

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Come è possibile configurare un processo facilmente ripetibile per introdurre le immagini aziendali personalizzate in un ambiente DevTest Labs?
Vedere questo [video sul modello Factory immagini](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Si tratta di uno scenario avanzato e gli script forniti sono solo di esempio. Se sono richieste modifiche, è necessario gestire gli script usati nell'ambiente.

Per informazioni dettagliate sulla creazione di una factory di immagini, vedere Creare una factory di [immagini personalizzata in Lab di Azure DevTest.](image-factory-create.md)

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Qual è la differenza tra un'immagine personalizzata e una formula?
Un'immagine personalizzata è un'immagine gestita. Una formula è un'immagine che è possibile configurare con impostazioni aggiuntive e quindi salvare e riprodurre. Un'immagine personalizzata può essere più adatta per creare rapidamente più ambienti usando la stessa immagine di base non modificabile. Una formula può essere migliore se si vuole riprodurre la configurazione della macchina virtuale con i bit più recenti, come parte di una rete virtuale o di una subnet o come macchina virtuale con dimensioni specifiche. Per una spiegazione più approfondita, vedere [Confronto tra immagini personalizzate e formule in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quando è opportuno usare una formula e quando un'immagine personalizzata?
I fattori decisivi in questo scenario sono in genere i costi e il riutilizzo. Se si dispone di uno scenario in cui molti utenti/laboratori richiedono un'immagine con molto software sopra l'immagine di base, è possibile ridurre i costi creando un'immagine personalizzata. Significa che l'immagine viene creata una sola volta. riducendo il tempo di configurazione della macchina virtuale e i costi dovuti all'esecuzione della macchina virtuale durante la configurazione.

Un altro fattore da considerare è la frequenza delle modifiche al pacchetto software. Se si eseguono build giornaliere e il software deve trovarsi sulle macchine virtuali degli utenti, valutare una formula anziché un'immagine personalizzata.

Per una spiegazione più approfondita, vedere [Confronto di immagini e formule personalizzate](devtest-lab-comparing-vm-base-image-types.md) in DevTest Labs.For a more in-depth explanation, see Comparing custom images and formulas in DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Come si automatizza il processo di caricamento dei file VHD per creare immagini personalizzate?

Per automatizzare il processo di caricamento dei file VHD per creare immagini personalizzate, sono disponibili due opzioni:

- Usare [AzCopy](../storage/common/storage-use-azcopy-v10.md) per copiare o caricare i file VHD nell'account di archiviazione associato al lab.
- Usare [Esplora archivi di Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer è un'app autonoma che è possibile eseguire in Windows, OSX e Linux.

Per trovare l'account di archiviazione di destinazione associato al lab:

1.  Accedere al [portale](https://portal.azure.com)di Azure .
2.  Nel menu di sinistra selezionare **Gruppi di risorse**.
3.  Trovare e selezionare il gruppo di risorse associato al lab.
4.  In **Panoramica** selezionare uno degli account di archiviazione.
5.  Selezionare **BLOB**.
6.  Cercare i caricamenti nell'elenco. Se non ce ne sono, tornare al passaggio 4 e provare con un altro account di archiviazione.
7.  Usare l'**URL** come destinazione nel comando AzCopy.

Quando è opportuno usare un'immagine di Azure Marketplace e quando un'immagine aziendale personalizzata?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Quando è opportuno usare un'immagine di Azure Marketplace e quando un'immagine aziendale personalizzata?
Azure Marketplace deve essere usato per impostazione predefinita, se non in caso di dubbi specifici o requisiti aziendali. Alcuni esempi comuni sono:

- Configurazione software complessa che richiede di includere un'applicazione nell'immagine di base.
- L'installazione e la configurazione di un'applicazione potrebbero richiedere molte ore, che non sono un uso efficiente del tempo di calcolo da aggiungere in un'immagine di Azure Marketplace.Installation and setup of an application could take many hours, which aren't an efficient use of compute time to be added on an Azure Marketplace image.
- Sviluppatori e tester richiedono l'accesso rapido a una macchina virtuale e intendono ridurre al minimo il tempo di configurazione di una nuova macchina virtuale.
- Condizioni normative o di conformità, ad esempio criteri di sicurezza, che devono essere definite per tutti i computer.
- L'utilizzo di immagini personalizzate non deve essere considerato con leggerezza. Introducono una maggiore complessità, poiché ora è necessario gestire i file VHD per le immagini di base sottostanti. È anche necessario applicare periodicamente patch alle immagini di base con aggiornamenti software. Tra questi rientrano i nuovi aggiornamenti del sistema operativo e tutti gli aggiornamenti o le modifiche di configurazione necessarie per il pacchetto software stesso.

## <a name="artifacts"></a>Elementi

### <a name="what-are-artifacts"></a>Cosa sono gli elementi?
Gli elementi sono componenti personalizzabili che è possibile usare per distribuire i bit più recenti o gli strumenti di sviluppo in una macchina virtuale. Collegare gli elementi alla macchina virtuale quando si crea la macchina virtuale. Dopo il provisioning della macchina virtuale, gli elementi vengono usati per distribuire e configurare la macchina virtuale. Nel nostro [repository pubblico GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)sono disponibili vari elementi preesistenti. È anche possibile [creare elementi personalizzati](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Si è verificato un errore per l'elemento durante la creazione della VM. Come si risolve il problema?
Per informazioni su come ottenere i log relativi all'elemento che presenta l'errore, vedere [Diagnosticare errori di elementi in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quando è consigliabile che un'organizzazione usi un repository di artefatti pubblico invece di uno privato in DevTest Labs?
Il [repository di artefatti pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) offre un set iniziale di pacchetti software di uso più frequente. Aiuta con la distribuzione rapida senza dover investire tempo per riprodurre gli strumenti di sviluppo comuni e componenti aggiuntivi. È possibile scegliere di distribuire il proprio repository privato. È possibile usare parallelamente un repository pubblico e uno privato. È anche possibile scegliere di disabilitare il repository pubblico. I criteri per distribuire un repository privato dovrebbero basarsi sulle domande e le considerazioni seguenti:

- Un requisito dell'organizzazione è la necessità di avere software con licenza aziendale come parte dell'offerta di DevTest Labs? Se la risposta è affermativa, è consigliabile creare un repository privato.
- L'organizzazione sviluppa software personalizzato che prevede un'operazione specifica, che è essenziale nell'ambito dell'intero processo di provisioning? Se la risposta è affermativa, è consigliabile distribuire un repository privato.
- Se i criteri di governance dell'organizzazione richiedono isolamento e i repository esterni non sono sottoposti a gestione diretta della configurazione da parte dell'organizzazione, è necessario distribuire un archivio di elementi privati. Nell'ambito di questo processo è possibile creare una copia del repository pubblico e integrarla nel repository privato. Si può successivamente disabilitare il repository pubblico in modo che nessun altro all'interno dell'organizzazione possa accedervi più. Questo approccio fa in modo che tutti gli utenti all'interno dell'organizzazione dispongano di un singolo repository che è approvato dall'organizzazione e che riduce al minimo la deviazione della configurazione.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>È consigliabile per un'organizzazione pianificare un singolo repository o consentire più repository?
Nell'ambito della strategia di gestione della configurazione della governance complessiva dell'organizzazione è consigliabile usare un repository centralizzato. Quando si usano più repository, questi possono diventare con il tempo sili di software non gestito. Con un repository centrale più team possono usare gli artefatti disponibili nel repository per i propri progetti. L'utilizzo di un repository centrale aumenta la standardizzazione, la sicurezza, la facilità di gestione ed elimina la duplicazione dei lavori richiesti. La centralizzazione prevede le azioni seguenti come procedure consigliate per la gestione a lungo termine e la sostenibilità:

- Associare Azure Repos con lo stesso tenant di Azure Active Directory usato dalla sottoscrizione di Azure per l'autenticazione e l'autorizzazione.
- Creare un `All DevTest Labs Developers` gruppo denominato in Azure Active Directory gestito centralmente. È opportuno inserire in questo gruppo tutti gli sviluppatori che contribuiscono allo sviluppo degli artefatti.
- Lo stesso gruppo di Azure Active Directory può essere usato per consentire l'accesso al repository di Azure Repos e al lab.
- In Azure Repos è consigliabile usare la funzionalità di branching o di creazione di una copia tramite fork per separare un repository per ambiente di sviluppo dal repository di produzione primario. Il contenuto viene aggiunto al ramo principale solo con una richiesta pull dopo un adeguato esame del codice. Dopo che il revisore del codice approva la modifica, un responsabile sviluppatori, che è responsabile della manutenzione del ramo principale, unisce il codice aggiornato.

## <a name="cicd-integration"></a>Integrazione CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs si integra con la toolchain di integrazione continua/distribuzione continua?
Se si usa DevOps di Azure, è possibile usare [un'estensione per le attività devTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) per automatizzare la pipeline di rilascio in DevTest Labs.If you're using Azure DevOps, you can use a DevTest Labs Tasks extension to automate your release pipeline in DevTest Labs. Ecco alcune delle attività che è possibile eseguire con questa estensione:

- Creare e distribuire automaticamente una macchina virtuale. È anche possibile configurare la macchina virtuale con la build più recente usando le attività di Azure DevOps Services in PowerShell o Copia dei file di Azure.
- Acquisire automaticamente lo stato di una macchina virtuale dopo il test per riprodurre un bug nella stessa macchina virtuale per ulteriori indagini.
- Eliminare la macchina virtuale alla fine della pipeline di rilascio, quando non è più necessaria.

I post di blog seguenti forniscono indicazioni e informazioni sull'uso dell'estensione di Azure DevOps Services:

- [DevTest Labs and the Azure DevOps extension](integrate-environments-devops-pipeline.md) (DevTest Labs e l'estensione Azure DevOps)
- [Deploy a new VM in an existing DevTest Labs lab from Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) (Distribuire una nuova macchina virtuale in un lab di DevTest Labs esistente da Azure DevOps Services)
- [Using Azure DevOps Services release management for continuous deployments to DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) (Uso della gestione del rilascio di Azure DevOps Services per distribuzioni continue in DevTest Labs)

Per altre toolchain di integrazione continua/recapito continuo è possibile ottenere gli stessi scenari distribuendo i [modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/) usando i [cmdlet di Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). È anche possibile usare le [API REST per DevTest Labs](https://aka.ms/dtlrestapis) per l'integrazione con la toolchain.

## <a name="networking"></a>Rete

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quando è consigliabile creare una nuova rete virtuale per l'ambiente DevTest Labs e quando usare invece una rete virtuale esistente?
Se le macchine virtuali devono interagire con l'infrastruttura esistente, è consigliabile usare una rete virtuale esistente all'interno dell'ambiente DevTest Labs.If your VMs need to interact with existing infrastructure, then consider using an existing virtual network inside your DevTest Labs environment. Se si usa ExpressRoute, è possibile ridurre al minimo la quantità di reti virtuali/subnet in modo da non frammentare lo spazio di indirizzi IP che viene assegnato per l'uso nelle sottoscrizioni.

È consigliabile usare il modello di peering della rete virtuale anche qui ([modello Hub-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)). Questo approccio consente la comunicazione vnet/subnet tra sottoscrizioni. In caso contrario, ogni ambiente di DevTest Labs potrebbe avere una specifica rete virtuale.

Esistono [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md) al numero di reti virtuali per sottoscrizione. La quantità predefinita è 50, sebbene questo limite possa essere incrementato a 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quando è opportuno usare un indirizzo IP condiviso, pubblico o privato?

Se si usa una VPN da sito a sito o ExpressRoute, è consigliabile usare indirizzi IP privati, in modo che i computer siano accessibili tramite la rete interna e inaccessibili sulla rete Internet pubblica.

> [!NOTE]
> I proprietari dei lab possono modificare questi criteri di subnet per garantire che nessuno crei accidentalmente indirizzi IP pubblici per le macchine virtuali. Il proprietario della sottoscrizione deve creare criteri di sottoscrizione che impediscano la creazione di indirizzi IP pubblici.

Quando si usano indirizzi IP pubblici condivisi, le macchine virtuali in un lab condividono un indirizzo IP pubblico. Questo approccio può essere utile se è necessario evitare di superare i limiti sugli indirizzi IP pubblici per una determinata sottoscrizione.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Come è possibile garantire che le macchine virtuali di sviluppo e test non siano in grado di raggiungere la rete Internet pubblica? Esistono criteri consigliati per impostare la configurazione di rete?

Sì. Due sono gli aspetti da considerare: il traffico in ingresso e in uscita.

- **Traffico in ingresso:** se la macchina virtuale non dispone di un indirizzo IP pubblico, non può essere raggiunta da Internet.Inbound traffic – If the virtual machine doesn't have a public IP address, then it cannot be reached by the internet. Un approccio comune consiste nell'assicurare che venga impostato un criterio a livello di sottoscrizione, in modo che nessun utente possa creare un indirizzo IP pubblico.
- **Traffico in uscita:** se si vuole impedire alle macchine virtuali di accedere direttamente a Internet pubblico e forzare il traffico attraverso un firewall aziendale, è possibile instradare il traffico in locale tramite route rapida o VPN, usando il routing forzato.

> [!NOTE]
> Se si dispone di un server proxy che blocca il traffico senza impostazioni proxy, non dimenticare di aggiungere eccezioni all'account di archiviazione degli elementi del lab.

È anche possibile usare gruppi di sicurezza di rete per macchine virtuali o subnet. Questo passaggio aggiunge un livello di protezione per consentire o bloccare il traffico.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Perché la rete virtuale esistente non viene salvata correttamente?
È possibile che il nome della rete virtuale contenga dei punti. In tal caso, provare a rimuovere i punti o a sostituirli con trattini. Provare quindi a salvare di nuovo la rete virtuale.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Perché quando si effettua il provisioning di una macchina virtuale da PowerShell viene visualizzato un errore che indica che la risorsa padre non è stata trovata?
Quando una risorsa è l'elemento padre di un'altra risorsa, deve essere già presente prima di creare la risorsa figlio. Se la risorsa padre non esiste, viene visualizzato un messaggio **ParentResourceNotFound.If** the parent resource doesn't exist, you see a ParentResourceNotFound message. Se non si specifica una dipendenza dalla risorsa padre, la risorsa figlio può essere distribuita prima dell'elemento padre.

Le macchine virtuali sono risorse figlio di un lab in un gruppo di risorse. Quando si usano modelli di Resource Manager per distribuire macchine virtuali tramite PowerShell, il nome del gruppo di risorse specificato nello script di PowerShell deve coincidere con quello del gruppo di risorse del lab. Per altre informazioni, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Se la distribuzione di una macchina virtuale ha esito negativo, dove è possibile trovare maggiori informazioni sul tipo di errore?
Gli errori di distribuzione delle macchine virtuali vengono acquisiti nei log attività. È possibile trovare i log attività della macchina virtuale del lab in **Log di controllo** o Diagnostica macchina **virtuale** nel menu delle risorse nella pagina della macchina virtuale del lab (la pagina viene visualizzata dopo aver selezionato la macchina virtuale dall'elenco Macchine virtuali personali).

In alcuni casi, l'errore di distribuzione si verifica prima dell'inizio della distribuzione della macchina virtuale. Questo succede, ad esempio, quando viene superato il limite della sottoscrizione per una risorsa creata con la macchina virtuale. In questo caso, i dettagli dell'errore vengono acquisiti nei log attività a livello di lab. Tali log attività si trovano nella parte inferiore delle impostazioni **Configurazione e criteri**. Per altre informazioni sull'utilizzo dei log attività in Azure, vedere [Visualizzare i log attività per controllare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Perché viene visualizzato l'errore "Il percorso non è disponibile per il tipo di risorsa" quando si tenta di creare un lab?
Quando si tenta di creare un lab, è possibile che venga visualizzato un messaggio di errore analogo al seguente:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

È possibile risolvere questo errore eseguendo una delle seguenti operazioni:

#### <a name="option-1"></a>Opzione 1
Controllare la disponibilità del tipo di risorsa nelle aree di Azure nella pagina [Prodotti disponibili per area.](https://azure.microsoft.com/global-infrastructure/services/) Se il tipo di risorsa non è disponibile in una determinata area, DevTest Labs non supporta la creazione di un lab in tale area. Selezionare un'altra area durante la creazione del lab.

#### <a name="option-2"></a>Opzione 2
Se il tipo di risorsa è disponibile nell'area geografica, verificare se è registrato con la sottoscrizione. Può essere eseguita a livello di proprietario della sottoscrizione, come illustrato in [questo articolo.](../azure-resource-manager/management/resource-providers-and-types.md)
