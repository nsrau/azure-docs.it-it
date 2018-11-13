---
title: Connettere Active Directory ad Azure Active Directory. | Microsoft Docs
description: Azure AD Connect integra le directory locali con Azure Active Directory. Consente di fornire un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD.
keywords: introduzione ad Azure AD Connect, panoramica di Azure AD Connect, che cos'è Azure AD Connect, installare active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/02/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2aca42c23cc213d5d7e451105052d5d5d697b77d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979472"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>Identità ibrida e soluzioni Microsoft per la gestione delle identità
Le soluzioni ibride di gestione delle identità di [Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) consentono di sincronizzare oggetti di directory locali con Azure AD gestendo al contempo gli utenti in locale. La prima decisione da adottare quando si intende sincronizzare Windows Server Active Directory in locale con Azure AD è se usare le identità gestite o l'identità federata. 

- **Identità gestite**: account utente e gruppi sincronizzati da un ambiente Active Directory in locale; l'autenticazione degli utenti viene gestita da Azure.   
- Le **identità federate** garantiscono maggiore controllo sugli utenti separando l'autenticazione utente da Azure e delegandola a un provider di identità locale attendibile. 

Sono disponibili diverse opzioni per la configurazione della gestione di identità ibrida. Quando si valuta il modello di identità migliore in base alle esigenze specifiche dell'organizzazione, è necessario considerare fattori come il tempo, l'infrastruttura esistente, la complessità e i costi. Questi fattori variano in base all'organizzazione e possono cambiare nel tempo. Tuttavia, se i requisiti cambiano, si dispone della flessibilità per passare a un modello di gestione delle identità diverso.

## <a name="managed-identity"></a>Identità gestita 

L'identità gestita è il modo più semplice per sincronizzare gli oggetti di directory locali (utenti e gruppi) con Azure AD. 

![Identità ibrida sincronizzata](./media/whatis-hybrid-identity/managed.png)

Benché l'identità gestita sia il metodo più semplice e rapido, gli utenti devono tuttavia mantenere una password diversa per le risorse basate su cloud. Per evitare questo problema, è possibile anche (facoltativamente) [sincronizzare un hash delle password utente](how-to-connect-password-hash-synchronization.md) nella directory di Azure AD. La sincronizzazione di hash delle password consente agli utenti di accedere alle risorse aziendali basate su cloud con lo stesso nome utente e password usati in locale. Azure AD Connect controlla periodicamente la directory locale per rilevare eventuali modifiche e mantiene sincronizzata la directory di Azure AD. Quando cambia un attributo utente o una password in Active Directory locale, il valore viene aggiornato automaticamente in Azure AD. 

Per la maggior parte delle organizzazioni che ha solo la necessità di consentire agli utenti di accedere a Office 365, alle applicazioni SaaS e ad altre risorse basate su Azure AD, è consigliabile l'opzione di sincronizzazione dell'hash delle password predefinita. Se questa opzione non è adatta, è necessario scegliere tra l'autenticazione pass-through e AD FS.

> [!TIP]
> Le password utente vengono archiviate in Windows Server Active Directory locale sotto forma di un valore hash che rappresenta la password utente effettiva. Un valore hash è il risultato di una funzione matematica unidirezionale, chiamata algoritmo di hash. Non esiste un metodo per ripristinare la versione in testo normale di una password dal risultato di una funzione unidirezionale. Non è possibile usare l'hash della password per accedere alla rete locale. Se si sceglie di sincronizzare le password, Azure AD Connect estrae gli hash delle password da Active Directory locale e applica un'elaborazione della sicurezza aggiuntiva all'hash delle password prima della sincronizzazione in Azure AD. La sincronizzazione dell'hash delle password può essere usata anche insieme al writeback delle password per consentire la reimpostazione autonoma delle password in Azure AD. È possibile abilitare l'accesso Single Sign-On anche per gli utenti di computer aggiunti al dominio connessi alla rete aziendale. Con l'accesso Single Sign-On, gli utenti abilitati possono accedere in modo sicuro alle risorse cloud immettendo semplicemente un nome utente. 
>

## <a name="pass-through-authentication"></a>Autenticazione pass-through

L'[autenticazione pass-through di Azure AD](how-to-connect-pta.md) offre una soluzione di convalida delle password semplice per i servizi basati su Azure AD usando Active Directory locale. È consigliabile usare l'autenticazione pass-through se i criteri di conformità e sicurezza per l'organizzazione non consentono l'invio di password degli utenti, anche in formato hash, ed è sufficiente supportare l'accesso SSO desktop per i dispositivi appartenenti a un dominio. L'autenticazione pass-through non richiede la distribuzione nella rete perimetrale, semplificando l'infrastruttura di distribuzione rispetto ad AD FS. Quando gli utenti eseguono l'accesso usando Azure AD, questo metodo di autenticazione convalida le loro password direttamente con l'istanza locale di Active Directory.

