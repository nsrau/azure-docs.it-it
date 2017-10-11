---
title: Procedure consigliate per la sicurezza dei carichi di lavoro IaaS in Azure | Microsoft Docs
description: " La migrazione dei carichi di lavoro ad Azure IaaS offre l'opportunità di rivalutare i progetti "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: f93211d289553b7a8afbe8c17fa4847f3d4585a8
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Procedure consigliate per la sicurezza dei carichi di lavoro IaaS in Azure

Alcune delle considerazioni necessarie per lo spostamento di carichi di lavoro nell'infrastruttura distribuita come servizio (IaaS) di Azure sono simili a quelle relative alla protezione degli ambienti virtuali. Nel passaggio all'infrastruttura IaaS di Azure è possibile applicare le competenze acquisite nella protezione degli ambienti virtuali e usare un nuovo set di opzioni per la protezione degli asset.

Prima di tutto, occorre precisare che le risorse locali non verranno trasferite con una corrispondenza uno-a-uno in Azure. Le nuove sfide e le nuove opzioni offrono l'opportunità di rivalutare i progetti, gli strumenti e i processi esistenti.

Le responsabilità di sicurezza dell'utente dipendono dal tipo di servizio cloud. Il grafico seguente mostra un riepilogo delle responsabilità di Microsoft e dell'utente:


![Aree di responsabilità](./media/azure-security-iaas/sec-cloudstack-new.png)


Questo articolo illustra alcune delle opzioni disponibili in Azure che permettono di rispondere ai requisiti di sicurezza dell'organizzazione. I requisiti di sicurezza possono variare in base al tipo di carico di lavoro. Nessuna di queste procedure consigliate è di per sé sufficiente a proteggere i sistemi. Nel campo della sicurezza, è più che mai necessario scegliere le opzioni appropriate e fare in modo che le varie soluzioni si completino a vicenda.

## <a name="use-privileged-access-workstations"></a>Usare workstation con accesso con privilegi

Le organizzazioni sono spesso vittima di attacchi informatici perché gli amministratori eseguono azioni mentre usano account con diritti elevati. Questo avviene di solito in modo non intenzionale, ma perché la configurazione e i processi esistenti lo consentono. La maggior parte degli utenti conosce a livello concettuale i rischi legati a tali azioni, ma sceglie comunque di eseguirle.

Controllare la posta elettronica e navigare in Internet possono sembrare operazioni innocue, Ma potrebbe esporre gli account con privilegi elevati alla compromissione da parte di malintenzionati. Attività di esplorazione, messaggi di posta elettronica creati appositamente o altre tecniche possono essere utilizzate per ottenere l'accesso all'azienda. Per eseguire tutte le attività di amministrazione di Azure è consigliabile usare workstation di gestione sicure (SAW). Le workstation di gestione sicure rappresentano un modo per ridurre l'esposizione a danni accidentali.

Per le attività sensibili, le workstation con accesso con privilegi offrono un sistema operativo dedicato, protetto dagli attacchi provenienti da Internet e dai vettori di minacce. Separando queste attività e account sensibili dalle workstation e i dispositivi di uso giornaliero si ottiene una protezione avanzata. Questa separazione consente di limitare l'impatto di attacchi di phishing, le vulnerabilità di applicazioni e sistema operativo, vari attacchi di rappresentazione e attacchi con furto di credenziali. (registrazione delle digitazioni e Pass-the-Hash e Pass-the-Ticket)

L'uso di workstation dotate di accesso con privilegi rappresenta un'estensione della procedura consigliata e ampiamente diffusa di adottare un account amministrativo. L'account amministrativo è separato da un account utente standard. Una workstation dotata di accesso con privilegi offre un ambiente affidabile per gli account sensibili.

Per altre informazioni e istruzioni sull'implementazione, vedere [Workstation con accesso con privilegi](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Usare Multi-Factor Authentication

