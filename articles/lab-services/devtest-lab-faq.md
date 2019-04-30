---
title: Domande frequenti su Azure DevTest Labs | Documentazione Microsoft
description: Risposte alle domande comuni su Azure DevTest Labs.
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
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: 91c598bde0912cffb8aa1dd7ba022c86a9084faa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127396"
---
# <a name="azure-devtest-labs-faq"></a>Domande frequenti su Azure DevTest Labs
Ottenere le risposte ad alcune delle domande più comuni relative ad Azure DevTest Labs.

## <a name="blog-post"></a>Post di blog
Blog del Team di DevTest Labs è stato ritirato a partire da 20 marzo 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>In cui è possibile tenere traccia degli aggiornamenti delle funzionalità nel?
D'ora in poi verranno resi disponibili gli aggiornamenti delle funzionalità e i post di blog informativo sul blog di Azure e gli aggiornamenti di Azure. Questi post di blog contiene collegamenti alla documentazione laddove necessario.

Sottoscrivere il [Blog di Azure DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) e [DevTest Labs Azure Aggiorna](https://azure.microsoft.com/updates/?product=devtest-lab) per rimanere informati sulle nuove funzionalità in DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Cosa accade ai post di blog esistente?
Attualmente impegnata nella migrazione dei post di blog esistente (ad eccezione degli aggiornamenti di interruzione) nel nostro [documentazione di DevTest Labs](devtest-lab-overview.md). Quando nel blog di MSDN è deprecato, si verrà reindirizzato alla panoramica della documentazione di DevTest Labs. Dopo aver reindirizzato, è possibile cercare l'articolo che stanno cercando il titolo 'Filtro da'. Microsoft non hanno ancora eseguito la migrazione tutti i post, ma deve essere eseguite dalla fine di questo mese. 


### <a name="where-do-i-see-outage-updates"></a>In cui vengono visualizzati gli aggiornamenti di interruzione di servizio?
Verranno resi disponibili gli aggiornamenti di interruzione di servizio usando l'handle di Twitter a partire da ora e versioni successive. Seguici su Twitter per ottenere gli aggiornamenti più recenti su interruzioni ed errori noti.

### <a name="twitter"></a>Twitter
L'handle di Twitter: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Generale
### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Se la domanda non è elencata qui, comunicare all'utente, in modo che possiamo aiutarti a trovare una risposta.

- Pubblicare una domanda alla fine di questo articolo di domande frequenti. Interagire con il team di Cache di Azure e altri membri della community in merito a questo articolo.
- Per raggiungere un gruppo di destinatari più ampio, pubblicare una domanda nel [forum MSDN di Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Interagire con il team di Azure DevTest Labs e altri membri della community.
- Per richiedere funzionalità, inviare le richieste e le idee al [sito User Voice per Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Che cos'è un account Microsoft?
Un account Microsoft è l'account che si usa per quasi tutte le attività eseguite con servizi e dispositivi Microsoft. È un indirizzo di posta elettronica e password usati per accedere a Skype, Outlook.com, OneDrive, Windows phone, Azure e Xbox Live. Grazie a questo singolo account, file, foto, contatti e impostazioni possono seguire l'utente su qualsiasi dispositivo.
 
> [!NOTE]
> Un account Microsoft usato per chiamare un Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Perché usare Azure DevTest Labs?
Azure DevTest Labs può far risparmiare tempo e denaro al team. Gli sviluppatori possono creare ambienti personalizzati usando svariate basi diverse. Possono anche usare gli elementi per distribuire e configurare rapidamente le applicazioni. Usando formule e immagini personalizzate, è possibile salvare le macchine virtuali come modelli e riprodurle facilmente nel team. DevTest Labs offre anche diversi criteri configurabili che consentono agli amministratori di lab di ridurre gli sprechi e di gestire gli ambienti di un team. Questi criteri includono l'arresto automatico, la soglia di costo, il numero massimo di macchine virtuali per utente e le dimensioni massime delle macchine virtuali. Per una spiegazione più approfondita di DevTest Labs, vedere la [panoramica](devtest-lab-overview.md) o guardare il [video introduttivo](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Cosa significa "self-service senza preoccupazioni"?
Self-service senza preoccupazioni significa che gli sviluppatori e i tester creano i propri ambienti in base alle esigenze. Gli amministratori hanno la sicurezza di sapere che DevTest Labs possono aiutare a impostare l'accesso appropriato, ridurre al minimo gli sprechi e controllare i costi. Gli amministratori possono specificare quali sono le dimensioni consentite per le VM e il numero massimo di VM e quando le VM vengono avviate e arrestate. DevTest Labs consente anche di monitorare facilmente i costi e di impostare avvisi per sapere come vengono usate le risorse del lab.

### <a name="how-can-i-use-devtest-labs"></a>Come si usa DevTest Labs?
DevTest Labs è utile quando si richiedono dev o ambienti di test e vuole riprodurli rapidamente o gestirli con criteri di riduzione dei costi.
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
Accesso sicuro è determinato dal controllo di accesso basato sui ruoli (RBAC). Per comprendere il funzionamento dell'accesso, è utile conoscere le differenze tra un'autorizzazione, un ruolo e un ambito, come definiti dal controllo degli accessi in base al ruolo.

- **Autorizzazione**: un'autorizzazione è un accesso definito a un'azione specifica. Un'autorizzazione può ad esempio essere l'accesso in lettura a tutte le macchine virtuali.
- **Ruolo**: un ruolo è un set di autorizzazioni che possono essere raggruppate e assegnate a un utente. Un utente con il ruolo di proprietario della sottoscrizione ha ad esempio accesso a tutte le risorse all'interno di una sottoscrizione.
- **Ambito**: un ambito è un livello nella gerarchia di una risorsa di Azure. Un ambito può ad esempio essere un gruppo di risorse, un singolo lab oppure l'intera sottoscrizione.

Nell'ambito di DevTest Labs, ci sono due tipi di ruoli che definiscono le autorizzazioni utente:

- **Proprietario del lab**: un proprietario del lab ha accesso a tutte le risorse all'interno dello stesso. Un proprietario del lab può modificare i criteri, leggere e scrivere in qualsiasi macchina virtuale, modificare la rete virtuale e così via.
- **Utente del lab**: un utente del lab può visualizzare tutte le risorse del lab, ad esempio macchine virtuali, criteri e reti virtuali. Tuttavia, un utente del lab non è possibile modificare i criteri o tutte le macchine virtuali che sono state create da altri utenti.

È anche possibile creare ruoli personalizzati in DevTest Labs. Per informazioni su come creare ruoli personalizzati in DevTest Labs, vedere [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Poiché gli ambiti sono gerarchici, quando un utente ha le autorizzazioni per un determinato ambito, gli vengono automaticamente concesse tali autorizzazioni per ogni ambito di livello inferiore. Se ad esempio a un utente è assegnato il ruolo di proprietario della sottoscrizione, l'utente ha accesso a tutte le risorse in una sottoscrizione. Queste risorse includono macchine virtuali, reti virtuali e lab. Il proprietario di una sottoscrizione eredita automaticamente il ruolo di proprietario del lab, ma non il contrario. Il proprietario di un lab ha accesso a un lab, che è un ambito più basso del livello della sottoscrizione. Pertanto, un proprietario del lab non può visualizzare le macchine virtuali, reti virtuali o qualsiasi altra risorsa esterna al lab.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Come definisco il controllo degli accessi in base al ruolo per gli ambienti DevTest Labs al fine di garantire che l'IT possa governare mentre gli sviluppatori/i test possono svolgere il proprio lavoro?
Esiste un criterio ampio, tuttavia i dettagli dipendono dalla propria organizzazione.

Centrale IT deve solo ciò che serve il proprietario e abilitare i team di progetto e applicazioni avere il livello di controllo necessario. In genere significa che l'IT centrale possiede la sottoscrizione e gestisce le funzioni IT di base come le configurazioni di rete. Il set di **proprietari** per una sottoscrizione deve essere di piccole dimensioni. I proprietari possono designare altri proprietari quando è necessario o applicare criteri a livello di sottoscrizione, ad esempio "Nessun indirizzo IP pubblico".

Un sottoinsieme di utenti potrebbe richiedere l'accesso a una sottoscrizione, ad esempio il supporto di Livello 1 o 2. In questo caso, si consiglia di concedere a questi utenti l'accesso **collaboratore** in modo che possano gestire le risorse, ma non fornire l'accesso utente o modificare i criteri.

La risorsa di DevTest Labs deve appartenere a proprietari vicini al team di progetto/applicazione. È perché comprendono i requisiti per i computer e software necessari. Nella maggior parte delle organizzazioni, il proprietario della risorsa di DevTest Labs è in genere il responsabile del progetto/sviluppo. Questo proprietario può gestire gli utenti e i criteri all'interno dell'ambiente lab, nonché tutte le macchine virtuali nell'ambiente di DevTest Labs.

I membri del team di progetto/applicazione devono essere aggiunti per il **gli utenti DevTest Labs** ruolo. Questi utenti possono creare macchine virtuali (in linea con i criteri a livello di sottoscrizione e lab). Possono anche gestire le proprie macchine virtuali. Non possono gestire le macchine virtuali che appartengono ad altri utenti.

Per altre informazioni, vedere [scaffold Azure enterprise: documentazione di governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


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
- Sviluppo di un'immagine personalizzata, di artefatti personalizzati e di un processo di distribuzione che consente l'orchestrazione nel campo della sicurezza definito con Active Directory. Questo approccio applica limiti aziendali e imposta un set comune di controlli nell'ambiente. Questi sono controlli nell'ambiente che uno sviluppatore può prendere in considerazione mentre sviluppa e segue un ciclo di vita di sviluppo sicuro come parte del processo complessivo. L'obiettivo è anche per fornire un ambiente che non sia troppo restrittivo che potrebbe ridurre lo sviluppo, ma un set limitato di controlli. I criteri di gruppo nell'unità organizzativa che contiene macchine virtuali lab possono essere un sottoinsieme dei criteri di gruppo totali che si trovano nell'ambiente di produzione. Possono altrimenti rappresentare un set aggiuntivo per attenuare adeguatamente eventuali rischi identificati.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>In che modo un'organizzazione può garantire l'integrità dei dati per assicurare che gli sviluppatori in remoto non rimuovano codice o introducano malware o software non approvato?
Esistono diversi livelli di controllo per attenuare la minaccia che può sorgere da consulenti esterni, terzisti o dipendenti che collaborano in DevTest Labs in remoto.

Come indicato in precedenza, il primo passaggio deve avere criteri di utilizzo accettabile redatti e definiti che descrivono chiaramente le conseguenze quando un utente li viola.

Il primo livello dei controlli per l'accesso remoto consiste nell'applicare un criterio di accesso remoto tramite una connessione VPN che non è direttamente connesso al lab.

Il secondo livello di controlli consiste nell'applicare un set di oggetti criteri di gruppo che impediscono le operazioni di copia e incolla tramite un desktop remoto. È possibile implementare criteri di rete per non consentire servizi in uscita dall'ambiente, ad esempio i servizi FTP e RDP esterni all'ambiente. Routing definito dall'utente è stato possibile forzare tutto il traffico di rete di Azure in locale, ma il routing non è stato dell'account per tutti gli URL che potrebbero consentire il caricamento dei dati a meno che non controllata tramite un proxy per analizzare il contenuto e le sessioni. Gli indirizzi IP pubblici possono essere limitati all'interno della rete virtuale che supporta DevTest Labs per non consentire il bridging di una risorsa di rete esterna.

In definitiva, lo stesso tipo di restrizioni deve essere applicato all'interno dell'organizzazione, che verrebbe per tutti i possibili metodi di supporto rimovibile o URL esterni in grado di accettare una richiesta post del contenuto. Consultare i propri esperti di sicurezza per esaminare e implementare i criteri di sicurezza. Per altre raccomandazioni, vedere il sito sulla [Sicurezza informatica di Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configurazione del lab

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Come si crea un lab da un modello di Resource Manager?
È disponibile un [repository GitHub di modelli di Azure Resource Manager per lab](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) che è possibile distribuire così come sono oppure modificare per creare modelli personalizzati per i propri lab. Ogni modello ha un collegamento per distribuire il lab perché è nella propria sottoscrizione di Azure. In alternativa, è possibile personalizzare il modello ed [eseguire la distribuzione con PowerShell o l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>È possibile avere tutte le macchine virtuali deve essere creato in un gruppo di risorse comuni invece che ogni computer nel proprio gruppo di risorse? 
Sì, come proprietario del lab, è possibile scegliere di consentire il lab di gestire l'allocazione di gruppo di risorse per l'utente o avere tutte le macchine virtuali creato in un gruppo di risorse comuni che si specifica. 

Scenario con gruppo di risorse separati:
-   DevTest Labs consente di creare un nuovo gruppo di risorse per ogni macchina virtuale IP pubblico/privato che lasciamo
-   DevTest Labs consente di creare un gruppo di risorse per le macchine IP condivisi che appartengono alla stessa dimensione.

Scenario del gruppo risorse comuni:
-   Tutte le macchine virtuali vengono riattivate nel gruppo di risorse comuni specificate. Altre informazioni [allocazione di gruppo di risorse per l'ambiente lab](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Come è possibile gestire una convenzione di denominazione nell'ambiente di DevTest Labs?
È possibile estendere le convenzioni di denominazione correnti dell'organizzazione alle operazioni di Azure e renderle coerenti in tutto l'ambiente DevTest Labs. In fase di distribuzione di DevTest Labs è consigliabile avere criteri iniziali specifici. Questi criteri vengono distribuiti in base a uno script centrale e a modelli JSON per garantire la coerenza. I criteri di denominazione possono essere implementati tramite i criteri di Azure applicati a livello di sottoscrizione. Per esempi JSON per Criteri di Azure, vedere [Esempi di Criteri di Azure](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quanti lab è possibile creare nella stessa sottoscrizione?
Non c'è un limite specifico al numero di lab che è possibile creare per ogni sottoscrizione. È tuttavia previsto un limite per la quantità di risorse usate per ogni sottoscrizione. Sono disponibili informazioni su [limiti e quote per le sottoscrizioni di Azure](../azure-subscription-service-limits.md) e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Quante VM è possibile creare per ogni lab?
Non esiste un limite specifico al numero di VM che possono essere create per ogni lab. È tuttavia previsto un limite per le risorse (core delle macchine virtuali, indirizzi IP pubblici e così via) usate per ogni sottoscrizione. Sono disponibili informazioni su [limiti e quote per le sottoscrizioni di Azure](../azure-subscription-service-limits.md) e su [come aumentare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Come si determina il rapporto tra gli utenti per ogni lab e il numero complessivo di Lab che sono necessari all'interno dell'organizzazione?
È consigliabile che le business unit e i gruppi di sviluppo dello stesso progetto di sviluppo siano associati allo stesso lab. In questo modo è possibile applicare a entrambi i gruppi gli stessi tipi di criteri, immagini e criteri di arresto.

Potrebbe anche essere necessario considerare i limiti geografici. Ad esempio, gli sviluppatori nel nord orientale United States (Stati Uniti) potrebbero utilizzare un laboratorio sottoposte a provisioning in Stati Uniti orientali 2. E gli sviluppatori a Dallas, in Texas, e a Denver, in Colorado, potrebbero essere indirizzati all'uso di una risorsa negli Stati Uniti centro-meridionali. In caso di un'attività collaborativa con una terza parte esterna, quest'ultima potrebbe essere assegnata a un lab non usato dagli sviluppatori interni.

È anche possibile usare un lab per un progetto specifico all'interno dei progetti DevOps di Azure. In questo caso, applicare la sicurezza tramite un gruppo di Azure Active Directory specificato, che consente l'accesso a entrambi i set di risorse. La rete virtuale assegnata al lab può essere un altro limite al consolidamento degli utenti.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Come è possibile impedire l'eliminazione delle risorse all'interno di un lab?
È consigliabile impostare le autorizzazioni appropriate a livello di lab in modo che solo gli utenti autorizzati possano eliminare le risorse o modificare i criteri del lab. Gli sviluppatori devono trovarsi all'interno del gruppo di **utenti DevTest Labs**. Il responsabile degli sviluppatori o dell'infrastruttura deve essere il **proprietario di DevTest Labs**. È consigliabile che disporre di due soli i proprietari del lab. Questi criteri si estendono al repository di codice per evitare il danneggiamento. Gli utenti del lab hanno diritto a usare le risorse, ma non è possibile aggiornare i criteri del lab. Vedere l'articolo seguente che elenca i ruoli e i diritti di ogni gruppo incorporato all'interno di un lab: [Aggiungere proprietari e utenti in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Come è possibile condividere un collegamento diretto al mio lab?

1. Nel [portale di Azure](https://portal.azure.com), passare al lab.
2. Copia il **URL del lab** dal browser e quindi condividerlo con gli utenti del lab.

> [!NOTE]
> Se un utente del lab è un utente esterno che ha un account Microsoft, ma che non è un membro dell'istanza di Active Directory dell'organizzazione, l'utente potrebbe essere visualizzato un messaggio di errore quando tentano di accedere al collegamento condiviso. In questo caso, chiedere all'utente di selezionare prima di tutto il proprio nome nell'angolo superiore destro del portale di Azure. Quindi, nella sezione Directory del menu, l'utente può selezionare la directory in cui si trova il lab.

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Perché non riesco a visualizzare VM nella pagina macchine virtuali che vengono visualizzate in DevTest Labs?
Quando si crea una macchina virtuale in DevTest Labs, si riceve l'autorizzazione per accedere a tale macchina virtuale. È possibile visualizzare la macchina virtuale sia nella pagina di laboratori e scegliere il **macchine virtuali** pagina. Gli utenti assegnati al **proprietario di DevTest Labs** ruolo possa vedere tutte le macchine virtuali che sono state create nel lab del lab **tutte le macchine virtuali** pagina. Tuttavia, gli utenti che dispongono di **utente DevTest Labs** ruolo non vengono concessi automaticamente alle risorse di macchine Virtuali create da altri utenti hanno accesso in lettura. Pertanto, tali macchine virtuali non vengono visualizzati nei **macchine virtuali** pagina.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Come si creano più VM dallo stesso modello contemporaneamente?
Ci sono due opzioni per creare simultaneamente più macchine virtuali dallo stesso modello:

- È possibile usare l'[estensione Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- È possibile [generare un modello di Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) mentre si crea una macchina virtuale, e [distribuire il modello di Resource Manager da Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- È anche possibile specificare più di un'istanza di una macchina deve essere creato durante la creazione della macchina virtuale. Per altre informazioni sulla creazione di più istanze di macchine virtuali, vedere la documentazione sulla [crea una macchina virtuale lab](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Come si spostano le macchine virtuali di Azure esistenti nel lab di DevTest Labs?
Per copiare le macchine virtuali esistenti in DevTest Labs:

1.  Copiare il file VHD della macchina virtuale esistente usando uno [script di Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Creare il [immagine personalizzata](devtest-lab-create-template.md) nel lab di DevTest Labs.
3.  Creare una macchina virtuale nel lab dall'immagine personalizzata.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>È possibile collegare più dischi alle VM?

Sì, è possibile collegare più dischi alle macchine virtuali.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Per usare un'immagine del sistema operativo per le operazioni di test è necessario acquistare un abbonamento a MSDN?
Per usare le immagini del sistema operativo client Windows (Windows 7 o versione successiva) per lo sviluppo o test in Azure, eseguire una delle operazioni seguenti:

- [Acquistare un abbonamento a MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se si ha un contratto Enterprise Agreement, creare una sottoscrizione di Azure con l'[offerta Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p).

Per altre informazioni sui crediti Azure per ogni offerta MSDN, vedere [Credito Azure mensile per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Come è possibile automatizzare il processo di eliminazione di tutte le macchine virtuali nel lab?
Come proprietario del lab, è possibile eliminare le macchine virtuali nell'ambiente lab nel portale di Azure. È anche possibile eliminare tutte le macchine virtuali del lab usando uno script di PowerShell. Nell'esempio seguente, con la **valori da modificare** commentare, modificare i valori dei parametri. È possibile recuperare l'ID sottoscrizione, labResourceGroup e valori labName dal pannello del lab nel portale di Azure.

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
Distribuire modelli di Resource Manager in un ambiente di DevTest Labs usando i passaggi indicati nel [funzionalità di ambienti in DevTest Labs](devtest-lab-test-env.md) articolo. In sostanza, si esegue il check-in dei modelli di Resource Manager in un repository Git (Azure Repos o GitHub) e si aggiunge un [repository privato per i modelli](devtest-lab-test-env.md) al lab. Questo scenario potrebbe non essere utile se si usa DevTest Labs per ospitare computer di sviluppo, ma può risultare utile se si sta creando un ambiente di staging, che è rappresentativo dell'ambiente di produzione.

È anche importante notare che il numero di macchine virtuali per lab o per l'opzione limita solo il numero di computer creati in modo nativo nel laboratorio stesso e non per tutti gli ambienti (modelli di Resource Manager).

## <a name="custom-images"></a>Immagini personalizzate

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Come è possibile configurare un processo facilmente ripetibile per introdurre le immagini aziendali personalizzate in un ambiente DevTest Labs?
Vedere questo [video sul modello di Factory di immagini](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Si tratta di uno scenario avanzato e gli script forniti sono solo di esempio. Se sono richieste modifiche, è necessario gestire gli script usati nell'ambiente.

Per informazioni dettagliate sulla creazione di una factory di immagini, vedere [creare una factory di immagini personalizzate in Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Qual è la differenza tra un'immagine personalizzata e una formula?
Un'immagine personalizzata è un'immagine gestita. Una formula è un'immagine che è possibile configurare con impostazioni aggiuntive e quindi salvare e riprodurre. Un'immagine personalizzata può essere più adatta per creare rapidamente più ambienti usando la stessa immagine di base non modificabile. Una formula può essere migliore se si vuole riprodurre la configurazione della macchina virtuale con i bit più recenti, come parte di una rete virtuale o di una subnet o come macchina virtuale con dimensioni specifiche. Per una spiegazione più approfondita, vedere [Confronto tra immagini personalizzate e formule in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quando è opportuno usare una formula e quando un'immagine personalizzata?
I fattori decisivi in questo scenario sono in genere i costi e il riutilizzo. Se si dispone di uno scenario in cui molti utenti/laboratori richiedono un'immagine con molti software sopra l'immagine di base, è possibile ridurre i costi tramite la creazione di un'immagine personalizzata. Significa che l'immagine viene creata una sola volta. riducendo il tempo di configurazione della macchina virtuale e i costi dovuti all'esecuzione della macchina virtuale durante la configurazione.

Un altro fattore da considerare è la frequenza delle modifiche al pacchetto software. Se si eseguono build giornaliere e il software deve trovarsi sulle macchine virtuali degli utenti, valutare una formula anziché un'immagine personalizzata.

Per una spiegazione più approfondita, vedere [confronto tra immagini personalizzate e formule](devtest-lab-comparing-vm-base-image-types.md) in DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Come si automatizza il processo di caricamento dei file VHD per creare immagini personalizzate?

Per automatizzare il processo di caricamento dei file VHD per creare immagini personalizzate, sono disponibili due opzioni:

- Usare [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) per copiare o caricare i file VHD nell'account di archiviazione associato al lab.
- Usare [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer è un'app autonoma che è possibile eseguire in Windows, OSX e Linux.

Per trovare l'account di archiviazione di destinazione associato al lab:

1.  Accedere al [portale di Azure](https://portal.azure.com).
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
- Installazione e configurazione di un'applicazione può richiedere diverse ore, che non sono un utilizzo efficiente del tempo di calcolo da aggiungere a un'immagine di Azure Marketplace.
- Sviluppatori e tester richiedono l'accesso rapido a una macchina virtuale e intendono ridurre al minimo il tempo di configurazione di una nuova macchina virtuale.
- Condizioni normative o di conformità, ad esempio criteri di sicurezza, che devono essere definite per tutti i computer.
- Uso delle immagini personalizzate non deve essere considerato alla leggera. Introducono complessità aggiuntive, come è ora necessario gestire i file VHD per le immagini di base sottostante. È anche necessario applicare periodicamente patch alle immagini di base con aggiornamenti software. Tra questi rientrano i nuovi aggiornamenti del sistema operativo e tutti gli aggiornamenti o le modifiche di configurazione necessarie per il pacchetto software stesso.

## <a name="artifacts"></a>Elementi

### <a name="what-are-artifacts"></a>Cosa sono gli elementi?
Gli elementi sono componenti personalizzabili che è possibile usare per distribuire i bit più recenti o gli strumenti di sviluppo in una macchina virtuale. Collegare gli elementi alla macchina virtuale quando si crea la macchina virtuale. Dopo il provisioning della macchina virtuale, gli elementi vengono usati per distribuire e configurare la macchina virtuale. Svariati elementi preesistenti sono disponibili nel nostro [repository GitHub pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). È anche possibile [creare elementi personalizzati](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Si è verificato un errore per l'elemento durante la creazione della VM. Come si risolve il problema?
Per informazioni su come ottenere i log relativi all'elemento che presenta l'errore, vedere [Diagnosticare errori di elementi in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quando è consigliabile che un'organizzazione usi un repository di artefatti pubblico invece di uno privato in DevTest Labs?
Il [repository di artefatti pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) offre un set iniziale di pacchetti software di uso più frequente. È utile nella distribuzione rapida perché non richiede che si dedichi tempo a riprodurre gli strumenti di sviluppo e i componenti aggiuntivi di uso comune. È possibile scegliere di distribuire il proprio repository privato. È possibile usare parallelamente un repository pubblico e uno privato. È anche possibile scegliere di disabilitare il repository pubblico. I criteri per distribuire un repository privato dovrebbero basarsi sulle domande e le considerazioni seguenti:

- Un requisito dell'organizzazione è la necessità di avere software con licenza aziendale come parte dell'offerta di DevTest Labs? Se la risposta è affermativa, è consigliabile creare un repository privato.
- L'organizzazione sviluppa software personalizzato che prevede un'operazione specifica, che è essenziale nell'ambito dell'intero processo di provisioning? Se la risposta è affermativa, è consigliabile distribuire un repository privato.
- Se i criteri di governance dell'organizzazione richiedono l'isolamento e repository esterni non sono nella gestione della configurazione diretta dall'organizzazione, un repository di elementi privati deve essere distribuito. Nell'ambito di questo processo è possibile creare una copia del repository pubblico e integrarla nel repository privato. Si può successivamente disabilitare il repository pubblico in modo che nessun altro all'interno dell'organizzazione possa accedervi più. Questo approccio fa in modo che tutti gli utenti all'interno dell'organizzazione dispongano di un singolo repository che è approvato dall'organizzazione e che riduce al minimo la deviazione della configurazione.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>È consigliabile per un'organizzazione pianificare un singolo repository o consentire più repository?
Nell'ambito della strategia di gestione della configurazione della governance complessiva dell'organizzazione è consigliabile usare un repository centralizzato. Quando si usano più repository, questi possono diventare con il tempo sili di software non gestito. Con un repository centrale più team possono usare gli artefatti disponibili nel repository per i propri progetti. L'utilizzo di un repository centrale aumenta la standardizzazione, la sicurezza, la facilità di gestione ed elimina la duplicazione dei lavori richiesti. La centralizzazione prevede le azioni seguenti come procedure consigliate per la gestione a lungo termine e la sostenibilità:

- Associare Azure Repos con lo stesso tenant di Azure Active Directory usato dalla sottoscrizione di Azure per l'autenticazione e l'autorizzazione.
- Creare un gruppo denominato `All DevTest Labs Developers` in Azure Active Directory che viene gestito centralmente. È opportuno inserire in questo gruppo tutti gli sviluppatori che contribuiscono allo sviluppo degli artefatti.
- Lo stesso gruppo di Azure Active Directory può essere usato per consentire l'accesso al repository di Azure Repos e al lab.
- In Azure Repos è consigliabile usare la funzionalità di branching o di creazione di una copia tramite fork per separare un repository per ambiente di sviluppo dal repository di produzione primario. Il contenuto viene aggiunto al ramo principale solo con una richiesta pull dopo un adeguato esame del codice. Dopo che il revisore del codice approva la modifica, un responsabile sviluppatori, che è responsabile della manutenzione del ramo principale, unisce il codice aggiornato.

## <a name="cicd-integration"></a>Integrazione continua/distribuzione

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs si integra con la toolchain di integrazione continua/distribuzione continua?
Se si usa Azure DevOps, è possibile usare una [estensione DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) per automatizzare la pipeline di rilascio in DevTest Labs. Ecco alcune delle attività che è possibile eseguire con questa estensione:

- Creare e distribuire automaticamente una macchina virtuale. È anche possibile configurare la macchina virtuale con la build più recente usando le attività di Azure DevOps Services in PowerShell o Copia dei file di Azure.
- Acquisire automaticamente lo stato di una macchina virtuale dopo il test per riprodurre un bug nella stessa macchina virtuale per ulteriori indagini.
- Eliminare la macchina virtuale alla fine della pipeline di rilascio quando non è più necessario.

I post di blog seguenti forniscono indicazioni e informazioni sull'uso dell'estensione di Azure DevOps Services:

- [DevTest Labs and the Azure DevOps extension](integrate-environments-devops-pipeline.md) (DevTest Labs e l'estensione Azure DevOps)
- [Deploy a new VM in an existing DevTest Labs lab from Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) (Distribuire una nuova macchina virtuale in un lab di DevTest Labs esistente da Azure DevOps Services)
- [Using Azure DevOps Services release management for continuous deployments to DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) (Uso della gestione del rilascio di Azure DevOps Services per distribuzioni continue in DevTest Labs)

Per altre toolchain di integrazione continua/recapito continuo è possibile ottenere gli stessi scenari distribuendo i [modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/) usando i [cmdlet di Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). È anche possibile usare le [API REST per DevTest Labs](https://aka.ms/dtlrestapis) per l'integrazione con la toolchain.

## <a name="networking"></a>Rete

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quando è consigliabile creare una nuova rete virtuale per l'ambiente DevTest Labs e quando usare invece una rete virtuale esistente?
Se le macchine virtuali devono interagire con l'infrastruttura esistente, quindi è consigliabile usare una rete virtuale esistente all'interno dell'ambiente di DevTest Labs. Se si usa ExpressRoute, è possibile ridurre al minimo la quantità di reti virtuali / subnet in modo che non frammentare lo spazio degli indirizzi IP assegnato per l'uso nelle sottoscrizioni. 

Provare a usare il modello di peering della rete virtuale qui ([modello Hub-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) troppo. Questo approccio consente la comunicazione di rete virtuale/subnet tra sottoscrizioni. In caso contrario, ogni ambiente di DevTest Labs potrebbe avere una specifica rete virtuale. 

Esistono [limiti](../azure-subscription-service-limits.md) sul numero di reti virtuali per ogni sottoscrizione. La quantità predefinita è 50, sebbene questo limite possa essere incrementato a 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quando è opportuno usare un indirizzo IP condiviso, pubblico o privato?
 
Se si usa una VPN da sito a sito o ExpressRoute, è consigliabile usare indirizzi IP privati, in modo che i computer siano accessibili tramite la rete interna e inaccessibili sulla rete Internet pubblica.

> [!NOTE]
> I proprietari dei lab possono modificare questi criteri di subnet per garantire che nessuno crei accidentalmente indirizzi IP pubblici per le macchine virtuali. Il proprietario della sottoscrizione deve creare criteri di sottoscrizione che impediscano la creazione di indirizzi IP pubblici.

Quando si usano indirizzi IP pubblici condivisi, le macchine virtuali in un lab condividono un indirizzo IP pubblico. Questo approccio può essere utile se è necessario evitare di superare i limiti sugli indirizzi IP pubblici per una determinata sottoscrizione.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Come è possibile garantire che le macchine virtuali di sviluppo e test non siano in grado di raggiungere la rete Internet pubblica? Esistono criteri consigliati per impostare la configurazione di rete?

Sì. Due sono gli aspetti da considerare: il traffico in ingresso e in uscita.

- **Il traffico in ingresso** : se la macchina virtuale non ha un indirizzo IP pubblico, quindi non può essere raggiunto da internet. Un approccio comune è garantire che sia impostato un criterio a livello di sottoscrizione, in modo che nessun utente possa creare un indirizzo IP pubblico.
- **Il traffico in uscita** : se si desidera evitare che le macchine virtuali che accedono a internet pubblico direttamente e forzare il traffico attraverso un firewall aziendale, quindi è possibile instradare il traffico locale tramite expressroute o VPN, usando forzato il routing.

> [!NOTE]
> Se si dispone di un server proxy che blocca il traffico senza le impostazioni del proxy, non dimenticare di aggiungere le eccezioni per account di archiviazione artefatto del lab.

È anche possibile usare gruppi di sicurezza di rete per macchine virtuali o subnet. Questo passaggio aggiunge un livello di protezione per consentire o bloccare il traffico.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Perché la rete virtuale esistente non viene salvata correttamente?
È possibile che il nome della rete virtuale contenga dei punti. In tal caso, provare a rimuovere i punti o a sostituirli con trattini. Provare quindi a salvare di nuovo la rete virtuale.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Perché quando si effettua il provisioning di una macchina virtuale da PowerShell viene visualizzato un errore che indica che la risorsa padre non è stata trovata?
Quando una risorsa è l'elemento padre di un'altra risorsa, deve essere già presente prima di creare la risorsa figlio. Se la risorsa padre non esiste, viene visualizzato un **ParentResourceNotFound** messaggio. Se non si specifica una dipendenza dalla risorsa padre, la risorsa figlio può essere distribuita prima dell'elemento padre.

Le macchine virtuali sono risorse figlio di un lab in un gruppo di risorse. Quando si usano modelli di Resource Manager per distribuire macchine virtuali tramite PowerShell, il nome del gruppo di risorse specificato nello script di PowerShell deve coincidere con quello del gruppo di risorse del lab. Per altre informazioni, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Se la distribuzione di una macchina virtuale ha esito negativo, dove è possibile trovare maggiori informazioni sul tipo di errore?
Gli errori di distribuzione delle macchine virtuali vengono acquisiti nei log attività. È possibile trovare i log attività delle VM nel lab **log di controllo** oppure **diagnostica della macchina virtuale** nel menu delle risorse nella pagina macchine Virtuali del lab (la pagina viene visualizzata dopo aver selezionato la macchina virtuale dal mio elenco di macchine virtuali).

In alcuni casi, l'errore di distribuzione si verifica prima dell'inizio della distribuzione della macchina virtuale. Questo succede, ad esempio, quando viene superato il limite della sottoscrizione per una risorsa creata con la macchina virtuale. In questo caso, i dettagli dell'errore vengono acquisiti nei log attività a livello di lab. Tali log attività si trovano nella parte inferiore delle impostazioni **Configurazione e criteri**. Per altre informazioni sull'utilizzo dei log attività in Azure, vedere [Visualizzare i log attività per controllare le azioni sulle risorse](../azure-resource-manager/resource-group-audit.md).




