---
title: Cos'è Azure Stack? | Microsoft Docs
description: Informazioni su come Azure Stack ti permette di eseguire servizi di Azure nel tuo Data Center.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631292"
---
# <a name="azure-stack-overview"></a>Panoramica di Azure Stack

Azure Stack è un'estensione di Azure che fornisce un modo per eseguire applicazioni in un ambiente locale e distribuire servizi di Azure nel tuo Data Center. Con una piattaforma cloud uniforme, le organizzazioni potranno rendere in tutta sicurezza decisioni relative alle tecnologie in base ai requisiti aziendali, invece di decisioni aziendali basate sulle limitazioni di tecnologia.

## <a name="why-use-azure-stack"></a>Perché usare Azure Stack?

Azure offre una piattaforma avanzata per gli sviluppatori di compilare applicazioni moderne. Tuttavia, alcune applicazioni basata sul cloud devono affrontare gli ostacoli, ad esempio latenza, la connettività intermittente e normative. Azure e Azure Stack sbloccare nuovi casi d'uso cloud ibridi per sia per i clienti e interni applicazioni line of business:

- **Soluzioni disconnesse ed edge**. Indirizzo latenza e i requisiti di connettività elaborando i dati in locale in Azure Stack e quindi aggregandoli in Azure per un'ulteriore analitica, con la logica dell'applicazione comune in entrambi. È anche possibile distribuire Azure Stack disconnesso da internet senza la connettività ad Azure. Considerato fabbrica alle navi, crociera viene fornito e alberi di data mining come esempi.

- **Applicazioni cloud che soddisfano diverse normative**. Sviluppa e Distribuisci applicazioni in Azure, con la flessibilità completa per distribuire in locale con Azure Stack per soddisfare i requisiti normativi o relativi ai criteri, senza modifiche al codice necessita. Esempi di applicazioni includono controllo globale, creazione di report finanziari, cambio esteri commerciali, giochi online ed expense reporting.

- **Modello di applicazioni cloud in locale**. Usare servizi di Azure, contenitori, senza server e le architetture di microservizi per aggiornare e di estendere le applicazioni esistenti o creane di nuove. I processi DevOps coerente di usare in Azure nel cloud e Azure Stack in locale per velocizzare la modernizzazione delle applicazioni per applicazioni mission-critical di core.

Azure Stack consente di questi scenari di utilizzo fornendo:

- Un'esperienza integrata di recapito per iniziare subito e in esecuzione rapidamente con Azure Stack progettati su misura i sistemi integrati da partner hardware attendibile. Dopo il recapito, Stack di Azure si integra facilmente nel datacenter con il monitoraggio tramite l'estensione per il Management Pack di System Center Operations Manager o Nagios.

- Gestione delle identità flessibili tramite Azure Active Directory (Azure AD) per gli ambienti ibridi di Azure e Azure Stack e uso di Active Directory Federation Services (ADFS) per la disconnessione le distribuzioni. 

- Sta per raggiungere un ambiente di sviluppo di applicazioni coerenti con Azure per ottimizzare la produttività degli sviluppatori e Abilita DevOps comuni in ambienti ibridi.

- Distribuzione di servizi di Azure da locale usando il cloud ibrido potenza di elaborazione. Adottare procedure operative comuni in Azure e Azure Stack per distribuire e gestire servizi PaaS e IaaS di Azure usando le stesse esperienze amministrative e gli strumenti di Azure. Microsoft offre un'innovazione continua Azure allo Stack di Azure, tra cui nuovi servizi di Azure, gli aggiornamenti per i servizi esistenti e altre applicazioni di Azure Marketplace e immagini.

