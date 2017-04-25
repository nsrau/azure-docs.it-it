--- 
title: Introduzione ad Automazione di Azure | Microsoft Docs
description: Questo articolo offre una panoramica del servizio Automazione di Azure e illustra i concetti principali e i dettagli sull&quot;implementazione per prepararsi a caricare l&quot;offerta da Azure Marketplace.
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 1876b78186da3aa6c0ae9dc7de3b7ab7e46888bb
ms.lasthandoff: 04/17/2017

---

## <a name="getting-started-with-azure-automation"></a>Introduzione ad Automazione di Azure

Questa guida introduttiva presenta i concetti principali relativi alla distribuzione di Automazione di Azure. Se non si ha familiarità con Automazione di Azure o si ha esperienza con un software di flusso di lavoro di automazione, ad esempio System Center Orchestrator, questa guida consente di iniziare a conoscere i concetti e i dettagli della distribuzione. 

## <a name="key-concepts"></a>Concetti chiave

### <a name="automation-service"></a>Servizio Automazione
Automazione è un servizio di Azure che usa le tecnologie di Windows PowerShell e Azure per controllare la gestione dell'infrastruttura e delle applicazioni Azure, cloud e locali.  Automazione di Azure consente di gestire il ciclo di vita completo dei servizi e delle applicazioni con la distribuzione automatizzata tramite l'automazione dei processi, la configurazione del sistema operativo tramite PowerShell DSC (Desired State Configuration), l'aggiornamento e il monitoraggio continui con la gestione aggiornamenti e il rilevamento delle modifiche e la risoluzione dei problemi e la correzione automatizzate.

### <a name="automation-account"></a>Account di Automazione
Un account di Automazione è una risorsa di Azure creata dall'utente.  È possibile gestire tutte le risorse di Azure, cloud e locali con un solo account di Automazione.  Un account di Automazione è un contenitore per gli elementi necessari a eseguire il lavoro di automazione, ovvero runbook, moduli, asset, ad esempio credenziali e pianificazioni, e configurazioni. È possibile usare più account di Automazione per separare le risorse in ambienti logici distinti, ad esempio sviluppo, test e produzione, o in aree geografiche.  

### <a name="hybrid-runbook-worker"></a>ruolo di lavoro ibrido per runbook
Per eseguire runbook in sistemi fisici o virtuali nel data center locale, in Azure o in un altro provider di servizi cloud, la funzionalità Ruolo di lavoro ibrido per runbook consente di accedere a tali risorse locali e di gestirle.     

### <a name="automation-desired-state-configuration"></a>Configurazione dello stato desiderato di Automazione
Automation DSC (Desired State Configuration), basato su PowerShell DSC, configura, monitora e aggiorna automaticamente lo stato desiderato dei sistemi operativi ospitati in Azure, in locale o in un altro cloud.  

### <a name="management-solutions"></a>Soluzioni di gestione
Le soluzioni di gestione, ad esempio Gestione aggiornamenti e Rilevamento modifiche, estendono la funzionalità di Automazione di Azure e vengono usate con Log Analytics.  Possono includere più risorse che supportano un particolare scenario di gestione, ad esempio runbook di Automazione, query e avvisi di Log Analytics predefiniti e visualizzazioni.  

## <a name="architecture-overview"></a>Panoramica dell'architettura

Automazione di Azure è un'applicazione SaaS (Software as a Service) che offre un ambiente multi-tenant scalabile e affidabile per automatizzare i processi con i runbook e gestire le modifiche di configurazione ai sistemi Windows e Linux usando DSC (Desired State Configuration) in Azure, in altri servizi cloud o in locale. Le entità contenute nell'account di Automazione, ad esempio runbook, asset, account RunAs, sono isolate dagli altri account di Automazione nella propria sottoscrizione e in altre sottoscrizioni.  

I runbook eseguiti in Azure vengono eseguiti nelle sandbox di Automazione, ospitate nelle macchine virtuali della piattaforma distribuita come servizio (PaaS) di Azure.  Le sandbox di Automazione forniscono l'isolamento dei tenant per tutti gli aspetti dell'esecuzione dei runbook: moduli, spazio di archiviazione, memoria, comunicazione di rete, flussi di processo e così via. Questo ruolo viene gestito dal servizio e non è accessibile dall'account Azure o Automazione di Azure per poterlo controllare.         

