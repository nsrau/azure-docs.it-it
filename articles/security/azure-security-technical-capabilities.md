---

title: "Funzionalità tecniche per la sicurezza di Azure | Microsoft Docs"
description: "Informazioni sui servizi di calcolo basati sul cloud che includono un&quot;ampia gamma di istanze e servizi di calcolo con scalabilità automatica per soddisfare le esigenze dell&quot;applicazione o dell&quot;organizzazione."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2017
ms.author: TomSh
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 549f11d3d55d13f3a2ddf7bd0a2bc58e527d1d11
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="azure-security-technical-capabilities"></a>Funzionalità tecniche per la sicurezza di Azure

Per consentire ai clienti attuali e potenziali di Azure di comprendere e utilizzare le diverse funzionalità correlate alla sicurezza disponibili nel contesto della piattaforma Azure, Microsoft ha sviluppato una serie di white paper, panoramiche sulla sicurezza, procedure consigliate ed elenchi di controllo. Gli argomenti variano per ampiezza e livello di approfondimento e vengono aggiornati periodicamente. Questo documento fa parte di tale serie, come descritto nella sezione di sintesi di seguito. Per altre informazioni su questa serie sulla sicurezza di Azure, vedere la pagina all'indirizzo (URL).

## <a name="azure-platform"></a>Piattaforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) è una piattaforma cloud costituita da servizi di infrastruttura e applicazioni, con servizi dati integrati, analisi avanzata e strumenti e servizi per sviluppatori, ospitata nei data center del cloud pubblico Microsoft. I clienti usano Azure per molti diversi scenari e capacità, dalle funzionalità di calcolo, rete e archiviazione di base a servizi app Web e per dispositivi mobili e scenari cloud completi come Internet delle cose, che possono essere usati con tecnologie open source e distribuiti come cloud ibrido oppure ospitati in un data center del cliente. Azure offre tecnologia cloud in blocchi predefiniti per consentire alle aziende di ridurre i costi, velocizzare l'innovazione e gestire i sistemi in modo proattivo. Quando si creano asset IT o se ne esegue la migrazione in un provider di servizi cloud, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli offerti per gestire la sicurezza degli asset basati sul cloud.

Microsoft Azure è l'unico provider di servizi di cloud computing che offre una piattaforma applicativa e un'infrastruttura distribuite come servizio coerenti e sicure affinché i team possano operare con diverse competenze di cloud e diversi livelli di complessità dei progetti, con servizi dati integrati e funzionalità di analisi che consentono di ottenere informazioni dai dati ovunque si trovino, in piattaforme sia Microsoft che non Microsoft e in strumenti e framework aperti, e la possibilità di integrare cloud e locale nonché distribuire i servizi cloud di Azure in data center locali. Nell'ambito di Microsoft Trusted Cloud, i clienti fanno affidamento su Azure per usufruire di livelli di sicurezza, affidabilità, conformità e riservatezza leader del settore, nonché di una vasta rete di persone, partner e processi per il supporto delle organizzazioni nel cloud.

Microsoft Azure consente di:

- Accelerare l'innovazione con il cloud.

- Basare le app e le decisioni aziendali su informazioni dettagliate.

- Creare con facilità e distribuire ovunque.

- Proteggere l'azienda.

## <a name="scope"></a>Scope