## <a name="azure-stack-architecture"></a>Architettura di Azure Stack
Azure Stack i sistemi integrati sono composti in rack di 4-16 server creati dai partner hardware affidabili e recapitati direttamente nel tuo Data Center. Dopo il recapito, un provider di soluzioni collaborerà con te per distribuire il sistema integrato e assicurarsi che la soluzione di Azure Stack soddisfa i requisiti aziendali. È necessario preparare il tuo Data Center e garantendo tutti obbligatori power and Cooling Module, connettività del bordo e altri requisiti di integrazione di Data Center necessari siano soddisfatti. 

> Per altre informazioni sull'esperienza di integrazione del Data Center Azure Stack, vedere [integrazione di Data Center di Azure Stack](azure-stack-customer-journey.md).

Azure Stack si basa su hardware standard del settore e viene gestito usando gli stessi strumenti che usi già per la gestione delle sottoscrizioni di Azure. Di conseguenza, è possibile applicare i processi DevOps coerente se è connessi ad Azure oppure No. 

L'architettura di Azure Stack consente di fornire servizi di Azure nei dispositivi perimetrali per posizioni remote o connettività intermittente, disconnesso da internet. È possibile creare soluzioni ibride che elaborano i dati in locale in Azure Stack e quindi aggregano in Azure per l'analitica e un'ulteriore elaborazione. Infine, poiché Azure Stack è installato in locale, è possibile soddisfare specifici requisiti normativi o dei criteri con la flessibilità di distribuzione cloud dell'applicazione in locale senza modificare il codice. 

## <a name="deployment-options"></a>Opzioni di distribuzione

### <a name="production-or-evaluation-environments"></a>Ambienti di produzione o di valutazione
Azure Stack è disponibile in due opzioni di distribuzione in base alle esigenze, i sistemi integrati di Azure Stack per la produzione e Azure Stack Development Kit (ASDK) per la valutazione di Azure Stack:

- **Sistemi integrati di Azure Stack**. Azure Stack i sistemi integrati sono disponibili tramite una collaborazione dei partner Microsoft e hardware, la creazione di una soluzione che offre l'innovazione paced cloud e semplicità di gestione di elaborazione. Poiché Azure Stack viene offerto come sistema di software e hardware integrato, si hanno la flessibilità e il controllo necessari, nonché la possibilità di apportare innovazione dal cloud. Sistemi Azure Stack integrati dimensioni variano da 4 a 16 nodi e congiuntamente supportati dai partner hardware e Microsoft. Usare i sistemi integrati di Azure Stack per creare nuovi scenari e distribuire nuove soluzioni per i carichi di lavoro di produzione.

- **Azure Stack Development Kit**. Il [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) è una distribuzione gratuita a nodo singolo di Azure Stack che è possibile utilizzare per valutare e ottenere informazioni su Azure Stack. È anche possibile usare il ASDK come un ambiente di sviluppo per compilare le app usando le API e strumenti che è coerente con Azure. Tuttavia, il ASDK non è destinato a essere utilizzato come ambiente di produzione e presenta le limitazioni seguenti rispetto alla distribuzione di produzione completo di sistemi integrati:

    - Il ASDK solo può essere associato a un singolo Azure Active Directory (Azure AD) o provider di identità di Active Directory Federation Services (ADFS).
    - Poiché i componenti dello Stack di Azure vengono distribuiti in un singolo computer host, sono disponibili risorse fisiche limitate per le risorse del tenant. Questa configurazione non è destinata alla scala o per la valutazione delle prestazioni.
    - Scenari di rete sono limitati a causa il singolo host e i requisiti di distribuzione di interfaccia di rete.

### <a name="connection-models"></a>Modelli di connessione
È possibile scegliere di distribuire Azure Stack, ovvero **connessi** a internet (e in Azure) o **disconnesso** da quest'ultimo. Questa scelta definisce quali opzioni sono disponibili per l'archivio di identità (Azure AD o AD FS) e il modello di fatturazione (pagare quando si usa basata su fatturazione o basati su capacità di fatturazione).