Per automatizzare la distribuzione e la gestione delle risorse nel data center locale o in altri servizi cloud, è possibile designare uno o più computer per eseguire il ruolo di lavoro ibrido per runbook.  Ogni ruolo di lavoro ibrido per runbook richiede l'agente di gestione Microsoft (MMA) con una connessione a un'area di lavoro di Log Analytics e un account di Automazione.  Log Analytics viene usato per avviare l'installazione, gestire l'agente MMA e monitorare la funzionalità del ruolo di lavoro ibrido per runbook.  La distribuzione dei runbook e l'istruzione per eseguirli vengono gestite mediante Automazione di Azure.

È possibile distribuire più ruoli di lavoro ibridi per runbook per garantire una disponibilità elevata per i runbook, bilanciare il carico dei processi dei runbook e in alcuni casi dedicarli a carichi di lavoro o ambienti specifici.  Il ruolo di lavoro ibrido per runbook comunica con il servizio Automazione tramite la porta in uscita TCP 443.  Quando il runbook è in esecuzione in un ruolo di lavoro ibrido per runbook nel data center e si vuole che il runbook esegua attività di gestione in altri computer o servizi del data center, il runbook potrebbe dover accedere ad altre porte.  Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, vedere l'articolo [Gateway OMS](../log-analytics/log-analytics-oms-gateway.md). Questo gateway funge da proxy per consentire al ruolo di lavoro ibrido per runbook di raccogliere lo stato dei processi e di ricevere informazioni di configurazione dall'account di Automazione.

I runbook in esecuzione in un ruolo di lavoro ibrido per runbook vengono eseguiti nel contesto dell'account di sistema locale del computer, che è il contesto di sicurezza consigliato quando si eseguono azioni amministrative nel computer Windows locale. Se si vuole che il runbook esegua attività in risorse esterne al computer locale, potrebbe essere necessario definire asset di credenziali sicure nell'account di Automazione, a cui accedere dal runbook e da usare per l'autenticazione con la risorsa esterna. Nel proprio runbook è possibile usare asset di tipo [Credenziali](automation-credentials.md) [Certificato](automation-certificates.md) e [Connessione](automation-connections.md) con cmdlet che consentono di specificare le credenziali per poter eseguire l'autenticazione.

Le configurazioni DSC archiviate in Automazione di Azure possono essere applicate direttamente alle macchine virtuali di Azure. Altre macchine fisiche e virtuali possono richiedere configurazioni al server di pull DSC di Automazione di Azure.  Per la gestione delle configurazioni dei sistemi Windows e Linux fisici o virtuali locali, non è necessario distribuire alcuna infrastruttura per supportare il server pull di Automation DSC, ma solo l'accesso Internet in uscita da ogni sistema da gestire con Automation DSC, comunicando tramite la porta TCP 443 con il servizio OMS.   

