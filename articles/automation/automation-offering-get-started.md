---
title: Introduzione ad Automazione di Azure | Microsoft Docs
description: Questo articolo offre una panoramica del servizio Automazione di Azure e illustra i dettagli di progettazione e implementazione per prepararsi a caricare l'offerta da Azure Marketplace.
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 2a57b60a2222d6e2ea864410edc6a32a0bf0c76c
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="getting-started-with-azure-automation"></a>Introduzione ad Automazione di Azure

Questa guida introduttiva presenta i concetti principali relativi alla distribuzione di Automazione di Azure. Se non si ha familiarità con Automazione di Azure o si ha esperienza con un software di flusso di lavoro di automazione come System Center Orchestrator, questa guida consente di acquisire le informazioni necessarie per preparare e caricare Automazione.  Successivamente, verranno fornite informazioni per iniziare a sviluppare i runbook necessari per soddisfare le esigenze di automazione del processo. 


## <a name="automation-architecture-overview"></a>Panoramica dell'architettura di Automazione

![Panoramica di Automazione di Azure](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Automazione di Azure è un'applicazione SaaS (Software as a Service) che offre un ambiente multi-tenant scalabile e affidabile per automatizzare i processi con i runbook e gestire le modifiche di configurazione ai sistemi Windows e Linux usando DSC (Desired State Configuration) in Azure, in altri servizi cloud o in locale. Le entità contenute nell'account di Automazione, ad esempio runbook, asset, account RunAs, sono isolate dagli altri account di Automazione nella propria sottoscrizione e in altre sottoscrizioni.  

I runbook eseguiti in Azure vengono eseguiti nelle sandbox di Automazione, ospitate nelle macchine virtuali della piattaforma distribuita come servizio (PaaS) di Azure.  Le sandbox di Automazione forniscono l'isolamento dei tenant per tutti gli aspetti dell'esecuzione dei runbook: moduli, spazio di archiviazione, memoria, comunicazione di rete, flussi di processo e così via. Questo ruolo viene gestito dal servizio e non è accessibile dall'account Azure o Automazione di Azure per poterlo controllare.         

Per automatizzare la distribuzione e la gestione delle risorse nel data center locale o in altri servizi cloud, dopo aver creato un account di Automazione è possibile designare uno o più computer per eseguire il [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md).  Ogni ruolo di lavoro ibrido per runbook richiede l'agente di gestione Microsoft con una connessione a un'area di lavoro di Log Analytics e un account di Automazione.  Log Analytics viene usato per avviare l'installazione, gestire l'agente di gestione Microsoft e monitorare la funzionalità del ruolo di lavoro ibrido per runbook.  La distribuzione dei runbook e l'istruzione per eseguirli vengono gestite mediante Automazione di Azure.

È possibile distribuire più ruoli di lavoro ibridi per runbook per garantire una disponibilità elevata per i runbook, bilanciare il carico dei processi dei runbook e in alcuni casi dedicarli a carichi di lavoro o ambienti specifici.  Microsoft Monitoring Agent sul ruolo di lavoro ibrido per runbook avvia le comunicazioni con il servizio Automazione sulla porta TCP 443 e non sono previsti requisiti per il firewall in ingresso.  Quando il runbook è in esecuzione in un ruolo di lavoro ibrido per runbook nell'ambiente e si vuole che il runbook esegua attività di gestione in altri computer o servizi di tale ambiente, il runbook potrebbe dover accedere ad altre porte.  Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, vedere l'articolo [Gateway OMS](../log-analytics/log-analytics-oms-gateway.md). Questo gateway funge da proxy per consentire al ruolo di lavoro ibrido per runbook di raccogliere lo stato dei processi e di ricevere informazioni di configurazione dall'account di Automazione.

I runbook in esecuzione in un ruolo di lavoro ibrido per runbook vengono eseguiti nel contesto dell'account di sistema locale del computer, che è il contesto di sicurezza consigliato quando si eseguono azioni amministrative nel computer Windows locale. Se si vuole che il runbook esegua attività in risorse esterne al computer locale, potrebbe essere necessario definire asset di credenziali sicure nell'account di Automazione, a cui accedere dal runbook e da usare per l'autenticazione con la risorsa esterna. Nel proprio runbook è possibile usare asset di tipo [Credenziali](automation-credentials.md) [Certificato](automation-certificates.md) e [Connessione](automation-connections.md) con cmdlet che consentono di specificare le credenziali per poter eseguire l'autenticazione.

Le configurazioni DSC archiviate in Automazione di Azure possono essere applicate direttamente alle macchine virtuali di Azure. Altre macchine fisiche e virtuali possono richiedere configurazioni al server di pull DSC di Automazione di Azure.  Per la gestione delle configurazioni dei sistemi Windows e Linux fisici o virtuali locali, non è necessario distribuire alcuna infrastruttura per supportare il server pull di Automation DSC, ma solo l'accesso Internet in uscita da ogni sistema da gestire con Automation DSC, comunicando tramite la porta TCP 443 con il servizio OMS.   

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
* .NET Framework 4.6.2 o versioni successive
* Almeno due core
* Almeno 4 GB di RAM

### <a name="permissions-required-to-create-automation-account"></a>Autorizzazioni necessarie per creare un account di Automazione
Per creare o aggiornare un account di Automazione, è necessario avere le autorizzazioni e i privilegi specifici seguenti, necessari per il completamento di questo argomento.   
 
* Per creare un account di Automazione, l'account utente di AD deve essere aggiunto a un ruolo con autorizzazioni equivalenti al ruolo Proprietario per le risorse di Microsoft.Automation, come indicato nell'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  
* Se le impostazioni delle registrazioni dell'app sono impostate su **Sì**, gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni di AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Se le impostazioni delle registrazioni dell'app sono impostate su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD. 

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In questa situazione si riceve un avviso di tipo "L'utente non è autorizzato a creare..." nel pannello **Aggiungi account di Automazione**. Gli utenti che prima sono stati aggiunti al ruolo di amministratore globale/coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione dal riquadro **Azure Active Directory** nel portale di Azure selezionare **Utenti e gruppi**, **Tutti gli utenti** e, dopo avere selezionato l'utente specifico, selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="authentication-planning"></a>Pianificazione di autenticazione
Automazione di Azure consente di automatizzare le attività sulle risorse in Azure, in locale e con altri provider di servizi cloud.  Un runbook, per eseguire le azioni obbligatorie, deve avere le autorizzazioni per accedere in modo sicuro alle risorse con i diritti minimi necessari nella sottoscrizione.  

### <a name="what-is-an-automation-account"></a>Che cos'è un account di Automazione 
Tutte le attività di automazione eseguite sulle risorse con i cmdlet di Azure in Automazione di Azure eseguono l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure Active Directory.  Un account di Automazione è distinto dall'account usato per accedere al portale per configurare e usare le risorse di Azure.  Di seguito sono elencate le risorse di Automazione incluse in un account:

* **Certificati**: contiene un certificato necessario per eseguire l'autenticazione da un runbook o una configurazione DSC o per aggiungerli.
* **Connessioni**: contiene le informazioni di autenticazione e configurazione necessarie per eseguire la connessione a un servizio esterno o a un'applicazione da un runbook o una configurazione DSC.
* **Credenziali**: oggetto PSCredential contenente le credenziali di sicurezza (ad es. nome utente e password) necessarie per eseguire l'autenticazione da un runbook o una configurazione DSC.
* **Moduli di integrazione**: moduli PowerShell forniti con un account di Automazione di Azure per usare i cmdlet all'interno di runbook e configurazioni DSC.
* **Pianificazioni**: contiene pianificazioni che avviano o arrestano un runbook a un'ora specifica, incluse frequenze ricorrenti.
* **Variabili**: contiene i valori disponibili da un runbook o una configurazione DSC.
* **Configurazioni DSC**: script di PowerShell che descrivono come configurare un'impostazione o una funzionalità del sistema operativo o come installare un'applicazione in un computer Windows o Linux.  
* **Runbook**: set di attività che esegue un processo automatizzato in Automazione di Azure basato su Windows PowerShell.    

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

Nella sezione **Procedure\Autenticazione e sicurezza** sono disponibili articoli che offrono una panoramica e illustrano la procedura di implementazione per configurare l'autenticazione per tali ambienti, con un account esistente o nuovo dedicato all'ambiente.  Per l'account RunAs di Azure e l'account RunAs classico, l'argomento [Aggiornare l'account RunAs di Automazione](automation-create-runas-account.md) descrive come aggiornare l'account di Automazione esistente con account RunAs tramite il portale o PowerShell se in origine non è stato configurato con un account RunAs o RunAs classico. Se si vuole creare un account RunAs o RunAs classico con un certificato rilasciato dall'autorità di certificazione dell'organizzazione (enterprise), leggere questo articolo per informazioni su come creare gli account con questa configurazione.     
 
