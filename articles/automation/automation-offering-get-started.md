---
title: Introduzione ad Automazione di Azure
description: Questo articolo offre una panoramica del servizio Automazione di Azure. Esamina i dettagli di progettazione e implementazione in preparazione dell'onboarding dell'offerta da Azure Marketplace.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: dab404178b45828732e137835213046cedaf0d03
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="get-started-with-azure-automation"></a>Introduzione ad Automazione di Azure

Questa articolo presenta i concetti principali relativi alla distribuzione di Automazione di Azure. Se non si ha familiarità con Automazione di Azure o si ha esperienza con un software per i flussi di lavoro di automazione come System Center Orchestrator, è possibile acquisire le informazioni necessarie per la preparazione e l'onboarding di Automazione. Dopo aver letto questo articolo, sarà possibile iniziare a sviluppare runbook per supportare le esigenze di automazione dei processi. 


## <a name="automation-architecture-overview"></a>Panoramica dell'architettura di Automazione

![Panoramica di Automazione di Azure](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Automazione di Azure è un'applicazione software come servizio (SaaS) che fornisce un ambiente multi-tenant scalabile e affidabile in cui è possibile usare i runbook per automatizzare i processi. È possibile usare Desired State Configuration (DSC) in Azure, in altri servizi cloud o in un ambiente locale per gestire le modifiche di configurazione per i sistemi Windows e Linux. Le entità nell'account di Automazione, ad esempio runbook, asset e account RunAs, sono isolate dagli altri account di Automazione nella propria sottoscrizione e da altre sottoscrizioni.  

I runbook eseguiti in Azure vengono eseguiti in sandbox di Automazione. Le sandbox sono ospitate nella piattaforma Azure come macchine virtuali PaaS (piattaforma distribuita come servizio). 

Le sandbox di Automazione forniscono l'isolamento dei tenant per tutti gli aspetti dell'esecuzione dei runbook, inclusi moduli, spazio di archiviazione, memoria, comunicazione di rete e flussi di processo. Questo ruolo è gestito dal servizio. Non è possibile accedere o gestire il ruolo dall'account di Azure o di Automazione.         

Per automatizzare la distribuzione e la gestione delle risorse nel data center locale o in altri servizi cloud, dopo aver creato un account di Automazione è possibile designare una o più macchine virtuali per eseguire il [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md). Ogni ruolo di lavoro ibrido per runbook richiede l'installazione dell'agente di gestione Microsoft e di un account di Automazione. L'agente deve avere una connessione a un'area di lavoro di Azure Log Analytics. È possibile usare Log Analytics per avviare l'installazione, gestire l'agente di gestione Microsoft e monitorare la funzionalità del ruolo di lavoro ibrido per runbook. Automazione di Azure esegue la distribuzione dei runbook e l'istruzione per eseguirli.

È possibile distribuire più ruoli di lavoro ibridi per runbook. Usare i ruoli di lavoro ibridi per runbook per garantire un'elevata disponibilità per i runbook e il bilanciamento del carico dei processi dei runbook. In alcuni casi, è possibile dedicare i processi dei runbook a specifici carichi di lavoro o ambienti. Microsoft Monitoring Agent nel ruolo di lavoro ibrido per runbook avvia la comunicazione con il servizio Automazione sulla porta TCP 443. Per i ruoli di lavoro ibridi per runbook non sono previsti requisiti del firewall in entrata.  

Può essere necessario che un runbook in esecuzione in un ruolo di lavoro ibrido per runbook esegua attività di gestione su altri computer o servizi nell'ambiente in uso. In questo scenario, il runbook potrebbe avere anche la necessità di accedere ad altre porte. Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, vedere [Gateway OMS](../log-analytics/log-analytics-oms-gateway.md). Il gateway di Operations Management Suite (OMS) funge da proxy per il ruolo di lavoro ibrido per runbook. Raccoglie lo stato dei processi e riceve le informazioni di configurazione dall'account di Automazione.

I runbook eseguiti in un ruolo di lavoro ibrido per runbook vengono eseguiti nel contesto dell'account di sistema locale nel computer. È consigliabile un contesto di sicurezza quando si eseguono azioni amministrative nel computer Windows locale. Se si vuole che il runbook esegua attività in risorse esterne al computer locale, potrebbe essere necessario definire asset di credenziali sicure nell'account di Automazione, a cui accedere dal runbook e da usare per l'autenticazione con la risorsa esterna. È possibile usare gli asset [Credenziali](automation-credentials.md), [Certificato](automation-certificates.md) e [Connessione](automation-connections.md) nel runbook. Usare gli asset con i cmdlet che è possibile usare per specificare le credenziali per l'autenticazione.

È possibile applicare configurazioni DSC che vengono archiviate in Automazione di Azure nelle macchine virtuali. Altri computer fisici e altre macchine virtuali possono richiedere configurazioni al server di pull di Automation DSC. Non è necessario distribuire un'infrastruttura per supportare il server di pull di Automation DSC per gestire le configurazioni dei sistemi Windows e Linux locali, fisici o virtuali. È solo necessario l'accesso a Internet in uscita da ogni sistema che verrà gestito con Automation DSC. La comunicazione avviene sulla porta TCP 443 al servizio OMS.   

## <a name="prerequisites"></a>prerequisiti

### <a name="automation-dsc"></a>Automation DSC
È possibile usare Automation DSC per gestire questi computer:

* Macchine virtuali di Azure (versione classica) che eseguono Windows o Linux.
* Macchine virtuali di Azure che eseguono Windows o Linux.
* Macchine virtuali di Amazon Web Services (AWS) che eseguono Windows o Linux.
* Computer fisici e virtuali Windows locali o in un cloud diverso da Azure o AWS.
* Computer fisici e virtuali Linux locali o in un cloud diverso da Azure o AWS.

Per i computer Windows, è necessario installare la versione più recente di Windows Management Framework (WMF) 5. Per i computer Linux, deve essere installata la versione più recente dell'[agente DSC PowerShell per Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150). L'agente DSC PowerShell usa WMF 5 per comunicare con il servizio Automazione. 

### <a name="hybrid-runbook-worker"></a>ruolo di lavoro ibrido per runbook  
Quando si designa un computer per l'esecuzione di processi del runbook ibridi, il computer deve soddisfare i prerequisiti seguenti:

* Windows Server 2012 o versioni successive.
* Windows PowerShell 4.0 o versioni successive. Per una maggiore affidabilità, si consiglia Windows PowerShell 5.0. È possibile [scaricare la nuova versione](https://www.microsoft.com/download/details.aspx?id=50395) dall'Area download Microsoft.
* .NET Framework 4.6.2 o versioni successive.
* Minimo due core.
* Minimo 4 GB di RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Autorizzazioni necessarie per creare un account di Automazione
Per creare o aggiornare un account di Automazione e completare le attività descritte in questo articolo, è necessario disporre dei privilegi e delle autorizzazioni seguenti:   
 
* Per creare un account di automazione, l'account utente di Azure Active Directory (Azure AD) deve essere aggiunto a un ruolo con autorizzazioni equivalenti al ruolo Proprietario per le risorse **Microsoft.Automation**. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  
* Nel portale di Azure, in **Azure Active Directory** > **Gestisci** > **Registrazioni per l'app**, se l'opzione **Registrazioni per l'app** è impostata su **Sì**, gli utenti senza privilegi di amministratore nel tenant di Azure AD possono [registrare applicazioni Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Se l'opzione **Registrazioni per l'app** è impostata su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD. 

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In questo scenario, viene visualizzato questo messaggio nella pagina **Aggiungi account di Automazione**: "Le autorizzazioni non sono sufficienti per creare." 

Gli utenti che prima sono stati aggiunti al ruolo di amministratore globale/coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente al ruolo Utente completo in Active Directory.

Per verificare i ruoli utente:
1. Nel portale di Azure passare al riquadro **Azure Active Directory**.
2. Selezionare **Utenti e gruppi**.
3. Selezionare **Tutti gli utenti**. 
4. Dopo aver selezionato un utente specifico selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="authentication-planning"></a>Pianificare l'autenticazione
In Automazione di Azure è possibile automatizzare le attività per le risorse che sono in Azure, in locale e in altri servizi cloud. Un runbook, per eseguire le azioni richieste, deve avere le autorizzazioni per accedere in modo sicuro alle risorse. È necessario che disponga dei diritti minimi necessari all'interno della sottoscrizione.  

### <a name="what-is-an-automation-account"></a>Che cos'è un account di Automazione 
Tutte le attività di automazione eseguite sulle risorse con i cmdlet in Automazione di Azure eseguono l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure AD.  Un account di Automazione è distinto dall'account usato per accedere al portale per configurare e usare le risorse di Azure. 

Le risorse seguenti sono incluse in un account di Automazione:

* **Certificati**. Contiene un certificato usato per eseguire l'autenticazione da un runbook o una configurazione DSC. È anche possibile aggiungere certificati.
* **Connessioni**. Contiene le informazioni di autenticazione e configurazione necessarie per la connessione a un servizio o a un'applicazione esterni da un runbook o una configurazione DSC.
* **Credenziali**. Contiene un oggetto **PSCredential** con credenziali di sicurezza, ad esempio un nome utente e una password. Le credenziali sono necessarie per l'autenticazione da un runbook o da una configurazione DSC.
* **Moduli di integrazione**. Moduli di PowerShell inclusi in un account di Automazione. Usare i moduli di PowerShell per eseguire i cmdlet nei runbook e nelle configurazioni DSC.
* **Pianificazioni**. Contiene pianificazioni che avviano o arrestano un runbook a un'ora specifica, incluse frequenze ricorrenti.
* **Variabili**. Contiene i valori disponibili da un runbook o una configurazione DSC.
* **Configurazioni DSC**. Script di PowerShell che descrivono come configurare un'impostazione o una funzionalità del sistema operativo o come installare un'applicazione in un computer Windows o Linux.  
* **Runbook**. Set di attività che eseguono un processo automatizzato in Automazione di Azure basato su Windows PowerShell.    

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure. Tuttavia, è possibile usare gli account di Automazione per gestire tutte le risorse nella sottoscrizione. Creare gli account di Automazione in aree diverse se sono presenti criteri che richiedono l'isolamento di dati e risorse in un'area specifica.

Quando si crea un account di Automazione nel portale di Azure, vengono create automaticamente due entità di autenticazione:

* **Account RunAs**. Questo account esegue le attività seguenti:
  - Crea un'entità servizio in Azure AD.
  - Crea un certificato.
  - Assegna il controllo degli accessi in base al ruolo Collaboratore, che permette di gestire le risorse di Azure Resource Manager usando i runbook.
* **Account RunAs classico**. Questo account carica un certificato di gestione. Il certificato viene usato per gestire le risorse classiche tramite runbook.

Il controllo degli accessi in base al ruolo è disponibile con Resource Manager per concedere le azioni consentite a un account utente di Azure AD e a un account RunAs. È anche possibile usare il controllo degli accessi in base al ruolo per l'autenticazione di tale entità servizio. Per altre informazioni e per assistenza per lo sviluppo di un modello per la gestione delle autorizzazioni di Automazione, vedere l'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Metodi di autenticazione
La tabella seguente riepiloga i metodi di autenticazione disponibili per ogni ambiente supportato da Automazione di Azure.

| Metodo | Environment 
| --- | --- | 
| Account RunAs di Azure e account RunAs classico |Distribuzione Azure Resource Manager e distribuzione classica di Azure |  
| Account utente di Azure AD |Distribuzione Azure Resource Manager e distribuzione classica di Azure |  
| Autenticazione di Windows |Data center locale o altro provider di servizi cloud tramite il ruolo di lavoro ibrido per runbook |  
| Credenziali di Amazon Web Services |Amazon Web Services |  

Gli articoli seguenti illustrano una panoramica e i passaggi di implementazione per configurare l'autenticazione per questi ambienti. Gli articoli descrivono l'uso di un account esistente e di un account nuovo dedicato a tale ambiente. 
* [Creare un account di Automazione di Azure autonomo](automation-create-standalone-account.md)
* [Autenticare i runbook con la distribuzione classica di Azure e Resource Manager](automation-create-aduser-account.md)
* [Autenticare i runbook con Amazon Web Services](automation-config-aws-account.md)
* [Aggiornare l'account RunAs di Automazione](automation-create-runas-account.md)

Per l'account RunAs di Azure e l'account RunAs classico, l'articolo [Aggiornare l'account RunAs di Automazione](automation-create-runas-account.md) descrive come aggiornare l'account di Automazione esistente con account RunAs tramite il portale. Viene anche descritto come usare PowerShell, se l'account di Automazione non è stato configurato in origine con un account RunAs o un account RunAs classico. È possibile creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'Autorità di certificazione globale (enterprise). Vedere [Aggiornare l'account RunAs di Automazione](automation-create-runas-account.md) per informazioni sulla procedura per creare gli account usando questa configurazione.     
 
## <a name="network-planning"></a>Pianificare la rete
Per connettersi e registrarsi a OMS, il ruolo di lavoro ibrido per runbook deve avere accesso al numero di porta e agli URL descritti in questa sezione, in aggiunta [alle porte e agli URL necessari per la connessione di Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) a OMS. 

Se si usa un server proxy per la comunicazione tra l'agente e il servizio OMS, verificare che le risorse appropriate siano accessibili. Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso.

La porta e gli URL seguenti sono necessari affinché il ruolo di lavoro ibrido per runbook comunichi con Automazione:

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita.
* URL globale: *.azure-automation.net.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area.

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net |
| Asia sudorientale |sea-jobruntimedata-prod-su1.azure-automation.net |
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net |
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft. 

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Gli intervalli per le risorse di calcolo, SQL e di archiviazione sono inclusi nel file. 
>
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. 
>
> È consigliabile scaricare il nuovo file XML ogni settimana e aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese. 
> 

## <a name="creating-an-automation-account"></a>Creare un account di Automazione

La tabella seguente introduce i metodi per la creazione di un account di Automazione nel portale di Azure. La tabella illustra ogni tipo di esperienza di distribuzione e ne spiega le differenze.  

|Metodo | DESCRIZIONE |
|-------|-------------|
| Selezionare **Automazione e controllo** in Azure Marketplace | Offerta di Azure Marketplace che crea sia un account di Automazione che un'area di lavoro OMS collegati e nello stesso gruppo di risorse e nella stessa area. L'integrazione con OMS offre anche il vantaggio di poter usare Log Analytics per monitorare e analizzare lo stato del processo del runbook e i flussi di processo nell'arco di un periodo di tempo, nonché di usare le funzionalità avanzate in Log Analytics per inoltrare o esaminare i problemi. Questa offerta distribuisce le soluzioni **Rilevamento modifiche** e **Gestione aggiornamenti**, abilitate per impostazione predefinita. |
| Selezionare **Automazione** nel Marketplace | Questo metodo crea un account di Automazione in un gruppo di risorse nuovo o esistente che non è collegato a un'area di lavoro OMS. Non include alcuna soluzione disponibile dall'offerta **Automazione e controllo**. Questo metodo è una configurazione di base introduttiva per il servizio Automazione. Può essere utile per imparare a scrivere i runbook e le configurazioni DSC, oltre a imparare a usare le funzionalità del servizio. |
| Selezionare soluzioni **Gestione** | Se si seleziona una soluzione **Gestione**, tra cui [Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md), [Avvia/Arresta macchine virtuali durante gli orari di minore attività](automation-solution-vm-management.md) o [Rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md), la soluzione richiede di selezionare un account di Automazione e un'area di lavoro OMS esistenti. La soluzione offre la possibilità di creare un account di Automazione e l'area di lavoro OMS come richiesto per la soluzione da distribuire nella sottoscrizione. |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>Creare un account di Automazione integrato con OMS
Per l'onboarding del servizio Automazione, è consigliabile selezionare l'offerta **Automazione e controllo** in Marketplace. Questo metodo crea un account di Automazione e stabilisce l'integrazione con un'area di lavoro OMS. Quando si usa questo metodo, è anche possibile scegliere di installare le soluzioni di gestione disponibili con l'offerta.  

L'articolo [Creare un account di Automazione autonomo](automation-create-standalone-account.md) illustra il processo di creazione di un account di Automazione e un'area di lavoro OMS con l'onboarding dell'offerta **Automazione e controllo**. È possibile scoprire come creare un account di Automazione autonomo per testare il servizio o esaminarlo in anteprima.  

Per creare un account di Automazione e l'area di lavoro OMS tramite l'offerta del Marketplace **Automazione e controllo**:

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Selezionare **Nuovo**.<br><br> ![Selezionare Nuovo nel portale di Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Cercare **Automazione**. Nei risultati della ricerca selezionare **Automazione e controllo**.<br><br> ![Cercare e selezionare Automazione e controllo in Azure Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Leggere la descrizione dell'offerta e quindi selezionare **Crea**.  
5. In **Automazione e controllo** selezionare **Area di lavoro OMS**. In **Aree di lavoro OMS** selezionare un'area di lavoro OMS collegata alla sottoscrizione di Azure in cui è incluso l'account di Automazione. Se non è disponibile un'area di lavoro OMS, selezionare **Crea una nuova area di lavoro**. In **Area di lavoro OMS**: 
  1. In **Area di lavoro OMS** immettere un nome per la nuova area di lavoro.
  2. In **Sottoscrizione** selezionare una sottoscrizione a cui collegarsi. Se la selezione predefinita non è la sottoscrizione che si vuole usare, selezionare la sottoscrizione nell'elenco a discesa.
  3. In **Gruppo di risorse** è possibile selezionare un gruppo di risorse esistente o crearne uno.  
  4. In **Località** selezionare un'area. Per altre informazioni, vedere le [aree in cui è disponibile Automazione di Azure](https://azure.microsoft.com/regions/services/). Le soluzioni sono offerte in due livelli, ovvero quello gratuito e il livello per nodo (OMS). Il livello gratuito presenta un limite per la quantità di dati raccolti al giorno, il periodo di memorizzazione e i minuti di esecuzione dei processi dei runbook. Il livello per nodo (OMS) non ha limiti per la quantità di dati raccolti quotidianamente.  
  5. Selezionare **Account di Automazione**.  Se si crea una nuova area di lavoro OMS, è necessario creare anche un account di Automazione associato alla nuova area di lavoro OMS. Includere la sottoscrizione, il gruppo di risorse e l'area di Azure. 
    1. Selezionare **Crea un account di automazione**.
    2. In **Account di automazione**, nel campo **Nome** immettere il nome dell'account di Automazione.
    Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro OMS selezionata e non è possibile modificarle. 
    3. Il metodo di autenticazione predefinito per l'offerta è un account RunAs di Azure. Dopo aver selezionato **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione. Per tenere traccia dello stato, scegliere **Notifiche** dal menu. 
    4. In alternativa, selezionare un account RunAs di Automazione esistente. L'account selezionato non può essere già collegato a un'altra area di lavoro OMS. In questo caso, viene visualizzato un messaggio di notifica. Se l'account è già collegato a un'area di lavoro OMS, selezionare un account RunAs di Automazione diverso o crearne uno.
    5. Dopo aver immesso o selezionato le informazioni necessarie, selezionare **Crea**. Le informazioni vengono verificate e vengono creati l'account di Automazione e gli account RunAs. Si torna automaticamente al riquadro **Area di lavoro OMS**.  
6. Dopo aver immesso o selezionato le informazioni necessarie nel riquadro **Area di lavoro OMS** selezionare **Crea**.  Le informazioni vengono verificate e viene creata l'area di lavoro. Per tenere traccia dello stato, scegliere **Notifiche** dal menu. Viene visualizzato nuovamente il riquadro **Aggiungi soluzione**.  
7. Nelle impostazioni **Automazione e controllo** confermare che si vogliono installare le soluzioni preselezionate consigliate. Se si modifica una qualsiasi delle opzioni predefinite, è possibile installare le soluzioni singolarmente in un secondo momento.  
8. Per procedere all'onboarding di Automazione e di un'area di lavoro OMS, selezionare **Crea**. Tutte le impostazioni vengono convalidate e Azure tenta di distribuire l'offerta nella sottoscrizione. Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato, scegliere **Notifiche** dal menu. 

Al termine dell'onboarding dell'offerta è possibile eseguire le attività seguenti:
* Iniziare a creare i runbook.
* Utilizzare le soluzioni di gestione abilitate.
* Distribuire un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md).
* Iniziare a usare [Log Analytics](https://docs.microsoft.com/azure/log-analytics) per raccogliere i dati generati dalle risorse nel cloud o nell'ambiente locale.   

## <a name="next-steps"></a>Passaggi successivi
* Verificare che il nuovo account di Automazione possa completare l'autenticazione per le risorse di Azure. Per altre informazioni, vedere [Testare l'autenticazione con un account RunAs di Automazione di Azure](automation-verify-runas-authentication.md).
* Informazioni introduttive sulla creazione dei runbook e sulle considerazioni correlate prima di iniziare la creazione. Per altre informazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md).