> Per altre informazioni, vedere le considerazioni relative alla [connessi](azure-stack-connected-deployment.md) e [disconnesso](azure-stack-disconnected-deployment.md) modelli di distribuzione. 

### <a name="identity-provider"></a>Provider di identità 
Stack di Azure Usa Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) come provider di identità per stabilire le identità di Azure Stack. 

> [!IMPORTANT]
> Questo è un punto di decisione fondamentale. La scelta di Azure AD o AD FS come provider di identità è una decisione una-tantum che è necessario prendere in fase di distribuzione. È possibile modificare questo in un secondo momento senza ridistribuzione dell'intero sistema.

Azure AD è il provider di identità basato sul cloud e multi-tenant di Microsoft. La maggior parte degli scenari ibridi con distribuzioni connesso a internet usano Azure AD come archivio identità. Tuttavia, è possibile scegliere di utilizzare Active Directory Federation Services (ADFS) per le distribuzioni non connessione di Azure Stack. I provider di risorse di Azure Stack e altre applicazioni, lavorare molto simile a quello con AD FS come avviene con Azure AD. Lo Stack di Azure include una propria istanza di Active Directory e un'API Graph di Active Directory. 

> Altre informazioni sulle considerazioni relative all'identità di Azure Stack in [panoramica dell'identità per Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Modalità di gestione Azure Stack?
Dopo la distribuzione di Azure Stack in una distribuzione di sistemi integrati o un'installazione ASDK, i metodi principali per l'interazione con Azure Stack sono il portale di amministrazione, portale per gli utenti e PowerShell. I portali di Azure Stack sono ognuno supportati da istanze separate di Azure Resource Manager. Un' **operatore di Azure Stack** Usa il portale di amministrazione per la gestione di Azure Stack e per eseguire operazioni come creare offerte di tenant e mantenere l'integrità e monitorare lo stato del sistema integrato. Il portale utenti (detto anche il portale tenant) offre un'esperienza self-service per l'utilizzo delle risorse cloud, come le macchine virtuali, gli account di archiviazione e le app web. 

> Per altre informazioni sulla gestione di Azure Stack tramite il portale di amministrazione, vedere l'utilizzo di [introduttiva nel portale di Azure Stack amministrazione](azure-stack-manage-portals.md).

Un operatore di Stack di Azure, è possibile distribuire un'ampia gamma di applicazioni e servizi, ad esempio [macchine virtuali](azure-stack-tutorial-tenant-vm.md), [applicazioni web](azure-stack-app-service-overview.md), a disponibilità elevata [SQL Server](azure-stack-tutorial-sql.md), e [il MySQL Server](azure-stack-tutorial-mysql.md) i database. È anche possibile usare [modelli di Azure Resource Manager di Azure Stack quickstart](https://github.com/Azure/AzureStack-QuickStart-Templates) per la distribuzione di SharePoint, Exchange e altro ancora. 

Tramite il portale di amministrazione, è possibile [configurare Azure Stack per offrire servizi](azure-stack-plan-offer-quota-overview.md) ai tenant attraverso i piani, le quote, offerte e sottoscrizioni. Gli utenti tenant possono sottoscrivere più offerte. Le offerte possono includere uno o più piani, che a loro volta possono includere uno o più servizi. Gli operatori anche gestire la capacità e rispondono agli avvisi. 

Quando si configura Azure Stack, un' **utente di Azure Stack** (detta anche un tenant) utilizza i servizi che offre l'operatore. Utenti possono effettuare il provisioning, monitorare e gestire i servizi che hanno sottoscritto, ad esempio App web, archiviazione e macchine virtuali.

