---
title: Procedure consigliate per proteggere e gestire i carichi di lavoro migrati ad Azure | Microsoft Docs
description: È possibile ottenere le procedure consigliate per usare, gestire e proteggere i carichi di lavoro migrati ad Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 0bd19492c844e217dc520ae0c189ca467bb7ac0a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011090"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Procedure consigliate per proteggere e gestire i carichi di lavoro migrati ad Azure

In fase di pianificazione e progettazione per la migrazione, oltre a considerare la migrazione stessa, è necessario valutare il modello di sicurezza e gestione in Azure dopo la migrazione. Questo articolo descrive la pianificazione e le procedure consigliate per proteggere la distribuzione di Azure dopo la migrazione, nonché le attività continuative da svolgere per mantenere un'esecuzione ottimale della distribuzione. 

> [!IMPORTANT]
> Le procedure consigliate e le opinioni descritte in questo articolo si basano sulle funzionalità dei servizi e della piattaforma di Azure disponibili al momento della redazione di questo documento. Caratteristiche e funzionalità variano nel tempo.

## <a name="secure-migrated-workloads"></a>Proteggere i carichi di lavoro migrati

Dopo la migrazione, l'attività più importante consiste nel proteggere i carichi di lavoro migrati da minacce interne ed esterne. A tale scopo, seguire queste procedure consigliate:

