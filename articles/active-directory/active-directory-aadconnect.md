<properties
	pageTitle="Integrazione delle identità locali con Azure Active Directory | Microsoft Azure"
	description="Articolo relativo ad Azure AD Connect che ne descrive le funzionalità e spiega perché è consigliabile usarlo."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/04/2015"
	ms.author="andkjell;billmath"/>

# Integrazione delle identità locali con Azure Active Directory
Azure AD Connect è lo strumento che permette di integrare il sistema di gestione delle identità locale, ad esempio Windows Server Active Directory, con Azure Active Directory e connettere gli utenti a Office 365, ad Azure e a migliaia di applicazioni SaaS. Questo argomento fornisce una guida completa alla preparazione e alla distribuzione dei componenti necessari per permettere agli utenti finali di accedere ai servizi cloud con la stessa identità che usano oggi per accedere alle app aziendali esistenti.

![Cos'è Azure AD Connect](./media/active-directory-aadconnect/arch.png)

## Perché usare Azure AD Connect
L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi, in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Con questa integrazione utenti e organizzazioni possono sfruttare i vantaggi seguenti:

- Gli utenti possono usare un'unica identità per accedere alle applicazioni locali e ai servizi cloud, come ad esempio Office 365.

- Un unico strumento che offre un'esperienza di distribuzione semplificata per la sincronizzazione e l'accesso.

- Offre le funzionalità più recenti per gli scenari in uso. Azure AD Connect sostituisce le versioni precedenti degli strumenti di integrazione delle identità, ad esempio DirSync e Azure AD Sync. Per altre informazioni, vedere [Confronto degli strumenti di integrazione directory](active-directory-aadconnect-get-started-tools-comparison.md).


### Funzionamento di Azure AD Connect

Azure Active Directory Connect è costituito da tre parti principali, ovvero i servizi di sincronizzazione, il componente facoltativo Active Directory Federation Services e il componente di monitoraggio eseguito tramite [Azure AD Connect Health](active-directory-aadconnect-health.md).

<center>![Stack di Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Sincronizzazione: questa parte è costituita dai componenti e le funzionalità precedentemente disponibili come [DirSync e Azure AD Sync](active-directory-aadconnect-get-started-tools-comparison.md). Si tratta della parte responsabile della creazione di utenti e gruppi. Questa parte garantisce anche che le informazioni su utenti e gruppi nell'ambiente locale corrispondano a quelle nel cloud.
- ADFS: questa è una parte facoltativa di Azure AD Connect e può essere usata per configurare un ambiente ibrido mediante un'infrastruttura ADFS locale. Può essere usata dalle organizzazioni per gestire distribuzioni complesse che includono, ad esempio, accesso SSO di aggiunta al dominio, applicazione di criteri di accesso di Active Directory e autenticazione a più fattori con smart card o di terze parti.
- Monitoraggio dell'integrità: Azure AD Connect Health può offrire monitoraggio affidabile dei server federativi e una posizione centralizzata nel portale di Azure per visualizzare questa attività. Per altre informazioni, vedere [Azure Active Directory Connect Health](active-directory-aadconnect-health.md).

## Installare Azure AD Connect

Il download per Azure AD Connect è disponibile nell'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).


| Soluzione | Scenario |
| ----- | ----- |
| [Impostazioni rapide](active-directory-aadconnect-get-started-express.md) | <li>Scelta consigliata e opzione predefinita se è presente una singola foresta Active Directory.</li> <li>Accesso utente con la stessa password tramite la sincronizzazione delle password.</li>
| [Impostazioni personalizzate](active-directory-aadconnect-get-started-custom.md) | <li>Da usare quando sono presenti più foreste. Supporta numerose [topologie](active-directory-aadconnect-topologies.md) locali.</li> <li>Personalizzazione dell'opzione di accesso, ad esempio tramite AD FS per la federazione o un provider di identità di terze parti.</li> <li>Personalizzazione delle funzionalità di sincronizzazione, ad esempio filtro e writeback.</li>
| [Aggiornamento da DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Se è presente un server DirSync esistente già in esecuzione.</li>
| Aggiornamento da Azure AD Sync | <li>Si tratta di un'operazione di aggiornamento sul posto.</li>


[Dopo l'installazione](active-directory-aadconnect-whats-next.md) sarà necessario verificare se funziona come previsto e assegnare le licenze agli utenti.

### Installare Azure AD Connect - Passaggi successivi

| Argomento | |
| --------- | --------- |
| Hardware e prerequisiti | [Azure AD Connect: Hardware e prerequisiti](active-directory-aadconnect-prerequisites.md) |
| Scaricare Azure AD Connect | [Scaricare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| Eseguire l'installazione con le Impostazioni rapide | [Installazione rapida di Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Eseguire l'installazione mediante le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Aggiornamento da DirSync | [Aggiornamento dallo strumento Azure AD Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Dopo l'installazione | [Verificare l'installazione e assegnare le licenze ](active-directory-aadconnect-whats-next.md) |

### Altre informazioni su come installare Azure AD Connect

Preparazione per la gestione delle attività [operative](active-directory-aadconnectsync-operations.md). È consigliabile disporre di un server in standby per agevolare il failover in caso di [emergenza](active-directory-aadconnectsync-operations.md#disaster-recovery). Se si prevede di apportare modifiche frequenti alla configurazione, è consigliabile valutare l'uso di un server in [modalità di staging](active-directory-aadconnectsync-operations.md#staging-mode).

| Argomento | |
| --------- | --------- |
| Topologie supportate | [Topologie per Azure AD Connect](active-directory-aadconnect-topologies.md) |
| Concetti relativi alla progettazione | [Concetti relativi alla progettazione per Azure AD Connect](active-directory-aadconnect-design-concepts.md) |
| Account usati per l'installazione | [Altre informazioni sulle credenziali e le autorizzazioni di Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |
| Pianificazione per la gestione delle attività operative | [Servizio di sincronizzazione Azure AD Connect: Attività operative e considerazioni](active-directory-aadconnectsync-operations.md) |

## Configurare le funzionalità
Azure AD Connect include numerose funzionalità che è possibile abilitare o che sono abilitate per impostazione predefinita. Alcune funzionalità potrebbero talvolta richiedere attività di configurazione aggiuntive in scenari e topologie specifici.

Il [filtro](active-directory-aadconnectsync-configure-filtering.md) consente di limitare gli oggetti da sincronizzare con Azure AD. Per impostazione predefinita, tutti gli utenti, i contatti, i gruppi e i computer Windows 10 sono sincronizzati, ma è possibile limitare gli oggetti sincronizzati in base a domini, unità organizzative o attributi.

La [sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md) consente di sincronizzare l'hash della password in Active Directory con Azure AD. In questo modo l'utente potrà usare la stessa password per accedere alle risorse locali e nel cloud, ma gestirla da una sola posizione. Poiché verrà usata l'istanza di Active Directory locale, sarà anche possibile usare i criteri per le password già definiti.

Il [writeback delle password](active-directory-passwords-getting-started.md) consente agli utenti di modificare e reimpostare le proprie password nel cloud, applicando i criteri per le password locali.

Il [writeback dei dispositivi](active-directory-aadconnect-get-started-custom-device-writeback.md) consente il writeback di un dispositivo registrato in Azure AD nell'istanza di Active Directory locale, affinché possa essere usato per l'accesso condizionale.

La funzionalità di [prevenzione delle eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) è attivata per impostazione predefinita e consente di proteggere la directory cloud da numerose eliminazioni contemporanee. Per impostazione predefinita, consente 500 eliminazioni per esecuzione. Questo valore può essere modificato in base alle dimensioni dell'organizzazione.

### Configurare le funzionalità - Passaggi successivi

| Argomento | |
| --------- | --------- |
| Configurare il filtro | [Servizio di sincronizzazione Azure AD Connect: Configurare il filtro](active-directory-aadconnectsync-configure-filtering.md) |
| Sincronizzazione delle password | [Servizio di sincronizzazione Azure AD Connect: Implementare la sincronizzazione della password](active-directory-aadconnectsync-implement-password-synchronization.md) |
| Writeback password | [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md) |
| Writeback dispositivi | [Abilitazione del writeback dei dispositivi in Azure AD Connect](active-directory-aadconnect-get-started-custom-device-writeback.md) |
| Impedire eliminazioni accidentali | [Servizio di sincronizzazione Azure AD Connect: Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |

## Personalizzare il servizio di sincronizzazione Azure AD Connect
Il servizio di sincronizzazione Azure AD Connect viene fornito con una configurazione predefinita adatta alla maggior parte dei clienti e delle topologie. In alcune situazioni, tuttavia, la configurazione predefinita non funzionerà e richiederà alcune rettifiche. Le modifiche sono supportate, purché in linea con quanto documentato in questa sezione e negli argomenti collegati.

Se è la prima volta che si usa una topologia di sincronizzazione, è consigliabile vedere le nozioni di base e i termini usati nella sezione relativa ai [concetti tecnici](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect è l'evoluzione di MIIS2003, ILM2007 e FIM2010. Anche se alcuni elementi sono identici, sono state introdotte numerose modifiche.

La configurazione presuppone che possano essere presenti più foreste. In queste topologie un oggetto utente può essere rappresentato come un contatto in un'altra foresta. L'utente può anche avere una cassetta postale collegata in un'altra foresta di risorse. Il comportamento della configurazione predefinita è descritto in [utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Il modello di configurazione nella sincronizzazione è denominato [provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). I flussi di attributi avanzati usano [funzioni](active-directory-aadconnectsync-functions-reference.md) per esprimere le trasformazioni di attributo. È possibile visualizzare ed esaminare l'intera configurazione con gli strumenti disponibili in Azure AD Connect. Se è necessario apportare modifiche, assicurarsi di seguire le [procedure consigliate](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) che semplificano l'adozione delle nuove versioni quando vengono rese disponibili.

### Personalizzare il servizio di sincronizzazione Azure AD Connect - Passaggi successivi

| Argomento | |
| --------- | --------- |
| Concetti tecnici | [Servizio di sincronizzazione Azure AD Connect: Concetti tecnici](active-directory-aadconnectsync-technical-concepts.md) |
| Informazioni su utenti e contatti | [Servizio di sincronizzazione Azure AD Connect: Informazioni su utenti e contatti](active-directory-aadconnectsync-understanding-users-and-contacts.md) |
| Provisioning dichiarativo | [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Riferimento alle funzioni del provisioning dichiarativo | [Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md) |
| Procedure consigliate | [Procedure consigliate per modificare la configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |

## Altri riferimenti e informazioni

| Argomento | |
| --------- | --------- |
| Cronologia delle versioni | [Cronologia delle versioni](active-directory-aadconnect-version-history.md) |
| Confronto tra DirSync, Azure ADSync e Azure AD Connect | [Confronto degli strumenti di integrazione directory](active-directory-aadconnect-get-started-tools-comparison.md) |
| Attributi sincronizzati | [Attributi sincronizzati](active-directory-aadconnectsync-attributes-synchronized.md) |
| Monitoraggio con Azure AD Connect Health | [Azure AD Connect Health](active-directory-aadconnect-health.md) |
| Domande frequenti | [Domande frequenti su Azure AD Connect](active-directory-aadconnect-faq.md) |


**Risorse aggiuntive**


Presentazione Ignite 2015 su come estendere le directory locali nel cloud.

[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]

<!---HONumber=Nov15_HO2-->