![Autenticazione pass-through](./media/whatis-hybrid-identity/pass-through-authentication.png)

Con l'autenticazione pass-through non è necessaria un'infrastruttura di rete complessa e non è necessario archiviare le password locali nel cloud. Combinata con l'accesso Single Sign-On, l'autenticazione pass-through offre un'esperienza realmente integrata in fase di accesso ad Azure AD o ad altri servizi cloud.

L'autenticazione pass-through può essere configurata tramite Azure AD Connect e usa un semplice agente locale che rimane in ascolto delle richieste di convalida delle password. L'agente può essere facilmente distribuito su più computer per garantire un'elevata disponibilità e bilanciamento del carico. Dal momento che tutte le comunicazioni avvengono solo in uscita, non esiste alcun requisito per l'installazione del connettore in una rete perimetrale. Di seguito sono riportati i requisiti del computer server per il connettore:

- Windows Server 2012 R2 o versione successiva
- Aggiunta a un dominio nella foresta tramite cui gli utenti sono convalidati

## <a name="federated-identity-ad-fs"></a>Identità federata (AD FS)

Per un maggiore controllo sul modo in cui gli utenti accedono a Office 365 e ad altri servizi cloud, è possibile impostare la sincronizzazione delle directory con l'accesso Single Sign-On (SSO) usando [Active Directory Federation Services (ADFS)](how-to-connect-fed-whatis.md). La federazione degli accessi dell'utente con AD FS delega l'autenticazione a un server locale che convalida le credenziali utente. In questo modello, le credenziali di Active Directory locale non vengono mai passate ad Azure AD.

![Identità federata](./media/whatis-hybrid-identity/federated-identity.png)

Questo metodo di accesso, noto anche come federazione delle identità, assicura che tutte le autenticazioni utente vengano controllate in locale e consente agli amministratori di implementare livelli di controllo dell'accesso più rigorosi. La federazione delle identità con AD FS è l'opzione più complessa e richiede la distribuzione di server aggiuntivi nell'ambiente locale. La federazione delle identità impegna a fornire il supporto 24 ore su 24, 7 giorni su 7 per l'infrastruttura AD FS e Active Directory. Questo livello elevato di supporto è necessario perché se non sono disponibili l'accesso a Internet locale, il controller di dominio o i server AD FS, gli utenti non possono accedere ai servizi cloud.

> [!TIP]
> Se si decide di usare la federazione con Active Directory Federation Services (AD FS), è possibile configurare facoltativamente la sincronizzazione dell'hash delle password come backup in caso di errore dell'infrastruttura di AD FS.
>

## <a name="common-scenarios-and-recommendations"></a>Scenari comuni e raccomandazioni

Di seguito sono riportati alcuni scenari comuni di gestione di identità ibride e degli accessi con raccomandazioni riguardo all'opzione o alle opzioni di gestione delle identità ibride più appropriate per ciascuno di essi.