![Panoramica di Automazione di Azure](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

## <a name="prerequisites"></a>Prerequisiti

### <a name="automation-dsc"></a>Automation DSC
È possibile usare Automation DSC per Azure in molti tipi di computer:

* Macchine virtuali di Azure (versione classica) che eseguono Windows o Linux
* Macchine virtuali di Azure che eseguono Windows o Linux
* Macchine virtuali di Amazon Web Services (AWS) che eseguono Windows o Linux
* Computer fisici/virtuali Windows locali o in un cloud diverso da Azure o AWS
* Computer fisici/virtuali Linux locali o in un cloud diverso da Azure o AWS

Perché l'agente DSC di PowerShell per Windows possa comunicare con Automazione di Azure, deve essere installata la versione più recente di WMF 5. È necessario installare la versione più recente dell'[agente DSC di PowerShell per Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) per poter comunicare con Automazione di Azure.

### <a name="hybrid-runbook-worker"></a>ruolo di lavoro ibrido per runbook  
Quando si designa un computer per l'esecuzione di processi di runbook ibridi, il computer presenta i requisiti seguenti:

* Windows Server 2012 o versioni successive
* Windows PowerShell 4.0 o versioni successive.  Si consiglia di installare Windows PowerShell 5.0 nel computer per aumentare l'affidabilità. È possibile scaricare la nuova versione dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=50395)
* Almeno due core
* Almeno 4 GB di RAM

## <a name="security"></a>Sicurezza
Automazione di Azure consente di automatizzare le attività sulle risorse in Azure, in locale e con altri provider di servizi cloud.  Un runbook, per eseguire le azioni obbligatorie, deve avere le autorizzazioni per accedere in modo sicuro alle risorse con i diritti minimi necessari nella sottoscrizione.  

### <a name="automation-account"></a>Account di automazione 
Tutte le attività di automazione eseguite sulle risorse con i cmdlet di Azure in Automazione di Azure eseguono l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure Active Directory.  Un account di Automazione è distinto dall'account usato per accedere al portale per configurare e usare le risorse di Azure.  

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure, ma gli account di Automazione possono gestire tutte le risorse nella sottoscrizione. Creare gli account di Automazione in aree diverse se sono presenti criteri che richiedono l'isolamento di dati e risorse in un'area specifica.

> [!NOTE]
> Gli account di Automazione e le risorse che contengono, creati nel portale di Azure, non sono accessibili nel portale di Azure classico. Se si vogliono gestire questi account o le relative risorse con Windows PowerShell, è necessario usare i moduli di Gestione risorse di Azure.
> 

Quando si crea un account di Automazione nel portale di Azure, vengono create automaticamente due entità di autenticazione:

* Un account RunAs. Questo account crea un'entità servizio in Azure Active Directory (Azure AD) e un certificato. Assegna il controllo degli accessi in base al ruolo Collaboratore, che permette di gestire le risorse di Resource Manager usando i runbook.
* Un account RunAs classico. Questo account carica un certificato di gestione, che viene usato per gestire le risorse classiche usando i runbook.

Il controllo degli accessi in base al ruolo è disponibile con Azure Resource Manager per l'esecuzione di azioni consentite con un account utente di Azure AD e per l'autenticazione di tale entità servizio.  Per altre informazioni utili per sviluppare il modello per la gestione delle autorizzazioni di Automazione, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  



#### <a name="authentication-methods"></a>Metodi di autenticazione
La tabella seguente riepiloga i diversi metodi di autenticazione per ogni ambiente supportato da Automazione di Azure.

| Metodo | Environment 
| --- | --- | 
| Account RunAs di Azure e account RunAs classico |Distribuzione Azure Resource Manager e distribuzione classica di Azure |  
| Account utente di Azure AD |Distribuzione Azure Resource Manager e distribuzione classica di Azure |  
| Autenticazione di Windows |Data center locale o altro provider di servizi cloud che usa il ruolo di lavoro ibrido per runbook |  
| Credenziali AWS |Amazon Web Services |  

Nella sezione **Procedure\Autenticazione e sicurezza** sono disponibili articoli che offrono una panoramica e illustrano la procedura di implementazione per configurare l'autenticazione per tali ambienti, con un account esistente o nuovo dedicato a tale ambiente.  Per l'account RunAs di Azure e l'account RunAs classico, l'argomento [Update Automation Run As account using PowerShell](automation-update-account-powershell.md) (Aggiornare l'account RunAs di Automazione usando PowerShell) descrive come aggiornare l'account di Automazione esistente con l'account RunAs usando PowerShell se in origine non è stato configurato con un account RunAs o RunAs classico.   
 
## <a name="network"></a>Rete
Per connettersi e registrarsi a Microsoft Operations Management Suite (OMS), il ruolo di lavoro ibrido per runbook deve avere accesso al numero di porta e agli URL descritti di seguito,  in aggiunta [alle porte e agli URL necessari per la connessione di Microsoft Monitoring Agent](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent) a OMS. Se si usa un server proxy per la comunicazione tra l'agente e il servizio OMS, è necessario verificare che le risorse appropriate siano accessibili. Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso.

Di seguito sono elencati la porta e gli URL necessari affinché il ruolo di lavoro ibrido per runbook comunichi con Automazione.

* Porta: è necessaria solo la porta TCP 443 per l'accesso a Internet in uscita
* URL globale: *.azure-automation.net

Se è stato definito un account di Automazione per un'area specifica e si vuole limitare la comunicazione con il data center di tale area, nella tabella seguente è riportato il record DNS per ogni area.

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

## <a name="implementation"></a>Implementazione

### <a name="creating-an-automation-account"></a>Creare un account di Automazione

Esistono diversi modi per creare un account di Automazione nel portale di Azure.  La tabella seguente illustra ogni tipo di esperienza di distribuzione e ne spiega le differenze.  

|Metodo | Descrizione |
|-------|-------------|
| Selezionare Automation & Control nel Marketplace | Offerta che crea sia un account di Automazione che un'area di lavoro OMS collegate tra loro nello stesso gruppo di risorse e nella stessa area.  Distribuisce anche le soluzioni Rilevamento modifiche e Gestione aggiornamenti, abilitate per impostazione predefinita. |
| Selezionare Automazione nel Marketplace | Crea un account di Automazione in un gruppo di risorse nuovo o esistente non collegato a un'area di lavoro OMS e non include nessuna delle soluzioni disponibili nell'offerta Automation & Control. Si tratta di una configurazione di base che introduce Automazione e consente di imparare a scrivere runbook e configurazioni DSC e a usare le funzionalità del servizio. |
| Soluzioni di gestione selezionate | Se si seleziona una soluzione (**[Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md)**, **[Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management.md)** o **[Rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md)**), viene chiesto di selezionare un'area di lavoro OMS e un account di Automazione esistenti o viene data la possibilità di crearli entrambi perché sono necessari per la distribuzione della soluzione nella sottoscrizione. |

Questo argomento illustra come creare un account di Automazione e un'area di lavoro OMS caricando l'offerta Automation & Control.  Per creare un account di Automazione autonomo per il test o l'anteprima del servizio, vedere l'articolo [Creare un account di Automazione autonomo](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-log-analytics"></a>Creare un account di Automazione integrato con Log Analytics
Il metodo consigliato per caricare Automazione prevede la selezione dell'offerta Automation & Control nel Marketplace.  In questo modo viene creato un account di Automazione e viene stabilita l'integrazione con un'area di lavoro OMS, inclusa l'opzione per installare le soluzioni di gestione disponibili con l'offerta.  

>[!NOTE]
>Per creare un account di Automazione, l'utente deve essere membro del ruolo Amministratori del servizio o coamministratore della sottoscrizione che concede l'accesso alla sottoscrizione. Deve anche essere aggiunto come utente all'istanza di Active Directory predefinita di tale sottoscrizione. Non è necessario assegnare all'account un ruolo con privilegi.
>
>Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In tal caso si riceve un avviso di tipo "L'utente non è autorizzato a creare..." nel pannello **Aggiungi account di Automazione**.
>
>Gli utenti che prima sono stati aggiunti al ruolo di coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione dal riquadro **Azure Active Directory** nel portale di Azure, selezionare **Utenti e gruppi**, **Tutti gli utenti**, selezionare l'utente specifico e quindi scegliere **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

2. Fare clic su **New**.<br><br> ![Selezionare l'opzione Nuovo nel portale di Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Cercare **Automazione** e quindi nei risultati della ricerca selezionare **Automation & Control***.<br><br> ![Cercare e selezionare Automation & Control nel Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Dopo avere letto la descrizione dell'offerta, fare clic su **Crea**.  

5. Nel pannello delle impostazioni **Automation & Control** selezionare **Area di lavoro OMS**.  Nel pannello **Aree di lavoro OMS** selezionare un'area di lavoro OMS collegata alla stessa sottoscrizione di Azure in cui si trova l'account di Automazione o creare un'area di lavoro OMS.  Se non è disponibile un'area di lavoro OMS, selezionare **Crea nuova area di lavoro** e seguire questa procedura nel pannello **Area di lavoro OMS**: 
   - Specificare un nome per la nuova **area di lavoro OMS**.
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno.  
   - Selezionare un **percorso**.  Le uniche località attualmente disponibili sono l'**Australia sud-orientale**, gli **Stati Uniti orientali**, l'**Asia sud-orientale**, gli **Stati Uniti centrali** e l'**Europa occidentale**.
   - Selezionare un **Piano tariffario**.  Per la soluzione sono disponibili due livelli, quello gratuito e il livello Per nodo (OMS).  Il livello gratuito presenta un limite per la quantità di dati raccolti al giorno, il periodo di memorizzazione e i minuti di esecuzione dei processi dei runbook.  Il livello Per nodo (OMS) non ha limiti per la quantità di dati raccolti al giorno.  
   - Selezionare **Account di Automazione**.  Se si sta creando una nuova area di lavoro OMS, viene richiesto di creare anche un account di Automazione da associare alla nuova area di lavoro OMS specificata in precedenza, nonché l'area, il gruppo di risorse e la sottoscrizione di Azure.  È possibile selezionare **Crea un account di Automazione** e specificare le informazioni seguenti nel pannello **Account di Automazione**: 
  - Nel campo **Nome** immettere il nome dell'account di Automazione.

    Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro OMS selezionata e non è possibile modificarle.  Il metodo di autenticazione predefinito per l'offerta è un account RunAs di Azure.  Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione.  Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

    In alternativa, selezionare un account RunAs di Automazione esistente.  L'account selezionato non può essere già collegato a un'altra area di lavoro OMS. In caso contrario nel pannello viene visualizzato un messaggio di notifica.  Se è già collegato, è necessario selezionare un account RunAs di Automazione diverso o crearne uno.

    Dopo avere immesso le informazioni obbligatorie, fare clic su **Crea**.  Le informazioni vengono verificate e vengono creati l'account di Automazione e gli account RunAs.  Si torna automaticamente al pannello **Area di lavoro OMS**.  

6. Dopo aver specificato le informazioni necessarie nel pannello **Area di lavoro OMS**, fare clic su **Crea**.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu.  Viene visualizzato nuovamente il pannello **Aggiungi soluzione**.  

7. Nel pannello delle impostazioni di **Automation & Control** confermare l'installazione delle soluzioni preselezionate consigliate. Se ne viene deseleziona qualcuna, sarà possibile installarle singolarmente in un secondo momento.  

8. Fare clic su **Crea** per procedere all'onboarding di Automazione e di un'area di lavoro OMS. Tutte le impostazioni vengono convalidate e viene eseguito un tentativo di distribuzione dell'offerta nella sottoscrizione.  Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

Dopo il caricamento dell'offerta, è possibile iniziare a creare i runbook, a utilizzare le soluzioni di gestione abilitate o a utilizzare [Log Analytics](https://docs.microsoft.com/azure/log-analytics) per raccogliere i dati generati dalle risorse negli ambienti cloud o locali.   

### <a name="resources-included"></a>Risorse incluse
Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse. Le risorse sono riepilogate nelle due tabelle seguenti:<br>

#### <a name="run-as-account-resources"></a>Risorse dell'account RunAs

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureAutomationTutorial | Runbook grafico di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialScript | Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| AzureRunAsCertificate | Asset di certificato creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell riportato più avanti per un account esistente. Il certificato permette di eseguire l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager con i runbook. La durata di questo certificato è di un anno. |
| AzureRunAsConnection | Asset di connessione creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell per un account esistente. |

#### <a name="classic-run-as-account-resources"></a>Risorse dell'account RunAs classico

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureClassicAutomationTutorial | Runbook grafico di esempio che ottiene tutte le macchine virtuali create con il modello di distribuzione classica in una sottoscrizione usando l'account RunAs classico (certificato) e quindi scrive il nome e lo stato delle macchine virtuali. |
| Runbook di script AzureClassicAutomationTutorial | Runbook di PowerShell di esempio che ottiene tutte le macchine virtuali classiche in una sottoscrizione usando l'account RunAs classico (certificato) e quindi scrive il nome e lo stato delle macchine virtuali. |
| AzureClassicRunAsCertificate | Asset di certificato creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. La durata di questo certificato è di un anno. |
| AzureClassicRunAsConnection | Asset di connessione creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. |Eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. |

## <a name="next-steps"></a>Passaggi successivi
* Per verificare che il nuovo account di Automazione possa eseguire l'autenticazione con le risorse di Azure, vedere [Test Azure Automation Run As account authentication](automation-verify-runas-authentication.md) (Testare l'autenticazione di un account RunAs di Automazione di Azure).
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).


