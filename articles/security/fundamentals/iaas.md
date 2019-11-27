---
title: Procedure consigliate per la sicurezza dei carichi di lavoro IaaS in Azure | Microsoft Docs
description: " La migrazione dei carichi di lavoro ad Azure IaaS offre l'opportunità di rivalutare i progetti "
services: security
documentationcenter: na
author: barclayn
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 3368f72aeb7909c3e0a8653bb5b094729c4c45ed
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228031"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Procedure consigliate per la sicurezza dei carichi di lavoro IaaS in Azure
Questo articolo descrive le procedure consigliate per la sicurezza delle macchine virtuali e dei sistemi operativi.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Poiché le opinioni e le tecnologie possono cambiare nel tempo, questo articolo verrà aggiornato regolarmente per riflettere tali variazioni.

Nella maggior parte degli scenari Infrastructure as a Service (IaaS) le [macchine virtuali (VM) di Azure](/azure/virtual-machines/) rappresentano il carico di lavoro principale per le organizzazioni che usano il cloud computing. Questo è evidente negli [scenari ibridi](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) in cui le organizzazioni vogliono eseguire lentamente la migrazione dei carichi di lavoro nel cloud. In questi scenari seguire la [considerazioni generali sulla sicurezza per IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) e applicare le procedure consigliate di sicurezza a tutte le VM.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Proteggere le VM tramite l'autenticazione e il controllo di accesso
Il primo passo per proteggere le VM consiste nel garantire che solo gli utenti autorizzati possano configurare nuove VM e accedervi.

> [!NOTE]
> Per migliorare la sicurezza delle macchine virtuali Linux in Azure, è possibile integrare con l'autenticazione Azure AD. Quando si usa [l'autenticazione Azure ad per le macchine virtuali Linux](/azure/virtual-machines/linux/login-using-aad), è possibile controllare e applicare i criteri in modo centralizzato che consentono o negano l'accesso alle macchine virtuali.
>
>

**Procedura consigliata**: controllare l'accesso alle VM.   
**Dettagli**: usare i [criteri di Azure](/azure/azure-policy/azure-policy-introduction) per stabilire convenzioni per le risorse all'interno dell'organizzazione e creare criteri personalizzati. Applicare questi criteri alle risorse, ad esempio ai [gruppi di risorse](/azure/azure-resource-manager/resource-group-overview). Le VM che appartengono a un gruppo di risorse ereditano i suoi criteri.

Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I [gruppi di gestione di Azure](/azure/azure-resource-manager/management-groups-overview) forniscono un livello di ambito al di sopra delle sottoscrizioni. Le sottoscrizioni sono organizzate in gruppi di gestione, o contenitori, a cui vengono applicate le condizioni di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo. I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono.

**Procedura consigliata**: ridurre la variabilità nel programma di configurazione e distribuzione delle macchine virtuali.   
**Dettagli**: usare i modelli di [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) per rafforzare le opzioni di distribuzione e facilitare l'individuazione e la creazione di un inventario delle macchine virtuali dell'ambiente.

