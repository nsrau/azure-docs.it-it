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
ms.date: 11/17/2016
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: f74352c9f9ef358441c4322dbe7f894db16c2619
ms.openlocfilehash: f8af03050daf1864b5faf8b897dc4c71e2dd3194




---

# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Procedure consigliate per la sicurezza dei carichi di lavoro IaaS in Azure

Quando si valuta di trasferire i carichi di lavoro ad Azure IaaS, si presentano alcune considerazioni già note. È probabile che si abbia già avuto a che fare con la protezione degli ambienti virtuali. La migrazione ad Azure IaaS consente di applicare le competenze acquisite in merito alla protezione degli ambienti virtuali, introducendo inoltre un nuovo set di opzioni per la protezione degli asset.

Prima di iniziare, occorre precisare che le risorse locali non diventeranno uno-a-uno in Azure. Le nuove sfide e le nuove opzioni offrono l'opportunità di rivalutare i progetti, gli strumenti e i processi esistenti.




![Aree di responsabilità](./media/azure-security-iaas/sec-cloudstack-new.png)

>[!NOTE]
> Le responsabilità di sicurezza dell'utente dipendono dal tipo di servizio cloud. Nel grafico qui sopra vengono riepilogate le responsabilità di Microsoft e del cliente.

## <a name="best-practices"></a>Procedure consigliate
Verranno presentate alcune delle opzioni disponibili in Azure utili per soddisfare i requisiti di sicurezza dell'organizzazione. Durante questa operazione è necessario tenere presenti i diversi tipi di carichi di lavoro e i vari requisiti di protezione di ciascuno. Nessuna di queste procedure consigliate è di per sé sufficiente a proteggere i sistemi. Nel campo della sicurezza, è più che mai necessario scegliere le opzioni appropriate e osservare come le varie soluzioni vadano a completarsi a vicenda, andando a rimediare le lacune delle altre.

### <a name="use-privileged-access-workstations-paw"></a>Usare le workstation dotate di accesso con privilegi

Spesso le organizzazioni subiscono attacchi informatici a causa di amministratori che eseguono azioni mentre usano account con diritti elevati. Di solito non lo fanno intenzionalmente, ma perché la configurazione e i processi esistenti lo consentono. La maggior parte degli utenti conosce il rischio dal punto di vista concettuale, ma sceglie comunque di eseguire operazioni notoriamente rischiose.

Controllare l'e-mail ed esplorare Internet sembrano cose innocue, ma possono esporre gli account con privilegi elevati ai rischi di compromissione ad opera di utenti malintenzionati, che potrebbero usare attività di esplorazione, messaggi e-mail creati ad arte o altre tecniche per ottenere l'accesso all'azienda. Per eseguire tutte le attività di amministrazione di Azure riducendo l'eventualità di danni accidentali, si consiglia caldamente di usare le workstation con gestione sicura.

Per le attività sensibili, le workstation dotate di accesso con privilegi (PAW, Privileged Access Workstation) forniscono un sistema operativo dedicato, protetto dagli attacchi provenienti da Internet e dai vettori di minacce. Separare le attività e gli account sensibili da workstation e dispositivi usati quotidianamente assicura una protezione estrema contro gli attacchi di phishing, le vulnerabilità di applicazioni e sistemi operativi, svariati attacchi di rappresentazione e tecniche di furto delle credenziali, come registrazione delle pressioni di tasti, Pass the Hash e Pass The Ticket.

L'approccio PAW è un'estensione dell'ormai consolidata buona prassi di usare per il personale amministrativo due account diversi per i ruoli di utente e amministratore, assegnando un account amministrativo personale diverso dal normale account utente. Tenendo separati questi account sensibili, l'approccio PAW garantisce l'affidabilità della workstation.

Per altre informazioni sulle workstation dotate di accesso con privilegi e per linee guida sull'implementazione dell'approccio PAQ, fare clic sul collegamento di seguito:

- [Workstation dotate di accesso con privilegi](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)

### <a name="use-multifactor-authentication"></a>Usare l'autenticazione a più fattori

