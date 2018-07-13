---
title: Contoso - Configurare un'infrastruttura di migrazione | Microsoft Docs
description: Informazioni sul modo in cui Contoso configura un'infrastruttura di Azure per la migrazione ad Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf1406c8e361e0a1433b0e26c477c3c34e987fcf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38562759"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - Distribuire un'infrastruttura di migrazione

Questo articolo spiega in che modo Contoso configura un'infrastruttura locale e di Azure, per preparare la migrazione ad Azure e l'esecuzione delle attività in un ambiente ibrido.

- Si tratta di un'architettura di esempio specifica di Contoso.
- La necessità di disporre di tutti gli elementi descritti nell'articolo varia in base alla strategia di migrazione adottata. Ad esempio, in caso di creazione di sole app native per il cloud in Azure, potrebbe essere necessaria una struttura di rete meno complessa.

Questo documento è il secondo di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e un set di scenari di distribuzione che illustrano la configurazione di un'infrastruttura di migrazione, l'idoneità delle risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. Gli scenari aumentano di complessità e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
Articolo 2: Distribuire un'infrastruttura di Azure (questo articolo) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli scenari di migrazione Contoso. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md) | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel ad Azure. Esegue la migrazione della macchina virtuale front-end dell'app mediante [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e del database dell'app mediante il servizio [Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting in macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel solo con Site Recovery.
[Articolo 6: Eseguire il rehosting nelle macchine virtuali di Azure e nei gruppi di disponibilità di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un gruppo di disponibilità di SQL Server. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso migra l'app osTicket di Linux alle VM di Azure. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux usando Site Recovery, nonché MySQL Workbench per la migrazione (backup e ripristino) a un'istanza del server MySQL di Azure. | Disponibile

In questo articolo Contoso configura tutti gli elementi dell'infrastruttura necessari per completare gli scenari di migrazione. 


## <a name="overview"></a>Panoramica

Prima di eseguire la migrazione ad Azure, Contoso deve predisporre l'infrastruttura.  In generale, deve considerare cinque grandi aree:

**Passaggio 1: Sottoscrizioni di Azure**: come acquisterà Azure e interagirà con la piattaforma e i servizi di Azure?  
**Passaggio 2: Identità ibrida**: come gestirà e controllerà l'accesso alle risorse locali e di Azure dopo la migrazione? Come estenderà o migrerà la gestione delle identità al cloud?  
**Passaggio 3: Ripristino di emergenza e resilienza**: come garantirà la resilienza delle app e dell'infrastruttura in caso di emergenze e interruzioni?  
**Passaggio 4: Rete**: in che modo devono progettare l'infrastruttura di rete e stabilire la connettività tra i data center locale e Azure?  
**Passaggio 5: Sicurezza**: in che modo proteggerà la distribuzione ibrida/Azure?  
**Passaggio 6: Governance**: in che modo verranno mantenuti allineati i requisiti di sicurezza e di governance?

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare a esaminare l'infrastruttura, è consigliabile leggere alcune informazioni generali sulle funzionalità di Azure affrontate in questo articolo:

- Sono disponibili diverse opzioni per l'acquisto dell'accesso ad Azure, tra cui la tariffa con pagamento in base al consumo, i Contratti Enterprise (EA) o le licenze Open di rivenditori di Microsoft o partner Microsoft noti come Cloud Solution Provider (CSP). Informazioni sulle [opzioni per l'acquisto](https://azure.microsoft.com/pricing/purchase-options/) e sull'[organizzazione delle sottoscrizioni di Azure](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- È disponibile una panoramica della [gestione delle identità e degli accessi](https://www.microsoft.com/en-us/trustcenter/security/identity) di Azure, oltre a informazioni su [Azure Active Directory e sull'estensione di account AD locali nel cloud](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). È anche disponibile un utile e-Book scaricabile sulla [gestione delle identità e degli accessi (IAM) in un ambiente ibrido](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure offre un'infrastruttura di rete affidabile con opzioni di connettività ibrida. È disponibile una panoramica delle [reti e del controllo di accesso di rete](https://docs.microsoft.com/azure/security/security-network-overview).
- È possibile consultare un'introduzione alla [sicurezza di Azure](https://docs.microsoft.com/azure/security/azure-security) e ottenere informazioni sulla creazione di un piano per la [governance](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Architettura locale

Ecco un diagramma che illustra l'infrastruttura locale corrente di Contoso.

 ![Architettura di Contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso dispone di un data center principale nella città di New York, negli Stati Uniti orientali.
- Possiede anche tre filiali locali dislocate in tre località negli Stati Uniti.
- Il data center principale è connesso a Internet tramite connessione Metro Ethernet in fibra (500 Mbps).
- Ogni filiale è connessa in locale a Internet tramite connessioni aziendali, con tunnel VPN IPSec in uscita nel data center principale. In questo modo l'intera rete è connessa in modo permanente e la connettività Internet risulta ottimizzata.
- Il data center principale è completamente virtualizzato con VMware. Due host di virtualizzazione ESXi 6.5 sono gestiti dal server vCenter 6.5.
- Contoso usa Active Directory per la gestione delle identità e server DNS nella rete interna.
- I controller di dominio nel data center vengono eseguiti in macchine virtuali VMware. Il controller di dominio nelle filiali locali vengono eseguiti in server fisici.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Passaggio 1: Acquistare ed eseguire la sottoscrizione a Azure

Contoso deve decidere come acquistare Azure, architettare le sottoscrizioni e concedere in licenza servizi e risorse.

### <a name="buy-azure"></a>Acquistare Azure

Contoso scegliere un [Contratto Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), che prevede un impegno monetario anticipato ad Azure per acquisire notevoli vantaggi, tra cui opzioni di fatturazione flessibili e prezzi ottimizzati.

- Contoso ha stimato la spesa annuale per Azure. Quando ha firmato il contratto, ha pagato per intero il primo anno.
- Contoso deve usare tutti gli impegni prima della fine dell'anno per non perderli.
- Se per qualche motivo supera l'impegno e spende un importo superiore, Microsoft le addebiterà la differenza.
- Eventuali costi sostenuti oltre l'impegno verranno addebitati alla stessa tariffa e alle stesse condizioni previste nel contratto. Non sono previste penali per il superamento.

### <a name="manage-subscriptions"></a>Gestisci sottoscrizioni

Dopo il pagamento di Azure, Contoso deve decidere come gestire le sottoscrizioni. Poiché ha stipulato un EA, non sussistono limiti sul numero di sottoscrizioni di Azure configurabili.

- Un'iscrizione Enterprise di Azure definisce la forma e l'uso dei servizi di Azure all'interno di un'azienda e una struttura di governance di base.
- Per iniziare, Contoso ha determinato una struttura (nota come scaffolding aziendale) per l'iscrizione Enterprise. Ha usato [questo articolo](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) per comprendere e progettare uno scaffolding.
- Per il momento, Contoso ha deciso di adottare un approccio funzionale alla gestione delle sottoscrizioni.
    - All'interno dell'azienda ha un unico reparto IT che controlla il budget di Azure. Si tratterà dell'unico gruppo con sottoscrizioni.
    - L'azienda estenderà il modello in futuro, in modo che altri gruppi aziendali possano aderire come reparti all'iscrizione Enterprise.
    - Nel reparto IT Contoso ha strutturato due sottoscrizioni, Produzione e Sviluppo.
    - Se Contoso avrà bisogno di altre sottoscrizioni in futuro, dovrà gestire l'accesso, i criteri e la conformità per queste sottoscrizioni. A tale scopo potrà introdurre [gruppi di gestione di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview) come livello aggiuntivo al di sopra delle sottoscrizioni.

    ![Struttura aziendale](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Esaminare le licenze

Una volta configurate le sottoscrizioni, Contoso potrà esaminare le licenze Microsoft. La strategia di gestione delle licenze dipende dalle risorse che intende migrare ad Azure e dalla modalità di selezione e distribuzione delle VM e dei servizi di Azure. 

#### <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

In caso di distribuzione di VM in Azure, le immagini standard includono una licenza che prevede per Contoso un addebito per minuto di software usato. Tuttavia, Contoso è cliente Microsoft da lungo tempo e ha gestito contratti EA e licenze open con Software Assurance (SA). 

Vantaggio Azure Hybrid offre un metodo conveniente per la migrazione di Contoso, consentendo di risparmiare sulle VM di Azure e sui carichi di lavoro SQL Server convertendo o riusando le licenze Windows Server Datacenter e Standard Edition coperte con Software Assurance. In questo modo Contoso può pagare una tariffa di calcolo di base inferiore per VM e SQL Server. [Altre informazioni](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Mobilità delle licenze

Mobilità delle licenze tramite Software Assurance fornisce ai clienti dei contratti multilicenza Microsoft come Contoso la flessibilità di distribuire le app server idonee con SA attivo in Azure. In questo modo non è necessario acquistare nuove licenze. Senza costi per la mobilità associati, le licenze esistenti possono essere facilmente distribuite in Azure. [Altre informazioni](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Riservare istanze per carichi di lavoro prevedibili

I carichi di lavoro prevedibili devono essere sempre disponibili con VM in esecuzione. Ad esempio, app line-of-business come esempio un sistema ERP SAP.  D'altro canto, i carichi di lavoro imprevedibili sono variabili. Ad esempio, le VM disponibili in periodi di richiesta elevata e non disponibili durante le ore non di punta.

![Istanza riservata](./media/contoso-migration-infrastructure/reserved-instance.png) 

Anziché usare istanze riservate per specifiche istanze di VM che dovrà sottoporre a manutenzione per lunghi periodi, Contoso può ottenere uno sconto e capacità in ordine di priorità. Grazie alle [istanze riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/), unitamente a Vantaggio Azure Hybrid, Contoso può risparmiare fino all'82% sui prezzi normali con pagamento in base al consumo (aprile 2018).


## <a name="step-2-manage-hybrid-identity"></a>Passaggio 2: Gestire l'identità ibrida

Fornire e controllare l'accesso utente alle risorse di Azure con la gestione delle identità e degli accessi (IAM) è essenziale nell'assemblaggio dell'infrastruttura Azure.  

- Contoso ha deciso di estendere gli account Active Directory locali nel cloud, anziché creare un nuovo sistema distinto in Azure.
- A tale scopo, crea un account Active Directory basato su Azure.
- Poiché Contoso non dispone di Office 365, deve eseguire il provisioning di un nuovo account Azure AD.
- Office 365 usa Azure AD per la gestione degli utenti. Se Contoso usasse Office 365, avrebbe già un tenant Azure AD da usare come account AD primario.
- [Altre informazioni](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) su Azure AD per Office 365 e su [come aggiungere una sottoscrizione](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) a un account Azure AD esistente.

### <a name="create-an-azure-ad"></a>Creare Azure AD

Contoso usa l'edizione Azure Active Directory Free inclusa con una sottoscrizione di Azure e aggiunge una nuova directory di Active Directory come segue:

1. Nel [portale di Azure](http://portal.azure.com/) Contoso passa a **Crea una risorsa** > **Identità** > **Azure Active Directory**.
2. In **Crea directory** specifica un nome per la directory, un nome di dominio iniziale e l'area in cui deve essere creata la directory di Azure AD.

    ![Creare Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > La directory creata include un nome di dominio iniziale in formato nomedominio.onmicrosoft.com. Il nome non può essere modificato o eliminato. Contoso deve invece aggiungere il nome di dominio registrato ad Azure AD.

### <a name="add-the-domain-name"></a>Aggiungere il nome di dominio

Per usare il nome di dominio standard, Contoso deve aggiungerlo come nome personalizzato ad Azure AD. Questa opzione consente agli amministratori di assegnare nomi utente familiari. Ad esempio, un utente può accedere con l'indirizzo di posta elettronica billg@contoso.com, anziché con billg@contosomigration.onmicrosoft.com. 

Per impostare il nome personalizzato, lo aggiunge alla directory, aggiunge una voce DNS e verifica il nome in Azure AD.

1. In **Nomi di dominio personalizzati** > **Aggiungi dominio personalizzato** aggiunge il dominio.
2. Per usare una voce DNS, deve registrarla con il registrar di dominio. 

    - Nell'elenco **Nomi di dominio personalizzati** prende nota delle informazioni DNS per il nome. Contoso usa una voce MX.
    - A tale scopo, deve poter accedere al Domain Name Server. Nel caso di Contoso, l'azienda ha eseguito l'accesso al dominio Contoso.com e creato un nuovo record MX per la voce DNS fornita da Azure AD usando i dettagli di cui ha preso nota.  
1. In seguito alla propagazione dei record DNS, nel nome dettagliato del dominio fa clic su **Verifica** per controllare il nome personalizzato.

     ![DNS di Azure AD](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Configurare gruppi e utenti locali e di Azure

Ora che Azure AD è in esecuzione, Contoso deve aggiungere dipendenti ai gruppi AD locali che eseguiranno la sincronizzazione con Azure AD. È consigliabile usare nomi di gruppi locali che corrispondono ai nomi dei gruppi di risorse in Azure. In questo modo è più semplice identificare le corrispondenze ai fini della sincronizzazione.

#### <a name="create-resource-groups-in-azure"></a>Creare gruppi di risorse in Azure

I gruppi di risorse di Azure raccolgono le risorse di Azure. L'uso di un ID gruppo di risorse consente ad Azure di eseguire operazioni sulle risorse all'interno del gruppo.

- Una sottoscrizione di Azure può avere più gruppi di risorse, ma un gruppo di risorse può essere presente in una sola sottoscrizione.
- Inoltre, un singolo gruppo di risorse può avere più risorse, ma una risorsa può appartenere solo a un singolo gruppo.

Contoso configura i gruppi di risorse di Azure, come riepilogato nella tabella seguente.

**Gruppo di risorse** | **Dettagli**
--- | ---
**ContosoCobRG** | Questo gruppo contiene tutte le risorse correlate alla continuità aziendale.  Include gli insiemi di credenziali che verranno creati da Contoso in caso di utilizzo del servizio Site Recovery e del servizio Backup di Azure.<br/><br/> Include anche le risorse usate per la migrazione, compresi Azure Migrate e il Servizio Migrazione del database.
**ContosoDevRG** | Questo gruppo contiene le risorse di sviluppo e test.
**ContosoFailoverRG** | Questo gruppo viene usato come destinazione per le risorse sottoposte a failover.
**ContosoNetworkingRG** | Questo gruppo contiene tutte le risorse di rete.
**ContosoRG** | Questo gruppo contiene le risorse correlate ai database e alle app di produzione.

L'azienda crea gruppi di risorse come indicato di seguito:

1. Nel portale di Azure > **Gruppi di risorse**, aggiunge un gruppo.
2. Per ogni gruppo specifica un nome, la sottoscrizione a cui appartiene e l'area.
3. I gruppi di risorse vengono visualizzati nell'elenco **Gruppi di risorse**.

    ![Gruppi di risorse](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Creare gruppi di sicurezza corrispondenti in locale

1. Nell'account Active Directory locale, Contoso imposta i gruppi di sicurezza con nomi che corrispondono a quelli dei gruppi di risorse di Azure.
 
    ![Gruppi di sicurezza di AD locali](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Per motivi di gestione, crea un gruppo aggiuntivo che verrà aggiunto a tutti gli altri gruppi. Questo gruppo disporrà di diritti su tutti i gruppi di risorse in Azure. A questo gruppo verrà aggiunto un numero limitato di amministratori globali.

### <a name="synchronize-ad"></a>Sincronizzare AD

Contoso intende fornire un'identità comune per l'accesso alle risorse locali e nel cloud. A tale scopo, integra gli account Active Directory locali in Azure AD. Con questo modello:

- Utenti e organizzazioni possono usufruire di una singola identità per l'accesso alle applicazioni locali e ai servizi cloud, come Office 365, o a migliaia di altri siti su Internet.
- Gli amministratori possono usare i gruppi in AD per implementare il [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) in Azure.

Per agevolare l'integrazione, Contoso usa lo [strumento Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Con l'installazione e la configurazione in un controller di dominio, lo strumento sincronizza le identità AD locali con Azure AD. 

### <a name="download-the-tool"></a>Scaricare lo strumento

1. Nel portale di Azure, Contoso passa ad **Azure Active Directory** > **Azure AD Connect** e scarica la versione più recente dello strumento nel server usato per la sincronizzazione.

    ![Scaricare Azure AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Avvia il file di installazione **AzureADConnect.msi** usando **Usa impostazioni rapide**. Si tratta dell'installazione più comune, che può essere usata per una topologia a foresta singola con sincronizzazione dell'hash delle password per l'autenticazione.

    ![Procedura guidata di AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. In **Connessione ad Azure AD** l'azienda specifica le credenziali per la connessione ad Azure AD (nel formato CONTOSO\admin o contoso.com\admin).

    ![Procedura guidata di AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. In **Connessione a Servizi di dominio Active Directory** specifica le credenziali per Active Directory in locale.

     ![Procedura guidata di AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. In **Pronto per la configurazione** fa clic su **Avvia il processo di sincronizzazione al termine della configurazione** per avviare immediatamente la sincronizzazione. Quindi Contoso esegue l'installazione.


- Contoso dispone di una connessione diretta ad Azure. Se l'account Active Directory locale si trova dietro un proxy, leggere questo [articolo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Dopo la prima sincronizzazione, in gli oggetti AD locali possono essere visualizzati in Azure AD.

    ![AD locale in Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Il team IT di Contoso è rappresentato in ogni gruppo, in base al ruolo.

    ![Membri AD locali in Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Impostare il Controllo degli accessi in base al ruolo

Il [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di Azure consente la gestione specifica degli accessi per Azure. L'uso del Controllo degli accessi in base al ruolo permette di concedere agli utenti solo il livello di accesso necessario per lavorare. L'utente assegna il ruolo Controllo degli accessi appropriato a utenti, gruppi e applicazioni a livello di ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. 

Contoso ora assegna ruoli ai gruppi di AD sincronizzati in locale.

1. Nel gruppo di risorse **ControlCobRG** fa clic su **Controllo di accesso (IAM)** > **Aggiungi**.
2. In **Aggiungi autorizzazioni** > **Ruolo** seleziona **Collaboratore** e l gruppo AD **ContosoCobRG** dall'elenco. Il gruppo viene visualizzato nell'elenco **Membri selezionati**. 
3. L'azienda ripete questa operazione con le stesse autorizzazioni per gli altri gruppi di risorse (ad eccezione di **ContosoAzureAdmins**), aggiungendo le autorizzazioni Collaboratore all'account di Active Directory che corrisponde al gruppo di risorse.
4. Per il gruppo AD **ContosoAzureAdmins** assegna il ruolo **Proprietario**.

    ![Membri AD locali in Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Passaggio 3: Pianificare resilienza e situazioni di emergenza

Le risorse di Azure vengono distribuite all'interno di aree.
- Le aree sono organizzate in sottoaree geografiche e il rispetto dei requisiti di residenza, sovranità, conformità e resilienza viene garantito entro limiti geografici.
- Un'area è composta da un set dei centri dati. Questi data center vengono distribuiti entro un perimetro definito dalla latenza e connessi tramite una rete regionale dedicata a bassa latenza.
- Ogni area di Azure è associata a un'altra area per la resilienza.
- Informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/)e sul [modo in cui vengono abbinate le aree](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso ha scelto gli Stati Uniti orientali 2 (in Virginia) come area primaria e gli Stati Uniti centrali come area secondaria. Questa scelta è dettata da un paio di motivi:

- Il data center Contoso si trova a New York e l'azienda ha tenuto conto della latenza al più vicino data center.
- L'area degli Stati Uniti orientali 2 include tutti i servizi e i prodotti che l'azienda deve usare. Non tutte le aree di Azure sono uguali in termini di prodotti e servizi disponibili. È possibile consultare [Prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/).
- Quella degli Stati Uniti centrali è l'area abbinata di Azure per gli Stati Uniti orientali 2.

Tenendo conto dell'ambiente ibrido, Contoso deve ora considerare come creare una strategia di resilienza e ripristino di emergenza nella progettazione dell'area. Su vasta scala, le strategie spaziano da una distribuzione in una singola area, che si basa sulle funzionalità della piattaforma di Azure come i domini di errore e le associazioni regionali per la resilienza, fino a un modello attivo/attivo completo in cui i servizi cloud e il database vengono distribuiti e usati dagli utenti di due aree.

Contoso ha optato per una via intermedia. Distribuirà le app e le risorse in un'area primaria e manterrà un'infrastruttura completa nell'area secondaria, in modo che possa agire da backup completo in caso di emergenza completa per l'app o di un errore nell'area.


## <a name="step-4-design-a-network-infrastructure"></a>Passaggio 4: Progettare un'infrastruttura di rete

Una volta progettata l'area, Contoso è pronta considerare una strategia di rete. Deve capire in che modo il data center locale e Azure si connetteranno e comunicheranno tra loro e come progettare l'infrastruttura di rete in Azure. In particolare, dovranno:

**Pianificare la connettività di rete ibrida**: decidere come connettere le reti in locale e in Azure.
**Progettare un'infrastruttura di rete di Azure**: decidere come distribuire le reti tra le aree e in che modo le reti comunicheranno nella stessa area e tra aree diverse.
**Progettare e configurare le reti di Azure**: configurare le reti e le subnet di Azure e decidere cosa si troverà al loro interno.

### <a name="plan-hybrid-network-connectivity"></a>Pianificare una connettività di rete ibrida

Contoso ha considerato una [serie di architetture](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) per la rete ibrida tra Azure e il data center locale. [Altre informazioni](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) sul confronto tra le opzioni.

Come promemoria, l'infrastruttura di rete locale di Contoso è attualmente costituita dal data center di New York e da filiali locali nell'area orientale degli Stati Uniti.  Tutte le sedi dispongono di connessione aziendale a Internet.  Ogni filiale è connessa al data center con un tunnel VPN IPSec tramite Internet.

![Rete di Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Ecco in che modo Contoso ha deciso di implementare la connettività ibrida:

1. Ha configurato una nuova connessione VPN da sito a sito tra il data center di Contoso a New York e le due aree di Azure negli Stati Uniti orientali 2 e negli Stati Uniti centrali.
2. Il traffico delle filiali per le reti virtuali di Azure verrà instradato attraverso il data center principale di Contoso. 
3. Nel momento in cui aumenterà la distribuzione di Azure, stabilirà una connessione ExpressRoute tra i data center e le aree di Azure. In questo caso manterrà la connessione VPN da sito a sito solo a fini del failover.
    - [Altre informazioni](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) sulla scelta tra una soluzione ibrida VPN ed ExpressRoute.
    - Verificare le [sedi e il supporto ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Solo VPN**

![VPN di Contoso](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN ed ExpressRoute**

![VPN Contoso/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Progettare l'infrastruttura di rete di Azure

È essenziale che Contoso posizioni le reti in modo da rendere la distribuzione ibrida sicura e scalabile. A tale scopo, Contoso adotta un approccio a lungo termine e progetta reti virtuali resilienti e pronte per l'azienda. [Altre informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) sulla pianificazione di reti virtuali.

Per connettere le due aree, Contoso ha deciso di implementare un modello di rete da hub a hub:

- In ogni area, Contoso userà un modello hub e spoke.
- Per connettere reti e hub, Contoso userà il peering di rete di Azure.

#### <a name="network-peering"></a>Peering di rete

Azure offre il peering di rete per connettere reti virtuali e hub. Il peering globale consente connessioni tra reti virtuali/hub in aree diverse. Il peering locale connette le reti virtuali nella stessa area. Il peering di reti virtuali offre una serie di vantaggi:

- Il traffico di rete tra reti virtuali di cui è stato eseguito il peering è privato.
- Il traffico tra le reti virtuali viene mantenuto nella rete backbone Microsoft. Nella comunicazione tra le reti virtuali non sono necessari gateway, una rete Internet pubblica o la crittografia.
- Il peering offre una connessione predefinita a larghezza di banda elevata e bassa latenza tra le risorse in reti virtuali diverse.

[Altre informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sul peering di rete.

#### <a name="hub-to-hub-across-regions"></a>Da hub a hub tra regioni

Contoso distribuirà un hub in ogni area. Un hub è una rete virtuale in Azure che funge da punto di connettività centrale alla rete locale. Le reti virtuali dell'hub si connetteranno tra loro mediante peering globale. Il peering globale di reti virtuali connette le reti virtuali in diverse aree di Azure.

- L'hub in ogni regione è associato all'hub partner nell'altra regione.
- L'hub è associato a ogni rete nell'area e può connettersi a tutte le risorse di rete.

    ![Peering globale](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Hub e spoke all'interno di un'area

All'interno di ogni area, Contoso distribuirà le reti virtuali per scopi diversi, come le reti spoke dall'hub dell'area. Le reti virtuali all'interno di un'area usano il peering connettersi all'hub e tra loro.

#### <a name="design-the-hub-network"></a>Progettare la rete dell'hub

All'interno del modello hub e spoke scelto da Contoso, l'azienda deve decidere come verrà instradato il traffico dal data center locale e da Internet. Ecco come Contoso ha deciso di gestire il routing per gli hub degli Stati Uniti orientali 2 e degli Stati Uniti centrali:

- Progetta una rete nota come "reverse c", poiché si tratta del percorso che seguono i pacchetti dalla rete in ingresso a quella in uscita.
- L'architettura di rete prevede due limiti, una zona perimetrale front-end non attendibile e una zona back-end attendibile.
- Un firewall avrà una scheda di rete in ogni area per il controllo dell'accesso alle zone attendibili.
- Da Internet:
    - Il traffico Internet accede a un indirizzo IP pubblico con bilanciamento del carico nella rete perimetrale.
    - Il traffico viene instradato attraverso il firewall ed è soggetto alle regole del firewall.
    - Una volta implementati i controlli di accesso alla rete, il traffico verrà inoltrato alla posizione appropriata nella zona attendibile.
    - Il traffico in uscita dalla rete virtuale verrà instradato in Internet mediante route definite dall'utente. Il traffico viene forzato attraverso il firewall e controllato in linea con i criteri di Contoso.
- Dal data center di Contoso:
    - Il traffico in ingresso tramite VPN da sito a sito (ExpressRoute) accede all'indirizzo IP pubblico del gateway VPN di Azure.
    - Il traffico viene instradato attraverso il firewall ed è soggetto alle regole del firewall.
    - Dopo aver applicato le regole, il traffico verrà inoltrato a un bilanciamento del carico interno (SKU Standard) nella subnet della zona interna attendibile.
    - Il traffico in uscita dalla subnet attendibile verso il data center locale tramite VPN viene instradato attraverso il firewall e vengono applicate regole prima di attraversare la connessione VPN da sito a sito.



### <a name="design-and-set-up-azure-networks"></a>Progettare e configurare reti di Azure

Con una topologia di rete e routing, Contoso è pronta a configurare le reti e le subnet di Azure.

- Contoso implementerà una rete privata di classe A in Azure (da 0.0.0.0 a 127.255.255.255). La rete funzione, dal momento che l'azienda dispone in locale di uno spazio di indirizzi privato di classe B 172.160.0/16, quindi può essere certa che non si verificheranno sovrapposizioni tra gli intervalli di indirizzi.
- Intende distribuire le reti virtuali nelle aree primarie e secondarie.
- Userà una convenzione di denominazione che include il prefisso **VNET** e l'abbreviazione **EUS2** o **CUS** per l'area. Con questo standard, le reti dell'hub verranno denominate **VNET-HUB-EUS2** (Stati Uniti orientali 2) e **VNET-HUB-CUS** (Stati Uniti centrali).
- Contoso non ha una [soluzione IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), quindi deve predisporre il routing di rete senza NAT.


#### <a name="virtual-networks-in-east-us-2"></a>Reti virtuali negli Stati Uniti orientali 2

Quella degli Stati Uniti orientali 2 è l'area primaria che Contoso userà per distribuire risorse e servizi. Ecco in che modo l'azienda intende architettare le reti al suo interno:

- **Hub**: la rete virtuale dell'hub negli Stati Uniti orientali 2 è il punto di connettività primaria centrale al data center locale.
- **Reti virtuali**: le reti virtuali spoke negli Stati Uniti orientali 2 possono essere usate per isolare i carichi di lavoro, se necessario. Oltre alla rete virtuale dell'hub, Contoso avrà due reti virtuali spoke negli Stati Uniti orientali 2:
    - **VNET-DEV-EUS2**. Questa rete virtuale offre al team di sviluppo e test una rete completamente funzionale per i progetti di sviluppo. Verrà usata come area pilota di produzione e si baserà sull'infrastruttura di produzione.
    - **VNET-PROD-EUS2**: i componenti di produzione Azure IaaS si troveranno in questa rete. 
    -  Ogni rete virtuale avrà un proprio spazio di indirizzi univoco, senza sovrapposizioni. L'azienda intende configurare il routing senza NAT.
- **Subnet**:
    - Sarà presente una subnet in ogni rete per ogni livello di app
    - Ogni subnet nella rete di produzione avrà una subnet corrispondente nella rete virtuale di sviluppo.
    - La rete di produzione ha anche una subnet per i controller di dominio.

Le reti virtuali negli Stati Uniti orientali 2 sono riepilogate nella tabella seguente.

**Rete virtuale** | **Range** | **Peer**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CUS

![Hub/spoke nell'area primaria](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Subnet nella rete dell'hub degli Stati Uniti orientali 2 (VNET-HUB-EUS2)

**Subnet/zona** | **CIDR** | **Indirizzi IP utilizzabili
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Subnet nella rete di sviluppo degli Stati Uniti orientali 2 (VNET-DEV-EUS2)

La rete virtuale di sviluppo viene usata dal team di sviluppo come area pilota di produzione. Include tre subnet.

**Subnet** | **CIDR** | **Indirizzi** | **Nella subnet**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Front-end/VM livello Web
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | VM livello app
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | VM database


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Subnet nella rete di produzione degli Stati Uniti orientali 2 (VNET-PROD-EUS2)

I componenti Azure IaaS si trovano nella rete di produzione. Ogni livello dell'app presenta una subnet specifica. Le subnet corrispondono a quelle nella rete di sviluppo, con l'aggiunta di una subnet per i controller di dominio.

**Subnet** | **CIDR** | **Indirizzi** | **Nella subnet**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | Front-end/VM livello Web
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | VM livello app
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | VM database
**PROD-DC-EUS2** | 10.245.42.0/23 | 251 | VM controller di dominio


![Architettura di rete dell'hub](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Reti virtuali negli Stati Uniti centrali (area secondaria)

Quella degli Stati Uniti centrali è l'area secondaria di Contoso. Ecco in che modo l'azienda intende architettare le reti al suo interno:

- **Hub**: la rete virtuale dell'hub negli Stati Uniti orientali 2 è il punto di connettività centrale al data center locale e le reti virtuali spoke negli Stati Uniti orientali 2 possono essere usate per isolare i carichi di lavoro se necessario, con gestione separata rispetto alle altre reti spoke.
- **Reti virtuali**: l'azienda avrà due reti virtuali negli Stati Uniti centrali:
    - VNET-PROD-CUS. Questa rete virtuale è una rete di produzione, simile a VNET-PROD_EUS2. 
    - VNET-ASR-CUS. Questa rete virtuale verrà usata come posizione in cui creare le VM dopo il failover da locale o come posizione per le VM di Azure di cui è stato effettuato il failover dall'area primaria all'area secondaria. Questa rete è simile alle reti di produzione, ma è sprovvista di controller di dominio.
    -  Ogni rete virtuale nell'area avrà un proprio spazio di indirizzi, senza sovrapposizioni. L'azienda intende configurare il routing senza NAT.
- **Subnet**: le subnet verranno architettate in modo analogo a quelle negli Stati Uniti orientali 2, ad eccezione del fatto che non sarà necessaria una subnet per i controller di dominio.

Le reti virtuali negli Stati Uniti centrali sono riepilogate nella tabella seguente.

**Rete virtuale** | **Range** | **Peer**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-PROD-CUS
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![Hub/spoke nell'area abbinata](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Subnet nella rete dell'hub degli Stati Uniti centrali (VNET-HUB-CUS)

**Subnet** | **CIDR** | **Indirizzi IP utilizzabili**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Subnet nella rete di produzione degli Stati Uniti centrali (VNET-PROD-CUS)

Parallelamente alla rete di produzione nell'area primaria degli Stati Uniti orientali 2, è presente una rete di produzione nell'area secondaria degli Stati Uniti centrali. 

**Subnet** | **CIDR** | **Indirizzi** | **Nella subnet**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | Front-end/VM livello Web
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | VM livello app
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | VM database
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | VM controller di dominio

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Subnet nella rete di failover/recupero negli Stati Uniti centrali (VNET-ASR-CUS)

La rete VNET-ASR-CUS viene usata a scopo di failover tra le aree. Verrà usato Site Recovery per la replica e il failover delle VM di Azure tra le aree. Funge anche da data center di Contoso per la rete di Azure per i carichi di lavoro protetti che rimangono in locale, ma vengono sottoposti a failover in Azure per il ripristino di emergenza.

VNET-ASR-CUS è la stessa subnet di base della rete virtuale di produzione negli Stati Uniti orientali 2, ma senza richiedere una subnet per i controller di produzione.

**Subnet** | **CIDR** | **Indirizzi** | **Nella subnet**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | Front-end/VM livello Web
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | VM livello app
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | VM database

![Architettura di rete dell'hub](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Configurare connessioni con peering

L'hub in ogni area verrà associato all'hub nell'altra area e a tutte le reti virtuali all'interno dell'area dell'hub. In questo modo gli hub potranno comunicare e visualizzare tutte le reti virtuali all'interno di un'area. Si noti che:

- Il peering crea una connessione doppia, una dal peer di avvio sulla prima rete virtuale e un'altra sulla seconda rete virtuale.
- In una distribuzione ibrida, il traffico tra i peer deve essere visibile dalla connessione VPN tra il data center locale e Azure. A tale scopo, è necessario configurare alcune impostazioni specifiche sulle connessioni con peering.

Per qualsiasi connessione dalle reti virtuali spoke attraverso l'hub al data center locale, Contoso deve consentire l'inoltro del traffico e attraversare i gateway VPN.

##### <a name="domain-controller"></a>Controller di dominio

Per i controller di dominio nella rete VNET-PROD-EUS2, Contoso vuole indirizzare sia il traffico tra la rete di produzione e l'hub EUS2 sia la connessione VPN in locale. A tale scopo deve consentire quanto segue:

1. **Consenti traffico inoltrato** e **Consenti transito gateway** sulla connessione con peering. In questo esempio si tratta della connessione da VNET-HUB-EUS2 a VNET-PROD-EUS2.

    ![Peering](./media/contoso-migration-infrastructure/peering1.png)

2. **Consenti traffico inoltrato** e **Usa gateway remoti** sull'altro lato del peering, nella connessione da VNET-PROD-EUS2 a VNET-HUB-EUS2.

    ![Peering](./media/contoso-migration-infrastructure/peering2.png)

3. In locale l'azienda configura una route statica che indirizza il traffico attraverso il tunnel VPN alla rete virtuale. La configurazione verrà completata nel gateway che fornisce il tunnel VPN da Contoso ad Azure. A tale scopo, Contoso usa Routing e accesso remoto di Windows.

    ![Peering](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Reti di produzione 

Una rete di peer con spoke non può visualizzarne un'altra in un'altra regione tramite un hub.

Affinché le reti di produzione di Contoso possano visualizzarsi a vicenda in entrambe le aree, l'azienda deve creare una connessione con peering diretta per VNET-PROD-EUS2 e VENT-PROD-CUS. 

![Peering](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Configurare DNS

In caso di distribuzione di risorse in reti virtuali, per la risoluzione dei nomi di dominio sono disponibili due opzioni. È possibile usare la risoluzione dei nomi fornita da Azure oppure fornire server DNS per la risoluzione. Il tipo di risoluzione dei nomi usato dipende dal modo in cui le risorse devono comunicare tra loro. [Altre informazioni](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) sul servizio DNS di Azure.

Contoso ha deciso che il servizio DNS di Azure non è una scelta ottimale per il suo ambiente ibrido. Sceglie quindi di usare i server DNS locali.

- Poiché si tratta di una rete ibrida, tutte le VM in locale e in Azure devono essere in grado di risolvere i nomi. È quindi necessario applicare impostazioni DNS personalizzate a tutte le reti virtuali.
- Contoso ha attualmente distribuito controller di dominio nel suo data center e nelle filiali. I server DNS primari sono CONTOSODC1(172.16.0.10) e CONTOSODC2(172.16.0.1)
- Quando vengono distribuite le reti virtuali, i controller di dominio locali verranno configurati per essere usati come server DNS nelle reti. 
- A tale scopo, se per la rete virtuale sono specificate impostazioni DNS personalizzate, è necessario aggiungere all'elenco DNS l'indirizzo IP dei resolver ricorsivi di Azure (ad esempio 168.63.129.16).  Contoso configura le impostazioni del server DNS in ogni rete virtuale. Ad esempio, le impostazioni DNS personalizzate per la rete VNET-HUB-EUS2 saranno:
    
    ![DNS personalizzato](./media/contoso-migration-infrastructure/custom-dns.png)

Oltre ai controller di dominio locali, Contoso intende implementarne altri quattro per supportare le reti di Azure, due per ogni area. Ecco cosa distribuirà in Azure.

**Area** | **Controller di dominio** | **Rete virtuale** | **Subnet** | **Indirizzo IP**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
CUS | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

Dopo aver distribuito i controller di dominio locali, Contoso dovrà aggiornare le impostazioni di DNS nelle reti in una delle aree per includere i nuovi controller di dominio nell'elenco di server DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Configurare i controller di dominio in Azure

Dopo aver aggiornato le impostazioni di rete, Contoso è pronta a creare i controller di dominio in Azure.

1. Nel portale di Azure distribuisce una nuova VM Windows Server nella rete virtuale appropriata.
2. Crea set di disponibilità in ogni posizione per la VM. I set di disponibilità:
    - Fanno in modo che l'infrastruttura di Azure separi le VM in infrastrutture distinte nell'area di Azure. 
    -  Garantiscono a Contoso l'idoneità del contratto di servizio al 99,95% per le VM in Azure.  [Altre informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

    ![Gruppo di disponibilità](./media/contoso-migration-infrastructure/availability-group.png) 
3. Dopo aver distribuito la VM, apre l'interfaccia di rete per la VM. Qui imposta come statico l'indirizzo IP privato e specifica un indirizzo valido.

    ![Scheda NIC della VM](./media/contoso-migration-infrastructure/vm-nic.png)

4. A questo punto l'azienda collega un nuovo disco dati alla VM. Il disco contiene il database Active Directory e la condivisione sysvol. 
    - Le dimensioni del disco determinano il numero di IOPS supportate.
    - Nel tempo le dimensioni del disco potrebbero dover aumentare contestualmente all'ambiente.
    - L'unità non deve essere impostata su Lettura/Scrittura per la memorizzazione nella cache dell'host. I database Active Directory non supportano questa condizione.

     ![Disco Active Directory](./media/contoso-migration-infrastructure/ad-disk.png)

5. Dopo aver aggiunto il disco, l'azienda si connette alla VM tramite Desktop remoto e apre Server Manager.
6. In **Servizi file e archiviazione** esegue la Procedura guidata Nuovo volume, verificando che all'unità sia assegnata la lettera F: o una lettera superiore nella VM locale.

     ![Procedura guidata Nuovo volume](./media/contoso-migration-infrastructure/volume-wizard.png)

7. In Server Manager aggiunge il ruolo **Active Directory Domain Services**. Configura la VM come controller di dominio.

      ![Ruolo server](./media/contoso-migration-infrastructure/server-role.png)  

9. Dopo aver configurato la VM come controller di dominio e riavviato, apre Gestore DNS e configura il resolver DNS di Azure come server d'inoltro.  In questo il controller di dominio può inoltrare le query DNS che non riesce a risolvere a DNS di Azure.

    ![Server d'inoltro DNS](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. A questo punto, l'azienda aggiorna le impostazioni DNS personalizzate per ogni rete virtuale con il controller di dominio appropriato per l'area della rete virtuale. Nell'elenco include i controller di dominio locali.

### <a name="set-up-active-directory"></a>Configurare Active Directory

AD è un servizio fondamentale nella rete e deve essere configurato correttamente. Contoso creerà siti Active Directory per il data center di Contoso e per le aree EUS2 e CUS.  

1. Crea due nuovi siti (AZURE-EUS2 e AZURE-CUS) insieme al sito del data center (ContosoDatacenter).
2. Dopo aver creato i siti, creerà subnet al loro interno per la corrispondenza con le reti virtuali e il data center.

    ![Subnet di controller di dominio](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Ora crea due collegamenti ai siti per connettere tutti gli elementi. I controller di dominio devono essere spostati nelle relative posizioni.

    ![Collegamenti ai controller di dominio](./media/contoso-migration-infrastructure/dc-links.png)

4. In seguito alla configurazione, la topologia di replica di Active Directory è pronta.
    
    ![Replica di controller di dominio](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Al termine verrà visualizzato un elenco dei controller di dominio e dei siti nel Centro di amministrazione di Active Directory locale.

    ![Centro di amministrazione di Active Directory](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Passaggio 5: Pianificare la governance

Azure offre una gamma di controlli di governance nei servizi e nella piattaforma di Azure. [Altre informazioni](https://docs.microsoft.com/azure/security/governance-in-azure) per una conoscenza di base delle opzioni.

Nel momento in cui configura il controllo delle identità e degli accessi, Contoso ha già iniziato a implementare alcuni aspetti di governance e sicurezza. Su vasta scala, sono tre le aree che deve prendere in considerazione:

- **Criteri**: i criteri in Azure applicano regole ed effetti alle risorse, in modo che queste siano sempre conformi ai requisiti e ai contratti di servizio aziendali.
- **Blocchi**: Azure consente di bloccare sottoscrizioni, gruppi di risorse e risorse di altro tipo, in modo che possano essere modificati solo dagli utenti autorizzati.
- **Tag**: le risorse possono essere controllate e gestite tramite tag. I tag collegano i metadati alle risorse, fornendo informazioni sulle risorse o sui proprietari.

### <a name="set-up-policies"></a>Configurare i criteri

Il servizio Criteri di Azure esegue una valutazione delle risorse, alla ricerca di quelle che non sono conformi alle definizioni di criteri specificate. Ad esempio, potrebbero essere presenti criteri che consentono solo un determinato tipo di VM o che richiedono un tag specifico nelle risorse. 

I criteri di Azure specificano la definizione dei criteri e l'assegnazione specifica l'ambito in cui devono essere applicati. L'ambito può spaziare da un gruppo di gestione a un gruppo di risorse. [Informazioni](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) sulla creazione e sulla gestione dei criteri.

Contoso intende iniziare con un paio di criteri:

- Vuole che un criterio garantisca la distribuzione delle risorse esclusivamente nelle aree EUS2 e CUS.
- Intende limitare gli SKU di VM ai soli SKU approvati. La finalità è quella di garantire che non vengano usati SKU di VM dispendiosi.

#### <a name="limit-resources-to-regions"></a>Limitare le risorse alle aree

Contoso usa la definizione dei criteri predefinita **Località consentite** per limitare le aree delle risorse.

1. Nel portale di Azure l'azienda fa clic su **Tutti i servizi** e cerca **Criteri**.
2. Seleziona **Assegnazioni** > **Assegna criteri**.
3. Nell'elenco dei criteri seleziona **Località consentite**.
4. Imposta **Ambito** sul nome della sottoscrizione di Azure e seleziona le due aree nell'elenco di elementi consentiti.

    ![Regioni consentite dai criteri](./media/contoso-migration-infrastructure/policy-region.png)

5. Per impostazione predefinita i criteri vengono impostati con **Nega**, vale a dire che se un utente avvia una distribuzione nella sottoscrizione che non si trova nell'area EUS2 o CUS, la distribuzione avrà esito negativo. Ecco cosa accade se un utente nella sottoscrizione Contoso tenta di configurare una distribuzione negli Stati Uniti occidentali.

    ![Criterio non riuscito](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Consentire specifici SKU di VM

Contoso userà la definizione dei criteri predefinita **Allow virtual machines SKUs** (Consenti SKU macchine virtuali) per limitare il tipo di VM che possono essere create nella sottoscrizione. 

![SKU criteri](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Controllare la conformità dei criteri

I criteri hanno effetto immediato e Contoso può verificare la conformità delle risorse. 

1. Nel portale di Azure l'azienda fa clic sul collegamento **Conformità**.
2. Viene visualizzato il dashboard di conformità. È possibile eseguire il drill-down per maggiori dettagli.

    ![Conformità ai criteri](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Configurare i blocchi

Contoso usa da tempo il framework ITIL per la gestione dei sistemi. Uno degli aspetti più importanti del framework è il controllo modifiche. Contoso intende assicurarsi che venga implementato nella distribuzione di Azure.

Contoso implementerà i blocchi nel modo seguente:

- Qualsiasi componente di produzione o failover deve essere in un gruppo di risorse con un blocco ReadOnly.  Ciò significa che, per modificare o eliminare gli elementi di produzione, è necessario rimuovere il blocco. 
- I gruppi di risorse non di produzione non avranno blocchi CanNotDelete. Gli utenti autorizzati possono quindi leggere o modificare una risorsa, ma non eliminarla.

[Altre informazioni](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) sui blocchi.

### <a name="set-up-tagging"></a>Impostare i tag

Per tenere traccia delle risorse man mano che vengono aggiunte, sarà sempre più importante per Contoso associarle a un reparto, un cliente e un ambiente appropriati. 

Oltre a fornire informazioni sulle risorse e sui proprietari, i tag consentiranno a Contoso di aggregare e raggruppare le risorse e usare tali dati ai fini di chargeback.

Contoso deve visualizzare le risorse di Azure in modo significativo per l'azienda. Ad esempio, come ruolo o reparto. Non è necessario che le risorse si trovino nello stesso gruppo di risorse per condividere un tag. A tale scopo, Contoso creerà una tassonomia di tag semplice in modo che tutti usino gli stessi tag.

**Nome del tag** | **Valore**
--- | ---
CostCenter | 12345: deve essere un centro di costo valido di SAP.
BusinessUnit | Nome della business unit (di SAP). Corrisponde a CostCenter.
ApplicationTeam | Alias di posta elettronica del team responsabile del supporto dell'app.
CatalogName | Nome dell'app o ShareServices, in base al catalogo di servizi supportato dalla risorsa.
ServiceManager | Alias di posta elettronica dell'ITIL Service Manager per la risorsa.
COBPriority | Priorità impostata dall'azienda per BCDR. Valori da 1 a 5.
ENV | I valori possibili sono DEV, STG, PROD, che rappresentano sviluppo, gestione temporanea e produzione.

Ad esempio:  

 ![Tag di Azure](./media/contoso-migration-infrastructure/azure-tag.png)

Dopo aver creato il tag, Contoso creerà nuove definizioni e assegnazioni dei criteri di Azure per forzare l'uso dei tag richiesti nell'intera organizzazione.


## <a name="step-6-consider-security"></a>Passaggio 6: Considerare la sicurezza

La sicurezza è fondamentale nel cloud e Azure offre un'ampia gamma di funzionalità e strumenti di sicurezza, che consentono di creare soluzioni sicure nella piattaforma di Azure. In [Fiducia nel cloud attendibile](https://azure.microsoft.com/overview/trusted-cloud/) sono disponibili altre informazioni sulla sicurezza di Azure.

Contoso deve prendere in considerazione alcuni aspetti principali

- **Centro sicurezza di Azure**: il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. Con il Centro sicurezza, è possibile applicare i criteri di sicurezza sui carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi.  [Altre informazioni](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Gruppi di sicurezza di rete (NSG)**: un gruppo di sicurezza di rete è un filtro (firewall) contenente un elenco di regole di sicurezza che, se applicate, consentono o rifiutano il traffico di rete verso le risorse connesse alle reti virtuali di Azure. [Altre informazioni](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Crittografia dischi**: Crittografia dischi di Azure è una funzionalità che consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. [Altre informazioni](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Uso del Centro sicurezza di Azure

Contoso ha bisogno di una rapida panoramica dell'approccio alla sicurezza del nuovo cloud ibrido e, in particolare, dei carichi di lavoro di Azure.  Di conseguenza, ha deciso di implementare il Centro sicurezza di Azure iniziando con le funzionalità seguenti: 

- Gestione centralizzata dei criteri
- Valutazione continua
- Consigli operativi 

#### <a name="centralize-policy-management"></a>Gestione centralizzata dei criteri

Con la gestione centralizzata dei criteri Contoso garantisce la conformità ai requisiti di sicurezza nell'intero ambiente. Può implementare in modo rapido e semplice un criterio applicabile a tutte le risorse di Azure.

![Criteri di sicurezza](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Valutazione e azione

Contoso usa la valutazione continua della sicurezza, che monitora la sicurezza delle macchine, delle reti, delle risorse di archiviazione, dei dati e delle applicazioni per individuare potenziali problemi di sicurezza. 

- Il Centro sicurezza analizzerà lo stato di sicurezza delle risorse di calcolo, dati e infrastruttura di Contoso e delle app e dei servizi di Azure.
- La valutazione continua consente al team delle operazioni di Contoso di individuare potenziali problemi di sicurezza, ad esempio i sistemi con aggiornamenti della sicurezza mancanti o con porte di rete esposte. 
- In particolare Contoso intende assicurarsi che tutte le VM siano protette. Il Centro sicurezza è utile in tal senso, in quanto verifica l'integrità delle VM e offre consigli operativi e in ordine di priorità per risolvere le vulnerabilità della sicurezza prima che vengano sfruttate.

![Monitoraggio](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Uso dei gruppi di sicurezza di rete

Contoso può limitare il traffico di rete verso le risorse in una rete virtuale usando gruppi di sicurezza di rete.

- Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo.
- Se applicate a una subnet, le regole si applicano a tutte le risorse al suo interno. Oltre alle interfacce di rete, sono incluse le istanze dei servizi di Azure distribuiti nella subnet.
- I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'app, raggruppando le VM e definendo i criteri di sicurezza di rete in base a tali gruppi.
    - Con i gruppi di sicurezza delle app è possibile riusare i criteri di sicurezza su larga scala senza gestire manualmente indirizzi IP espliciti. La piattaforma gestisce la complessità degli indirizzi IP espliciti e di più set di regole, consentendo all'utente di concentrarsi sulla logica di business.
    - Si può specificare un gruppo di sicurezza delle applicazioni come origine e destinazione in una regola di sicurezza. Dopo aver definito i criteri di sicurezza, sarà possibile creare VM e assegnare le relative schede di interfaccia a un gruppo. 


Contoso implementerà una combinazione di gruppi di sicurezza di rete e delle applicazioni. È interessata alla gestione dei gruppi di sicurezza di rete. Ne teme un uso eccessivo e le conseguenti complessità per il personale addetto alle operazioni.  Alla luce di ciò ha adottato due principi chiave che applica come regola generale:

- Tutto il traffico in ingresso e in uscita da tutte le subnet (nord-sud) sarà soggetto a una regola NSG, ad eccezione delle GatewaySubnet nelle reti dell'hub.
- Eventuali firewall o controller di dominio saranno protetti dai gruppi di sicurezza di rete delle subnet e delle schede di interfaccia rete.
- A tutte le applicazioni di produzione verranno applicati gruppi di sicurezza delle applicazioni.


Contoso ha creato un modello che riflette l'aspetto di questa condizione per le sue applicazioni.

![Sicurezza](./media/contoso-migration-infrastructure/asg.png)


I gruppi di sicurezza di rete associati ai gruppi di sicurezza delle applicazioni verranno configurati con privilegi minimi per garantire che solo i pacchetti consentiti possano essere trasmessi da una parte della rete alla destinazione.

**Azione** | **Nome** | **Origine** | **Destinazione** | **Porta**
--- | --- | --- | --- | --- 
CONSENTI | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
CONSENTI | AllowWebToApp | APP1-FE | APP1-DB | 1433
CONSENTI | AllowAppToDB | APP1-APP | Qualsiasi | Qualsiasi
Nega | DenyAllInbound | Qualsiasi | Qualsiasi | Qualsiasi

### <a name="encrypt-data"></a>Crittografare i dati

Crittografia dischi di Azure è integrata con Azure Key Vault per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti in una sottoscrizione dell'insieme di credenziali delle chiavi. Assicura anche che tutti i dati nei dischi delle VM vengano crittografati quando inattivi in Archiviazione di Azure.  

- Contoso ha deciso che per alcune VM è necessaria la crittografia:
- ha intenzione di applicarla per le VM con dati PPI, riservati o dei clienti.


## <a name="conclusion"></a>Conclusioni

In questo articolo Contoso ha configurato la propria infrastruttura di Azure e impostato o pianificato criteri di infrastruttura per sottoscrizione di Azure, identità ibride, ripristino di emergenza, reti, governance e sicurezza. 

Non tutti i passaggi completati da Contoso in questo articolo sono obbligatori per la migrazione al cloud. Nel caso specifico, l'azienda intendeva pianificare un'infrastruttura di rete che potesse essere usata per tutti i tipi di migrazioni e che fosse sicura, resiliente e scalabile. 

Una volta implementata questa infrastruttura, l'azienda è pronta a proseguire e provare la migrazione.

## <a name="next-steps"></a>Passaggi successivi

Come primo scenario di migrazione, Contoso [valuterà l'app SmartHotel locale a due livelli per la migrazione ad Azure](contoso-migration-assessment.md). 
