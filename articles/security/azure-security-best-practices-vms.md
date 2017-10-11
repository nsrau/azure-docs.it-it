---
title: Procedure consigliate per la sicurezza delle macchine virtuali di Azure | Documentazione Microsoft
description: Questo articolo riporta varie procedure consigliate per la sicurezza da usare per le macchine virtuali di Azure.
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
ms.date: 07/19/2017
ms.author: yurid
ms.openlocfilehash: c8a920a0523cb4737e6bbca7e49d0b9e2c942565
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="best-practices-for-azure-vm-security"></a>Procedure consigliate per la sicurezza delle VM di Azure

Nella maggior parte degli scenari Infrastructure as a Service (IaaS) le [macchine virtuali (VM) di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/) rappresentano il carico di lavoro principale per le organizzazioni che usano il cloud computing. Questo è particolarmente evidente negli [scenari ibridi](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) in cui le organizzazioni vogliono eseguire lentamente la migrazione dei carichi di lavoro nel cloud. In questi scenari seguire la [considerazioni generali sulla sicurezza per IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) e applicare le procedure consigliate di sicurezza a tutte le VM.

Questo articolo tratta varie procedure consigliate di sicurezza per le VM derivate da esperienze dei nostri clienti e nostre esperienze dirette con le VM stesse.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Poiché le opinioni e le tecnologie possono cambiare nel tempo, questo articolo verrà aggiornato regolarmente per riflettere tali variazioni.

Per ciascuna procedura consigliata l'articolo spiega:

* In cosa consiste la procedura consigliata.
* Perché è preferibile abilitarla.
* Come imparare ad abilitarla.
* Cosa potrebbe succedere se non la si abilita.
* Possibili alternative alla procedura consigliata.

L'articolo esamina le seguenti procedure consigliate di sicurezza per le VM:

* Autenticazione e controllo di accesso della VM
* Disponibilità e accesso alla rete della VM
* Protezione dei dati inattivi nelle VM tramite l'applicazione della crittografia
* Gestire gli aggiornamenti della VM
* Gestire le condizioni di sicurezza della VM
* Monitorare le prestazioni della VM

## <a name="vm-authentication-and-access-control"></a>Autenticazione e controllo di accesso della VM

Il primo passo per proteggere la VM è garantire che solo gli utenti autorizzati possano configurare la VM. È possibile usare [i criteri di Azure Resource Manager](../azure-resource-manager/resource-manager-policy.md) per definire le convenzioni per le risorse nell'organizzazione, creare criteri personalizzati e applicare questi criteri alle risorse, ad esempio [gruppi di risorse](../azure-resource-manager/resource-group-overview.md).

Le VM che appartengono a un gruppo di risorse ereditano naturalmente i suoi criteri. Sebbene questo approccio alla gestione delle VM sia consigliabile, è possibile controllare l'accesso ai singoli criteri della MV usando il [controllo degli accessi in base al ruolo (RBAC)](../active-directory/role-based-access-control-configure.md).

Quando si abilitano i criteri di Resource Manager e controllo degli accessi in base al ruolo per controllare l'accesso alla VM, si migliora la sicurezza complessiva della VM. Si consiglia di consolidare le VM con lo stesso ciclo di vita nello stesso gruppo di risorse. Usando i gruppi di risorse è possibile distribuire, monitorare ed eseguire il rollup dei costi di fatturazione per le risorse. Per consentire agli utenti di accedere e configurare le VM, usare un [approccio con privilegi minimi](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). E quando si assegnano privilegi agli utenti prevedere di usare i seguenti ruoli predefiniti di Azure:

- [Collaboratore Macchina virtuale](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): può gestire le VM, ma non la rete virtuale o l'account di archiviazione a cui sono connesse.
- [Collaboratore Macchina virtuale classica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): può gestire le VM create usando il modello di distribuzione classico ma non la rete virtuale o l'account di archiviazione a cui le VM sono connesse.
- [Gestore sicurezza](../active-directory/role-based-access-built-in-roles.md#security-manager): può gestire i componenti di sicurezza, i criteri di sicurezza e le VM.
- [Utente DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare le VM.

Non condividere account e password tra gli amministratori e non riutilizzare le password per più account utente o servizi, in particolare quelle dei social media o di altre attività non amministrative. Si consiglia di usare i modelli di [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per configurare in sicurezza le VM. Usando questo approccio è possibile rafforzare le opzioni di distribuzione e applicare le impostazioni di sicurezza per la distribuzione.

Le organizzazioni che non applicano il controllo di accesso ai dati sfruttando funzionalità come il controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario agli utenti. L'accesso utente inappropriato ad alcuni dati può compromettere direttamente i dati.

## <a name="vm-availability-and-network-access"></a>Disponibilità e accesso alla rete della VM

Se la VM esegue applicazioni critiche che richiedono un'elevata disponibilità, è consigliabile usare più VM. Per una maggiore disponibilità, creare almeno due macchine virtuali nel [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) richiede inoltre che le VM con carico bilanciato appartengano allo stesso set di disponibilità. Se per queste VM è necessario eseguire l'accesso da Internet, si dovrà configurare un [servizio di bilanciamento del carico con connessione Internet](../load-balancer/load-balancer-internet-overview.md).

Quando le VM sono esposte a Internet, è importante [controllare il flusso del traffico di rete con gruppi di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md). Poiché i gruppi di sicurezza di rete possono essere applicati alle subnet, è possibile ridurre al minimo il numero di gruppi di sicurezza di rete raggruppando le risorse per subnet e poi applicando i gruppi di sicurezza di rete alle subnet. Lo scopo è quello di creare un livello di isolamento della rete, che si può ottenere configurando correttamente le capacità di [sicurezza di rete](../best-practices-network-security.md) in Azure.

È inoltre possibile usare la funzionalità di accesso alle VM just in time dal Centro sicurezza di Azure per controllare chi può accedere da remoto a una VM specifica e per quanto tempo.

Le organizzazioni che non applicano restrizioni di accesso alle VM connesse a Internet sono esposte a rischi di sicurezza, ad esempio attacco di forza bruta Remote Desktop Protocol (RDP).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Protezione dei dati inattivi nelle VM di Azure tramite l'applicazione della crittografia

[La crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy, la conformità e la sovranità dei dati. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) consente agli amministratori IT di crittografare i dischi delle VM IaaS Windows e Linux. Crittografia dischi combina la funzionalità standard di settore BitLocker di Windows e la funzionalità dm-crypt di Linux per fornire la crittografia del volume per i dischi del sistema operativo e dei dati.

È possibile applicare Crittografia dischi per contribuire alla protezione dei dati in modo da rispettare i requisiti dell'organizzazione in merito a sicurezza e conformità. La crittografia permette anche all'organizzazione di ridurre i rischi correlati all'accesso non autorizzato ai dati. È consigliabile anche crittografare le unità prima di scrivere dati sensibili.

Assicurarsi di crittografare i volumi di dati della VM per proteggerli nello stato inattivo nell'account di archiviazione di Azure. Proteggere le chiavi di crittografia e la chiave privata con [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/).

Le organizzazioni che non applicano la crittografia dei dati sono più esposte a problemi di integrità dei dati. Gli utenti non autorizzati, ad esempio, potrebbero rubare dati negli account compromessi o ottenere l'accesso non autorizzato ai dati codificati in ClearFormat. Oltre a correre questi rischi, per la conformità alle normative di settore, le aziende devono dimostrare di operare con diligenza e di usare controlli di sicurezza appropriati per migliorare la sicurezza dei dati.

Per altre informazioni su Crittografia dischi, vedere [Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Gestire gli aggiornamenti della VM

Poiché le VM di Azure, come tutte le VM locali, dovranno essere gestite dall'utente, Azure non applica gli aggiornamenti di Windows a queste. Tuttavia si consiglia di lasciare abilitata l'impostazione automatica di Windows Update. Un'altra opzione consiste nel distribuire [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) o un altro prodotto appropriato di gestione degli aggiornamenti in un'altra VM o in locale. Sia WSUS che Windows Update mantengono aggiornate le VM. Si consiglia anche di usare un prodotto di analisi per verificare che tutte le VM IaaS siano aggiornate.

Le immagini di archivio fornite da Azure vengono regolarmente aggiornate per includere i più recenti aggiornamenti di Windows. Tuttavia non vi è alcuna garanzia che le immagini saranno aggiornate in fase di distribuzione. Sono possibili leggeri ritardi (di non più di alcune settimane) dopo i rilasci. Controllare e installare tutti gli aggiornamenti di Windows deve essere il primo passo di ogni distribuzione. Questa misura è particolarmente importante da applicare quando si distribuiscono immagini proprie o provenienti dalla propria libreria. Le immagini che vengono fornite come parte di Azure Marketplace vengono aggiornate automaticamente per impostazione predefinita.

Le organizzazioni che non applicano criteri di aggiornamento del software sono più esposte a minacce che sfruttano vulnerabilità note e corrette in precedenza. Oltre al rischio rappresentato da queste minacce, per la conformità con le normative di settore le aziende devono dimostrare di operare con diligenza e di usare controlli di sicurezza appropriati per garantire la sicurezza dei carichi di lavoro che si trovano nel cloud.

È importante sottolineare che le procedure consigliate di aggiornamento del software per i data center tradizionali e IaaS di Azure presentano molte analogie. Si consiglia perciò di valutare i criteri di aggiornamento del software correnti per includere le VM.

## <a name="manage-your-vm-security-posture"></a>Gestire le condizioni di sicurezza della VM

Le minacce informatiche si evolvono e per proteggere le VM è necessaria una capacità di monitoraggio completa che possa rilevare rapidamente le minacce, impedire l'accesso non autorizzato alle risorse, attivare gli avvisi e ridurre i falsi positivi. Le condizioni di sicurezza per un carico di lavoro di questo tipo includono tutti gli aspetti della sicurezza della VM, dalla gestione degli aggiornamenti all'accesso sicuro alla rete.

Per monitorare le condizioni di sicurezza delle [VM Windows](../security-center/security-center-virtual-machine.md) e [Linux](../security-center/security-center-linux-virtual-machine.md), usare [Centro sicurezza di Azure](../security-center/security-center-intro.md). In Centro sicurezza di Azure proteggere le VM sfruttando le seguenti capacità:

* Applicare le impostazioni di sicurezza del sistema operativo con le regole di configurazione consigliate
* Identificare e scaricare gli aggiornamenti critici e di sicurezza del sistema che potrebbero mancare
* Consigli per la protezione antimalware degli endpoint di distribuzione
* Convalidare la crittografia del disco
* Valutare e correggere le vulnerabilità
* Rilevare le minacce

Il Centro sicurezza può monitorare attivamente le minacce e le minacce potenziali sono esposte in **Avvisi sicurezza**. Le minacce correlate sono aggregate in un'unica visualizzazione denominata **Evento imprevisto della sicurezza**.

Per comprendere come il Centro sicurezza consenta di identificare le potenziali minacce nelle VM all'interno di Azure, guardare il video seguente:

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Le organizzazioni che non applicano condizioni di sicurezza avanzate per le proprie VM rimangono all'oscuro della presenza di potenziali tentativi da parte di utenti non autorizzati di aggirare i controlli di sicurezza stabiliti.

## <a name="monitor-vm-performance"></a>Monitorare le prestazioni della VM

L'uso improprio delle risorse può essere un problema quando i processi della VM utilizzano più risorse di quanto dovrebbero. I problemi di prestazioni di una VM possono causare interruzioni del servizio, il che viola il principio di disponibilità della sicurezza. Per questo motivo è fondamentale monitorare l'accesso alla VM non solo in modo reattivo (mentre un problema si sta verificando) ma anche in modo proattivo, rispetto alle prestazioni misurate durante il periodo di normale funzionamento.

Analizzando [i file di log di diagnostica di Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), è possibile monitorare le risorse della VM e identificare i potenziali problemi che potrebbero compromettere le prestazioni e la disponibilità. L'estensione Diagnostica di Azure offre funzionalità di monitoraggio e diagnostica nelle VM Windows. È possibile abilitare queste funzionalità includendo l'estensione come parte del [modello di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

È possibile usare [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) per ottenere visibilità sull'integrità della risorsa.

Le organizzazioni che non monitorano le prestazioni della VM non sono in grado di capire se determinate modifiche nei modelli di prestazioni sono normali o anomale. Se la VM consuma più risorse del normale, una tale anomalia potrebbe indicare un potenziale attacco proveniente da una risorsa esterna o un processo compromesso in esecuzione nella VM.