In passato il perimetro della rete era usato per controllare l'accesso ai dati aziendali. Ora, in un mondo incentrato sul cloud e sulle tecnologie mobili, il controllo risiede nell'identità, che viene usata per controllare l'accesso ai servizi IaaS da qualsiasi dispositivo e per sapere come e dove vengono usati i dati. Proteggere l'identità digitale degli utenti di Azure è la cosa fondamentale per difendere le sottoscrizioni dal furto delle identità e da altri crimini informatici.

Una delle cose più utili da fare per proteggere un account è abilitare l'autenticazione a due fattori, un metodo di autenticazione che non si limita all'uso di una password, ma che aggiunge un secondo fattore  per ridurre il rischio di accesso da parte di un utente che riesca a impossessarsi della password di qualcun altro.

Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice. Offre funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui telefonata, SMS o notifica tramite app per dispositivi mobili, in modo che ogni utente possa scegliere il metodo che preferisce.

Il mezzo più semplice per usare il servizio [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) è l'app per dispositivi mobili Microsoft Authenticator, disponibile per i dispositivi mobili che eseguono Windows, IOS e Android. Con Windows 10 e l'integrazione del servizio Active Directory locale in Azure AD, è possibile usare [Windows Hello for Business](../active-directory/active-directory-azureadjoin-passport-deployment.md) per un agevole accesso Single Sign-On alle risorse di Azure. In questo caso il secondo fattore di autenticazione sarà il dispositivo Windows 10.


Nel caso di Azure il modo più semplice per abilitare l'autenticazione a due fattori è usare il servizio Multi-Factor Authentication (MFA). Il servizio [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) dispone di un'applicazione che può essere integrata nella directory locale e usata nei dispositivi mobili, consultandola tramite chiamate telefoniche, SMS o un codice generato nell'app.

L'autenticazione a più fattori deve essere usata per gli account che gestiscono la sottoscrizione di Azure e, quando possibile, per quelli che possono accedere alle macchine virtuali. L'uso dell'autenticazione a più fattori per questi account offre un livello di sicurezza nettamente superiore rispetto alla sola password. Le altre forme di autenticazione a due fattori possono essere altrettanto efficaci, ma più complesse da distribuire se non sono già nell'ambiente di produzione.

La schermata di seguito mostra alcune delle opzioni disponibili per il servizio Azure Multi-Factor Authentication.

![Opzioni di autenticazione a più fattori](./media/azure-security-iaas/mfa-options.png)

### <a name="limit-and-constrain-administrative-access"></a>Limitare e vincoli dell'accesso amministrativo

Proteggere gli account che gestiscono la sottoscrizione di Azure è estremamente importante. La compromissione di uno qualunque di questi account vanifica tutte le altre operazioni eseguite per garantire la riservatezza e l'integrità dei dati. Come dimostra il caso di [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), colpevole di aver diffuso informazioni riservate del governo USA, gli attacchi sferrati dall'interno rappresentano una minaccia notevole per la sicurezza di qualunque organizzazione.

Le persone con diritti amministrativi devono aver superato una valutazione basata su criteri del tipo:

- Eseguono attività che richiedono privilegi amministrativi?
- Con quale frequenza vengono eseguite queste attività?
- Qual è l'esatto motivo che impedisce di delegare le attività a un altro amministratore?
- Perché non sono accettabili gli approcci alternativi documentati per la concessione dei privilegi?

Il ricorso all'amministrazione JIT evita di avere account con diritti elevati nei periodi in cui tali diritti non sono necessari. Gli account mantengono diritti elevati per la durata del lavoro degli amministratori; dopodiché, a fine turno o al completamento di un'attività, i diritti vengono rimossi.

La tecnologia [PIM (Privileged Identity Management)](../active-directory/active-directory-privileged-identity-management-configure.md) consente di gestire, monitorare e controllare l'accesso a livello dell'organizzazione, osservando le azioni eseguite dalle persone al suo interno e introducendo l'amministrazione JIT in Azure AD, insieme al concetto di amministratore idoneo. Gli amministratori idonei sono persone che dispongono di account ai quali possono essere concessi diritti di amministratore. Gli utenti di questo tipo possono essere sottoposti a un processo di attivazione e vedersi concedere diritti amministrativi per un periodo di tempo limitato.