**Procedura consigliata**: accesso sicuro con privilegi.   
**Dettagli**: per consentire agli utenti di accedere e configurare le VM, usare un [approccio con privilegi minimi](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e i ruoli predefiniti di Azure:

- [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): può gestire le VM, ma non la rete virtuale o l'account di archiviazione a cui sono connesse.
- [Collaboratore Macchina virtuale classica](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): può gestire le VM create usando il modello di distribuzione classico ma non la rete virtuale o l'account di archiviazione a cui le VM sono connesse.
- [Amministratore della sicurezza](../../role-based-access-control/built-in-roles.md#security-admin): solo in Centro sicurezza: è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni.
- [Utente DevTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): può visualizzare tutti gli elementi e connettere, avviare, riavviare e arrestare le VM.

Gli amministratori e i coamministratori della sottoscrizione possono modificare questa impostazione, rendendoli amministratori di tutte le VM di una sottoscrizione. Assicurarsi che tutti gli amministratori e i coamministratori della sottoscrizione che possono accedere ai computer siano attendibili.

> [!NOTE]
> Si consiglia di raggruppare le VM con lo stesso ciclo di vita nel medesimo gruppo di risorse. Usando i gruppi di risorse è possibile distribuire, monitorare ed eseguire il rollup dei costi di fatturazione per le risorse.
>
>

Le organizzazioni che controllano l'accesso alle VM e la loro configurazione migliorano la sicurezza complessiva delle VM.

## <a name="use-multiple-vms-for-better-availability"></a>Usare più VM per una maggiore disponibilità
Se la VM esegue applicazioni critiche che richiedono un'elevata disponibilità, è consigliabile usare più VM. Per una migliore disponibilità, usare un [set di disponibilità](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) o le [zone](../../availability-zones/az-overview.md)di disponibilità.

Un set di disponibilità è un raggruppamento logico che è possibile usare in Azure per garantire che le risorse delle macchine virtuali inserite dall'utente siano isolate tra loro quando vengono distribuite all'interno di un data center di Azure. Azure garantisce che le macchine virtuali inserite all'interno di un set di disponibilità vengano eseguite tra più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. In caso di guasto hardware o errore software in Azure, viene interessato solo un subset delle macchine virtuali. L'applicazione nel suo complesso rimarrà disponibile per i clienti. I set di disponibilità sono una funzionalità essenziale da sfruttare quando si vogliono creare soluzioni cloud affidabili.

## <a name="protect-against-malware"></a>Protezione dal malware
È consigliabile installare una protezione antimalware per identificare e rimuovere virus, spyware e altro software dannoso. È possibile installare [Microsoft Antimalware](antimalware.md) o una soluzione di protezione degli endpoint di un partner Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) e [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Microsoft Antimalware include caratteristiche come la protezione in tempo reale, l'analisi pianificata, il monitoraggio e aggiornamento malware, l'aggiornamento delle firme e del motore, il reporting di campioni e la raccolta degli eventi di esclusione. Per gli ambienti ospitati separatamente dall'ambiente di produzione, è possibile usare un'estensione antimalware per la protezione delle VM e dei servizi cloud.

È possibile integrare Microsoft Antimalware e soluzioni partner con [Centro sicurezza di Azure](../../security-center/index.yml) per facilitare la distribuzione e i rilevamenti predefiniti (avvisi ed eventi imprevisti).

**Procedura consigliata**: installare una soluzione antimalware per proteggersi dai malware.   
**Dettagli**: [installare una soluzione di un partner Microsoft o Microsoft Antimalware](../../security-center/security-center-install-endpoint-protection.md)

**Procedura consigliata**: integrare la soluzione antimalware con il Centro sicurezza per monitorare lo stato della protezione.   
**Dettagli**: [gestire i problemi di protezione degli endpoint con il Centro sicurezza](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Gestire gli aggiornamenti della VM
Le VM di Azure, ad esempio tutte le VM locali, sono progettate per essere gestite dagli utenti. Azure non effettua il push degli aggiornamenti di Windows verso le VM. È compito dell'utente gestire gli aggiornamenti delle VM.

**Procedura consigliata**: mantenere aggiornate le VM.   
**Dettagli**: la soluzione [Gestione aggiornamenti](../../automation/automation-update-management.md) in Automazione di Azure consente di gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux distribuiti in Azure, in ambienti locali o in altri provider di servizi cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

I computer gestiti da Gestione aggiornamenti usano le configurazioni seguenti per le valutazioni e le distribuzioni degli aggiornamenti:

- Microsoft Monitoring Agent (MMA) per Windows o Linux
- PowerShell DSC (Desired State Configuration) per Linux
- Ruolo di lavoro ibrido per runbook di Automazione
- Microsoft Update o Windows Server Update Services (WSUS) per computer Windows

Se si usa Windows Update, lasciare abilitata l'impostazione automatica di Windows Update.

**Procedura consigliata**: in fase di distribuzione verificare che le immagini create includano gli aggiornamenti più recenti di Windows.   
**Dettagli**: controllare e installare tutti gli aggiornamenti di Windows come primo passo di ogni distribuzione. Questa misura è particolarmente importante da applicare quando si distribuiscono immagini proprie o provenienti dalla propria libreria. Anche se le immagini di Azure Marketplace vengono aggiornate automaticamente per impostazione predefinita, dopo un rilascio pubblico può esserci un tempo di ritardo (fino a qualche settimana).

**Procedura consigliata**: ridistribuire periodicamente le VM per forzare una versione aggiornata del sistema operativo.   
**Dettagli**: definire la VM con un [modello di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) in modo che sia possibile ridistribuirla facilmente. La scelta di un modello offre l'opportunità di avere una VM sicura e con patch applicate quando serve.

**Procedura consigliata**: applicare rapidamente gli aggiornamenti della sicurezza alle macchine virtuali.   
**Dettagli**: abilitare il Centro sicurezza di Azure (livello gratuito o standard) per [identificare gli aggiornamenti della sicurezza mancanti e applicarli](../../security-center/security-center-apply-system-updates.md).

**Procedura consigliata**: installare gli aggiornamenti della sicurezza più recenti.   
**Dettagli**: i lab e i sistemi esterni sono tra i primi carichi di lavoro che i clienti spostano in Azure. Se le VM di Azure ospitano applicazioni o servizi che devono essere accessibili da Internet, è importante fare attenzione all'applicazione di patch. Non limitarsi all'applicazione di patch relative al sistema operativo. Anche le vulnerabilità senza patch delle applicazioni di partner possono causare problemi facilmente evitabili con una buona gestione delle patch.

**Procedura consigliata**: distribuire e testare una soluzione di backup.   
**Dettagli**: il backup deve essere gestito come qualsiasi altra operazione, anche per quanto riguarda i sistemi dell'ambiente di produzione che si estendono al cloud.

I sistemi di sviluppo e test devono seguire strategie di backup con funzionalità di ripristino simili a quelle che gli utenti degli ambienti locali sono ormai abituati a usare. Se possibile, i carichi di lavoro di produzione spostati in Azure devono integrarsi con le soluzioni di backup esistenti. In alternativa, per le esigenze di backup è possibile usare [Backup di Azure](../../backup/backup-azure-vms-first-look-arm.md).

Le organizzazioni che non applicano criteri di aggiornamento del software sono più esposte a minacce che sfruttano vulnerabilità note e corrette in precedenza. Per conformarsi alle normative del settore, le aziende devono dimostrare di operare con diligenza e di usare controlli di sicurezza appropriati per garantire la sicurezza dei carichi di lavoro che si trovano nel cloud.

Le procedure consigliate per l'aggiornamento del software per i data center tradizionali e IaaS di Azure presentano molte analogie. Si consiglia di valutare i criteri di aggiornamento del software correnti per includere le VM di Azure.

## <a name="manage-your-vm-security-posture"></a>Gestire le condizioni di sicurezza della VM
Le minacce informatiche sono in continua evoluzione. Per proteggere le VM è necessaria una capacità di monitoraggio che possa rilevare rapidamente le minacce, impedire l'accesso non autorizzato alle risorse, attivare gli avvisi e ridurre i falsi positivi.

Per monitorare le condizioni di sicurezza delle [VM Windows](../../security-center/security-center-virtual-machine.md) e [Linux](../../security-center/security-center-linux-virtual-machine.md), usare [Centro sicurezza di Azure](../../security-center/security-center-intro.md). In Centro sicurezza proteggere le VM sfruttando le seguenti capacità:

- Applicare le impostazioni di sicurezza del sistema operativo con le regole di configurazione consigliate.
- Identificare e scaricare gli aggiornamenti critici e di sicurezza del sistema che potrebbero mancare.
- Distribuire raccomandazioni per la protezione antimalware degli endpoint.
- Convalidare la crittografia del disco.
- Valutare e correggere le vulnerabilità.
- Rilevare le minacce.

Il Centro sicurezza può monitorare attivamente le minacce e le minacce potenziali sono esposte negli avvisi di sicurezza. Le minacce correlate sono aggregate in un'unica visualizzazione denominata evento imprevisto per la sicurezza.

Il Centro sicurezza archivia i dati nei [log di monitoraggio di Azure](/azure/log-analytics/log-analytics-overview). Log di monitoraggio di Azure fornisce un linguaggio di query e un motore di analisi che offre informazioni dettagliate sul funzionamento delle applicazioni e delle risorse. I dati vengono raccolti anche da [Monitoraggio di Azure](../../batch/monitoring-overview.md), dalle soluzioni di gestione e dagli agenti installati su macchine virtuali nel cloud o in locale. Questa funzionalità condivisa permette di ottenere il quadro completo dell'ambiente.

Le organizzazioni che non applicano condizioni di sicurezza avanzate per le proprie VM rimangono all'oscuro della presenza di potenziali tentativi da parte di utenti non autorizzati di aggirare i controlli di sicurezza.

## <a name="monitor-vm-performance"></a>Monitorare le prestazioni della VM
L'uso improprio delle risorse può essere un problema quando i processi della VM utilizzano più risorse di quanto dovrebbero. I problemi di prestazioni di una VM possono causare interruzioni del servizio, il che viola il principio di disponibilità della sicurezza. Ciò è particolarmente importante per le VM che ospitano IIS o altri server Web, perché un utilizzo elevato di CPU o memoria potrebbe indicare un attacco DoS (Denial of Service). È fondamentale monitorare l'accesso alla VM non solo in modo reattivo (mentre un problema si sta verificando) ma anche in modo proattivo, rispetto alle prestazioni misurate durante il periodo di normale funzionamento.

Si consiglia di usare [Monitoraggio di Azure](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) per ottenere visibilità sull'integrità della risorsa. Monitoraggio di Azure offre:

- [File di log di diagnostica delle risorse](../../azure-monitor/platform/resource-logs-overview.md): monitora le risorse della VM e identifica i potenziali problemi che potrebbero compromettere le prestazioni e la disponibilità.
- [Estensione Diagnostica di Azure](/azure/azure-monitor/platform/diagnostics-extension-overview): offre funzionalità di monitoraggio e diagnostica nelle VM Windows. È possibile abilitare queste funzionalità includendo l'estensione come parte del [modello di Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

Le organizzazioni che non monitorano le prestazioni delle VM non possono capire se determinate modifiche nei modelli di prestazioni sono normali o anomale. Una VM che consuma più risorse del normale potrebbe indicare un attacco proveniente da una risorsa esterna o un l'esecuzione di un processo compromesso.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Crittografare i file dei dischi rigidi virtuali
È consigliabile crittografare i dischi rigidi virtuali per proteggere il volume di avvio e i volumi dei dati inattivi in archiviazione, oltre alle chiavi di crittografia e ai segreti.

[Crittografia dischi di Azure](../azure-security-disk-encryption-overview.md) consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Crittografia dischi di Azure usa la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e il sistema operativo. La soluzione è integrata con [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione di Key Vault. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Di seguito sono illustrate le procedure consigliate per l'uso della Crittografia dischi di Azure:

**Procedura consigliata**: abilitare la crittografia nelle VM.   
**Dettagli**: Crittografia dischi di Azure genera e scrive le chiavi di crittografia nell'insieme di credenziali delle chiavi. La gestione delle chiavi di crittografia nell'insieme di credenziali delle chiavi richiede l'autenticazione di Azure AD. Creare un'applicazione Azure AD per questo scopo. Ai fini dell'autenticazione, è possibile usare l'autenticazione basata sul segreto client o l'[autenticazione di Azure AD basata sul certificato client](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Procedura consigliata**: usare una chiave di crittografia della chiave per aggiungere un livello di sicurezza ulteriore per le chiavi di crittografia. Aggiungere una chiave di crittografia della chiave all'insieme di credenziali delle chiavi.   
**Dettagli**: usare il cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) per creare una chiave di crittografia della chiave nell'insieme di credenziali delle chiavi. Per gestire le chiavi, è anche possibile importare una chiave di crittografia della chiave dal modulo di protezione hardware. Per altre informazioni, vedere la [documentazione di Azure Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi. Mantenere una copia di deposito della chiave in un modulo di protezione hardware locale offre un ulteriore livello di protezione contro l'eliminazione accidentale delle chiavi.

**Procedura consigliata**: catturare uno [snapshot](../../virtual-machines/windows/snapshot-copy-managed-disk.md) e/o eseguire il backup prima che i dischi vengano crittografati. Se si verifica un errore imprevisto durante la crittografia, i backup offrono un'opzione di ripristino.   
**Dettagli**: le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet **set-AzVMDiskEncryptionExtension** per crittografare i dischi gestiti specificando il parametro *-skipVmBackup* . Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere l'articolo [Backup di Azure](../../backup/backup-azure-vms-encryption.md).

**Procedura consigliata**: per assicurarsi che i segreti di crittografia non superino i confini a livello di area, Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area.   
**Dettagli**: creare e usare un insieme di credenziali delle chiavi nella stessa area della macchina virtuale da crittografare.

Quando si applica Crittografia dischi di Azure, è possibile soddisfare le esigenze aziendali seguenti:

- Le VM IaaS inattive sono protette con la tecnologia di crittografia standard, per soddisfare i requisiti di sicurezza e conformità dell'organizzazione.
- Le VM IaaS vengono avviate con chiavi e criteri controllati dai clienti ed è possibile controllarne l'utilizzo nell'insieme di credenziali delle chiavi.

## <a name="restrict-direct-internet-connectivity"></a>Limita connettività Internet diretta
Monitorare e limitare la connettività Internet diretta della macchina virtuale. Gli utenti malintenzionati analizzano costantemente gli intervalli IP del cloud pubblico per le porte di gestione aperte e tentano gli attacchi "semplici", come le password comuni e le vulnerabilità note senza patch. La tabella seguente elenca le procedure consigliate per la protezione da questi attacchi:

**Procedura consigliata**: impedire l'esposizione accidentale al routing e alla sicurezza di rete.   
**Dettagli**: usare il controllo degli accessi in base al ruolo per assicurarsi che solo il gruppo di rete centrale disponga delle autorizzazioni per la rete

**Procedura consigliata**: identificare e correggere le macchine virtuali esposte che consentono l'accesso da un indirizzo IP di origine "any".   
**Dettagli**: usare il Centro sicurezza di Azure. Il Centro sicurezza consiglia di limitare l'accesso tramite endpoint con connessione Internet se uno dei gruppi di sicurezza di rete ha una o più regole in ingresso che consentono l'accesso da un indirizzo IP di origine "any". Il Centro sicurezza consiglierà di modificare queste regole in ingresso per [limitare l'accesso](../../security-center/security-center-network-recommendations.md) agli indirizzi IP di origine che necessitano effettivamente dell'accesso.

**Procedura consigliata**: limitare le porte di gestione (RDP, SSH).   
**Dettagli**: [l'accesso alla macchina virtuale JIT (just-in-Time)](../../security-center/security-center-just-in-time.md) può essere usato per bloccare il traffico in ingresso verso le macchine virtuali di Azure, riducendo l'esposizione agli attacchi offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. Quando JIT è abilitato, il Centro sicurezza blocca il traffico in ingresso nelle macchine virtuali di Azure creando una regola del gruppo di sicurezza di rete. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione JIT.

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
