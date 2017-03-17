---
title: Procedure consigliate per la sicurezza delle macchine virtuali di Azure | Microsoft Docs
description: Questo articolo offre una raccolta di procedure consigliate per la sicurezza da usare per le macchine virtuali presenti in Azure.
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Procedure consigliate per la sicurezza delle macchine virtuali di Azure

Nella maggior parte degli scenari IaaS (Infrastructure as a Service) le [macchine virtuali](https://docs.microsoft.com/en-us/azure/virtual-machines/) sono il carico di lavoro principale per le organizzazioni che usano il cloud computing. Questo avviene prevalentemente negli [scenari ibridi](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) in cui le organizzazioni desiderano eseguire lentamente la migrazione dei carichi di lavoro nel cloud. È necessario seguire lo scenario relativo alle [considerazioni generali sulla sicurezza per IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) e assicurarsi che le pratiche di sicurezza consigliate siano applicate a tutte le VM in Azure.

In questo articolo verrà illustrato un insieme di procedure consigliate per la sicurezza delle VM di Azure. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con le VM di Azure. 

Per ogni procedura consigliata verrà illustrato:

- Qual è la procedura consigliata
- Il motivo per cui si vuole abilitare tale procedura consigliata
- Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
- Alternative possibili alla procedura consigliata
- Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza della macchine virtuali di Azure si basa su un parere condiviso, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della redazione di questo articolo. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Le procedure consigliate per la sicurezza della VM di Azure discusse in questo articolo includono:

- l'autenticazione della macchina virtuale e il controllo di accesso
- la disponibilità della macchina virtuale e l'accesso alla rete
- la protezione dei dati inattivi nelle macchine virtuali di Azure tramite l'applicazione della crittografia
- la gestione degli aggiornamenti sulla macchina virtuale
- la gestione del comportamento sicuro della macchina virtuale
- il monitoraggio delle prestazioni della macchina virtuale

## <a name="virtual-machine-authentication-and-access-control"></a>Autenticazione della macchina virtuale e controllo di accesso

Il primo passo per proteggere la macchina virtuale è garantire che solo gli utenti autorizzati possano eseguire il provisioning delle nuove macchine virtuali. È possibile usare [i criteri di Resource Manager](../azure-resource-manager/resource-manager-policy.md) per definire le convenzioni per le risorse nell'organizzazione, creare criteri personalizzati e applicare questi criteri alle risorse, ad esempio a un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). Le macchine virtuali che appartengono al gruppo di risorse ereditano tali criteri. Sebbene questo sia l'approccio consigliato per la gestione delle risorse (incluse le macchine virtuali) che hanno esigenze diverse e si trovano in diversi gruppi di risorse, è inoltre possibile controllare il singolo accesso alle macchine virtuali utilizzando [il controllo degli accessi in base al ruolo (RBAC)](../active-directory/role-based-access-control-configure.md).

Con l'abilitazione dei criteri di Azure Resource Manager e del controllo degli accessi in base al ruolo per controllare l'accesso alle macchine virtuali, si intende potenziare la sicurezza complessiva della macchina virtuale. Si consiglia di posizionare in uno stesso gruppo di risorse le macchine virtuali strettamente associate che condividono lo stesso ciclo di vita. I gruppi di risorse consentono di distribuire e monitorare le risorse in gruppo, distribuendo i costi per ogni gruppo di risorse. Usare l'approccio [privilegio minimo](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) per abilitare l'accesso agli utenti al fine di eseguire il provisioning delle macchine virtuali e programmare di usare i ruoli predefiniti seguenti in Azure durante l'assegnazione dei privilegi per gli utenti:

- [Collaboratore Macchina virtuale](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): può gestire le macchine virtuali, ma non la rete virtuale o l'account di archiviazione a cui sono connesse.
- [Collaboratore Macchina virtuale classica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): può gestire le macchine virtuali classiche, ma non la rete virtuale o l'account di archiviazione a cui sono connesse.
- [Gestore sicurezza](../active-directory/role-based-access-built-in-roles.md#security-manager): può gestire i componenti di sicurezza, i criteri di sicurezza e le macchine virtuali.
- [Utente DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare le macchine virtuali

Non condividere account e password tra gli amministratori o riusare le password per più account utente o servizi, in particolare quelli destinati a social media o ad altre attività non amministrative. Si consiglia di usare i modelli di [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per proteggere il provisioning delle macchine virtuali. Usando questo approccio è possibile stabilire in modo definitivo le opzioni di distribuzione e applicare le impostazioni di sicurezza per la distribuzione.

Le organizzazioni che non applicano il controllo dell'accesso ai dati con funzionalità come Controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario ai propri utenti. Questo può comportare la compromissione dei dati perché l'utente potrebbe avere accesso a determinati livelli di dati a cui non dovrebbe accedere.
 

## <a name="virtual-machine-availability-and-network-access"></a>Disponibilità della macchina virtuale e accesso alla rete

Se la macchina virtuale esegue applicazioni critiche che richiedono un'elevata disponibilità, è consigliabile usare più macchine virtuali.  Per una maggiore disponibilità, creare almeno due macchine virtuali nel [set di disponibilità](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Il [bilanciamento del carico](../load-balancer/load-balancer-overview.md) di Azure richiede inoltre che le macchine virtuali con carico bilanciato appartengano allo stesso set di disponibilità. Se per queste macchine virtuali è necessario eseguire l'accesso da Internet, sarà necessario configurare un [Servizio di bilanciamento del carico con connessione Internet](../load-balancer/load-balancer-internet-overview.md).

Quando le macchine virtuali sono esposte in Internet, è importante assicurarsi di [controllare il flusso del traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).  Poiché i gruppi di sicurezza di rete possono essere applicati alle subnet, è possibile ridurre al minimo il numero di gruppi di sicurezza di rete raggruppando le risorse per subnet e applicando i gruppi di sicurezza di rete alle subnet. Lo scopo è quello di creare un livello di isolamento della rete, che si può ottenere mediante la configurazione corretta delle funzionalità della [sicurezza di rete](../best-practices-network-security.md) in Azure.  

È inoltre possibile usare la funzionalità di accesso alle macchine virtuali just in time dal Centro sicurezza di Azure per controllare chi e per quanto tempo è possibile accedere da remoto a una macchina virtuale specifica. Guardare il video di seguito per altre informazioni su come usare questa funzionalità:


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Le organizzazioni che non applicano restrizioni di accesso alla rete per le macchine virtuali connesse a Internet sono esposte a rischi di sicurezza, ad esempio un attacco a forza bruta RDP. 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>Protezione dei dati inattivi nelle macchine virtuali di Azure tramite l'applicazione della crittografia

Oggi [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy, la conformità e la sovranità dei dati. La [Crittografia dischi di Azure](../security/azure-security-disk-encryption.md) consente agli amministratori IT di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi di Azure si basa sulla funzionalità BitLocker standard di settore disponibile in Windows e sulla funzionalità DM-Crypt di Linux per offrire la crittografia del volume per i dischi dei dati e del sistema operativo.

Crittografia dischi di Azure contribuisce alla protezione dei dati e al rispetto degli impegni dell'organizzazione a livello di sicurezza e conformità. La crittografia permette anche alle organizzazioni di ridurre i rischi correlati all'accesso non autorizzato ai dati. È anche consigliabile crittografare le unità prima di scrivervi dati sensibili. 

Assicurarsi di crittografare i volumi dei dati della macchina virtuale per proteggere i volumi dei dati inattivi nell'account di archiviazione di Azure. Proteggere le chiavi di crittografia e i segreti grazie a [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/). 

Le organizzazioni che non applicano la crittografia dei dati hanno maggiori probabilità di esposizione a problemi di integrità dei dati, ad esempio il furto di dati da parte di utenti non autorizzati o malintenzionati e l'accesso non autorizzato a dati non crittografati con account compromessi. Oltre a questi rischi, le aziende che sono tenute a rispettare normative di settore devono dimostrare di operare in modo conforme e di implementare i controlli di sicurezza appropriati per aumentare la sicurezza dei dati.

Per altre informazioni su Crittografia dischi di Azure, leggere l'articolo [Crittografia dischi di Azure per le macchine virtuali IaaS Windows e Linux](azure-security-disk-encryption.md).


## <a name="manage-virtual-machine-updates"></a>Gestione degli aggiornamenti sulle macchine virtuali

Azure non effettua il push degli aggiornamenti di Windows sulle macchine virtuali di Windows Azure poiché queste macchine sono pensate per essere gestite dall'utente, proprio come accade per qualsiasi computer locale. Tuttavia i clienti sono invitati a lasciare abilitata l'impostazione automatica di Windows Update. Un'altra opzione consiste nel distribuire un server [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) o un altro prodotto appropriato alla gestione degli aggiornamenti in un'altra macchina virtuale o locale di Azure. Sia WSUS che Windows Update consento gli aggiornamenti delle macchine virtuali. È inoltre opportuno usare un prodotto per verificare che tutte le macchine virtuali IaaS siano aggiornate.

Le immagini fornite da Azure sono regolarmente aggiornate per includere il round più recente degli aggiornamenti di Windows. Tuttavia, non vi è alcuna garanzia che le immagini saranno aggiornate in fase di distribuzione. È possibile che ci sia un lieve ritardo (non più di qualche settimana) rispetto alle versioni pubbliche. Controllare e installare tutti gli aggiornamenti di Windows deve essere il primo passaggio di ogni distribuzione. È particolarmente importante eseguire questa operazione quando si vuole eseguire la distribuzione delle immagini o da una libreria personalizzata. Per le immagini fornite come parte della raccolta di Windows Azure gli aggiornamenti automatici di Windows sono sempre abilitati per impostazione predefinita.

Le organizzazioni che non applicano criteri di aggiornamento del software sono più esposti ai rischi che sfruttano le vulnerabilità note che sono già state corrette. Oltre a questi rischi, le aziende che sono tenute a rispettare normative di settore devono dimostrare di operare in modo conforme e di implementare i controlli di sicurezza appropriati per aumentare la sicurezza dei carichi di lavoro che si trovano nel cloud.
È importante evidenziare che esistono molte analogie tra le procedure consigliate per gli aggiornamenti software in un data center tradizionale e un ambiente IaaS di Azure, quindi è consigliabile valutare i criteri di aggiornamento software attuali e includere le macchine virtuali di Azure.

## <a name="manage-virtual-machine-secure-posture"></a>Gestione del comportamento sicuro della macchina virtuale

Le minacce informatiche si evolvono e per proteggere le macchine virtuali è necessaria una migliore capacità di monitoraggio che sia in grado di rilevare in modo rapido le minacce, attivare gli avvisi e ridurre i falsi positivi. Il comportamento sicuro per questo tipo di carico di lavoro comprende tutti gli aspetti di protezione della macchina virtuale, dalla gestione degli aggiornamenti, all'accesso alla rete protetta, durante il monitoraggio attivo delle minacce che tentano di ottenere accesso non autorizzato alle risorse.

È possibile usare il [Centro sicurezza di Azure](../security-center/security-center-intro.md) per monitorare il comportamento sicuro delle [macchine virtuali di Windows](../security-center/security-center-virtual-machine.md) e [Linux](../security-center/security-center-linux-virtual-machine.md). È possibile sfruttare le funzionalità seguenti nel Centro sicurezza di Azure per monitorare le macchine virtuali:

- Impostazioni di sicurezza del sistema operativo con le regole di configurazione consigliate
- Aggiornamenti della sicurezza del sistema e altri aggiornamenti di importanza critica eventualmente mancanti
- Consigli per la protezione degli endpoint (antimalware)
- Convalida della crittografia del disco
- Valutazione della vulnerabilità e correzioni
- Introduzione al rilevamento delle minacce

Il Centro sicurezza è in grado di monitorare attivamente le minacce e queste minacce verranno esposte in Avvisi sicurezza. Le minacce correlate verranno aggregate in un'unica visualizzazione denominata *Evento imprevisto per la sicurezza*. È possibile guardare il video seguente per comprendere come il Centro sicurezza consenta di identificare le potenziali minacce nelle macchine virtuali all'interno di Azure.

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Le organizzazioni che non applicano un comportamento sicuro per le proprie macchine virtuali non sono consapevoli dei potenziali tentativi di aggirare i controlli di sicurezza attivi.

## <a name="monitoring-virtual-machine-performance"></a>Monitoraggio delle prestazioni della macchina virtuale

L'uso improprio delle risorse, inoltre, può essere un problema quando una macchina virtuale dispone di processi che usano più risorse rispetto a quanto previsto. Una macchina virtuale con un problema di prestazioni può causare un'interruzione del servizio, che supera una delle entità di sicurezza: la disponibilità. Per questo motivo è fondamentale monitorare l'accesso alle macchine virtuali non solo in modo reattivo (quando si verifica un problema) ma anche con una linea di base durante il periodo di normale funzionamento.

[I log di diagnostica di Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) consentono di monitorare le risorse della macchina virtuale e di identificare i potenziali problemi che ne compromettono le prestazioni e la disponibilità. L'estensione di Diagnostica di Azure offre le funzionalità di monitoraggio e diagnostica in una macchina virtuale di Azure basata su Windows. È possibile abilitare queste funzionalità nella macchina virtuale includendo l'estensione come parte del [modello](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md) di Azure Resource Manager. È inoltre possibile usare il [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) per ottenere visibilità nell'integrità della risorsa.

Le organizzazioni che non eseguono il monitoraggio delle prestazioni della macchina virtuale non saranno in grado di determinare se determinate modifiche nei modelli di prestazioni fanno parte dell'uso normale o se si verifica un'operazione anomala che usa più risorse rispetto al normale. L'anomalia potrebbe indicare un potenziale attacco proveniente da una risorsa esterna o un processo compromesso in esecuzione nella macchina virtuale. 