> Per altre informazioni sulla gestione di Azure Stack, inclusi gli account da usare where, responsabilità da operatore tipiche, quali gli utenti e come ottenere assistenza, esaminare [nozioni fondamentali sull'amministrazione di Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Provider di risorse 
I provider di risorse sono servizi web che costituiscono la base per tutti i Azure Stack IaaS e PaaS. Azure Resource Manager si basa su diversi provider di risorse per fornire l'accesso ai servizi. Ogni provider di risorse consente di configurare e controllare le rispettive risorse. Gli amministratori del servizio è anche possono aggiungere nuovi provider di risorse personalizzati. 

### <a name="foundational-resource-providers"></a>Provider di risorse fondamentali 
Sono disponibili tre provider di risorse IaaS fondamentali: Calcolo, rete e archiviazione:

- **Calcolo**. Il Provider di risorse di calcolo consente il tenant di Azure Stack creare le proprie macchine virtuali. Il Provider di risorse di calcolo include la possibilità di creare macchine virtuali, nonché le estensioni macchina virtuale. Il servizio di estensione della macchina virtuale consente di fornire funzionalità IaaS per macchine virtuali Linux e Windows.  Ad esempio, è possibile utilizzare il Provider di risorse di calcolo per eseguire il provisioning di una macchina virtuale Linux ed eseguire gli script di Bash durante la distribuzione per configurare la macchina virtuale.
- **Provider di risorse di rete**. Il Provider di risorse di rete offre una serie di funzionalità di rete SDN (Software Defined) e rete funzione virtualizzazione (NFV) per il cloud privato. È possibile usare il Provider di risorse di rete per creare le risorse, ad esempio servizi di bilanciamento del carico software, gli indirizzi IP pubblici, gruppi di sicurezza di rete e reti virtuali.
- **Provider di risorse di archiviazione**. Il Provider di risorse di archiviazione offre quattro servizi di archiviazione coerenti con Azure: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [coda](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabella](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)e gestione degli account Key Vault offre la gestione e il controllo dei segreti, ad esempio le password e certificati. Il provider di risorse di archiviazione offre anche un servizio di amministrazione di archiviazione cloud per facilitare l'amministrazione del servizio provider di servizi di archiviazione coerenti con Azure. Archiviazione di Azure permette di archiviare e recuperare in modo flessibile grandi quantità di dati non strutturati, ad esempio documenti e file multimediali usando i BLOB di Azure e dati strutturati basati su NoSQL usando le tabelle di Azure. 

### <a name="optional-resource-providers"></a>Provider di risorse facoltative
Sono disponibili tre provider di risorse facoltative PaaS che è possibile distribuire e usare con Azure Stack: Provider di risorse del servizio App, SQL Server e il MySQL Server:

- **Servizio app**. [Servizio App di Azure in Azure Stack](azure-stack-app-service-overview.md) è un'offerta di platform-as-a-service (PaaS) di Microsoft Azure disponibili in Azure Stack. Il servizio consente ai clienti interni o esterni creare web, API e funzioni di Azure delle applicazioni per qualsiasi piattaforma o dispositivo. 
- **SQL Server**. Usare la [provider di risorse di SQL Server](azure-stack-sql-resource-provider.md) per offrire database SQL come servizio di Azure Stack. Dopo avere installato il provider di risorse e connetterla a una o più istanze di SQL Server, gli utenti possono creare database per App native del cloud, siti Web che usano SQL e altri carichi di lavoro che usano SQL.
- **Il Server MySQL**. Usare la [provider di risorse MySQL Server](azure-stack-mysql-resource-provider-deploy.md) per esporre i database MySQL come servizio di Azure Stack. Il provider di risorse MySQL viene eseguito come servizio in una macchina virtuale di Windows Server 2016 Server Core (VM).

## <a name="providing-high-availability"></a>Disponibilità elevata
Per ottenere disponibilità elevata di un sistema di produzione di più macchine Virtuali in Azure, macchine virtuali vengono inserite in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) che queste ultime vengono distribuite tra più domini di errore e domini di aggiornamento. Nella scalabilità minore di Azure Stack, un dominio di errore in un set di disponibilità è definito come un singolo nodo nell'unità di scala.  