- [Usare il Centro sicurezza di Azure](#best-practice-follow-azure-security-center-recommendations): informazioni su come usare il monitoraggio, le valutazioni e i consigli proposti dal Centro sicurezza di Azure
- [Crittografare i dati](#best-practice-encrypt-data): ottenere le procedure consigliate per la crittografia dei dati in Azure.
- [Configurare l'antimalware](#best-practice-protect-vms-with-antimalware): proteggere le macchine virtuali da malware e attacchi dannosi.
- [Proteggere le app Web](#best-practice-secure-web-apps): proteggere informazioni riservate nelle app Web migrate.
- [Rivedere le sottoscrizioni](#best-practice-review-subscriptions-and-resource-permissions): verificare chi può accedere alle sottoscrizioni e alle risorse di Azure dopo la migrazione.
- [Usare i registri](#best-practice-review-audit-and-security-logs): rivedere periodicamente i registri di controllo e protezione di Azure.
- [Esaminare altre funzionalità di sicurezza](#best-practice-evaluate-other-security-features): comprendere e valutare le funzionalità di sicurezza avanzate offerte da Azure.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Procedura consigliata: seguire i consigli del Centro sicurezza di Azure

Microsoft si impegna a garantire che gli amministratori di tenant di Azure abbiano le informazioni necessarie per abilitare funzionalità di sicurezza in grado di proteggere i carichi di lavoro dagli attacchi.  Il Centro sicurezza di Azure offre un livello unificato di gestione della sicurezza. Dal Centro sicurezza è possibile applicare i criteri di sicurezza sui carichi di lavoro, limitare l'esposizione alle minacce, rilevare e rispondere agli attacchi. Il Centro sicurezza analizza le risorse e le configurazioni tra i tenant di Azure e offre consigli sulla sicurezza, tra cui:

- **Gestione centralizzata dei criteri**: garantisce la conformità con i requisiti di sicurezza normativi o aziendali tramite la gestione centralizzata dei criteri di sicurezza in tutti i carichi di lavoro cloud ibridi.
- **Continua valutazione della sicurezza**: monitora il comportamento di sicurezza dei computer, delle reti, dei servizi di archiviazione e dati e delle applicazioni per individuare potenziali problemi di sicurezza.
- **Consigli operativi**: corregge le vulnerabilità della sicurezza prima che possano essere sfruttate dagli utenti malintenzionati con indicazioni di sicurezza con priorità e operative.
- **Avvisi ed eventi imprevisti classificati in ordine di priorità**: si concentra per prima cosa sulle minacce principali con avvisi di sicurezza ed eventi imprevisti classificati in ordine di priorità.

Oltre a consigli e valutazioni, il Centro sicurezza offre molte altre funzionalità di sicurezza che possono essere abilitate per risorse specifiche.

- **Accesso JIT (Just-In-Time)**: ridurre la superficie di attacco di rete con accesso JIT controllato alle porte di gestione nelle macchine virtuali di Azure.
    - Una porta RDP VM 3389 aperta su Internet espone le macchine virtuali a continue attività di utenti malintenzionati. Gli indirizzi IP di Azure sono noti e i pirati informatici eseguono probe continui per sferrare attacchi sulle porte 3389 aperte. 
    - L'accesso JIT usa gruppi di sicurezza di rete e regole in ingresso che limitano la quantità di tempo in cui una specifica porta rimane aperta.
    - Con l'accesso JIT abilitato, il Centro sicurezza controlla che un utente disponga delle autorizzazioni di accesso in scrittura al controllo degli accessi in base al ruolo per una macchina virtuale. È anche possibile specificare le regole per la modalità con cui gli utenti possono connettersi alle macchine virtuali. Se le autorizzazioni sono impostate, viene approvata una richiesta di accesso e il Centro sicurezza configura i gruppi di sicurezza di rete per consentire il traffico in ingresso alle porte selezionate per il periodo di tempo specificato. Allo scadere del periodo, i gruppi di sicurezza di rete tornano al loro stato precedente.
- **Controlli applicazioni adattivi**: per mantenere le macchine virtuali libere da software e malware, è possibile controllare le app in esecuzione mediante inserimento dinamico delle app nell'elenco elementi consentiti.
    - I controlli applicazioni adattivi consentono di inserire app nell'elenco elementi consentiti e impediscono a utenti o amministratori non autorizzati di installare app non approvate o verificare le app software nelle macchine virtuali.
    - È possibile bloccare i tentativi di esecuzione di app dannose o inviare avvisi a riguardo, evitare app indesiderate o dannose e garantire la conformità ai criteri di sicurezza delle app dell'organizzazione.
- **Monitoraggio dell'integrità dei file**: garantire l'integrità dei file in esecuzione nelle macchine virtuali.
    - Non è necessario installare software per causare problemi delle macchine virtuali.  La modifica di un file di sistema può determinare un errore o una riduzione del livello delle prestazioni della macchina virtuale.  Il monitoraggio dell'integrità dei file esamina i file di sistema e le impostazioni del Registro e invia una notifica se rileva una modifica o un aggiornamento.
    - Il Centro sicurezza segnala i file che è consigliabile monitorare.


**Altre informazioni**:

- [Altre informazioni](https://docs.microsoft.com/azure/security-center/security-center-intro) sul Centro sicurezza di Azure.
- [Altre informazioni](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) sui vantaggi dell'accesso JIT alle macchine virtuali.
- [Informazioni](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) sull'applicazione di controlli applicazioni adattivi.
- [Introduzione](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) al monitoraggio dell'integrità dei file.


## <a name="best-practice-encrypt-data"></a>Procedura consigliata: Crittografare i dati 

La crittografia è un componente importante delle procedure di sicurezza di Azure. L'abilitazione della crittografia a tutti i livelli impedisce alle parti non autorizzate di ottenere l'accesso ai dati sensibili, inclusi i dati in movimento e inattivi. 

### <a name="encryption-for-iaas"></a>Crittografia per IaaS

- **Macchine virtuali**: per le macchine virtuali è possibile usare Crittografia dischi di Azure per crittografare i dischi delle macchine virtuali IaaS Windows e Linux.
    - Crittografia dischi sfrutta BitLocker per Windows e DM-Crypt per Linux per la crittografia del volume per i dischi dati e del sistema operativo.
    - È possibile usare una chiave di crittografia creata da Azure oppure chiavi di crittografia personali, protette in Azure Key Vault. 
    - Con Crittografia dischi i dati delle macchine virtuali IaaS sono protette quando sono inattive (sul disco) e durante l'avvio. 
    - Centro sicurezza di Azure invia un avviso se sono presenti macchine virtuali non crittografate.
- **Archiviazione**: proteggere i dati inattivi archiviati in Archiviazione di Azure.
    - I dati archiviati negli account di archiviazione di Azure possono essere crittografati tramite chiavi AES generate da Microsoft conformi a FIPS 140-2 oppure è possibile usare chiavi personali.
    - La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione nuovi ed esistenti e non può essere disabilitata.


### <a name="encryption-for-paas"></a>Crittografia per PaaS

A differenza delle soluzioni IaaS in cui si gestiscono infrastruttura e macchine virtuali personali, in un modello PaaS infrastruttura e piattaforma vengono gestite dal provider, permettendo all'utente di concentrarsi sulle funzionalità e sulla logica delle app di base. Con così tanti tipi di servizi PaaS a disposizione, ogni servizio verrà valutato singolarmente per motivi di sicurezza. Vediamo ad esempio come abilitare la crittografia per il database SQL di Azure.

- **Always Encrypted**: usare la procedura guidata Always Encrypted in SQL Server Management Studio per proteggere i dati inattivi.
    - Per crittografare singoli dati di colonne, creare chiavi Always Encrypted.
    - Le chiavi Always Encrypted possono essere archiviate come crittografate nei metadati del database oppure in archivi chiavi attendibili, come Azure Key Vault.
    - Per usare questa funzionalità potrebbe essere necessario apportare modifiche alle app.
- **Transparent Data Encryption (TDE)**: proteggere il database SQL di Azure con crittografia e decrittografia in tempo reale del database, dei backup associati e dei file di log delle transazioni inattivi.
    - Transparent Data Encryption consente di eseguire attività di crittografia senza apportare modifiche a livello dell'app.
    - TDE può usare le chiavi di crittografia fornite da Microsoft oppure è possibile usare chiavi personali mediante il supporto Bring Your Own Key.


**Altre informazioni**:
- [Informazioni ](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) su Crittografia dischi di Azure per macchine virtuali IaaS.
- [Abilitare](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) la crittografia per le macchine virtuali IaaS di Windows.
- [Informazioni ](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) sulla crittografia del servizio Archiviazione di Azure per i dati inattivi.
- [Leggere](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) una panoramica di Always Encrypted.
- [Leggere](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) informazioni su TDE per il database SQL di Azure.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) su TDE con Bring Your Own Key.

## <a name="best-practice-protect-vms-with-antimalware"></a>Procedura consigliata: proteggere le VM con l'antimalware

In particolare, le macchine virtuali di Azure più obsolete sottoposte a migrazione potrebbero non avere il livello appropriato di antimalware installato.  Azure offre una soluzione di endpoint gratuita che consente di proteggere le macchine virtuali da virus, spyware e altri malware.
- Microsoft Antimalware per Azure genera avvisi quando un software dannoso o indesiderato tenta l'installazione automatica.
- È una soluzione ad agente singolo eseguita in background senza intervento umano.
- Nel Centro sicurezza di Azure è possibile identificare facilmente le macchine virtuali su cui non è in esecuzione Endpoint Protection e installare Microsoft Antimalware in base alle esigenze.

![Antimalware per macchine virtuali](./media/migrate-best-practices-security-management/antimalware.png)
*Antimalware per macchine virtuali*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/security/azure-security-antimalware) su Microsoft Antimalware.

## <a name="best-practice-secure-web-apps"></a>Procedura consigliata: proteggere le app Web

Con le app Web migrate si riscontrano un paio di problemi:

- La maggior parte delle applicazioni Web legacy tendono ad avere informazioni riservate nei file di configurazione.  I file contenenti queste informazioni possono presentare problemi di sicurezza quando le app vengono sottoposte a backup oppure quando il codice viene archiviato o estratto dal controllo del codice sorgente.
- Se si esegue la migrazione delle app Web che si trovano in una macchina virtuale, probabilmente il computer viene spostato da una rete locale e un ambiente protetto da firewall a un ambiente con connessione Internet. È necessario assicurarsi di configurare una soluzione che svolga le stesse funzioni delle risorse di protezione locali.


Azure offre un paio di soluzioni:

- **Azure Key Vault**: oggi gli sviluppatori di app Web si impegnano a evitare la perdita di dati sensibili da questi file. Un metodo per proteggere le informazioni consiste nell'estrarle dai file e inserirle in Azure Key Vault.
    - È possibile usare un insieme di credenziali delle chiavi per centralizzare l'archiviazione dei segreti delle app e controllare la distribuzione. Evita la necessità di archiviare le informazioni di sicurezza nei file delle app.
    - Le app possono accedere alle informazioni di sicurezza nell'insieme di credenziali mediante URI, senza ricorrere a codice personalizzato.
    - Azure Key Vault consente di bloccare l'accesso mediante controlli di sicurezza di Azure e implementare chiavi in sequenza. Microsoft non vede o estrae i dati.
- **Ambiente del servizio app**: se un'app migrata richiede protezione aggiuntiva, è possibile valutare di aggiungere un ambiente del servizio app e Web Application Firewall per proteggere le risorse dell'app.
    - L'ambiente del servizio app di Azure offre un ambiente completamente isolato e dedicato in cui eseguire app del servizio app come le app Web Windows e Linux, contenitori Docker, app per dispositivi mobili e funzioni.
    - È utile per le app altamente scalabili, che richiedono l'isolamento, proteggono l'accesso alla rete o sono caratterizzate da uso intensivo della memoria
- **Web application firewall**: una funzionalità del gateway applicazione che offre protezione centralizzata per le app Web.
    - Protegge le app Web senza richiedere modifiche al codice di back-end.
    - Protegge contemporaneamente più app Web con un gateway applicazione.
    - Web application firewall può essere monitorato mediante Monitoraggio di Azure ed è integrato nel Centro sicurezza di Azure.



![Proteggere le app Web](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**Altre informazioni**:

- [Panoramica](https://docs.microsoft.com/azure/key-vault/key-vault-overview) di Azure Key Vault.
- [Informazioni](https://docs.microsoft.com/azure/application-gateway/waf-overview) su Web application firewall.
- [Introduzione](https://docs.microsoft.com/azure/app-service/environment/intro) agli ambienti del servizio app.
- [Informazioni su come](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) configurare un'app Web per leggere i segreti dall'insieme di credenziali delle chiavi.
- [Informazioni](https://docs.microsoft.com/azure/application-gateway/waf-overview) su Web application firewall

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Procedura consigliata: esaminare le sottoscrizioni e le autorizzazioni delle risorse

Nel momento in cui si esegue la migrazione dei carichi di lavoro e li si esegue in Azure, il personale con accesso ai carichi di lavoro si sposta. Il team di sicurezza deve esaminare l'accesso ai gruppi di risorse e tenant di Azure a intervalli regolari. Azure offre una serie di offerte per la sicurezza con il controllo di accesso e la gestione delle identità, tra cui il controllo degli accessi in base al ruolo per consentire autorizzazioni di accesso alle risorse di Azure.

- Il controllo degli accessi in base al ruolo assegna autorizzazioni di accesso per le entità di sicurezza. Le entità di sicurezza rappresentano utenti, gruppi (un set di utenti), entità servizio (identità usata da app e servizi) e identità gestite (un'identità di Azure Active Directory gestita automaticamente da Azure).
- Il controllo può assegnare ruoli ai principi di sicurezza, tra cui proprietario, collaboratore e lettore, e definizioni di ruoli (una raccolta di autorizzazioni) che specificano le operazioni eseguibili dai ruoli.
- Il controllo degli accessi in base al ruolo può anche definire ambiti che impostano il limite per un ruolo. L'ambito può essere impostato a un numero di livelli, tra cui un gruppo di gestione, una sottoscrizione, un gruppo di risorse o una risorsa
- Assicurarsi che gli amministratori con accesso ad Azure siano in grado di accedere alle sole risorse consentite dall'utente.  Se i ruoli predefiniti in Azure non sono sufficientemente granulari, è possibile creare ruoli personalizzati per separare e limitare le autorizzazioni di accesso.

![Controllo di accesso](./media/migrate-best-practices-security-management/subscription.png)
*Controllo di accesso - IAM*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/role-based-access-control/overview) sul controllo degli accessi in base al ruolo.
- [Informazioni](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) per gestire l'accesso mediante il controllo degli accessi in base al ruolo e il portale di Azure.
- [Informazioni](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) sui ruoli personalizzati.

## <a name="best-practice-review-audit-and-security-logs"></a>Procedura consigliata: Esaminare i registri di controllo e protezione

Azure Active Directory (AD) offre log di attivati, visualizzati in Monitoraggio di Azure. I log acquisiscono le operazioni eseguite nella tenancy di Azure, il momento in cui si verificano e gli utenti che le eseguono. 

- I log di controllo mostrano la cronologia delle attività nel tenant. I log attività di accesso indicano chi ha eseguito le attività. 
- L'accesso ai report di sicurezza dipende dalla licenza Azure AD. Le edizioni Free e Basic offrono un elenco di accessi e utenti a rischio. Le edizioni Premium 1 e 2 offrono le informazioni sugli eventi sottostanti.
- È possibile indirizzare i log attività a un numero di endpoint per garantire la conservazione a lungo termine e ottenere informazioni dettagliate sui dati.
- È consigliabile esaminare periodicamente i log o integrare strumenti di informazioni di sicurezza e gestione degli eventi per verificare automaticamente la presenza di anomalie.  Se non si usa Premium 1 o 2, sarà necessario eseguire numerose analisi in autonomia o mediante il sistema di informazioni di sicurezza e gestione degli eventi in uso.  L'analisi include la ricerca di accessi ed eventi a rischio e altri modelli di attacco degli utenti.


![Utenti e gruppi](./media/migrate-best-practices-security-management/azure-ad.png)
*Utenti e gruppi di Azure AD*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) sui log attività di Azure AD in Monitoraggio di Azure.
- [Informazioni](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) su come controllare i report attività nel portale di Azure AD.

## <a name="best-practice-evaluate-other-security-features"></a>Procedura consigliata: valutare altre funzionalità di sicurezza

Azure offre molte altre funzionalità che includono opzioni di sicurezza avanzate. Alcune di queste procedure consigliate richiedono licenze aggiuntive e opzioni Premium.

- **Implementare unità amministrative di Azure AD**: delegare attività amministrative al personale del supporto può essere complicato semplicemente con il controllo di accesso di base di Azure.  Concedere l'accesso al personale del supporto per amministrare tutti i gruppi in Azure AD potrebbe non essere l'approccio ideale per la sicurezza dell'organizzazione.  Mediante le unità amministrative è possibile separare le risorse di Azure in contenitori in modo analogo alle unità organizzative locali.  Per usare le unità amministrative, l'amministratore deve avere licenza Premium di Azure AD. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)
- **Usare l'autenticazione a più fattori (MFA)**: con una licenza Premium di Azure AD è possibile abilitare e applicare l'autenticazione a più fattori per gli account amministratore. Il phishing è il modo più comune con cui vengono compromesse le credenziali degli account.  Se utenti malintenzionati accedono alle credenziali degli account amministratore, è praticamente impossibile impedire loro di compiere azioni particolarmente a rischio, come l'eliminazione di tutti i gruppi di risorse. È possibile configurare MFA in diversi modi, ad esempio con indirizzi di posta elettronica, app di autenticazione ed SMS. L'amministratore può scegliere l'opzione meno intrusiva. MFA si integra con le analisi delle minacce e i criteri di accesso condizionale per richiedere in modo casuale una risposta a un problema. Altre informazioni sulle [indicazioni di sicurezza](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) e su [come configurare](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA.
- **Implementare l'accesso condizionale**: nella maggior parte delle piccole e medie imprese gli amministratori di Azure e il team di supporto si trovano probabilmente in un'unica area geografica. In questo caso, la maggior parte degli accessi verrà eseguita dalla stessa area. Se gli indirizzi IP di queste posizioni sono statici, avrebbe senso non visualizzare gli accessi amministratore all'esterno di queste aree. Anche nel caso in cui un utente malintenzionato remoto comprometta le credenziali di un amministratore, è possibile implementare funzionalità di sicurezza, ad esempio l'accesso condizionale combinato con MFA, per impedire l'accesso da posizioni remote o falsificate con indirizzi IP casuali. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) sull'accesso condizionale e [procedure consigliate](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) per l'accesso condizionale in Azure AD.
- **Esaminare le autorizzazioni delle applicazioni aziendali**: Nel tempo gli amministratori scelgono Microsoft e collegamenti di terze parti senza conoscerne l'impatto sull'organizzazione. I collegamenti possono mostrare schermate di consenso che assegnano autorizzazioni alle app di Azure e potrebbero consentire l'accesso in lettura ai dati di Azure AD o addirittura l'accesso completo per gestire l'intera sottoscrizione di Azure. È consigliabile esaminare regolarmente le app per cui gli amministratori e gli utenti hanno consentito l'accesso alle risorse di Azure. È opportuno verificare che queste app abbiano esclusivamente le autorizzazioni necessarie. Ogni tre o sei mesi è anche possibile inviare messaggi di posta elettronica agli utenti con un collegamento alle pagine delle app in modo che siano consapevoli di quelle per cui hanno consentito l'accesso ai dati dell'organizzazione. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) sui tipi di applicazioni e [su come controllare](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) le assegnazioni delle app in Azure AD.



## <a name="managed-migrated-workloads"></a>Carichi di lavoro migrati gestiti

In questa sezione vengono indicate alcune procedure consigliate per la gestione di Azure, tra cui:

- [Gestire le risorse](#best-practice-name-resource-groups): procedure consigliate per risorse e gruppi di risorse di Azure, tra cui denominazione intelligente, come evitare eliminazioni accidentali, gestione delle autorizzazioni per le risorse e assegnazione efficace di tag alle risorse.
- [Usare progetti](#best-practice-implement-blueprints): ottenere una rapida panoramica dell'uso di progetti per creare e gestire gli ambienti di distribuzione.
- [Esaminare le architetture](#best-practice-review-azure-reference-architectures): esaminare le architetture di esempio di Azure da cui apprendere nozioni durante la creazione di distribuzioni di post-migrazione.
- [Configurare gruppi di gestione](#best-practice-manage-resources-with-management-groups): è possibile raccogliere più sottoscrizioni in gruppi di gestione e applicare impostazioni di governance a questi gruppi.
- [Configurare criteri di accesso](#best-practice-deploy-azure-policy): applicare criteri di conformità alle risorse di Azure.
- [Implementare una strategia di continuità aziendale e ripristino di emergenza](#best-practice-implement-a-bcdr-strategy): realizzare una strategia di continuità aziendale e ripristino di emergenza (BCDR) per mantenere i dati sicuri, l'ambiente resiliente e le risorse operative in caso di interruzioni.
- [Gestire le macchine virtuali](#best-practice-use-managed-disks-and-availability-sets): raggruppare le macchine virtuali in gruppi di disponibilità per disponibilità elevata e resilienza. Usare dischi gestiti per semplificare la gestione dei dischi e dell'archiviazione delle macchine virtuali.
- [Monitorare l'uso delle risorse](#best-practice-monitor-resource-usage-and-performance): abilitare la registrazione diagnostica per le risorse di Azure, creare avvisi e playbook per la risoluzione proattiva dei problemi e usare il dashboard di Azure per una vista unificata dell'integrità e dello stato della distribuzione.
- [Gestire supporto e aggiornamenti](#best-practice-manage-updates): comprendere il piano di supporto tecnico di Azure e come implementarlo, apprendere le procedure consigliate per mantenere aggiornate le macchine virtuali e definire processi per la gestione delle modifiche.


## <a name="best-practice-name-resource-groups"></a>Procedura consigliata: denominare i gruppi di risorse

L'assegnazione di nomi significativi ai gruppi di risorse che gli amministratori e i membri del team di supporto possono riconoscere e usare facilmente determinerà livelli superiori di produttività ed efficienza.
- È consigliabile seguire le convenzioni di denominazione di Azure.
- Se si esegue la sincronizzazione di Active Directory Domain Services in locale con Azure AD mediante AD Connect, valutare la possibilità di assegnare ai gruppi di risorse in Azure gli stessi nomi dei gruppi di sicurezza locali.

![Denominazione](./media/migrate-best-practices-security-management/naming.png)
*Denominazione dei gruppi di risorse*


**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) sulle convenzioni di denominazione

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Procedura consigliata: implementare blocchi di eliminazione per i gruppi di risorse

L'eliminazione accidentale di un gruppo di risorse è tutto fuorché un'esperienza piacevole. Per evitare questa situazione, è consigliabile implementare blocchi di eliminazione.


![Blocchi di eliminazione](./media/migrate-best-practices-security-management/locks.png)
*Blocchi di eliminazione*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) su come bloccare le risorse per evitare modifiche impreviste.


## <a name="best-practice-understand-resource-access-permissions"></a>Procedura consigliata: comprendere le autorizzazioni di accesso alle risorse 

Il proprietario di una sottoscrizione ha accesso a tutte le risorse e i gruppi di risorse al suo interno.
- Aggiungere con moderazione utenti a questa utile assegnazione. Comprendere le implicazioni di questi tipi di autorizzazioni è importante per mantenere l'ambiente sicuro e stabile.
- Verificare che le risorse vengano inserite nei gruppi appropriati:
    - Abbinare le risorse con un ciclo di vita simile. In teoria non dovrebbe essere necessario spostare una risorsa quando è necessario eliminare un intero gruppo di risorse.
    - Le risorse che supportano una funzione o un carico di lavoro devono essere posizionate insieme per semplificare la gestione.

**Altre informazioni**:

- [Informazioni](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) su come organizzare le sottoscrizioni e i gruppi di risorse.

## <a name="best-practice-tag-resources-effectively"></a>Procedura consigliata: assegnare tag alle risorse in modo efficace

Spesso l'uso di un nome di un gruppo di risorse correlato solo alle risorse non specifica metadati sufficienti per un'implementazione efficace dei meccanismi, ad esempio la fatturazione interna o la gestione all'interno di una sottoscrizione.
- È consigliabile usare tag di Azure per aggiungere metadati utili che possano essere sottoposti a query e su cui possano essere generati report. 
- I tag offrono un modo per organizzare in modo logico le risorse con proprietà definite dall'utente.  I tag possono essere applicati direttamente alle risorse o a gruppi di risorse.
- I tag possono essere applicati a un gruppo di risorse o a singole risorse. I tag di gruppi di risorse non vengono ereditati dalle risorse nel gruppo.
- È possibile automatizzare l'assegnazione di tag tramite PowerShell o Automazione di Azure oppure assegnare tag a singoli gruppi e risorse, con un approccio di assegnazione di tag o self-service.  In presenza di un sistema di gestione delle richieste e delle modifiche, è possibile usare le informazioni nella richiesta per popolare i tag di risorse specifici dell'azienda.


![Assegnazione di tag](./media/migrate-best-practices-security-management/tagging.png)
*Assegnazione di tag*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) sull'assegnazione e sulle limitazioni dei tag.
- [Consultare](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) alcuni esempi di PowerShell e dell'interfaccia della riga di comando per configurare l'assegnazione di tag e applicarli da un gruppo alle relative risorse.
- [Leggere](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) le procedure consigliate sull'assegnazione di tag di Azure.


## <a name="best-practice-implement-blueprints"></a>Procedura consigliata: Implementare progetti

Così come un progetto consente a un ingegnere o un architetto di tracciare i parametri di progettazione, Azure Blueprints consente agli architetti cloud e ai gruppi centrali del reparto IT di definire un set ripetibile di risorse di Azure che implementa ed è conforme a standard, criteri e requisiti di un'organizzazione. Azure Blueprints consente ai team di sviluppo di creare e realizzare rapidamente nuovi ambienti che soddisfano i requisiti di conformità dell'organizzazione e includono un set di componenti integrati, ad esempio reti, per velocizzare lo sviluppo e il recapito.

- Usare i progetti per orchestrare la distribuzione di gruppi di risorse, modelli di Azure Resource Manager e assegnazioni di ruoli e criteri.
- Questi progetti di Azure vengono archiviati in un Azure Cosmos DB distribuito a livello globale. Gli oggetti del progetto vengono replicati in più aree di Azure. La replica garantisce bassa latenza, disponibilità elevata e accesso coerente al progetto, indipendentemente dall'area in cui un progetto distribuisce le risorse.

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/governance/blueprints/overview) sui progetti.
- [Consultare](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) un esempio di progetto usato per accelerare l'intelligenza artificiale nei servizi sanitari.

## <a name="best-practice-review-azure-reference-architectures"></a>Procedura consigliata: esaminare le architetture di riferimento di Azure

Creare carichi di lavoro sicuri, scalabili e gestibili in Azure può risultare complicato.  Con modifiche continue può essere difficile tenere il passo con le varie funzionalità per un ambiente ottimale. Può essere utile avere un riferimento durante la progettazione e la migrazione dei carichi di lavoro.  Azure e i suoi partner hanno realizzato varie architetture di riferimento di esempio per tipi di ambienti diversi. Questi esempi sono progettati per sfruttare idee da cui partire e su cui basarsi. 

Le architetture di riferimento sono organizzate in base allo scenario. Includono procedure consigliate e indicazioni su gestione, disponibilità, scalabilità e sicurezza.
L'ambiente del servizio app di Azure offre un ambiente completamente isolato e dedicato in cui eseguire app del servizio app come le app Web Windows e Linux, contenitori Docker, app per dispositivi mobili e funzioni. Il servizio app aggiunge la potenza di Azure all'applicazione in termini di sicurezza, bilanciamento del carico, ridimensionamento automatico e gestione automatizzata. È anche possibile usufruire delle funzionalità DevOps, come la distribuzione continua da Azure DevOps e GitHub, la gestione dei pacchetti, gli ambienti di gestione temporanea, il dominio personalizzato e i certificati SSL. Il servizio app è utile per le app che necessitano di isolamento e proteggono l'accesso alla rete e per quelle che usano quantità elevate di memoria e altre risorse che necessitano di scalabilità.
**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/architecture/reference-architectures/) sulle architetture di riferimento di Azure.
- [Consultare](https://docs.microsoft.com/azure/architecture/example-scenario/) alcuni scenari di esempio di Azure.

## <a name="best-practice-manage-resources-with-management-groups"></a>Procedura consigliata: Gestire le risorse con gruppi di gestione

Se un'organizzazione ha più sottoscrizioni, è necessario gestirne accesso, criteri e conformità. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni.

- Le sottoscrizioni sono organizzate in contenitori chiamati gruppi di gestione a cui vengono applicate condizioni di governance.
- Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni del gruppo di gestione.
- I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono.
- È ad esempio possibile applicare criteri di gruppi di gestione che limitano le aree in cui è possibile creare macchine virtuali. Questi criteri verranno applicati a tutti i gruppi di gestione, le sottoscrizioni e le risorse all'interno del gruppo di gestione.
- È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni, in modo da organizzare le risorse in una gerarchia per la gestione unificata di accesso e criteri.

Il diagramma seguente mostra un esempio di creazione di una gerarchia per la governance tramite gruppi di gestione.

![Gruppi di gestione](./media/migrate-best-practices-security-management/management-groups.png)
*Gruppi di gestione*

**Altre informazioni**:
- [Altre informazioni](https://docs.microsoft.com/azure/governance/management-groups/index) sull'organizzazione delle risorse in gruppi di gestione.

## <a name="best-practice-deploy-azure-policy"></a>Procedura consigliata: distribuire Criteri di Azure

Criteri di Azure è un servizio disponibile in Azure che consente di creare, assegnare e gestire criteri.

- I criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda.
- Criteri di Azure valuta le risorse, individuando quelle non conformi ai criteri.
- È ad esempio possibile creare criteri che consentano una sola dimensione SKU specifica per le macchine virtuali nell'ambiente in uso. Criteri di Azure valuterà questa impostazione durante la creazione e l'aggiornamento delle risorse e durante l'analisi delle risorse esistenti. 
- Azure offre alcuni criteri predefiniti che è possibile assegnare oppure è possibile crearne altri.


![Criteri di Azure](./media/migrate-best-practices-security-management/policy.png)
*Criteri di Azure*

**Altre informazioni**:
- [Ottenere una panoramica](https://docs.microsoft.com/azure/governance/policy/overview) di Criteri di Azure.
- [Informazioni](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) sulla creazione e sulla gestione dei criteri per applicare la conformità.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Procedura consigliata: implementare una strategia di continuità aziendale e ripristino di emergenza

La pianificazione di una strategia di continuità aziendale e ripristino di emergenza (BCDR) è una fase essenziale che è opportuno completare durante la pianificazione della migrazione ad Azure. In termini legali, il contratto include una clausola che esenta da alcuni obblighi per via di eventi di forza maggiore, ad esempio terremoti o uragani. Sussiste comunque l'obbligo di garantire la continuità dei servizi e il ripristino, laddove necessario, al verificarsi di uno di questi eventi. La capacità di garantire o meno questo servizio determina il futuro dell'azienda.

A livello generale, una strategia BCDR deve considerare:
- **Backup dei dati**: come proteggere i dati in modo che sia possibile recuperarli con facilità se si verificano interruzioni.
- **Ripristino di emergenza**: come mantenere le app resilienti e disponibili se si verificano interruzioni. 

### <a name="azure-resiliency-features"></a>Funzionalità di resilienza di Azure
La piattaforma di Azure offre numerose funzionalità di resilienza.

- **Associazione di aree**: Azure associa le aree per garantire la protezione a livello di area entro i limiti di residenza dei dati. Azure offre isolamento fisico tra coppie di aree, stabilisce come prioritario il ripristino di un'area della coppia in caso di un'interruzione su vasta scala, distribuisce gli aggiornamenti del sistema separatamente in ogni area e consente funzionalità come l'archiviazione con ridondanza geografica di Azure per la replica tra le coppie di aree.
- **Zone di disponibilità**: le zone di disponibilità proteggono dal malfunzionamento di un intero data center di Azure mediante la definizione di zone fisiche distinte in un'area di Azure. Ogni zona è caratterizzata da sorgenti di alimentazione, infrastrutture di rete e meccanismi di raffreddamento distinti.
- **Set di disponibilità**: i set di disponibilità proteggono dai guasti all'interno di un data center. Le macchine virtuali vengono raggruppate in set di disponibilità per garantirne la disponibilità elevata. All'interno di ogni set di disponibilità Azure implementa più domini di errore che raggruppano l'hardware sottostante con una sorgente di alimentazione comune e uno switch di rete e aggiorna contemporaneamente i domini che raggruppano l'hardware sottostante, che può essere sottoposto a manutenzione oppure riavviato. Quando ad esempio un carico di lavoro viene distribuito tra varie macchine virtuali di Azure, è possibile inserire due o più macchine virtuali per ogni livello di app in un set. È ad esempio possibile inserire macchine virtuali front-end in un set e macchine virtuali di livello dati in un altro. Poiché viene riavviato un solo dominio di aggiornamento per volta in un set e Azure garantisce che le macchine virtuali in un set vengano distribuite tra domini di errore, verificare che non tutte le macchine virtuali in un set presentino errori contemporaneamente.

### <a name="set-up-bcdr"></a>Configurare BCDR

Durante la migrazione ad Azure è importante comprendere che, sebbene la piattaforma di Azure offra queste funzionalità di resilienza integrate, è necessario progettare la distribuzione di Azure per sfruttare le funzionalità e i servizi di Azure che offrono disponibilità elevata, ripristino di emergenza e backup.

- La soluzione BCDR varia a seconda degli obiettivi aziendali ed è influenzata dalla strategia di distribuzione di Azure. Le distribuzioni IaaS (Infrastructure-as-a-Service, infrastruttura distribuita come servizio) e PaaS (Platform-as-a-Service, piattaforma distribuita come servizio) presentano problematiche diverse in termini di BCDR.
- Una volta definite, le soluzioni BCDR devono essere testare periodicamente per verificare che la strategia rimanga valida.


## <a name="best-practice-back-up-your-data"></a>Procedura consigliata: Eseguire il backup dei dati

Nella maggior parte dei casi un carico di lavoro locale viene ritirato dopo la migrazione e la strategia locale per il backup dei dati deve essere estesa o sostituita. Se si esegue la migrazione dell'intero data center ad Azure, è necessario progettare e implementare una soluzione di backup completo mediante tecnologie di Azure o soluzioni integrate di terze parti. 


### <a name="back-up-an-iaas-deployment"></a>Eseguire il backup di una distribuzione IaaS

Per i carichi di lavoro in esecuzione in macchine virtuali IaaS di Azure considerare queste soluzioni di backup:

- **Backup di Azure**: supporta i backup coerenti con l'applicazione per le macchine virtuali di Windows e di Linux.
- **Snapshot di archiviazione**: creare snapshot di archiviazione BLOB.

#### <a name="azure-backup"></a>Backup di Azure

Backup di Azure crea punti di recupero dei dati archiviati in Archiviazione di Azure. Backup di Azure può eseguire il backup di dischi di macchine virtuali e file di Azure (anteprima). I file di Azure offrono condivisioni file nel cloud, accessibile tramite SMB.
   
È possibile usare Backup di Azure per eseguire il backup di macchine virtuali in un paio di modi.

- **Backup diretto dalle impostazioni della macchina virtuale**: è possibile eseguire il backup di macchine virtuali con Backup di Azure direttamente dalle opzioni per macchine virtuali nel portale di Azure. È possibile eseguire il backup della macchina virtuale una volta al giorno e ripristinare il disco della macchina virtuale in base alle esigenze. Backup di Azure esegue snapshot di dati che supportano le app (VSS). Nella macchina virtuale non vengono installati agenti.
- **Backup diretto in un insieme di credenziali di Servizi di ripristino**: è possibile eseguire il backup delle macchine virtuali IaaS mediante la distribuzione di un insieme di credenziali di Servizi di ripristino di Backup di Azure. In un'unica posizione è possibile tenere traccia dei backup e gestirli e sono disponibili opzioni granulari di backup e ripristino. Il backup viene eseguito fino a tre volte al giorno, a livello di file/cartella. Non è compatibile con le app e Linux non è supportato. È necessario installare l'agente Servizi di ripristino di Microsoft Azure (MARS) in ogni macchina virtuale di cui si intende eseguire il backup.
- **Server di Backup di Azure. Proteggere la macchina virtuale nel server di Backup di Azure**: il server di Backup di Azure è disponibile gratuitamente con Backup di Azure. La macchina virtuale viene sottoposta a backup nell'archiviazione del server di Backup di Azure. Il server di Backup di Azure viene quindi sottoposto a backup in Azure all'interno di un insieme di credenziali. Il backup è compatibile con le app, con granularità completa su backup frequenti e conservazione. È possibile eseguire il backup a livello di app, ad esempio eseguendo il backup di SQL Server o SharePoint.

In termini di sicurezza, Backup di Azure crittografa i dati in transito mediante AES 256 e li invia tramite HTTPS ad Azure. I dati inattivi sottoposti a backup in Azure vengono crittografati mediante [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)e i dati per la trasmissione e l'archiviazione.


![Backup di Azure](./media/migrate-best-practices-security-management/iaas-backup.png)
*Backup di Azure*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) sui vari tipi di backup.
- [Pianificare un'infrastruttura di backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) per macchine virtuali di Azure.

#### <a name="storage-snapshots"></a>Snapshot di archiviazione

Le macchine virtuali di Azure vengono archiviate come BLOB di pagine in Archiviazione di Azure. 

- Gli snapshot acquisiscono lo stato del BLOB in un dato momento.
- Come metodo di backup alternativo per i dischi di macchine virtuali di Azure è possibile creare uno snapshot del BLOB di archiviazione e copiarlo in un altro account di archiviazione. 
- È possibile copiare un intero BLOB oppure usare una copia di snapshot incrementale per copiare solo le modifiche differenziali e ridurre lo spazio di archiviazione.
- Come precauzione aggiuntiva è possibile abilitare l'eliminazione temporanea per gli account di archiviazione BLOB. Con questa funzionalità abilitata un BLOB eliminato viene contrassegnato per l'eliminazione, ma non immediatamente ripulito. È possibile ripristinare il BLOB durante la fase intermedia.

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) sull'archiviazione BLOB di Azure.
- [Informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) su come creare uno snapshot BLOB.
- [Esaminare uno scenario di esempio](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) per il backup di archiviazione BLOB.
- [Informazioni](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) sull'eliminazione temporanea.
- [Ripristino di emergenza e failover forzato (anteprima) in Archiviazione di Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>Backup di terze parti

È anche possibile usare soluzioni di terze parti per eseguire il backup di macchine virtuali di Azure e contenitori di archiviazione nella risorsa di archiviazione locale o in altri provider di servizi cloud. [Altre informazioni](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) sulle soluzioni di backup in Azure Marketplace. 


### <a name="back-up-a-paas-deployment"></a>Eseguire il backup di una distribuzione PaaS


A differenza delle soluzioni IaaS in cui si gestiscono infrastruttura e macchine virtuali personali, in un modello PaaS infrastruttura e piattaforma vengono gestite dal provider, permettendo all'utente di concentrarsi sulle funzionalità e sulla logica delle app di base. Con così tanti tipi di servizi PaaS a disposizione, ogni servizio verrà valutato singolarmente ai fini di backup. Di seguiti verranno esaminati due servizi PaaS di Azure comuni: il database SQL di Azure e Funzioni di Azure.

#### <a name="back-up-azure-sql-database"></a>Eseguire il backup del database SQL di Azure

Il database SQL di Azure è un motore di database PaaS completamente gestito. Offre una serie di funzionalità per la continuità aziendale, tra cui il backup automatizzato.

- Il database SQL esegue automaticamente backup settimanali completi del database e backup differenziali ogni 12 ore. I backup dei log delle transazioni vengono eseguiti ogni cinque/dieci minuti per proteggere il database dalla perdita di dati.
- I backup sono trasparenti e non prevedono costi aggiuntivi.
- I backup vengono archiviati in una risorsa di archiviazione con ridondanza geografica e accesso in lettura e replicati nell'area geografica abbinata.
- La conservazione dei backup varia a seconda del modello di acquisto. I livelli di servizio basati su DTU spaziano da sette giorni per il livello Basic a 35 giorni per altri livelli.
- È possibile ripristinare un database in un punto nel tempo entro il periodo di conservazione. È anche possibile ripristinare un database eliminato, eseguire il ripristino in un'area geografica diversa oppure da un backup a lungo termine se il database include criteri di conservazione a lungo termine.


![Backup di Azure SQL](./media/migrate-best-practices-security-management/sql-backup.png)
*Backup di Azure SQL*

**Altre informazioni**:
- [Backup automatici](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) per il database SQL.
- [Ripristinare un database](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) mediante backup automatici.

 
#### <a name="back-up-azure-functions"></a>Eseguire il backup di Funzioni di Azure

Poiché Funzioni di Azure è analogo a una sorta di codice, è consigliabile eseguirne il backup con gli stessi metodi adottati proteggere il codice, ad esempio il controllo del codice sorgente in GitHub o Azure DevOps Services

**Altre informazioni**:

- [Protezione dei dati](https://docs.microsoft.com/azure/devops/articles/team-services-security-whitepaper?view=vsts) per Azure DevOps.

## <a name="best-practice-set-up-disaster-recovery"></a>Procedura consigliata: Configurare il ripristino di emergenza 

Oltre a proteggere i dati, la pianificazione BCDR deve considerare come mantenere app e carichi di lavoro disponibili in caso di emergenza. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Configurare il ripristino di emergenza per app IaaS

Per i carichi di lavoro in esecuzione in macchine virtuali IaaS di Azure e nell'archiviazione di Azure, considerare queste soluzioni:

- **Azure Site Recovery**: orchestra la replica di macchine virtuali di Azure da un'area primaria a un'area secondaria. Quando si verificano interruzioni, è possibile eseguire il failover dall'area primaria alla secondaria per permettere agli utenti di continuare ad accedere alle app. Al ripristino delle normali condizioni eseguire nuovamente il failback all'area primaria.
- **Archiviazione di Azure**: Azure offre resilienza e disponibilità elevata integrate per vari tipi di archiviazione:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Azure Site Recovery è il servizio primario di Azure per garantire che le macchine virtuali di Azure possano rimanere connesse e le app disponibili quando si verificano interruzioni. Site Recovery replica le macchine virtuali da un'area primaria a un'area secondaria di Azure. In caso di emergenza eseguire il failover delle macchine virtuali dall'area primaria per continuare ad accedervi normalmente nell'area secondaria. Al ripristino delle normali condizioni eseguire nuovamente il failback delle macchine virtuali all'area primaria.


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**Altre informazioni**:
- [Esaminare](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) alcuni scenari di ripristino di emergenza per macchine virtuali di Azure.
- [Informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) su come configurare il ripristino di emergenza per una macchina virtuale di Azure dopo la migrazione.

#### <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure viene replicato per la disponibilità elevata e la resilienza integrate.

-   **Archiviazione con ridondanza geografica**: offre protezione da un'interruzione a livello di area, con almeno il 99,99999999999999% (16 9) di durabilità degli oggetti in un anno specificato.
    - I dati di archiviazione vengono replicati nell'area secondaria a cui è associata l'area primaria.
    - Se l'area primaria smette di funzionare e Microsoft avvia un failover all'area secondaria, sarà comunque consentito l'accesso in lettura ai dati.
- **Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)**: offre protezione da un'interruzione a livello di area.
    - I dati di archiviazione vengono replicati nell'area secondaria.
    - È garantito l'accesso in lettura ai dati replicati nell'area secondaria, indipendentemente dall'avvio o meno di un failover da parte di Microsoft. In caso di problemi in due o più data center nella stessa area, i dati saranno comunque disponibili in un'area geograficamente separata.
-   **Archiviazione con ridondanza della zona**:  offre protezione dai guasti nel data center.
    - L'archiviazione con ridondanza della zona replica i dati in modo sincrono in tre cluster di archiviazione in una singola area. I cluster sono fisicamente distinti e situati nella rispettiva zona di disponibilità.
    - Se si verifica un'emergenza, la risorsa di archiviazione sarà comunque disponibile. L'archiviazione con ridondanza della zona deve rappresentare la destinazione minima per i carichi di lavoro critici.



**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy) sulla replica di archiviazione di Azure.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Configurare il ripristino di emergenza per carichi di lavoro PaaS

Prendiamo in esame le opzioni di ripristino di emergenza per i nostri esempi di carichi di lavoro PaaS.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Ripristino di emergenza per Azure SQL Server

Sono disponibili varie opzioni diverse, ognuna inerente alla perdita di dati, ai tempi di ripristino e così via.

È possibile usare gruppi di failover e replica geografica attiva per garantire resilienza alle interruzioni a livello di area ed errori irreversibili

- **Replica geografica attiva**: distribuire la replica geografica attiva per un rapido ripristino di emergenza in caso di interruzioni nel data center o se non è possibile stabilire una connessione a un database primario.
    - La replica geografica crea ripetutamente repliche leggibili del database in un massimo di quattro database secondari nella stessa area o in aree diverse.
    - In caso di interruzione, eseguire il failover a una delle aree secondarie e ripristinare la connettività del database.
- **Gruppi di failover automatico**: i gruppi di failover automatico estendono la replica geografica attiva con il failover trasparente di più database.
    - Un gruppo di failover automatico garantisce un'astrazione potente della replica geografica attiva grazie alla replica del database a livello di gruppo e al failover automatico.
    - È possibile creare un gruppo di failover contenente un server primario che ospita uno o più database primari, un server secondario che ospita repliche di sola lettura dei database primari, listener che puntano a ciascun server e criteri di failover automatico.
    - Gli endpoint del listener specificati eliminano l'esigenza di modificare la stringa di connessione SQL dopo il failover.
- **Ripristino geografico**: 
    -   il ripristino geografico consente di ripristinare il database in un'area diversa. Il backup automatico di tutti i database di Azure verrà replicato in un'area secondaria in background. Il database verrà sempre ripristinato dalla copia dei file di backup archiviati nell'area secondaria.
-   I **database con ridondanza della zona** offrono supporto integrato per le zone di disponibilità di Azure.
    - I database con ridondanza della zona ottimizzano la disponibilità elevata per Azure SQL Server in caso di guasto in un data center.
    - Con la ridondanza della zona è possibile posizionare repliche di database ridondanti in zone di disponibilità diverse all'interno di un'area. 



![Replica geografica](./media/migrate-best-practices-security-management/geo-replication.png)
*Replica geografica*

**Altre informazioni**:
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) sulla disponibilità elevata per Azure SQL Server.
- [Leggere](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) Azure SQL Databases 101 Disaster Recovery (Ripristino di emergenza 101 dei database SQL di Azure).
- [Panoramica](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) dei gruppi di failover e della replica geografica attiva.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) sulla progettazione per il ripristino di emergenza.
- [Procedure consigliate](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) per gruppi di failover.
- [Procedure consigliate](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) per la sicurezza dopo il failover o il ripristino geografico.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) sulla ridondanza della zona
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) su come eseguire un'esercitazione per il ripristino di emergenza del database SQL.

### <a name="disaster-recovery-for-azure-functions"></a>Ripristino di emergenza per Funzioni di Azure

In caso di errore dell'infrastruttura di calcolo in Azure, l'app per le funzioni di Azure potrebbe diventare non disponibile.

- Per ridurre al minimo la possibilità di incorrere in tempi di inattività, questo scenario usa due app per le funzioni distribuite in aree diverse.
- Gestione traffico di Azure può essere configurata per rilevare i problemi nell'app per le funzioni primaria e reindirizzare automaticamente il traffico verso l'app per le funzioni nell'area secondaria
- Gestione traffico con archiviazione con ridondanza geografica consente di avere la stessa funzione in più aree in caso di errore a livello di area


![Gestione traffico](./media/migrate-best-practices-security-management/traffic-manager.png)
*Gestione traffico*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) sul ripristino di emergenza per le app di Azure.
- [Informazioni](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) sul ripristino di emergenza e sulla distribuzione geografica per funzioni di Azure durevoli.


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>Procedura consigliata: usare dischi gestiti e set di disponibilità

Azure usa i set di disponibilità per raggruppare a livello logico le macchine virtuali e isolare le macchine in un set da altre risorse. Le macchine virtuali in un set di disponibilità vengono distribuite tra più domini di errore con sottosistemi distinti per garantire la protezione da errori locali e vengono distribuite anche tra più domini di aggiornamento in modo da evitare il riavvio simultaneo di tutte le macchine virtuali in un set.

Azure Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure grazie alla gestione degli account di archiviazione associati ai dischi delle macchine virtuali.

- È consigliabile usare dischi gestiti laddove possibile. È sufficiente specificare il tipo di archiviazione che si intende usare e la dimensione del disco di cui si ha bisogno per consentire ad Azure di creare e gestire il disco autonomamente, dietro le quinte.
- È possibile convertire dischi esistenti in gestiti.
- È consigliabile creare macchine virtuali in set di disponibilità per disponibilità e resilienza elevate. Quando si verificano interruzioni pianificate o non pianificate, i set di disponibilità garantiscono la disponibilità continua di almeno una macchina virtuale nel set.


![Dischi gestiti](./media/migrate-best-practices-security-management/managed-disks.png)
*Dischi gestiti*

**Altre informazioni**:
- [Panoramica](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) di dischi gestiti.
- [Informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) sulla conversione di dischi in gestiti.
- [Informazioni](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) su come gestire la disponibilità delle macchine virtuali Windows in Azure.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Procedura consigliata: monitorare l'uso delle risorse e le prestazioni 

Si potrebbe decidere di spostare carichi di lavoro in Azure per via delle sue notevoli funzionalità di ridimensionamento. Tuttavia, con lo spostamento di un carico di lavoro, Azure non implementerà automaticamente il ridimensionamento senza l'input dell'utente. Ad esempio:

- Se un'organizzazione di marketing lancia un nuovo annuncio che genera un aumento di traffico del 300%, potrebbero verificarsi problemi in termini di disponibilità del sito. Il carico di lavoro appena migrato potrebbe raggiunge i limiti assegnati e potrebbe verificarsi un arresto anomalo.
- Un altro esempio potrebbe essere rappresentato da un attacco Distributed Denial of Service (DDoS) sul carico di lavoro migrato. In questo caso potrebbe essere preferibile non eseguire il ridimensionamento, ma impedire che l'origine degli attacchi raggiunga le risorse.

Questi due casi presentano risoluzioni diverse, ma per entrambi è necessario acquisire informazioni dettagliate sull'uso e sul monitoraggio delle prestazioni.

- Monitoraggio di Azure porta alla luce queste metriche e garantisce una risposta con avvisi, ridimensionamento automatico, hub eventi, app per la logica e altro ancora.
- Oltre al monitoraggio di Azure è possibile integrare l'applicazione SIEM di terze parti per monitorare i log di Azure per eventi a livello di prestazioni e controllo.


![Monitoraggio di Azure](./media/migrate-best-practices-security-management/monitor.png)
*Monitoraggio di Azure*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/azure-monitor/overview) su Monitoraggio di Azure.
- [Procedure consigliate](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) per monitoraggio e diagnostica.
- [Informazioni](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) sul ridimensionamento automatico.
- [Informazioni](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) su come indirizzare i dati di Azure a uno strumento SIEM.

## <a name="best-practice-enable-diagnostic-logging"></a>Procedura consigliata: Abilitare la registrazione diagnostica

Le risorse di Azure generano un numero notevole di metriche di registrazione e dati di telemetria.

- Per impostazione predefinita, nella maggior parte dei tipi di risorsa non è abilitata la registrazione diagnostica.
- Abilitando la registrazione diagnostica in tutte le risorse, è possibile eseguire query sui dati di registrazione e creare avvisi e playbook di conseguenza.
- Quando si abilita la registrazione diagnostica, ogni risorsa avrà un set specifico di categorie. Selezionare una o più categorie di registrazione e una posizione per i dati di log. È possibile inviare i log per un account di archiviazione, hub eventi, o per i log di monitoraggio di Azure. 


![Registrazione diagnostica](./media/migrate-best-practices-security-management/diagnostics.png)
*Registrazione diagnostica*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sulla raccolta e sull'uso dei dati di log.
- [Informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) sul supporto per la registrazione diagnostica.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Procedura consigliata: configurare avvisi e playbook

Con la registrazione diagnostica abilitata per le risorse di Azure è possibile iniziare a usare i dati di registrazione per creare avvisi personalizzati.

- Gli avvisi inviano notifiche in modo proattivo quando vengono riscontrate alcune condizioni nei dati di monitoraggio. È quindi possibile risolvere i problemi prima che gli utenti del sistema se ne accorgano. È possibile inviare avvisi su elementi quali valori delle metriche, query di ricerca log, eventi del log attività, integrità della piattaforma e disponibilità dei siti Web.
- Quando vengono attivati avvisi, è possibile eseguire un playbook di app per la logica. Un playbook consente di automatizzare e orchestrare una risposta a un avviso specifico. I playbook sono basati su App per la logica di Azure. È possibile usare modelli di App per la logica per creare playbook oppure crearne alcuni personalizzati.
- Un esempio semplice è rappresentato dalla possibilità di creare un avviso che si attiva quando si verifica l'analisi di una porta rispetto a un gruppo di sicurezza di rete.  È possibile configurare un playbook che esegua e blocchi l'indirizzo IP dell'origine dell'analisi.
- Un altro esempio potrebbe essere un'app con una perdita di memoria.  Quando l'uso della memoria raggiunge una determinata soglia, un playbook può riciclare il processo.


![Avvisi](./media/migrate-best-practices-security-management/alerts.png)
*Avvisi*

**Altre informazioni**:
- [Informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) sugli avvisi.
- [Informazioni](https://docs.microsoft.com/azure/security-center/security-center-playbooks) sui playbook di sicurezza che rispondono ad avvisi del Centro sicurezza.

## <a name="best-practice-use-the-azure-dashboard"></a>Procedura consigliata: usare il dashboard di Azure

Il portale di Azure è una console unificata basata sul Web che consente di creare, gestire e monitorare qualsiasi elemento, da semplici app Web ad applicazioni cloud complesse. Include un dashboard personalizzabile e opzioni per l'accessibilità.
- È possibile creare più dashboard e condividerli con altri utenti che hanno accesso alle sottoscrizioni di Azure.
- Con questo modello condiviso il team ha visibilità sull'ambiente di Azure e riesce ad essere proattivo nella gestione dei sistemi nel cloud.


![Dashboard di Azure](./media/migrate-best-practices-security-management/dashboard.png)
*Dashboard di Azure*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) su come creare un dashboard.
- [Informazioni](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) sulla struttura del dashboard.


## <a name="best-practice-understand-support-plans"></a>Procedura consigliata: comprendere i piani di supporto

A un certo punto sarà necessario collaborare con il personale del supporto dell'azienda o di Microsoft. È fondamentale disporre di un set di criteri e procedure per il supporto in uno scenario come quello di un ripristino di emergenza. È anche consigliabile garantire il training agli amministratori e al personale di supporto sull'implementazione di questi criteri.

- Nel caso improbabile che un problema di servizio di Azure influisca sul carico di lavoro, gli amministratori devono essere in grado di inviare un ticket di supporto a Microsoft nel modo più efficiente e appropriato.
- Acquisire familiarità con i vari piani di supporto offerti per Azure, che spaziano da tempi di risposta dedicati a istanze di sviluppatore al supporto tecnico Premier con un tempo di risposta inferiore a 15 minuti.


![Piani di supporto](./media/migrate-best-practices-security-management/support.png)
*Piani di supporto*

**Altre informazioni**:
- [Panoramica](https://azure.microsoft.com/support/options/) dei piani di supporto tecnico di Azure.
- [Informazioni](https://azure.microsoft.com/support/legal/sla/) sui contratti di servizio.

## <a name="best-practice-manage-updates"></a>Procedura consigliata: Gestire gli aggiornamenti

Garantire gli aggiornamenti software e del sistema operativo più recenti per le macchine virtuali di Azure è un'incombenza significativa. La capacità di rilevare tutte le macchine virtuali, individuare gli aggiornamenti di cui hanno bisogno ed eseguire il push automatico di questi aggiornamenti è particolarmente utile.

- Gestione aggiornamenti in Automazione di Azure consente di gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux distribuiti in Azure, in locale o in altri provider di servizi cloud. 
- Usare Gestione aggiornamenti per valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire l'installazione degli aggiornamenti.
- È possibile abilitare Gestione aggiornamenti per le macchine virtuali direttamente da un account di Automazione di Azure. È anche possibile aggiornare una singola macchina virtuale dalla relativa pagina nel portale di Azure.
- Le macchine virtuali di Azure possono essere registrate con System Center Configuration Manager. È quindi possibile eseguire la migrazione del carico di lavoro di Configuration Manager ad Azure ed effettuare operazioni di creazione di report e aggiornamenti software da una singola interfaccia Web.


![Aggiornamenti per macchine virtuali](./media/migrate-best-practices-security-management/updates.png)
*Aggiornamenti*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/automation/automation-update-management) sulla gestione degli aggiornamenti in Azure.
- [Informazioni](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) su come integrare Configuration Manager con la gestione degli aggiornamenti.
- [Domande frequenti](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) su Configuration Manager in Azure.


## <a name="implement-a-change-management-process"></a>Implementare un processo di gestione delle modifiche

Come in qualsiasi sistema di produzione, qualsiasi tipo di modifica può avere ripercussioni sull'ambiente. Un processo di gestione delle modifiche che prevede l'invio di richieste per poter apportare modifiche ai sistemi di produzione rappresenta una valida aggiunta a un ambiente migrato.

- È possibile creare framework di procedure consigliate per la gestione delle modifiche al fine di sensibilizzare amministratori e personale del supporto.
- È possibile usare Automazione di Azure per facilitare la gestione della configurazione e il rilevamento delle modifiche per i flussi di lavoro migrati.
- In fase di applicazione del processo di gestione delle modifiche è possibile usare i log di controllo per collegare i registri modifiche di Azure a richieste di modifica presumibilmente esistenti (o meno). In questo modo, se viene apportata una modifica senza una richiesta corrispondente, è possibile esaminare il problema nel processo.

Azure include la soluzione Rilevamento modifiche in Automazione di Azure:

- La soluzione rileva le modifiche apportate al software e ai file di Windows e Linux, alle chiavi del Registro di sistema, ai servizi di Windows e ai daemon Linux.
- Le modifiche nei server monitorati vengono inviate al servizio di monitoraggio di Azure nel cloud per l'elaborazione.
- Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud.
- Nel dashboard Rilevamento modifiche è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.


![Gestione delle modifiche](./media/migrate-best-practices-security-management/change.png)
*Gestione delle modifiche*

**Altre informazioni**:

- [Informazioni](https://docs.microsoft.com/azure/automation/automation-change-tracking) su Rilevamento modifiche.
- [Informazioni](https://docs.microsoft.com/azure/automation/automation-intro) sulle funzionalità di Automazione di Azure.




## <a name="next-steps"></a>Passaggi successivi 

Esaminare altre procedure consigliate:


- [Procedure consigliate](migrate-best-practices-networking.md) per le risorse di rete dopo la migrazione.
- [Procedure consigliate](migrate-best-practices-costs.md) per la gestione dei costi dopo la migrazione.