### <a name="use-devtest-labs"></a>Usare DevTest Labs

Usando Azure negli ambienti lab e di sviluppo, le organizzazioni guadagnano agilità a livello di sviluppo e testing eliminando i ritardi legati all'approvvigionamento dell'hardware. Sfortunatamente, esiste il rischio che un amministratore poco esperto di Azure o che voglia accelerarne l'adozione diventi troppo permissivo nell'assegnare i diritti, esponendo involontariamente l'organizzazione ad attacchi interni, perché alcuni utenti potrebbero vedersi concedere un accesso molto più avanzato del necessario.

Ora Azure include il servizio [DevTest Labs](../devtest-lab/devtest-lab-overview.md), che usa il [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md) (RBAC). Il meccanismo RBAC consente di organizzare i compiti dei vari membri del team in ruoli che concedono solo il livello di accesso necessario per portare a termine i compiti assegnati. I ruoli disponibili sono quelli predefiniti di proprietario, utente lab e collaboratore. Questi ruoli possono anche essere usati per assegnare diritti a partner esterni e semplificare notevolmente la collaborazione.

Usando la tecnologia RBAC, DevTest Labs permette di creare ulteriori [ruoli personalizzati](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs non solo semplifica la gestione delle autorizzazioni, ma è anche progettato per semplificare il processo di provisioning negli ambienti e per gestire altre problematiche tipiche dei team che lavorano in ambienti di sviluppo e test. Richiede un po' di preparazione, ma con il tempo servirà a semplificare le cose per il team.

Di seguito sono elencate alcune caratteristiche chiave di Azure DevTest Labs.

- Controllo amministrativo sulle opzioni disponibili per gli utenti Possibilità per l'amministratore di gestire centralmente gli aspetti relativi alle macchine virtuali, come le dimensioni consentite, il numero massimo e gli orari di accensione e arresto
- Automazione della creazione dell'ambiente lab
- Verifica dei costi
- Distribuzione semplificata delle macchine virtuali per attività di collaborazione temporanee
- Funzionalità self-service che consentono agli utenti di eseguire il provisioning nei lab per mezzo di modelli
- Gestione e limitazione dell'utilizzo

![Creare un'istanza di DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

DevTest Labs è disponibile senza costi aggiuntivi. La creazione di lab, la configurazione dei criteri, i modelli e gli elementi sono tutti gratuiti. Si pagano solo le risorse di Azure usate nei lab, come le macchine virtuali, gli account di archiviazione e le reti virtuali.



### <a name="control-and-limit-endpoint-access"></a>Controllare e limitare l'accesso agli endpoint

Per poter eseguire l'hosting di lab o sistemi di produzione in Azure, i sistemi devono essere accessibili da Internet. Per impostazione predefinita, le macchine virtuali Windows hanno la porta RDP accessibile da Internet, mentre le macchine virtuali Linux hanno la porta SSH aperta. Ciò significa che, per ridurre al minimo il rischio di accesso non autorizzato, è necessario provvedere alle dovute operazioni per limitare gli endpoint esposti.

In Azure esistono tecnologie che consentono di limitare l'accesso agli endpoint amministrativi. Ad esempio, in Azure è possibile usare i gruppi di sicurezza di rete ([NSG](../virtual-network/virtual-networks-nsg.md)). Quando si usa Resource Manager per la distribuzione, gli NSG vengono usati per limitare l'accesso da tutte le reti ai soli endpoint di gestione (RDP o SSH).  Gli NSG sono un po' l'equivalente degli ACL dei router. È possibile usarli per controllare rigorosamente la comunicazione di rete tra i vari segmenti delle reti di Azure, con risultati analoghi a quelli della creazione di reti perimetrali o altre reti isolate. Gli NSG non controllano il traffico, ma contribuiscono alla segmentazione della rete.


In Azure è possibile configurare una [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) dalla rete locale, estendendo efficacemente la rete locale al cloud.  Qui si presenta un'ulteriore opportunità di usare gli NSG, essendo anche possibile modificarne uno in modo da impedire l'accesso da altre reti che non siano quella locale e quindi richiedere che l'amministrazione venga eseguita connettendosi prima alla rete di Azure tramite VPN.

L'opzione della VPN da sito a sito può essere più interessante quando si esegue l'hosting di sistemi di produzione strettamente integrati con le risorse locali in Azure.

Al contrario, l'opzione [da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) può essere usata quando si desidera gestire sistemi che non richiedono l'accesso alle risorse locali; questi sistemi possono essere isolati nella propria rete virtuale di Azure e gli amministratori possono accedere tramite VPN all'ambiente di Azure in hosting dalla propria workstation amministrativa.

>[!NOTE]
Entrambe le opzioni VPN permettono di riconfigurare gli elenchi ACL negli NSG in modo da non consentire di accedere da Internet agli endpoint di gestione.

Un'altra opzione che vale la pena considerare è la distribuzione di un [Gateway Desktop remoto](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md), utile per connettersi ai server desktop remoto in modo sicuro tramite HTTPS, applicando controlli più dettagliati alle connessioni eseguite.

Di seguito sono elencate alcune delle caratteristiche disponibili in questo caso.

- Opzioni di amministrazione per limitare le connessioni alle richieste provenienti da sistemi specifici
- Autenticazione tramite smart card o Azure Multi-Factor Authentication
- Possibilità di stabilire a quali sistemi sia possibile connettersi tramite il gateway
- Possibilità di controllare il reindirizzamento a dischi e dispositivi

### <a name="use-a-key-management-solution"></a>Usare una soluzione di gestione delle chiavi

La gestione sicura delle chiavi è fondamentale per proteggere i dati nel cloud. Con l'[insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md), è possibile archiviare in tutta sicurezza le chiavi di crittografia e altri dati segreti, come le password, all'interno di moduli di protezione hardware. Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware.

Se si sceglie di eseguire questa operazione, Microsoft elaborerà le chiavi in moduli di protezione hardware FIPS 140-2 livello 2 convalidati (hardware e firmware). L'uso delle chiavi può essere monitorato e controllato con le funzionalità di registrazione di Azure, inviando i log all'applicazione Azure o al sistema SIEM per un'ulteriore analisi e intercettazione delle minacce.

Chiunque abbia una sottoscrizione di Azure può creare e usare insiemi di credenziali delle chiavi. Pur essendo un vantaggio per sviluppatori e amministratori della sicurezza, l'insieme di credenziali delle chiavi può essere implementato e gestito dall'amministratore di un'organizzazione incaricato di gestire i servizi di Azure.


### <a name="encrypt-virtual-disks-and-disk-storage"></a>Crittografare i dischi virtuali e l'archivio su disco

La [Crittografia dischi di Azure](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) protegge dai rischi di furto dei dati o accesso non autorizzato alle informazioni durante lo spostamento di un disco, che potrebbe essere collegato a un altro sistema per aggirare altri controlli di sicurezza. La crittografia del disco usa [BitLocker](https://technet.microsoft.com/library/hh831713 ), in Windows, e DM-Crypt, in Linux, per crittografare il sistema operativo e le unità dati. Disponibile per le macchine virtuali standard e con Archiviazione Premium, la Crittografia dischi di Azure va a integrarsi con l'insieme di credenziali delle chiavi per controllare e gestire le chiavi di crittografia.

Per altre informazioni, leggere l'articolo su [Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux](azure-security-disk-encryption.md).

La [Crittografia del servizio di archiviazione di Azure](../storage/storage-service-encryption.md) protegge i dati inattivi. È abilitata a livello dell'account di archiviazione e permette di crittografare i dati nel momento stesso in cui vengono scritti nel data center, per poi decrittografarli automaticamente quando vi si accede. Supporta gli scenari seguenti:

- Crittografia di BLOB in blocchi, BLOB di aggiunta e BLOB di pagine.
- Crittografia di VHD archiviati e modelli trasferiti in Azure da posizioni locali.
- Crittografia del sistema operativo sottostante e dei dischi dati per VM IaaS create usando i VHD.

Prima di procedere con la crittografia dell'Archiviazione di Azure, è bene considerare due limiti rilevanti:

- non è disponibile per gli account di archiviazione classici;
- permette di crittografare solo i dati scritti dopo aver abilitato la crittografia.

### <a name="use-a-centralized-security-management-system"></a>Usare un sistema di gestione centralizzata della protezione

Sui server occorre monitorare l'applicazione delle patch, la configurazione, gli eventi e le attività potenzialmente problematiche per la sicurezza. Per farlo, è possibile usare il [Centro sicurezza](https://azure.microsoft.com/services/security-center/) e le funzionalità di [sicurezza e conformità in Operations Management Suite](https://azure.microsoft.com/services/security-center/). Entrambe queste opzioni non si limitano al sistema operativo, ma consentono di monitorare anche la configurazione dell'infrastruttura sottostante, come la configurazione della rete e l'uso delle appliance virtuali.

### <a name="operating-system-management-best-practices"></a>Procedure consigliate per la gestione del sistema operativo

Come per qualunque altro server o le altre workstation dell'ambiente in uso, in una distribuzione IaaS l'utente rimane responsabile della gestione dei sistemi distribuiti. Ciò significa che l'applicazione di patch, la protezione avanzata, l'assegnazione di diritti e qualsiasi altra attività correlata alla manutenzione del sistema rimane di responsabilità dell'utente.  Per i sistemi strettamente integrati con le risorse locali, può essere utile usare gli stessi strumenti e procedure in uso a livello locale per cose come gli antivirus, gli antimalware, l'applicazione di patch e i backup.

**Protezione avanzata**. Tutte le macchine virtuali in Azure IaaS devono usufruire della protezione avanzata, in modo tale da esporre solo gli endpoint di servizio necessari per le applicazioni installate. Per le macchine virtuali Windows, seguire i consigli pubblicati da Microsoft per la soluzione Security Compliance Manager.

[Security Compliance Manager (SCM)](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx). Ultimamente è stata rilasciata la versione 4.0 di questo strumento gratuito che consente di configurare velocemente e gestire desktop, data center tradizionali, cloud privati e pubblici tramite Criteri di gruppo e System Center Configuration Manager.

SCM fornisce criteri pronti da distribuire e pacchetti di configurazione DCM già testati. Prendendo spunto dalle indicazioni della [guida alla sicurezza Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx) e dalle procedure consigliate del settore, queste linee di base consentono di gestire le deviazioni dalla configurazione, soddisfare i requisiti di conformità e ridurre le minacce per la protezione.

Tramite SCM, è possibile importare la configurazione corrente dei computer con due metodi diversi: importando i Criteri di gruppo basati su Active Directory oppure importando la configurazione di un computer ottimale da tenere come riferimento, usando lo [strumento LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) per eseguire il backup dei Criteri di gruppo locali da importare in SCM.

È buona norma confrontare i propri standard rispetto alle procedure consigliate del settore, personalizzarli e creare nuovi criteri e pacchetti di configurazione DCM.
Le linee di base sono state pubblicate per tutti i sistemi operativi supportati, tra cui l'Aggiornamento dell'anniversario di Windows 10 e Windows Server 2016.


**Installare e gestire l'antimalware**

Per gli ambienti ospitati separatamente dall'ambiente di produzione, è disponibile un'estensione antimalware che può essere usata per proteggere le macchine virtuali e i servizi cloud e può essere integrata con il [Centro sicurezza di Azure](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) include caratteristiche come la protezione in tempo reale, l'analisi pianificata, il monitoraggio e aggiornamento malware, l'aggiornamento delle firme e del motore, il reporting di campioni, la raccolta degli eventi di esclusione e il [supporto della PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Antimalware Azure](./media/azure-security-iaas/azantimalware.png)

**Installare gli ultimi aggiornamenti per la sicurezza** Tra i primi carichi di lavoro che i clienti trasferiscono in Azure ci sono laboratori e sistemi rivolti all'esterno. Se le macchine virtuali di Azure eseguono l'hosting di applicazioni o servizi da rendere accessibili tramite Internet, è necessario tenere d'occhio l'applicazione delle patch, ricordando che non deve fermarsi al sistema operativo: anche le vulnerabilità senza patch delle applicazioni di terze parti possono causare problemi che con una buona gestione delle patch è facile evitare.

Per altre informazioni sulla gestione delle patch in Azure IaaS, esaminare [Procedure consigliate per gli aggiornamenti software nell'ambiente IaaS di Microsoft Azure](azure-security-best-practices-software-updates-iaas.md).

**Distribuire e testare una soluzione di backup**

Esattamente come gli aggiornamenti della sicurezza, anche il backup deve essere gestito come qualsiasi altra operazione, anche per quanto riguarda i sistemi dell'ambiente di produzione che si estendono al cloud. I sistemi di sviluppo e test devono seguire strategie di backup con funzionalità di ripristino simili a quelle che gli utenti degli ambienti locali sono ormai abituati a usare.

Per soddisfare le esigenze di backup, i carichi di lavoro di produzione trasferiti in Azure devono integrarsi con le soluzioni di backup esistenti, quando possibile, oppure è possibile usare il [Backup di Azure](../backup/backup-azure-arm-vms.md).


### <a name="monitor"></a>Monitorare

Il [Centro sicurezza](../security-center/security-center-intro.md) analizza in modo continuo lo stato di protezione delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Un elenco di suggerimenti illustra in dettaglio il processo di configurazione dei controlli necessari.

Tra gli esempi sono inclusi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso
- Configurazione dei gruppi di sicurezza di rete e delle regole per controllare il traffico verso le macchine virtuali
- Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
- Distribuzione degli aggiornamenti di sistema mancanti
- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

L'immagine seguente mostra alcune delle opzioni che è possibile abilitare nel Centro sicurezza.

![Criteri del Centro sicurezza di Azure](./media/azure-security-iaas/security-center-policies.png)

 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Poiché OMS viene implementato come servizio basato sul cloud, può essere distribuito rapidamente con un investimento minimo in termini di risorse dell'infrastruttura.

Le nuove funzionalità vengono rese disponibili automaticamente, evitando così i costi di manutenzione e di aggiornamento. Inoltre, può essere integrato con System Center Operations Manager.  OMS include diversi componenti che migliorano la gestione dei carichi di lavoro di Azure, tra cui il modulo [Security and Compliance](../operations-management-suite/oms-security-getting-started.md).

Le funzionalità di sicurezza e conformità di OMS consentono di visualizzare informazioni sulle risorse, organizzate in quattro categorie principali:

- Domini di sicurezza: in questa area è possibile esplorare in dettaglio il susseguirsi dei record di sicurezza, accedere alla valutazione degli aggiornamenti e della presenza di malware, controllare le informazioni su sicurezza della rete, identità e accesso, sapere quali computer presentano eventi di sicurezza e accedere velocemente al dashboard del Centro sicurezza di Azure.
- Errori rilevanti: questa opzione consente di identificare rapidamente il numero di problemi attivi e la relativa gravità.
- Rilevamenti (anteprima): consente di identificare i modelli di attacco, visualizzando gli avvisi di sicurezza relativi alle risorse non appena vengono generati.
- Intelligence per le minacce: consente di identificare i modelli di attacco, visualizzando il numero totale di server con traffico IP dannoso in uscita, il tipo di minaccia e una mappa che mostra la provenienza degli indirizzi IP in questione.
- Query comuni sulla sicurezza: questa opzione fornisce un elenco delle query di sicurezza più comuni che è possibile usare per monitorare l'ambiente. Facendo clic su una di queste query, viene aperto il pannello Ricerca con i risultati della query.

La schermata seguente mostra un esempio del tipo di informazioni che possono essere visualizzate in OMS.

![Linee di base sulla sicurezza di OMS](./media/azure-security-iaas/oms-security-baseline.png)



## <a name="next-steps"></a>Passaggi successivi


* [Blog del team della sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md)



<!--HONumber=Nov16_HO4-->