## <a name="network-planning"></a>Pianificazione della rete
Per connettersi e registrarsi a Microsoft Operations Management Suite (OMS), il ruolo di lavoro ibrido per runbook deve avere accesso al numero di porta e agli URL descritti di seguito,  in aggiunta [alle porte e agli URL necessari per la connessione di Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) a OMS. Se si usa un server proxy per la comunicazione tra l'agente e il servizio OMS, è necessario verificare che le risorse appropriate siano accessibili. Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso.

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

Per un elenco di indirizzi IP invece dei nomi, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft. 

> [!NOTE]
> Questo file contiene gli intervalli di indirizzi IP, inclusi gli intervalli di Calcolo, SQL e Archiviazione, usati nei data center di Microsoft Azure. Ogni settimana viene pubblicato un file che riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non verranno usati nei data center per almeno una settimana. Scaricare il nuovo file XML ogni settimana e apportare le modifiche necessarie nel sito per identificare correttamente i servizi in esecuzione in Azure. Gli utenti di ExpressRoute possono notare che questo file viene usato per aggiornare l'annuncio BGP dello spazio di Azure la prima settimana del mese. 
> 

## <a name="creating-an-automation-account"></a>Creare un account di Automazione

Esistono diversi modi per creare un account di Automazione nel portale di Azure.  La tabella seguente illustra ogni tipo di esperienza di distribuzione e ne spiega le differenze.  