Questo white paper si concentra sulle caratteristiche e le funzionalità di sicurezza che supportano i componenti principali di Microsoft Azure, ossia [Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction), i [database SQL di Microsoft Azure](https://docs.microsoft.com/azure/sql-database/), il [modello delle macchine virtuali di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/    ) e gli strumenti e l'infrastruttura con cui tutto viene gestito. Questo white paper illustra le funzionalità tecniche di Microsoft Azure a disposizione dei clienti affinché possano svolgere il proprio ruolo nell'ambito della protezione della sicurezza e della riservatezza dei dati.

Comprendere questo modello di responsabilità condivisa è di importanza fondamentale per i clienti che passano al cloud. I provider di servizi cloud offrono notevoli vantaggi ai fini della sicurezza e della conformità, ma tali vantaggi non esentano il cliente dal proteggere i propri utenti, le proprie applicazioni e i servizi offerti.

Per le soluzioni IaaS, il cliente è responsabile o condivide la responsabilità di proteggere e gestire il sistema operativo, la configurazione di rete, le applicazioni, le identità, i client e i dati.  Per le soluzioni PaaS, basate su distribuzioni IaaS, il cliente è responsabile o condivide la responsabilità di proteggere e gestire le applicazioni, le identità, i client e i dati. Il cliente è comunque responsabile anche per le soluzioni SaaS. Deve garantire che i dati siano classificati correttamente e condivide la responsabilità della gestione degli utenti e dei dispositivi endpoint.

Questo documento non offre una descrizione dettagliata di componenti correlati della piattaforma Microsoft Azure come Siti Web di Azure, Azure Active Directory, HDInsight, Servizi multimediali e altri servizi basati sui componenti principali. Nonostante venga offerto un livello minimo di informazioni generali, si presuppone che i lettori abbiano familiarità con i concetti di base di Azure descritti in altri documenti di riferimento forniti da Microsoft e inclusi nei collegamenti disponibili in questo white paper.


## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Funzionalità tecniche per la sicurezza disponibili per le responsabilità dell'utente (cliente): quadro generale

Microsoft Azure offre servizi che consentono ai clienti di soddisfare le esigenze di sicurezza, riservatezza e conformità. L'immagine seguente illustra i vari servizi di Azure a disposizione degli utenti per creare un'infrastruttura delle applicazioni sicura e conforme basata sugli standard del settore.

![Funzionalità tecniche per la sicurezza disponibili: quadro generale](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Gestire e controllare le identità e l'accesso degli utenti (protezione)

Azure consente di proteggere le informazioni aziendali e personali gestendo le identità e le credenziali degli utenti e controllando l'accesso.

### <a name="azure-active-directory"></a>Azure Active Directory

Le soluzioni Microsoft di gestione degli accessi e delle identità consentono all'IT di proteggere l'accesso ad applicazioni e risorse nel data center aziendale e nel cloud, abilitando altri livelli di convalida, ad esempio Multi-Factor Authentication e criteri di accesso condizionale. Il monitoraggio delle attività sospette tramite funzioni avanzate di report di sicurezza, controllo e avvisi consente di attenuare i potenziali problemi di sicurezza. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) offre l'accesso Single Sign-On a migliaia di app cloud (SaaS) e alle app Web eseguite in locale.

I vantaggi della sicurezza di Azure Active Directory (AD) includono la possibilità di:

- Creare e gestire una singola identità per ogni utente in un'azienda ibrida, mantenendo sincronizzati utenti, gruppi e dispositivi.

- Offrire l'accesso Single Sign-On alle applicazioni, incluse migliaia di app SaaS preintegrate.

- Abilitare la sicurezza dell'accesso alle applicazioni grazie all'autenticazione a più fattori basata su regole per applicazioni locali e cloud.

- Consentire l'accesso remoto sicuro ad applicazioni Web locali con il proxy di applicazione di Azure AD.

Il [portale di Azure Active Directory](http://aad.portal.azure.com/) è disponibile nell'ambito del portale di Azure. Da questo dashboard è possibile ottenere una panoramica dello stato dell'organizzazione e passare facilmente alla gestione della directory, degli utenti o dell'accesso alle applicazioni.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Di seguito sono illustrate le funzionalità principali di gestione delle identità di Azure:

- Single sign-on

- Autenticazione a più fattori

- Monitoraggio della sicurezza, avvisi e report basati su Machine Learning

- Gestione delle identità e dell'accesso degli utenti

- Registrazione del dispositivo

- Privileged Identity Management

- Identity Protection

#### <a name="single-sign-on"></a>Single sign-on

L'accesso [Single Sign-On (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) consente di accedere a tutte le applicazioni e le risorse necessarie per le attività aziendali effettuando l'accesso una sola volta con un singolo account utente. Dopo aver effettuato l'accesso, è possibile accedere a tutte le applicazioni necessarie senza dover ripetere una seconda volta l'autenticazione (ad esempio, digitando una password).

Molte organizzazioni si basano su applicazioni software come un servizio (SaaS), come Office 365, Box e Salesforce, per la produttività degli utenti finali. In passato, il personale IT doveva creare e aggiornare singoli account utente in ogni applicazione SaaS e gli utenti dovevano ricordare una password per ogni applicazione SaaS.

[Azure AD estende l'istanza locale di Active Directory nel cloud](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), consentendo agli utenti di usare il proprio account aziendale principale non solo per accedere ai dispositivi appartenenti a un dominio e alle risorse della società, ma anche a tutte le applicazioni Web e SaaS necessarie per svolgere il proprio lavoro.

Non solo gli utenti non devono più gestire diversi set di nomi utente e password, ma è anche possibile eseguire il provisioning o deprovisioning automatico dell'accesso alle applicazioni in base ai gruppi aziendali e al relativo stato di dipendente. [Azure AD introduce controlli di governance per la sicurezza e l'accesso](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) che consentono di gestire in modo centralizzato l'accesso degli utenti nelle applicazioni SaaS.

#### <a name="multi-factor-authentication"></a>Autenticazione a più fattori

[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) è un metodo di autenticazione che richiede l'uso di più metodi di verifica e aggiunge un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. [MFA consente di proteggere](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso agli utenti una procedura di accesso semplice. Offre autenticazione avanzata tramite diverse opzioni di verifica, ad esempio una telefonata, un SMS, una notifica o un codice di verifica dell'app per dispositivi mobili e token OAuth di terze parti.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitoraggio della sicurezza, avvisi e report basati su Machine Learning

Monitoraggio della sicurezza, avvisi e report basati su Machine Learning che identificano i modelli di accesso non coerenti per contribuire alla protezione dell'azienda. È possibile usare i report di utilizzo e accesso di Azure Active Directory per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione. Con queste informazioni un amministratore di directory può stabilire meglio dove potrebbero esserci possibili rischi per la sicurezza in modo da poterne pianificare adeguatamente la riduzione.

Nel portale di Azure classico o nel [portale di Azure Active Directory](http://aad.portal.azure.com/), i [report](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) sono classificati come descritto di seguito.

- Report anomalie: contengono eventi di accesso considerati anomali. L'obiettivo è rendere gli utenti consapevoli di tali attività e consentire loro di stabilire se un evento è sospetto.

- Report applicazioni integrate: offrono informazioni dettagliate sull'uso delle applicazioni cloud nell'organizzazione. Azure Active Directory offre l'integrazione con migliaia di applicazioni cloud.

- Report di errori: segnalano gli errori che possono verificarsi durante il provisioning di account in applicazioni esterne.

- Report specifici dell'utente: visualizzano i dati del dispositivo/dell'attività di accesso per un utente specifico.

- Log attività: contengono un record di tutti gli eventi controllati nelle ultime 24 ore, negli ultimi 7 giorni o negli ultimi 30 giorni, nonché le modifiche alle attività del gruppo e le attività di registrazione e di reimpostazione password.

#### <a name="consumer-identity-and-access-management"></a>Gestione delle identità e dell'accesso degli utenti

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) è un servizio di gestione delle identità globale a disponibilità elevata per le applicazioni rivolte agli utenti, con scalabilità fino a centinaia di milioni di identità. Il servizio può essere integrato tra piattaforme mobili e Web. Gli utenti possono accedere a tutte le applicazioni attraverso esperienze personalizzabili usando gli account dei propri social network esistenti o creando nuove credenziali.

In passato, per ottenere [l'iscrizione e l'accesso degli utenti](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) alle applicazioni, gli sviluppatori di applicazioni scrivevano il proprio codice. E dovevano utilizzare database locali o sistemi per archiviare nomi utente e password. Azure Active Directory B2C offre all'organizzazione un modo migliore per integrare la gestione delle identità degli utenti nelle applicazioni con una piattaforma sicura basata sugli standard e un set completo di criteri estendibili.

Con Azure Active Directory B2C, gli utenti possono registrarsi alle applicazioni usando gli account di social networking esistenti (Facebook, Google, Amazon, LinkedIn) o creando nuove credenziali (indirizzo di posta elettronica e password o nome utente e password).

Registrazione del dispositivo

[Registrazione dispositivo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) rappresenta il fondamento per gli scenari di [accesso condizionale](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo Azure Active Directory fornisce al dispositivo un'identità che viene usata per autenticare il dispositivo quando l'utente esegue l'accesso. Il dispositivo autenticato e gli attributi del dispositivo possono quindi essere usati per imporre criteri di accesso condizionale per le applicazioni locali e ospitate nel cloud.

In combinazione con una soluzione di [gestione di dispositivi mobili (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) come Intune, gli attributi del dispositivo in Azure Active Directory vengono aggiornati con informazioni aggiuntive sul dispositivo. Ciò permette di creare regole di accesso condizionale che subordinano l'accesso dai dispositivi al rispetto dei propri standard di sicurezza e conformità.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) consente di gestire, controllare e monitorare le identità con privilegi e l'accesso alle risorse in Azure AD e in altri Microsoft Online Services, come Office 365 o Microsoft Intune.

In alcuni casi gli utenti hanno la necessità di eseguire operazioni privilegiate in risorse di Azure o Office 365 o altre app SaaS. Per questa ragione, è spesso necessario che le organizzazioni concedano agli utenti l'accesso con privilegi permanente in Azure AD. Questo rappresenta un rischio di sicurezza crescente per le risorse ospitate nel cloud poiché le organizzazioni non sono in grado di monitorare completamente le operazioni eseguite dagli utenti con i privilegi amministrativi. Inoltre, se un account utente con accesso privilegiato è compromesso, tale singola violazione può compromettere la sicurezza dell'intero cloud. Gestione identità con privilegi di Azure AD consente di risolvere questo rischio.

Azure AD Privileged Identity Management consente di effettuare le operazioni seguenti:

- Individuare gli utenti amministratori di Azure AD

- Abilitare l'accesso come amministratore Just-In-Time su richiesta ai Microsoft Online Services, ad esempio Office 365 e Intune

- Ottenere report sulla cronologia degli accessi degli amministratori e sulle modifiche alle assegnazioni degli amministratori

- Ricevere avvisi relativi all'accesso a un ruolo con privilegi

#### <a name="identity-protection"></a>Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) è un servizio di sicurezza che offre una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. Identity Protection usa le funzionalità esistenti di rilevamento anomalie di Azure Active Directory, disponibili tramite i report Anomalie dell'attività di Azure AD, e introduce nuovi tipi di eventi di rischio che consentono di rilevare le anomalie in tempo reale.

## <a name="secured-resource-access-in-azure"></a>Accesso protetto alle risorse in Azure

La fatturazione costituisce il punto di partenza per il controllo di accesso in Azure. Il proprietario di un account Azure, accessibile tramite il [Centro account di Azure](https://account.windowsazure.com/subscriptions), è l'amministratore account. Le sottoscrizioni sono contenitori per la fatturazione, ma vengono usate anche come limiti per la sicurezza: ogni sottoscrizione ha un Amministratore del servizio (SA) che può aggiungere, rimuovere e modificare le risorse di Azure nella sottoscrizione tramite il [portale di Azure classico](https://manage.windowsazure.com/). L'Amministratore del servizio predefinito di una nuova sottoscrizione è l'Amministratore dell'account, ma quest'ultimo può modificare l'Amministratore del servizio nel Centro account di Azure.

![Accesso protetto alle risorse in Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Le sottoscrizioni dispongono anche di un'associazione a una directory. Una directory definisce un set di utenti. Possono essere utenti dell'azienda o dell'istituto di istruzione che ha creato la directory oppure utenti esterni, ossia utenti con account Microsoft. Le sottoscrizioni sono accessibili da un sottogruppo di questi utenti della directory che sono stati nominati Amministratori del servizio (SA) o Co-amministratori (CA); l'unica eccezione è che, per motivi di compatibilità, gli account Microsoft (precedentemente Windows Live ID) possono essere nominati SA o CA senza essere presenti nella directory.

Le aziende orientate sulla sicurezza devono concedere ai propri dipendenti la quantità esatta di autorizzazioni di cui necessitano. un numero eccessivo di autorizzazioni può esporre un account agli attacchi. Un numero di autorizzazioni insufficiente impedisce ai dipendenti di svolgere il proprio lavoro in modo efficiente. Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) consente di risolvere questo problema offrendo una gestione specifica degli accessi per Azure.

![Accesso protetto alle risorse ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Usando il Controllo degli accessi in base al ruolo, è possibile separare i compiti all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni. Ad esempio, usare il Controllo degli accessi in base al ruolo per consentire a un dipendente di gestire le macchine virtuali in una sottoscrizione, mentre un altro utente può gestire i database SQL della stessa sottoscrizione.

![Accesso protetto alle risorse in Azure (controllo degli accessi in base al ruolo)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Sicurezza e crittografia dei dati in Azure (protezione)

Uno degli aspetti fondamentali della protezione dei dati nel cloud consiste nel tenere conto dei possibili stati in cui possono trovarsi i dati e dei controlli disponibili per tale stato. In relazione alle procedure consigliate per la sicurezza e la crittografia dei dati in Azure, le raccomandazioni riguardano gli stati dei dati seguenti.

- Inattivi: sono inclusi tutti gli oggetti, i contenitori e i tipi di archiviazione di informazioni esistenti in forma statica nei supporti fisici, siano essi dischi magnetici o dischi ottici.

- In transito: quando i dati vengono trasferiti dati tra componenti, posizioni o programmi, ad esempio sulla rete, attraverso un bus di servizio (da locale a cloud e viceversa, incluse le connessioni ibride come ExpressRoute) oppure durante un processo di input/output, sono considerati in movimento.

### <a name="encryption--rest"></a>Crittografia dei dati inattivi

Per ottenere la crittografia dei dati inattivi, sono disponibili le opzioni seguenti.

Per crittografare i dati, supportare almeno uno dei modelli di crittografia consigliati illustrati in dettaglio nella tabella seguente.

| Modelli di crittografia |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Crittografia server | Crittografia server | Crittografia server | Crittografia client
| Crittografia lato server con chiavi gestite dal servizio | Crittografia lato server con chiavi gestite dal cliente in Azure Key Vault | Crittografia lato server con chiavi gestite dal cliente in locale |
| •    I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia <br> •    Microsoft gestisce le chiavi <br>•    Funzionalità cloud complete | •    I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia<br>•    Il cliente controlla le chiavi tramite Azure Key Vault<br>•    Funzionalità cloud complete | •    I provider di risorse di Azure eseguono le operazioni di crittografia e decrittografia <br>•    Il cliente controlla le chiave in locale <br> •    Funzionalità cloud complete| •    I servizi di Azure non possono visualizzare i dati decrittografati <br>•    I clienti mantengono le chiavi in locale o in altri archivi sicuri. Le chiavi non sono disponibili per i servizi di Azure <br>•    Funzionalità cloud ridotte|

### <a name="enabling-encryption-at-rest"></a>Abilitazione della crittografia dei dati inattivi

**Identificare tutte le posizioni in cui sono archiviati i dati**

L'obiettivo della crittografia dei dati inattivi è crittografare tutti i dati. In questo modo si elimina la possibilità di non includere dati importanti o tutte le posizioni permanenti. Enumerare tutti i dati archiviati dall'applicazione. 

> [!Note] 
> Non solo i "dati applicazione" o le "informazioni personali", ma tutti i dati correlati all'applicazione, inclusi i metadati degli account (mapping delle sottoscrizioni, informazioni relative al contratto, informazioni personali).

Valutare quali risorse di archiviazione vengono usate per archiviare i dati, ad esempio:

- Risorsa di archiviazione esterna (ad esempio, SQL Azure, DocumentDB, HDInsight, Data Lake e così via)

- Risorsa di archiviazione temporanea (qualsiasi cache locale contenente i dati dei tenant)

- Cache in memoria (eventualmente inserita nel file di paging)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Sfruttare il supporto della crittografia dei dati inattivi esistente in Azure

Per ogni risorsa di archiviazione usata, sfruttare il supporto della crittografia dei dati inattivi esistente.

- Archiviazione di Azure: vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/storage-service-encryption)

- SQL Azure: vedere [Transparent Data Encryption (TDE) e SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Archiviazione in disco locale o VM: vedere [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

In caso di archiviazione in disco locale o VM usare Crittografia dischi di Azure, se supportato:

IaaS

I servizi con VM IaaS (Windows o Linux) devono usare [Crittografia dischi di Azure](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) per crittografare i volumi contenenti i dati del cliente.

PaaS v2

I servizi eseguiti in PaaS v2 usando Service Fabric possono usare Crittografia dischi di Azure per set di scalabilità di macchine virtuali per crittografare le VM PaaS v2.

PaaS v1

Crittografia dischi di Azure non è attualmente supportato in PaaS v1. Di conseguenza, è necessario usare la crittografia a livello di applicazione per crittografare i dati inattivi salvati in modo permanente,  che includono, tra gli altri, dati applicazione, file temporanei, log e dump di arresto anomalo del sistema.

La maggior parte dei servizi dovrà tentare di sfruttare la crittografia di un provider di risorse di archiviazione. Alcuni servizi devono eseguire la crittografia esplicita. Ad esempio, tutto il materiale delle chiavi salvato in modo permanente (certificati e chiavi radice/master) deve essere archiviato in Key Vault.

Se si supporta la crittografia lato servizio con chiavi gestite dal cliente, deve essere possibile per il cliente trasmettere la chiave a Microsoft. A tale scopo, è supportata e consigliata l'integrazione con Azure Key Vault (AKV). In questo caso, i clienti possono aggiungere e gestire le proprie chiavi in Azure Key Vault. Per informazioni su come usare AKV, vedere [Introduzione a Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Per l'integrazione con Azure Key Vault, si aggiunge codice per richiedere una chiave da AKV quando è necessaria per la decrittografia.

- Per informazioni su come eseguire l'integrazione con AKV, vedere [Azure Key Vault – Step by Step](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) (Azure Key Vault: istruzioni dettagliate).

Se si supportano chiave gestite dal cliente, è necessario fornire un'esperienza utente che consenta al cliente di specificare l'istanza o l'URI di Key Vault da usare.

Dato che la crittografia dei dati inattivi prevede la crittografia dei dati dell'host, dell'infrastruttura e dei tenant, la perdita delle chiavi a causa di un errore di sistema o di attività dannose potrebbe determinare la perdita di tutti i dati crittografati. È quindi essenziale che la soluzione di crittografia dei dati inattivi includa una strategia di ripristino di emergenza completa resiliente agli errori di sistema e alle attività dannose.

I servizi che implementano la crittografia dei dati inattivi, in genere, sono comunque vulnerabili se le chiavi di crittografia o i dati vengono mantenuti non crittografati nell'unità host, ad esempio nel file di paging del sistema operativo host. Di conseguenza, è necessario garantire la crittografia del volume host dei servizi. Per facilitare il compito, il team di calcolo ha abilitato la distribuzione della crittografia host, che usa la protezione di rete con chiave [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) ed estensioni dell'agente e del servizio DCM per crittografare il volume host.

La maggior parte dei servizi viene implementata in VM di Azure standard. Tali servizi dovrebbero ottenere automaticamente la [crittografia host](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) quando viene abilitata dal team di calcolo. Per i servizi eseguiti in cluster gestiti dal team di calcolo, la crittografia host viene abilitata automaticamente con l'implementazione di Windows Server 2016.

### <a name="encryption-in-transit"></a>Crittografia dei dati in transito

La protezione dei dati in transito deve essere una parte essenziale della strategia di protezione dati. Poiché i dati transitano in modo bidirezionale tra molte posizioni, in generale è consigliabile usare sempre i protocolli SSL/TLS per lo scambio di dati tra posizioni diverse. In alcuni casi è consigliabile isolare l'intero canale di comunicazione tra l'infrastruttura locale e cloud con una rete privata virtuale (VPN).

Per lo spostamento dei dati tra l'infrastruttura locale e Azure, è opportuno considerare le misure di protezione appropriate, ad esempio HTTPS o VPN.

Per le organizzazioni che devono proteggere l'accesso ad Azure da più workstation locali, usare una [VPN da sito a sito di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Per le organizzazioni che hanno bisogno di proteggere l'accesso ad Azure da una workstation locale, usare una [VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Set di dati più grandi possono essere spostati su un collegamento WAN ad alta velocità dedicato, ad esempio [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se si decide di usare ExpressRoute, è possibile anche crittografare i dati a livello di applicazione usando [SSL/TLS](https://support.microsoft.com/kb/257591) o altri protocolli per una maggiore protezione.

Se si interagisce con Archiviazione di Azure tramite il portale di Azure, tutte le transazioni hanno luogo tramite HTTPS. È possibile usare anche l'[API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd179355.aspx) su HTTPS per interagire con [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) e il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/).

Le organizzazioni che non riescono a proteggere i dati in transito sono più vulnerabili ad [attacchi man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e hijack della sessione. Questi attacchi possono essere il primo passo per ottenere l'accesso ai dati riservati.

Per altre informazioni sull'opzione VPN di Azure, vedere l'articolo [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Applicare la crittografia dei dati a livello di file

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa criteri di crittografia, identità e autorizzazione per proteggere i file e la posta elettronica. Azure RMS opera su più dispositivi, tra cui telefoni, tablet e PC, applicando una protezione all'interno e all'esterno dell'organizzazione. Questa funzionalità è resa possibile dal fatto che Azure RMS aggiunge un livello di protezione che rimane insieme ai dati, anche quando fuoriescono dai confini dell'organizzazione.

Quando si usa Azure RMS per proteggere i file, viene sfruttata una crittografia standard di settore con pieno supporto di [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Quando si usa Azure RMS per proteggere i dati, la protezione rimane associata al file anche se questo viene copiato in una risorsa di archiviazione che non è sotto il controllo dell'IT, ad esempio un servizio di archiviazione cloud. Lo stesso accade per i file condivisi tramite posta elettronica: il file viene protetto come allegato di un messaggio di posta elettronica, con istruzioni su come aprire l'allegato protetto.
Quando si pianifica l'adozione di Azure RMS, si consiglia quanto segue:

- Installare l'[app RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Questa app si integra con le applicazioni Office installando un componente aggiuntivo di Office che offre agli utenti un modo semplice per proteggere direttamente i file.

- Configurare le applicazioni e i servizi per supportare Azure RMS

- Creare [modelli personalizzati](https://technet.microsoft.com/library/dn642472.aspx) che rispecchiano i requisiti aziendali, ad esempio un modello per i dati riservati da applicare in tutti i messaggi di posta elettronica correlati a informazioni riservate.

Le organizzazioni che sono carenti a livello di [classificazione dei dati](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e protezione dei file potrebbero essere più soggette alla perdita di dati. In assenza di un'adeguata protezione per i file, le organizzazioni non riusciranno a ottenere informazioni di business dettagliate, monitorare i possibili abusi e impedire l'accesso non autorizzato ai file.

> [!Note]
> Per altre informazioni su Azure RMS, leggere l'articolo [Introduzione a Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Proteggere l'applicazione (protezione)
Mentre Azure è responsabile della sicurezza dell'infrastruttura e della piattaforma su cui l'applicazione viene eseguita, è responsabilità dell'utente proteggere l'applicazione stessa. In altre parole, è necessario sviluppare, distribuire e gestire il codice e i contenuti dell'applicazione in modo sicuro. In caso contrario, il codice o il contenuto dell'applicazione può essere vulnerabile alle minacce.

### <a name="web-application-firewall-waf"></a>Web application firewall (WAF)
[Web application firewall (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) è una funzionalità del [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni.

Il Web application firewall si basa sulle regole di [OWASP Core Rule Set](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9. Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Per citarne alcuni, tra i più comuni troviamo gli attacchi SQL injection e gli attacchi di scripting intersito. Impedire questo tipo di attacchi nel codice dell'applicazione può risultare un'operazione complessa e potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un Web application firewall centralizzato semplifica notevolmente la gestione della sicurezza e offre agli amministratori delle applicazioni migliori garanzie contro le minacce o le intrusioni. Una soluzione WAF è anche in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione di patch su una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web. È possibile convertire facilmente i gateway applicazione esistenti in un gateway applicazione con Web application firewall.

Le vulnerabilità Web per le quali Web application firewall offre protezione includono:

- Protezione dagli attacchi SQL injection

- Protezione dagli attacchi di scripting intersito

- Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

- Protezione dalle violazioni del protocollo HTTP

- Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

- Prevenzione contro robot, crawler e scanner

- Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via)

> [!Note]
> Per un elenco dettagliato delle regole e delle relative funzionalità di protezione, vedere i [set di regole principali](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets) seguenti.

Azure offre anche diverse funzionalità facili da usare che consentono di proteggere il traffico in ingresso e in uscita per l'app. Consente inoltre ai clienti di proteggere il codice delle proprie applicazioni grazie alla disponibilità di funzionalità esterne per eseguire l'analisi dell'applicazione Web e individuare eventuali vulnerabilità.

- [Proteggere l'app Web mediante vari meccanismi di autenticazione e autorizzazione](https://docs.microsoft.com/azure/app-service-web/web-sites-authentication-authorization)

    - [Configurare l'autenticazione di Azure Active Directory per l'app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)


- [Proteggere il traffico per l'app abilitando Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service-web/web-sites-configure-ssl-certificate)

    - [Forzare tutto il traffico in ingresso tramite connessione HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Abilitare Strict Transport Security (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)


- [Limitare l'accesso all'app tramite indirizzo IP del client](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Limitare l'accesso all'app tramite comportamento del client: frequenza e concorrenza delle richieste](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Analizzare il codice dell'app Web per le vulnerabilità tramite Tinfoil Security Scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configurare l'autenticazione reciproca TLS per richiedere certificati client e connettersi all'app Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)

- [Configurare un certificato client da usare nell'app e connettersi in modo sicuro a risorse esterne](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Rimuovere le intestazioni standard dei server e impedire agli strumenti di rilevare l'impronta digitale dell'app](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Connettere in modo sicuro l'app alle risorse in una rete privata usando una VPN da punto a sito](https://docs.microsoft.com/azure/app-service-web/web-sites-integrate-with-vnet)

- [Connettere in modo sicuro l'app alle risorse in una rete privata usando connessioni ibride](https://docs.microsoft.com/azure/app-service-web/web-sites-hybrid-connection-get-started)

Il servizio app di Azure offre la stessa soluzione Antimalware usata da Servizi cloud e Macchine virtuali di Azure. Per altre informazioni, vedere la [documentazione di Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Proteggere la rete (protezione)
Microsoft Azure include una solida infrastruttura di rete per supportare i requisiti di connettività di applicazioni e servizi. La connettività di rete è possibile tra le risorse disponibili in Azure, le risorse locali e quelle ospitate in Azure, nonché da e verso Internet e Azure.

L'[infrastruttura di rete di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) consente di connettere tra loro le risorse di Azure in modo sicuro con [reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico della rete nel cloud di Azure dedicato alla sottoscrizione. È possibile connettere le reti virtuali alle reti locali.

![Proteggere la rete (protezione)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Se è necessario un controllo di accesso di base a livello di rete, basato sull'indirizzo IP e sul protocollo TCP o UDP, è possibile usare [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Un gruppo di sicurezza di rete (NSG) è un firewall di filtro dei pacchetti con stato di base e consente di gestire l'accesso sulla base di una [5-tupla](https://www.techopedia.com/definition/28190/5-tuple).

La rete di Azure supporta la personalizzazione del comportamento di routing per il traffico di rete nelle reti virtuali di Azure. A tale scopo è possibile configurare [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) in Azure.

[tunneling forzato](https://www.petri.com/azure-forced-tunneling) è un meccanismo che può essere usato per assicurarsi che ai servizi sia impedito di stabilire una connessione a dispositivi in Internet.

Azure supporta la connettività con collegamento WAN dedicato alla rete locale e una rete virtuale di Azure con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Il collegamento tra Azure e il sito dell'utente si avvale di una connessione dedicata che non usa la rete Internet pubblica. Se l'applicazione Azure è in esecuzione in più data center, è possibile usare [Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) per indirizzare le richieste degli utenti in modo intelligente tra le istanze dell'applicazione. È anche possibile instradare il traffico ai servizi non in esecuzione in Azure, se sono accessibili da Internet.

## <a name="virtual-machine-security-protect"></a>Sicurezza delle macchine virtuali (protezione)

[Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/) consente di distribuire in modo flessibile un'ampia gamma di soluzioni di calcolo. Grazie al supporto per Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Servizi BizTalk di Azure, è possibile distribuire qualsiasi carico di lavoro, in qualunque linguaggio, praticamente su tutti i sistemi operativi.

Con Azure è possibile usare [software antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) di fornitori di soluzioni di sicurezza come Microsoft, Symantec, Trend Micro e Kaspersky per proteggere le macchine virtuali da file dannosi, adware e altre minacce.

Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione in tempo reale che aiuta a identificare e rimuovere virus, spyware e altro software dannoso. Microsoft Antimalware offre avvisi configurabili quando software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure.

[Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) è una soluzione scalabile che protegge i dati delle applicazioni senza investimenti di capitale e con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette.

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) consente di orchestrare la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una località secondaria in caso di inattività di quella primaria.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Garantire la conformità: elenco di controllo della due diligence per i servizi cloud (protezione)

Microsoft ha sviluppato l'[elenco di controllo della due diligence per i servizi cloud](https://aka.ms/cloudchecklist.download) per consentire alle organizzazioni di applicare la due diligence nel valutare la possibilità di passare al cloud. L'elenco di controllo offre alle organizzazioni di qualsiasi tipo e dimensione (sia aziende private che organizzazioni del settore pubblico, inclusi enti pubblici di tutti i livelli e organizzazioni no profit) una struttura per identificare i propri obiettivi e requisiti di prestazioni, servizio, gestione dati e governance. Ciò consente di confrontare le offerte di diversi provider di servizi cloud, ottenendo così una base per un contratto per servizi cloud.

L'elenco di controllo offre una struttura allineata, clausola per clausola, al nuovo standard internazionale per i contratti per servizi cloud, ISO/IEC 19086. Questo standard offre alle organizzazioni un set unificato di considerazioni per facilitare le decisioni in merito all'adozione del cloud e creare un terreno comune per confrontare le offerte di servizi cloud.

L'elenco di controllo promuove un passaggio al cloud accuratamente ponderato, offrendo indicazioni strutturate e un approccio ripetibile e coerente per la scelta di un provider di servizi cloud.

L'adozione del cloud non è più semplicemente una decisione relativa alla tecnologia. Riguardando ogni aspetto di un'organizzazione, i requisiti dell'elenco di controllo coinvolgono tutti i principali decision maker interni: CIO, CISO e i professionisti responsabili degli affari legali, della gestione dei rischi, dell'approvvigionamento e della conformità. In questo modo, aumenta l'efficienza del processo decisionale e le decisioni risultano fondate su valide argomentazioni, con conseguente riduzione delle probabilità di ostacoli imprevisti all'adozione.

Inoltre, l'elenco di controllo:

- Espone i principali argomenti di discussione per i decision maker all'inizio del processo di adozione del cloud.

- Supporta discussioni aziendali esaustive sulle normative e sugli obiettivi specifici dell'organizzazione in materia di riservatezza, informazioni personali e sicurezza dei dati.

- Consente di identificare i potenziali problemi che potrebbero influire su un progetto cloud.

- Offre un set coerente di domande, con termini, definizioni, metriche e risultati finali identici per ogni provider, per semplificare il processo di confronto delle offerte di diversi provider di servizi cloud.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Convalida della sicurezza delle applicazioni e dell'infrastruttura di Azure (rilevamento)

La [sicurezza operativa di Azure](https://docs.microsoft.com/azure/security/azure-operational-security) include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Microsoft Azure.

![Convalida della sicurezza (rilevamento)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

La sicurezza operativa di Azure è basata su un framework che incorpora le conoscenze acquisite tramite varie funzionalità esclusive di Microsoft, tra cui Microsoft Security Development Lifecycle (SDL), il programma Microsoft Security Response Center e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) è la soluzione di gestione IT per il cloud ibrido. Usato autonomamente o come estensione per la distribuzione esistente di System Center, OMS offre la massima flessibilità e il massimo controllo per la gestione basata su cloud dell'infrastruttura.

![Microsoft Operations Management Suite (OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Con OMS, è possibile gestire qualsiasi istanza di qualsiasi cloud, inclusi i cloud locali, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a un costo inferiore rispetto alle soluzioni della concorrenza. OMS, realizzato per le realtà orientate principalmente al cloud, offre un nuovo approccio alla gestione dell'azienda, vale a dire il modo più veloce e più economico per affrontare nuove problematiche aziendali e gestire nuovi carichi di lavoro, applicazioni e ambienti cloud.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Questi dati possono includere eventi, dati sulle prestazioni o dati personalizzati forniti tramite l'API. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Questo metodo consente di consolidare i dati di diverse origini per poter combinare i dati dei servizi di Azure con l'ambiente locale esistente. Separa anche nettamente la raccolta dei dati dall'azione eseguita su tali dati in modo che tutte le azioni siano disponibili per tutti i tipi di dati.

### <a name="azure-security-center"></a>Centro sicurezza di Azure

Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) consente di prevenire, rilevare e rispondere alle minacce con un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Un elenco di suggerimenti illustra in dettaglio il processo di configurazione dei controlli necessari.

Tra gli esempi sono inclusi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso

- Configurazione dei gruppi di sicurezza di rete e delle regole per controllare il traffico verso le VM

- Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web

- Distribuzione degli aggiornamenti di sistema mancanti

- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

- VM compromesse in comunicazione con indirizzi IP dannosi noti

- Malware avanzato rilevato tramite Segnalazione errori Windows

- Attacchi di forza bruta alle VM

- Avvisi di sicurezza da programmi antimalware e firewall integrati

### <a name="azure-monitor"></a>Monitoraggio di Azure

[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) offre collegamenti a informazioni su tipi di risorse specifici. Offre anche funzionalità di visualizzazione, query, routing, avviso, ridimensionamento automatico e automazione dei dati sia dall'infrastruttura di Azure (log attività) che da ogni singola risorsa di Azure (log di diagnostica).

Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti.

![Monitoraggio di Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) È anche possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

Il controllo della sicurezza della rete è fondamentale per rilevare le vulnerabilità e garantire la conformità con il modello di governance normativo e della sicurezza IT. Con la visualizzazione Gruppo di sicurezza, è possibile recuperare il gruppo di sicurezza di rete e le regole di sicurezza configurati, nonché le regole di sicurezza effettive. Con l'elenco delle regole applicate, è possibile determinare le porte aperte e valutare la vulnerabilità della rete.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di rete da, verso e in Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure. Il servizio include l'acquisizione pacchetti, l'hop successivo, la verifica del flusso IP, la visualizzazione dei gruppi di sicurezza e i registri dei flussi dei gruppi di sicurezza di rete. A differenza del monitoraggio a livello di singole risorse di rete, il monitoraggio a livello di scenario consente una visualizzazione completa delle risorse di rete.

### <a name="storage-analytics"></a>Analisi archiviazione

[Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) può archiviare metriche che includono statistiche delle transazioni aggregate e dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate sia a livello di operazione API, sia a livello di servizio di archiviazione, mentre la capacità viene segnalata a livello di servizio di archiviazione. I dati delle metriche possono essere utilizzati per analizzare l'uso del servizio di archiviazione, diagnosticare i problemi relativi alle richieste effettuate al servizio di archiviazione e per migliorare le prestazioni delle applicazioni che usano un servizio.

### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Il servizio rileva automaticamente le anomalie nelle prestazioni e include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come viene usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità. Funziona per le app su un'ampia gamma di piattaforme, tra cui .NET o J2EE, ospitate in locale o nel cloud. Si integra con il processo DevOps e offre punti di connessione per diversi strumenti di sviluppo.

Esegue il monitoraggio di:

- **Frequenza delle richieste, tempi di risposta e percentuali di errore**: trovare le pagine più visitate, gli orari di visita e la posizione degli utenti.  Vedere quali pagine abbiano prestazioni migliori. Se i tempi di risposta e le percentuali di errore aumentano di pari passo con le richieste, è probabile che ci sia un problema di assegnazione delle risorse.

- **Tassi di dipendenza, tempi di risposta e percentuali di errore**: trovare quali servizi esterni causino un rallentamento.

- **Eccezioni**: analizzare le statistiche aggregate o selezionare istanze specifiche e approfondire l'analisi dello stack e le richieste correlate. Vengono segnalate eccezioni di server e browser.

- **Visualizzazioni pagina e prestazioni di carico**, segnalate dai browser degli utenti.

- **Chiamate AJAX da pagine Web**: frequenza, tempi di risposta e percentuali di errore.

- **Conteggi degli utenti e delle sessioni**.

- **Contatori delle prestazioni** dai computer server Windows o Linux, ad esempio l'uso di CPU, memoria e rete.

- **Diagnostica dell'host** da Docker o Azure.

- **Log di traccia di diagnostica** dall'app, in modo da poter correlare gli eventi di traccia con le richieste.

- **Eventi e metriche personalizzati** scritti dall'utente stesso nel codice del client o del server per tenere traccia di eventi aziendali come gli articoli venduti o delle partite vinte.
L'infrastruttura per l'applicazione è in genere costituita da vari componenti, ad esempio una macchina virtuale, un account di archiviazione e una rete virtuale oppure un'app Web, un database, un server di database e servizi di terze parti. Questi componenti non appaiono come entità separate, ma come parti correlate e interdipendenti di una singola entità e devono essere distribuite, gestite e monitorate come gruppo. [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) consente di usare le risorse incluse nella soluzione come un gruppo.

È possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. Per la distribuzione viene usato un modello; questo modello può essere usato per diversi ambienti, ad esempio di testing, staging e produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione.

**Vantaggi offerti dall'uso di Resource Manager**

Gestione risorse offre numerosi vantaggi:

- È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.

- È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

- È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.

- È possibile definire le dipendenze tra le risorse in modo che vengano distribuite nell'ordine corretto.

- è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.

- È possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.

- È possibile ottenere informazioni dettagliate sulla fatturazione per l'organizzazione visualizzando i costi di un gruppo di risorse che condividono lo stesso tag.

> [!Note]
> Gestione risorse offre un nuovo modo per distribuire e gestire le soluzioni. Per informazioni sulle modifiche introdotte rispetto al modello di distribuzione precedente, vedere l'articolo contenente le [informazioni su distribuzione classica e Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza, vedere alcuni degli approfondimenti sull'argomento:

- [Controllo e registrazione](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Crimine informatico](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Progettazione e sicurezza operativa](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Crittografia](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gestione delle identità e dell'accesso](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Sicurezza di rete](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gestione delle minacce](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)