|Esigenza:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Sincronizzare automaticamente nel cloud nuovi account utente, di contatti o di gruppo creati in Active Directory locale.|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Configurare il tenant per scenari ibridi di Office 365|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Consentire agli utenti di accedere ai servizi cloud usando la propria password locale|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Implementare l'accesso SSO usando le credenziali aziendali|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Assicurarsi che gli hash delle password non vengano archiviati nel cloud| |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Abilitare soluzioni di autenticazione a più fattori nel cloud| |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Abilitare soluzioni di autenticazione a più fattori locali| | |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Supportare l'autenticazione di smart card per gli utenti<sup>4</sup>| | |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|
|Visualizzare le notifiche di scadenza delle password nel portale di Office e sul desktop di Windows 10| | |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Sincronizzazione dell'hash delle password con accesso Single Sign-On.
>
> <sup>2</sup> Autenticazione pass-through e accesso Single Sign-On. 
>
> <sup>3</sup> Accesso Single Sign-On federato con AD FS.
>
> <sup>4</sup> ADFS può essere integrato con l'infrastruttura a chiave pubblica aziendale per consentire l'accesso usando certificati. Questi certificati possono essere certificati software distribuiti tramite canali di provisioning attendibili, ad esempio i certificati di gestione di dispositivi mobili (MDM) o l'oggetto Criteri di gruppo o smart card (comprese le schede PIV/CAC) o Hello for Business (cert-trust). Per altre informazioni sul supporto dell'autenticazione con smart card, vedere [questo blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="what-is-azure-ad-connect"></a>Cos'è Azure AD Connect?

Azure AD Connect è lo strumento di Microsoft progettato per soddisfare e raggiungere gli obiettivi relativi alla soluzione ibrida di gestione delle identità.  Consente quindi di fornire agli utenti un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD.  Offre le funzionalità seguenti:
    
- [Sincronizzazione](how-to-connect-sync-whatis.md): questo componente è responsabile della creazione di utenti, gruppi e altri oggetti. Deve anche garantire che le informazioni sulle identità per utenti e gruppi locali corrispondano a quelle nel cloud.  È responsabile della sincronizzazione degli hash delle password con Azure AD.
- [Sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md): componente facoltativo che consente agli utenti di usare la stessa password in locale e nel cloud sincronizzando un hash della password utente con Azure AD.
-   [AD FS e integrazione della federazione](how-to-connect-fed-whatis.md): la federazione è una parte facoltativa di Azure AD Connect e può essere usata per configurare un ambiente ibrido usando un'infrastruttura AD FS locale. Fornisce anche funzionalità di gestione di AD FS, ad esempio rinnovo dei certificati e distribuzioni aggiuntive di server AD FS.
-   [Autenticazione pass-through](how-to-connect-pta.md): un altro componente facoltativo che consente agli utenti di usare la stessa password in locale e nel cloud, ma non richiede l'infrastruttura aggiuntiva di un ambiente federato
-   [PingFederate e integrazione della federazione](how-to-connect-install-custom.md#configuring-federation-with-pingfederate): altra opzione di federazione che consente di usare PingFederate come provider di identità.
-   [Monitoraggio dell'integrità](whatis-hybrid-identity-health.md): Azure AD Connect Health può offrire un monitoraggio affidabile e una posizione centralizzata nel portale di Azure per visualizzare questa attività. 


![Cos'è Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Cos'è Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione. Consente di mantenere una connessione affidabile a Office 365 e Microsoft Online Services offrendo capacità di monitoraggio per i componenti chiave relativi all'identità, ad esempio i server Active Directory Federation Services (AD FS), i server Azure AD Connect (noto anche come motore di sincronizzazione), i controller di dominio di Active Directory e così via. Rende anche facilmente accessibili i punti dati chiave relativi a questi componenti, per poter recuperare dati sull'utilizzo e altre informazioni importanti e utili per prendere decisioni informate.

Queste informazioni vengono visualizzate nel [portale di Azure AD Connect Health](https://aka.ms/aadconnecthealth). Nel portale di Azure AD Connect Health è possibile visualizzare avvisi, informazioni sul monitoraggio delle prestazioni, analisi sull'utilizzo e altre informazioni. Azure AD Connect Health abilita la singola sezione dell'integrità per i componenti chiave per l'identità, in un'unica posizione.

![Cos’è Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Per quanto aumentino le funzionalità in Azure AD Connect Health, il portale offre un unico dashboard nella sezione dell'identità. L'ambiente risultante è ancora più affidabile, integro e integrato e offre agli utenti maggiori possibilità di eseguire le operazioni necessarie.


## <a name="why-use-azure-ad-connect"></a>Perché usare Azure AD Connect?
L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi, in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Utenti e organizzazioni possono sfruttare i vantaggi seguenti:

* Gli utenti possono usare un'unica identità per accedere alle applicazioni locali e ai servizi cloud, come ad esempio Office 365.
* Un unico strumento che offre un'esperienza di distribuzione semplificata per la sincronizzazione e l'accesso.
* Offre le funzionalità più recenti per gli scenari in uso. Azure AD Connect sostituisce le versioni precedenti degli strumenti di integrazione delle identità, ad esempio DirSync e Azure AD Sync. Per altre informazioni, vedere [Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Perché usare Azure AD Connect Health
Quando si integrano le directory locali con Azure AD, gli utenti sono più produttivi perché è disponibile un'identità comune per accedere alle risorse cloud e locali. Tuttavia, questa integrazione comporta la difficoltà di garantire che questo ambiente sia integro, per consentire agli utenti di accedere in modo affidabile alle risorse locali e cloud da qualsiasi dispositivo. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di identità locale usata per accedere a Office 365 o ad altre applicazioni Azure AD. È semplice come installare un agente in ogni server di identità locale in uso.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health per AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health per AD FS supporta AD FS 2.0 su Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Consente anche il monitoraggio dei server proxy AD FS o di applicazione Web che offrono il supporto per l'autenticazione per l'accesso alla rete Extranet. Con un'installazione semplice e rapida dell'agente per l'integrità, Azure AD Connect Health per AD FS offre un set di funzionalità chiave.

#### <a name="key-benefits-and-best-practices"></a>Vantaggi chiave e procedure consigliate

- *Sicurezza avanzata*
  - [Tendenze del blocco Extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Report sugli accessi non riusciti](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Conformità alla privacy](reference-connect-health-user-privacy.md)    
- *Avvisi su tutti i [problemi del sistema ADFS critici](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Configurazione e disponibilità del server 
    - [Prestazioni e connettività](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Manutenzione regolare    
- *Semplicità di distribuzione e gestione*
  - [Installazione dell'agente](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) rapida 
  - Aggiornamento automatico alla versione più recente dell'agente 
  - Dati disponibili nel portale entro pochi minuti    
- *[Metriche di utilizzo](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs) avanzate* 
  - Utilizzo delle applicazioni principali
  - Percorsi di rete e connessione TCP
  - Richieste di token per ogni server    
- *Miglioramento dell'esperienza utente* 
  - Modalità dashboard dal portale di Azure
  - [Avvisi tramite messaggi di posta elettronica](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Funzionalità principali

*   Monitoraggio tramite avvisi per segnalare quando i server AD FS e proxy AD FS non sono integri.
*   Notifiche tramite posta elettronica per gli avvisi critici
*   Tendenze nei dati delle prestazioni, utili per la pianificazione delle capacità di AD FS.
*   Analisi di utilizzo per gli accessi ad AD FS con pivot (app, utenti, percorso di rete e così via)
*   Report per AD FS, relativi ad esempio ai primi 50 utenti che hanno tentativi di nome utente/password non riusciti e l'ultimo indirizzo IP.
*   Report relativi agli indirizzi IP rischiosi per accessi ad AD FS non riusciti

Per altre informazioni, vedere [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
Azure AD Connect Health per la sincronizzazione monitora e fornisce informazioni sulle sincronizzazioni che si verificano tra Active Directory locale e Azure AD. Azure AD Connect Health per la sincronizzazione fornisce il set di funzionalità chiave seguente:

* Monitoraggio degli avvisi per sapere quando un server di Azure AD Connect, noto anche come motore di sincronizzazione, non è integro.
* Notifiche tramite posta elettronica per gli avvisi critici
* Informazioni operative approfondite sulla sincronizzazione, che includono grafici della latenza per le operazioni di sincronizzazione e tendenze relative a varie operazioni, come aggiunta, aggiornamento o eliminazione
* Riepilogo rapido delle informazioni sulle proprietà di sincronizzazione e sull'ultima esportazione riuscita in Azure AD
* Report sugli errori di sincronizzazione a livello di oggetto \(non è necessario Azure AD Premium\).

Per altre informazioni, vedere [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health per Active Directory Domain Services](how-to-connect-health-adds.md)
Azure AD Connect Health per Active Directory Domain Services consente il monitoraggio dei controller di dominio installati in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Con un'installazione dell'agente per l'integrità è possibile monitorare l'ambiente locale di Active Directory Domain Services dal cloud. Azure AD Connect Health per Servizi di dominio Active Directory offre il seguente insieme di funzionalità chiave:

* Avvisi di monitoraggio per rilevare quando i controller di dominio non sono integri e notifiche di posta elettronica per avvisi critici
* Dashboard dei controller di dominio, che permette di visualizzare rapidamente l'integrità e lo stato operativo dei controller di dominio
* Dashboard Stato replica con le informazioni di replica più recenti e collegamenti a guide per la risoluzione dei problemi quando vengono rilevati errori
* Accesso rapido ovunque a grafici di dati dei contatori delle prestazioni più diffusi, per obiettivi di risoluzione dei problemi e monitoraggio

Per altre informazioni, vedere [Uso di Azure AD Connect Health con Active Directory Domain Services](how-to-connect-health-adds.md)

## <a name="next-steps"></a>Passaggi successivi


- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Sincronizzazione degli hash delle password](how-to-connect-password-hash-synchronization.md)|
- [Autenticazione pass-through](how-to-connect-pta.md)
- [Azure AD Connect e federazione](how-to-connect-fed-whatis.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Servizio di sincronizzazione Azure AD Connect](how-to-connect-sync-whatis.md)
- [Cronologia delle versioni](reference-connect-version-history.md)
- [Confronto degli strumenti di integrazione directory](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Domande frequenti su Azure AD Connect](reference-connect-faq.md)