|Metodo | Descrizione |
|-------|-------------|
| Selezionare Automation & Control nel Marketplace | Offerta che crea sia un account di Automazione che un'area di lavoro OMS collegate tra loro nello stesso gruppo di risorse e nella stessa area.  L'integrazione con OMS offre anche il vantaggio di poter usare Log Analytics per monitorare e analizzare lo stato del processo del runbook e i flussi di processo nell'arco di un periodo di tempo e di usare funzionalità avanzate per inoltrare o esaminare i problemi. Questa offerta distribuisce anche le soluzioni Rilevamento modifiche e Gestione aggiornamenti, abilitate per impostazione predefinita. |
| Selezionare Automazione nel Marketplace | Crea un account di Automazione in un gruppo di risorse nuovo o esistente non collegato a un'area di lavoro OMS e non include nessuna delle soluzioni disponibili nell'offerta Automation & Control. Si tratta di una configurazione di base che introduce Automazione e consente di imparare a scrivere runbook e configurazioni DSC e a usare le funzionalità del servizio. |
| Soluzioni di gestione selezionate | Se si seleziona una soluzione (**[Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md)**, **[Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management.md)** o **[Rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md)**), viene chiesto di selezionare un'area di lavoro OMS e un account di Automazione esistenti o viene data la possibilità di crearli entrambi perché sono necessari per la distribuzione della soluzione nella sottoscrizione. |