In passato il perimetro della rete era usato per controllare l'accesso ai dati aziendali. In un mondo dominato dai dispositivi mobili e basato sul cloud l'identità deve essere il piano di controllo per l'accesso a servizi IaaS da qualsiasi dispositivo. L'identità permette anche di ottenere visibilità e informazioni dettagliate sulla posizione e la modalità d'uso dei dati. Proteggere l'identità digitale degli utenti di Azure è la cosa fondamentale per difendere le sottoscrizioni dal furto delle identità e da altri crimini informatici.

Una delle cose più utili da fare per proteggere un account è abilitare l'autenticazione a due fattori. Questo un metodo di autenticazione che non si limita all'uso di una password, ma permette di ridurre il rischio di accesso da parte di un utente che si sia impossessato della password di qualcun altro.

[Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) permette di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso agli utenti una procedura di accesso semplice. Offre un'autenticazione avanzata con una gamma di semplici opzioni di verifica, ad esempio una telefonata, un SMS o una notifica dell'app per dispositivi mobili. Gli utenti possono scegliere il metodo preferito.

Il mezzo più semplice per usare Multi-Factor Authentication è l'app per dispositivi mobili Microsoft Authenticator, disponibile per i dispositivi mobili che eseguono Windows, iOS e Android. Con la versione più recente di Windows 10 e l'integrazione dell'istanza locale di Active Directory con Azure Active Directory, è possibile usare [Windows Hello for Business](../active-directory/active-directory-azureadjoin-passport-deployment.md) per semplificare l'accesso Single Sign-On alle risorse di Azure. In tal caso il dispositivo Windows 10 viene usato come secondo fattore di autenticazione.

Per gli account che gestiscono la sottoscrizione di Azure e quelli che possono accedere a macchine virtuali, Multi-Factor Authentication offre un livello di sicurezza molto maggiore rispetto all'uso della sola password. Altre forme di autenticazione a due fattori possono essere altrettanto efficaci, ma più complesse da distribuire se non sono già nell'ambiente di produzione.

Lo screenshot seguente mostra alcune delle opzioni disponibili per Azure Multi-Factor Authentication:

![Opzioni di Multi-Factor Authentication](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Limitare e vincolare l'accesso amministrativo

Proteggere gli account che gestiscono la sottoscrizione di Azure è estremamente importante. La compromissione di uno di questi account vanifica qualsiasi azione intrapresa per garantire la riservatezza e l'integrità dei dati. Come dimostra il caso di [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), gli attacchi sferrati dall'interno rappresentano una minaccia notevole per la sicurezza di qualunque organizzazione.

Per valutare i singoli utenti a cui concedere diritti amministrativi, seguire criteri simili a questi:

- Eseguono attività che richiedono privilegi amministrativi?
- Con quale frequenza vengono eseguite queste attività?
- C'è un motivo specifico per cui le attività non possono essere eseguite da un altro amministratore?

Perché non sono accettabili gli approcci alternativi documentati per la concessione dei privilegi?

Il ricorso all'amministrazione JIT evita di avere account con diritti elevati nei periodi in cui tali diritti non sono necessari. Gli account hanno diritti elevati per un periodo di tempo limitato, per permettere agli amministratori di svolgere il proprio lavoro. Tali diritti vengono poi rimossi alla fine di un turno o al termine di un'attività.

È possibile usare [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) per gestire, monitorare e controllare l'accesso all'interno dell'organizzazione. Questa tecnologia permette di tenere sotto controllo le azioni intraprese dai singoli utenti nell'organizzazione e introduce l'amministrazione JIT in Azure AD attraverso il concetto di amministratori idonei. Gli amministratori idonei sono persone che dispongono di account ai quali possono essere concessi diritti di amministratore. Gli utenti di questo tipo possono essere sottoposti a un processo di attivazione e vedersi concedere diritti amministrativi per un periodo di tempo limitato.


## <a name="use-devtest-labs"></a>Usare DevTest Labs

L'uso di Azure negli ambienti lab e di sviluppo offre alle organizzazioni una maggiore agilità a livello di sviluppo e testing, grazie all'eliminazione dei ritardi dovuti all'approvvigionamento dell'hardware. Sfortunatamente, la mancanza di esperienza o il desiderio di accelerarne l'adozione può portare un amministratore a un'eccessiva permissività nell'assegnazione dei diritti. Tale rischio può esporre involontariamente l'organizzazione ad attacchi interni, perché alcuni utenti potrebbero vedersi concedere un accesso molto più avanzato del necessario.

Il servizio [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) fa uso del [controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md), che permette di organizzare i compiti all'interno del team in ruoli, che concedono solo il livello di accesso necessario agli utenti per svolgere il proprio lavoro. Il controllo degli accessi in base al ruolo prevede i ruoli predefiniti di proprietario, utente lab e collaboratore, che possono anche essere usati per assegnare diritti a partner esterni e semplificare notevolmente la collaborazione.

L'uso del controllo degli accessi in base al ruolo in DevTest Labs permette di creare [ruoli personalizzati](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md) aggiuntivi. DevTest Labs non si limita a semplificare la gestione delle autorizzazioni, ma semplifica anche il processo di provisioning degli ambienti e permette di gestire altre problematiche tipiche dei team che operano in ambienti di sviluppo e test. Richiede un po' di preparazione, ma con il tempo permette di semplificare il lavoro del team.

Le funzionalità di Azure DevTest Labs includono:

- Controllo amministrativo sulle opzioni disponibili per gli utenti Questo consente all'amministratore una gestione centralizzata di alcuni aspetti relativi alle macchine virtuali, come le dimensioni consentite, il numero massimo e gli orari di avvio e arresto.
- Automazione della creazione dell'ambiente lab.
- Verifica dei costi.
- Distribuzione semplificata di macchine virtuali per attività di collaborazione temporanee.
- Funzionalità self-service che consente agli utenti di effettuare il provisioning dei lab tramite modelli.
- Gestione e limitazione dell'utilizzo.

![Uso di DevTest Labs per creare un lab](./media/azure-security-iaas/devtestlabs.png)

L'uso di DevTest Labs è disponibile senza costi aggiuntivi. La creazione di lab, criteri, modelli ed elementi è gratuita. Si pagano solo le risorse di Azure usate nei lab, come le macchine virtuali, gli account di archiviazione e le reti virtuali.



## <a name="control-and-limit-endpoint-access"></a>Controllare e limitare l'accesso agli endpoint

Per poter eseguire l'hosting di lab o sistemi di produzione in Azure, i sistemi devono essere accessibili da Internet. Per impostazione predefinita, nelle nuove macchine virtuali Windows la porta RDP è accessibile da Internet, mentre nelle macchine virtuali Linux la porta SSH è aperta. Per ridurre al minimo il rischio di accesso non autorizzato, è necessario provvedere a limitare gli endpoint esposti.

Le tecnologie di Azure consentono di limitare l'accesso agli endpoint amministrativi. Ad esempio, è possibile usare i [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Quando si usa Azure Resource Manager per la distribuzione, i gruppi di sicurezza di rete limitano l'accesso da tutte le reti ai soli endpoint di gestione, RDP o SSH. Gli NSG sono un po' l'equivalente degli ACL dei router. È possibile usarli per controllare rigorosamente la comunicazione di rete tra i vari segmenti delle reti di Azure, con risultati analoghi a quelli della creazione di reti perimetrali o altre reti isolate. I gruppi di sicurezza di rete non controllano il traffico, ma contribuiscono alla segmentazione della rete.


In Azure è possibile configurare una [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) dalla rete locale. Una VPN da sito a sito consente di estendere la rete locale al cloud, offrendo una ulteriore opportunità di usare i gruppi di sicurezza di rete. È infatti possibile modificare il gruppo di sicurezza di rete per non consentire l'accesso se non dalla rete locale. È quindi possibile fare in modo che l'amministrazione avvenga eseguendo prima la connessione alla rete di Azure tramite VPN.

L'opzione VPN da sito a sito può risultare più interessante quando si ospitano sistemi di produzione strettamente integrati con le risorse locali in Azure.

In alternativa, è possibile usare l'opzione [da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) per la gestione di sistemi che non richiedono l'accesso alle risorse locali e che possono essere isolati nella relativa rete virtuale di Azure. Dalla workstation amministrativa gli amministratori possono connettersi tramite VPN all'ambiente ospitato in Azure.

>[!NOTE]
>Entrambe le opzioni VPN permettono di riconfigurare gli elenchi ACL nei gruppi di sicurezza di rete in modo da non consentire l'accesso da Internet agli endpoint di gestione.

Un'altra opzione che vale la pena considerare è la distribuzione di un [Gateway Desktop remoto](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md), utile per connettersi ai server desktop remoto in modo sicuro tramite HTTPS, applicando controlli più dettagliati alle connessioni.

Le funzionalità disponibili includono:

- Opzioni di amministrazione per limitare le connessioni alle richieste provenienti da sistemi specifici
- Autenticazione tramite smart card o Azure Multi-Factor Authentication.
- Possibilità di stabilire a quali sistemi sia possibile connettersi tramite il gateway
- Possibilità di controllare il reindirizzamento a dischi e dispositivi

## <a name="use-a-key-management-solution"></a>Usare una soluzione di gestione delle chiavi

La gestione sicura delle chiavi è fondamentale per proteggere i dati nel cloud. Con l'[insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md), è possibile archiviare in tutta sicurezza le chiavi di crittografia e altri dati segreti, come le password, all'interno di moduli di protezione hardware. Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware.

Microsoft elabora le chiavi in moduli di protezione hardware FIPS 140-2 di livello 2 convalidati (hardware e firmware). È possibile monitorare e controllare l'uso delle chiavi con la registrazione di Azure, inviando i log ad Azure o alla propria soluzione SIEM (Security Information and Event Management) per ulteriori analisi e rilevamento delle minacce.

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Anche se rappresenta un vantaggio per sviluppatori e amministratori della sicurezza, Key Vault può essere implementato e gestito da un amministratore responsabile della gestione dei servizi di Azure per un'organizzazione.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Crittografare i dischi virtuali e l'archiviazione su disco

[Crittografia dischi di Azure](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) protegge dai rischi di furto dei dati o esposizione da accesso non autorizzato attraverso lo spostamento di un disco, che potrebbe essere collegato a un altro sistema per aggirare altri controlli di sicurezza. La crittografia del disco usa [BitLocker](https://technet.microsoft.com/library/hh831713) in Windows e DM-Crypt in Linux per crittografare il sistema operativo e le unità dati. Crittografia dischi di Azure si integra con Key Vault per consentire il controllo e la gestione delle chiavi di crittografia. È disponibile per le macchine virtuali Standard e le macchine virtuali con Archiviazione Premium.

Per altre informazioni, vedere [Crittografia dischi di Azure per macchine virtuali IaaS Windows e Linux](azure-security-disk-encryption.md).

La [crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md) permette di protegge i dati inattivi. È abilitata a livello dell'account di Archiviazione e permette di crittografare i dati man mano che vengono scritti nel data center, per poi decrittografarli automaticamente quando vi si accede. Supporta gli scenari seguenti:

- Crittografia di BLOB in blocchi, BLOB di aggiunta e BLOB di pagine.
- Crittografia di dischi rigidi virtuali archiviati e modelli trasferiti in Azure da posizioni locali.
- Crittografia del sistema operativo sottostante e dei dischi dati per macchine virtuali IaaS create usando i dischi rigidi virtuali.

Prima di procedere con la crittografia del servizio di archiviazione di Azure, occorre tenere presenti due limitazioni:

- non è disponibile per gli account di archiviazione classici;
- permette di crittografare solo i dati scritti dopo aver abilitato la crittografia.

## <a name="use-a-centralized-security-management-system"></a>Usare un sistema di gestione della sicurezza centralizzato

I server devono essere monitorati per tenere sotto controllo l'applicazione di patch, la configurazione, gli eventi e le attività potenzialmente problematiche per la sicurezza. A tale scopo, è possibile usare il [Centro sicurezza](https://azure.microsoft.com/services/security-center/) e [Operations Management Suite Security & Compliance](https://azure.microsoft.com/services/security-center/). Entrambe queste opzioni non si limitano alla configurazione del sistema operativo, ma consentono anche di monitorare la configurazione dell'infrastruttura sottostante, come la configurazione di rete e l'uso di appliance virtuali.

## <a name="manage-operating-systems"></a>Gestire i sistemi operativi

Come per qualunque altro server o workstation dell'ambiente usato, in una distribuzione IaaS l'utente rimane responsabile della gestione dei sistemi distribuiti. L'applicazione di patch, la protezione avanzata, l'assegnazione di diritti e qualsiasi altra attività correlata alla manutenzione del sistema rimangono responsabilità dell'utente. Per i sistemi strettamente integrati con le risorse locali, può essere utile usare gli stessi strumenti e procedure adottati a livello locale per gli antivirus, gli antimalware, l'applicazione di patch e i backup.

### <a name="harden-systems"></a>Implementare la protezione avanzata dei sistemi
La protezione avanzata deve essere implementata in tutte le macchine virtuali nell'infrastruttura IaaS di Azure, in modo da esporre solo gli endpoint di servizio necessari per le applicazioni installate. Per le macchine virtuali Windows, seguire le raccomandazioni pubblicate da Microsoft per la soluzione [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx).

Security Compliance Manager è uno strumento gratuito che consente di configurare e gestire rapidamente desktop, data center tradizionali e cloud pubblici e privati tramite criteri di gruppo e System Center Configuration Manager.

Security Compliance Manager offre criteri pronti per la distribuzione e pacchetti di configurazione di Gestione configurazione desiderata già testati. Queste baseline si basano sulle [linee guida Microsoft sulla sicurezza](https://technet.microsoft.com/en-us/library/cc184906.aspx) e le procedure consigliate del settore e permettono di gestire eventuali deviazioni della configurazione, rispondere ai requisiti di conformità e ridurre le minacce per la sicurezza.

È possibile usare Security Compliance Manager per importare la configurazione corrente dei computer con due metodi diversi. Il primo consiste nell'importare criteri di gruppo basati su Active Directory. Il secondo consiste nell'importare la configurazione di un computer di riferimento "golden master" usando lo [strumento LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) per eseguire il backup dei criteri di gruppo locali. È quindi possibile importare i criteri di gruppo locali in Security Compliance Manager.

Confrontare gli standard applicati alle procedure consigliate del settore, personalizzarli e creare nuovi criteri e pacchetti di configurazione di Gestione configurazione desiderata. Le linee di base sono state pubblicate per tutti i sistemi operativi supportati, tra cui l'Aggiornamento dell'anniversario di Windows 10 e Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Installare e gestire l'antimalware

Per gli ambienti ospitati separatamente dall'ambiente di produzione, è possibile usare un'estensione antimalware per la protezione delle macchine virtuali e dei servizi cloud che si integra con il [Centro sicurezza di Azure](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) include caratteristiche come la protezione in tempo reale, l'analisi pianificata, il monitoraggio e aggiornamento malware, l'aggiornamento delle firme e del motore, il reporting di campioni, la raccolta degli eventi di esclusione e il [supporto della PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Antimalware Azure](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Installare gli aggiornamenti della sicurezza più recenti
I lab e i sistemi esterni sono tra i primi carichi di lavoro che i clienti spostano in Azure. Se le macchine virtuali di Azure ospitano applicazioni o servizi che devono essere accessibili da Internet, è importante fare attenzione all'applicazione di patch. Non limitarsi all'applicazione di patch relative al sistema operativo. Anche le vulnerabilità senza patch delle applicazioni di terze parti possono causare problemi facilmente evitabili con una buona gestione delle patch.

### <a name="deploy-and-test-a-backup-solution"></a>Distribuire e testare una soluzione di backup

Esattamente come gli aggiornamenti della sicurezza, anche il backup deve essere gestito come qualsiasi altra operazione, anche per quanto riguarda i sistemi dell'ambiente di produzione che si estendono al cloud. I sistemi di sviluppo e test devono seguire strategie di backup con funzionalità di ripristino simili a quelle che gli utenti degli ambienti locali sono ormai abituati a usare.

Se possibile, i carichi di lavoro di produzione spostati in Azure devono integrarsi con le soluzioni di backup esistenti. In alternativa, per le esigenze di backup è possibile usare [Backup di Azure](../backup/backup-azure-arm-vms.md).


## <a name="monitor"></a>Monitoraggio

Il [Centro sicurezza](../security-center/security-center-intro.md) analizza in modo continuo lo stato di protezione delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Un elenco di suggerimenti illustra in dettaglio il processo di configurazione dei controlli necessari.

Tra gli esempi sono inclusi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso.
- Configurazione dei gruppi di sicurezza di rete e delle regole per controllare il traffico verso le macchine virtuali.
- Provisioning di Web application firewall per la difesa da attacchi diretti alle applicazioni Web.
- Distribuzione di aggiornamenti di sistema mancanti.
- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate.

L'immagine seguente mostra alcune delle opzioni che è possibile abilitare nel Centro sicurezza.

![Criteri del Centro sicurezza di Azure](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) è una soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Dato che viene implementato come servizio basato sul cloud, Operations Management Suite può essere distribuito rapidamente e con un investimento minimo in termini di risorse dell'infrastruttura.

Le nuove funzionalità sono disponibili automaticamente, evitando così i costi di manutenzione e aggiornamento continui. Operations Management Suite si integra anche con System Center Operations Manager. Include diversi componenti che migliorano la gestione dei carichi di lavoro di Azure, tra cui il modulo [Sicurezza e conformità](../operations-management-suite/oms-security-getting-started.md).

È possibile usare le funzionalità di sicurezza e conformità in Operations Management Suite per visualizzare informazioni sulle risorse, organizzate in quattro categorie principali:

- **Domini di sicurezza**. Permette di esplorare ulteriormente i record di sicurezza nel tempo. Permette di accedere alla valutazione antimalware, alla valutazione degli aggiornamenti, a informazioni sulla sicurezza di rete, a informazioni su identità e accessi e ai computer che generano eventi di sicurezza. Consente un accesso rapido al dashboard del Centro sicurezza di Azure.
- **Errori rilevanti**. Permette di identificare rapidamente il numero di problemi attivi e la relativa gravità.
- **Rilevamenti (anteprima)**. Permette di identificare i modelli di attacco visualizzando gli avvisi di sicurezza relativi alle risorse non appena vengono generati.
- **Intelligence per le minacce**. Permette di identificare i modelli di attacco visualizzando il numero totale di server con traffico IP dannoso in uscita, il tipo di minaccia e una mappa che mostra la provenienza di tali indirizzi IP.
- **Query comuni sulla sicurezza**. Permette di visualizzare un elenco delle query più comuni sulla sicurezza che è possibile usare per monitorare l'ambiente. Facendo clic su una query, viene visualizzato il pannello **Ricerca** con i risultati della query.

Lo screenshot seguente mostra un esempio delle informazioni visualizzate in Operations Management Suite.

![Baseline della sicurezza di Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Passaggi successivi

* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md)