Mentre l'infrastruttura di Azure Stack è ancora resiliente agli errori, la tecnologia sottostante (clustering di failover) ancora comporta tempi di inattività per le macchine virtuali in un server fisico interessati se è presente un errore hardware. Azure Stack supporta un set di disponibilità con un massimo di tre domini di errore siano coerenti con Azure.

- **Domini di errore**. Le macchine virtuali inserite in un set di disponibilità è fisicamente isolate una da altra per la distribuzione di in modo più uniforme possibile in più domini di errore (nodi di Azure Stack). Se si verifica un errore hardware, le macchine virtuali dal dominio di errore vengono riavviate in altri domini di errore, ma, se possibile, mantenute in domini di errore da altre macchine virtuali nello stesso set di disponibilità. Quando l'hardware ritorna in linea, le macchine virtuali saranno ribilanciate per mantenere la disponibilità elevata. 
 
- **Domini di aggiornamento**. Domini di aggiornamento sono un altro concetto di Azure che fornisce elevata disponibilità nei set di disponibilità. Un dominio di aggiornamento è un gruppo logico di hardware sottostante che può essere sottoposto a manutenzione contemporaneamente. Macchine virtuali presenti nello stesso dominio di aggiornamento verranno riavviate contemporaneamente durante la manutenzione pianificata. Quando i tenant creano VM all'interno di un set di disponibilità, la piattaforma Azure distribuisce automaticamente le macchine virtuali in questi domini di aggiornamento. In Azure Stack le macchine virtuali sono in tempo reale la migrazione tra gli altri host nel cluster online prima dell'aggiornamento dell'host sottostante. Perché durante un aggiornamento host è presente alcun tempo di inattività di tenant, la funzionalità del dominio di aggiornamento in Azure Stack è presente solo per la compatibilità dei modelli di Azure. 

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
È possibile usare basato su accesso controllo ruoli (RBAC) per concedere l'accesso di sistema per gli utenti autorizzati, gruppi e servizi assegnando loro ruoli a una sottoscrizione, gruppo di risorse o a livello di singole risorse. Ogni ruolo definisce il livello di accesso di un utente, un gruppo o un servizio rispetto alle risorse di Microsoft Azure Stack.

Azure Stack RBAC dispone di tre ruoli di base che si applicano a tutti i tipi di risorse: Proprietario, collaboratore e lettore. Proprietario dispone dell'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Collaboratore può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti. Lettore può solo visualizzare le risorse esistenti. Il resto dei ruoli RBAC consentono la gestione delle risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore di macchina virtuale consente la creazione e la gestione delle macchine virtuali, ma non consente la gestione della rete virtuale o della subnet a cui la macchina virtuale si connette.

> Visualizzare [controllo di accesso di accessi](azure-stack-manage-permissions.md) per altre informazioni. 

## <a name="reporting-usage-data"></a>I dati di utilizzo dei report
Microsoft Azure Stack raccoglie e aggrega i dati di utilizzo in tutti i provider di risorse e lo trasmette in Azure per l'elaborazione dal commerce di Azure. I dati di utilizzo raccolti in Azure Stack possono essere visualizzati tramite un'API REST. Sono disponibili un'API del tenant coerente con Azure e API del provider e del provider delegato che permettono di ottenere dati di utilizzo di tutte le sottoscrizioni del tenant. Questi dati possono essere integrati con un servizio o uno strumento esterno di fatturazione o chargeback. Una volta che utilizzo è stato elaborato dal commercio Azure, possono essere visualizzato nel portale di fatturazione di Azure.

> Altre informazioni sulle [reporting i dati di utilizzo di Azure Stack in Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Passaggi successivi

[Confrontare Azure Stack e Azure globale](compare-azure-azure-stack.md)

[Nozioni fondamentali sull'amministrazione](azure-stack-manage-basics.md)

[Guida introduttiva: usare il portale di amministrazione di Azure Stack](azure-stack-manage-portals.md)