Questo argomento illustra come creare un account di Automazione e un'area di lavoro OMS caricando l'offerta Automation & Control.  Per creare un account di Automazione autonomo per il test o l'anteprima del servizio, vedere l'articolo [Creare un account di Automazione autonomo](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-oms"></a>Creare un account di Automazione integrato con OMS
Il metodo consigliato per caricare Automazione prevede la selezione dell'offerta Automation & Control nel Marketplace.  In questo modo viene creato un account di Automazione e viene stabilita l'integrazione con un'area di lavoro OMS, inclusa l'opzione per installare le soluzioni di gestione disponibili con l'offerta.  

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

2. Fare clic su **New**.<br><br> ![Selezionare l'opzione Nuovo nel portale di Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Cercare **Automazione** e quindi nei risultati della ricerca selezionare **Automation & Control***.<br><br> ![Cercare e selezionare Automation & Control nel Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Dopo avere letto la descrizione dell'offerta, fare clic su **Crea**.  

5. Nel pannello delle impostazioni **Automation & Control** selezionare **Area di lavoro OMS**.  Nel pannello **Aree di lavoro OMS** selezionare un'area di lavoro OMS collegata alla stessa sottoscrizione di Azure in cui si trova l'account di Automazione o creare un'area di lavoro OMS.  Se non è disponibile un'area di lavoro OMS, selezionare **Crea nuova area di lavoro** e seguire questa procedura nel pannello **Area di lavoro OMS**: 
   - Specificare un nome per la nuova **area di lavoro OMS**.
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno.  
   - Selezionare un **percorso**.  Per altre informazioni, vedere le [are in cui è disponibile Automazione di Azure](https://azure.microsoft.com/regions/services/).  Le soluzioni sono offerte in due livelli, ovvero quello gratuito e il livello Per nodo (OMS).  Il livello gratuito presenta un limite per la quantità di dati raccolti al giorno, il periodo di memorizzazione e i minuti di esecuzione dei processi dei runbook.  Il livello Per nodo (OMS) non ha limiti per la quantità di dati raccolti al giorno.  
   - Selezionare **Account di Automazione**.  Se si sta creando una nuova area di lavoro OMS, viene richiesto di creare anche un account di Automazione da associare alla nuova area di lavoro OMS specificata in precedenza, nonché l'area, il gruppo di risorse e la sottoscrizione di Azure.  È possibile selezionare **Crea un account di Automazione** e specificare le informazioni seguenti nel pannello **Account di Automazione**: 
  - Nel campo **Nome** immettere il nome dell'account di Automazione.

    Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro OMS selezionata e non è possibile modificarle.  Il metodo di autenticazione predefinito per l'offerta è un account RunAs di Azure.  Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione.  Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

    In alternativa, selezionare un account RunAs di Automazione esistente.  L'account selezionato non può essere già collegato a un'altra area di lavoro OMS. In caso contrario nel pannello viene visualizzato un messaggio di notifica.  Se è già collegato, è necessario selezionare un account RunAs di Automazione diverso o crearne uno.

    Dopo avere immesso le informazioni obbligatorie, fare clic su **Crea**.  Le informazioni vengono verificate e vengono creati l'account di Automazione e gli account RunAs.  Si torna automaticamente al pannello **Area di lavoro OMS**.  

6. Dopo aver specificato le informazioni necessarie nel pannello **Area di lavoro OMS**, fare clic su **Crea**.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu.  Viene visualizzato nuovamente il pannello **Aggiungi soluzione**.  

7. Nel pannello delle impostazioni di **Automation & Control** confermare l'installazione delle soluzioni preselezionate consigliate. Se ne viene deseleziona qualcuna, sarà possibile installarle singolarmente in un secondo momento.  

8. Fare clic su **Crea** per procedere all'onboarding di Automazione e di un'area di lavoro OMS. Tutte le impostazioni vengono convalidate e viene eseguito un tentativo di distribuzione dell'offerta nella sottoscrizione.  Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

Dopo il caricamento dell'offerta, è possibile iniziare a creare i runbook, usare le soluzioni di gestione abilitate e distribuire un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) oppure iniziare a usare [Log Analytics](https://docs.microsoft.com/azure/log-analytics) per raccogliere i dati generati dalle risorse negli ambienti cloud o locali.   

## <a name="next-steps"></a>Passaggi successivi
* Per verificare che il nuovo account di Automazione possa eseguire l'autenticazione con le risorse di Azure, vedere [Test Azure Automation Run As account authentication](automation-verify-runas-authentication.md) (Testare l'autenticazione di un account RunAs di Automazione di Azure).
* Prima di iniziare a creare i runbook, rivedere i [tipi di runbook di Automazione](automation-runbook-types.md) supportati e relative considerazioni.


