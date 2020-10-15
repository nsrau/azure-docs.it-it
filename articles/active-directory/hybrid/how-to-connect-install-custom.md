---
title: Personalizzare un'installazione di Azure Active Directory Connect
description: Questo articolo illustra le opzioni di installazione personalizzate per Azure AD Connect. Usare queste istruzioni per installare Active Directory con Azure AD Connect.
services: active-directory
keywords: che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096352"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Installazione personalizzata di Azure Active Directory Connect
Utilizzare *le impostazioni personalizzate* in Azure Active Directory (Azure ad) Connetti quando si desiderano più opzioni per l'installazione. Utilizzare queste impostazioni, ad esempio, se si dispone di più foreste o se si desidera configurare le funzionalità facoltative. Usare le impostazioni personalizzate in tutti i casi in cui l' [installazione rapida](how-to-connect-install-express.md) non soddisfi le esigenze di distribuzione o topologia.

Prerequisiti:
- [Scaricare Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Completare i passaggi preliminari in [Azure ad Connect: hardware e prerequisiti](how-to-connect-install-prerequisites.md). 
- Assicurarsi di disporre degli account descritti in [Azure ad Connect account e autorizzazioni](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Impostazioni di installazione personalizzata 

Per configurare un'installazione personalizzata per Azure AD Connect, esaminare le pagine della procedura guidata descritte nelle sezioni seguenti.

### <a name="express-settings"></a>Impostazioni rapide
Nella pagina **Impostazioni rapide** selezionare **Personalizza** per avviare un'installazione delle impostazioni personalizzate.  Il resto di questo articolo illustra il processo di installazione personalizzato. Usare i collegamenti seguenti per passare rapidamente alle informazioni per una pagina specifica:

- [Componenti necessari](#install-required-components)
- [Accesso utente](#user-sign-in)
- [Connessione ad Azure AD](#connect-to-azure-ad)
- [Sincronizzazione](#sync-pages)

### <a name="install-required-components"></a>Installare i componenti necessari
Quando si installano i servizi di sincronizzazione, è possibile lasciare deselezionata la sezione di configurazione facoltativa. Azure AD Connect configura tutti gli elementi automaticamente. Viene configurata un'istanza di SQL Server database locale Express 2012, vengono creati i gruppi appropriati e vengono assegnate le autorizzazioni. Se si desidera modificare le impostazioni predefinite, deselezionare le caselle appropriate.  Nella tabella seguente sono riepilogate queste opzioni e vengono forniti collegamenti a informazioni aggiuntive. 

![Screenshot che mostra le selezioni facoltative per i componenti di installazione necessari in Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Configurazione facoltativa | Description |
| --- | --- |
|Specificare un percorso di installazione personalizzato| Consente di modificare il percorso di installazione predefinito per Azure AD Connect.|
| Usare un server SQL esistente |Consente di specificare il nome del SQL Server e il nome dell'istanza. Scegliere questa opzione se si dispone già di un server di database che si desidera utilizzare. Per **nome istanza**, immettere il nome dell'istanza, una virgola e il numero di porta se nell'istanza di SQL Server non è abilitata l'esplorazione.  Specificare quindi il nome del database Azure AD Connect.  I privilegi SQL determinano se è possibile creare un nuovo database o se l'amministratore SQL deve creare il database in anticipo.  Se si dispone di autorizzazioni SQL Server amministratore (SA), vedere [installare Azure ad Connect utilizzando un database esistente](how-to-connect-install-existing-database.md).  Se si dispone di autorizzazioni delegate (DBO), vedere [Install Azure ad Connect by using SQL Delegated Administrator permissions](how-to-connect-install-sql-delegation.md). |
| Usare un account di servizio esistente |Per impostazione predefinita, Azure AD Connect fornisce un account del servizio virtuale per i servizi di sincronizzazione. Se si utilizza un'istanza remota di SQL Server o si utilizza un proxy che richiede l'autenticazione, è possibile utilizzare un *account del servizio gestito* o un account del servizio protetto da password nel dominio. In questi casi, immettere l'account che si vuole usare. Per eseguire l'installazione, è necessario essere un SA in SQL, in modo da poter creare le credenziali di accesso per l'account del servizio. Per altre informazioni, vedere [Account e autorizzazioni di Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Utilizzando la build più recente, l'amministratore SQL può ora effettuare il provisioning del database fuori banda. Quindi l'amministratore Azure AD Connect può installarlo con diritti di proprietario del database.  Per altre informazioni, vedere [Installare Azure AD Connect usando le autorizzazioni di amministratore con delega SQL](how-to-connect-install-sql-delegation.md).|
| Specificare i gruppi di sincronizzazione personalizzati |Per impostazione predefinita, quando vengono installati i servizi di sincronizzazione, Azure AD Connect crea quattro gruppi locali per il server. Questi gruppi sono Administrators, Operators, browse e reimpostazione della password. È possibile specificare qui i gruppi personalizzati. I gruppi devono essere locali nel server. Non possono trovarsi nel dominio. |
|Importa impostazioni di sincronizzazione (anteprima)|Consente di importare le impostazioni da altre versioni di Azure AD Connect.  Per ulteriori informazioni, vedere [importazione ed esportazione di Azure ad Connect impostazioni di configurazione](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Accesso utente
Dopo aver installato i componenti necessari, selezionare il metodo di Single Sign-On degli utenti. Nella tabella seguente vengono brevemente descritte le opzioni disponibili. Per una descrizione completa dei metodi di accesso, vedere [Accesso utente](plan-connect-user-signin.md).

![Screenshot che mostra la pagina "accesso utente". È selezionata l'opzione "sincronizzazione dell'hash delle password".](./media/how-to-connect-install-custom/usersignin4.png)

| Opzione Single Sign-on | Description |
| --- | --- |
| Sincronizzazione dell'hash delle password |Gli utenti possono accedere ai servizi cloud Microsoft, ad esempio Microsoft 365, usando la stessa password usata nella rete locale. Le password utente vengono sincronizzate con Azure AD come hash della password. L'autenticazione viene eseguita nel cloud. Per altre informazioni, vedere [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md). |
|Autenticazione pass-through|Gli utenti possono accedere ai servizi cloud Microsoft, ad esempio Microsoft 365, usando la stessa password usata nella rete locale.  Le password utente vengono convalidate tramite il passaggio all'Active Directory controller di dominio locale.
| Federazione con ADFS |Gli utenti possono accedere ai servizi cloud Microsoft, ad esempio Microsoft 365, usando la stessa password usata nella rete locale.  Gli utenti vengono reindirizzati all'istanza locale di Azure Directory Federation Services (AD FS) per eseguire l'accesso. L'autenticazione viene eseguita in locale. |
| Federazione con PingFederate|Gli utenti possono accedere ai servizi cloud Microsoft, ad esempio Microsoft 365, usando la stessa password usata nella rete locale.  Gli utenti vengono reindirizzati all'istanza locale di PingFederate per l'accesso. L'autenticazione viene eseguita in locale. |
| Non configurare |Nessuna funzionalità di accesso utente è installata o configurata. Scegliere questa opzione se si dispone già di un server federativo di terze parti o di un'altra soluzione sul posto. |
|Abilita Single Sign-On|Questa opzione è disponibile con la sincronizzazione dell'hash delle password e l'autenticazione pass-through. Offre un'esperienza Single Sign-On per gli utenti desktop nelle reti aziendali. Per ulteriori informazioni, vedere [Single Sign-on](how-to-connect-sso.md). </br></br>**Nota:** Per AD FS clienti, questa opzione non è disponibile. AD FS offre già lo stesso livello di Single Sign-On.</br>

### <a name="connect-to-azure-ad"></a>Connessione ad Azure AD
Nella pagina **Connetti a Azure ad** immettere un account amministratore globale e una password. Se è stata selezionata l'opzione **Federazione con ad FS** nella pagina precedente, non accedere con un account che si trova in un dominio che si intende abilitare per la Federazione. 

Potrebbe essere necessario usare un account nel dominio *onmicrosoft.com* predefinito, che viene incluso nel tenant Azure ad. Questo account viene utilizzato solo per creare un account del servizio in Azure AD. Non viene usato al termine dell'installazione.
  
![Screenshot che mostra la pagina "Connetti a Azure AD".](./media/how-to-connect-install-custom/connectaad.png)

Se per l'account amministratore globale è abilitata l'autenticazione a più fattori, fornire nuovamente la password nella finestra di accesso ed è necessario completare la richiesta di autenticazione a più fattori. La richiesta può essere un codice di verifica o una telefonata.  

![Screenshot che mostra la pagina "Connetti a Azure AD". Un campo di autenticazione a più fattori richiede all'utente un codice.](./media/how-to-connect-install-custom/connectaadmfa.png)

Per l'account amministratore globale è inoltre possibile abilitare [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) .

Se viene visualizzato un errore o si verificano problemi di connettività, vedere [risolvere i problemi di connettività](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Sincronizza pagine

Le sezioni seguenti descrivono le pagine della sezione **Sync** .

### <a name="connect-your-directories"></a>Connessione delle directory
Per connettersi a Active Directory Domain Services (Azure AD DS), Azure AD Connect necessario il nome della foresta e le credenziali di un account con autorizzazioni sufficienti.

![Screenshot che mostra la pagina "Connetti le directory".](./media/how-to-connect-install-custom/connectdir01.png)

Dopo aver immesso il nome della foresta e selezionato  **Aggiungi directory**, viene visualizzata una finestra. Nella tabella seguente vengono descritte le opzioni disponibili.

| Opzione | Descrizione |
| --- | --- |
| Crea un nuovo account | Creare l'account Azure AD DS che Azure AD Connect deve connettersi alla foresta Active Directory durante la sincronizzazione della directory. Dopo aver selezionato questa opzione, immettere il nome utente e la password per un account amministratore dell'organizzazione.  Azure AD Connect usa l'account amministratore aziendale specificato per creare l'account Azure AD DS necessario. È possibile immettere la parte di dominio nel formato NetBIOS o nel formato FQDN. Ovvero, immettere *FABRIKAM\administrator* o *Fabrikam. com\administrator*. |
| Usare l'account esistente | Fornire un account Azure AD DS esistente che Azure AD Connect possibile usare per connettersi alla foresta Active Directory durante la sincronizzazione della directory. È possibile immettere la parte di dominio nel formato NetBIOS o nel formato FQDN. Ovvero, immettere *FABRIKAM\syncuser* o *Fabrikam. com\syncuser*. Questo account può essere un account utente normale perché richiede solo le autorizzazioni di lettura predefinite. Tuttavia, a seconda dello scenario, potrebbero essere necessarie altre autorizzazioni. Per altre informazioni, vedere [Account e autorizzazioni di Azure AD Connect](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Screenshot che mostra la pagina "Connetti directory" e la finestra dell'account di una foresta D, in cui è possibile scegliere di creare un nuovo account o usare un account esistente.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> Al momento della compilazione di 1.4.18.0, non è possibile usare un account amministratore dell'organizzazione o amministratore di dominio come account del connettore Azure AD DS. Quando si seleziona **Usa account esistente**, se si prova ad immettere un account amministratore dell'organizzazione o un account amministratore di dominio, viene visualizzato l'errore seguente: "l'uso di un account amministratore di dominio o aziendale per l'account della foresta di Active Directory non è consentito. Consentire Azure AD Connect creare l'account o specificare un account di sincronizzazione con le autorizzazioni corrette ".
>

### <a name="azure-ad-sign-in-configuration"></a>Configurazione dell'accesso ad Azure AD
Nella pagina **configurazione dell'accesso Azure ad** esaminare i domini nome entità utente (UPN) in locale Azure AD DS. Questi domini UPN sono stati verificati in Azure AD. In questa pagina è possibile configurare l'attributo da usare per userPrincipalName.

![Screenshot che mostra i domini non verificati nella pagina "configurazione di accesso di Azure A D".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Esaminare ogni dominio contrassegnato come **non aggiunto** o **non verificato**. Assicurarsi che i domini usati siano stati verificati in Azure AD. Dopo aver verificato i domini, selezionare l'icona di aggiornamento circolare. Per ulteriori informazioni, vedere [aggiungere e verificare il dominio](../fundamentals/add-custom-domain.md).

Gli utenti usano l'attributo *userPrincipalName* quando accedono a Azure AD e Microsoft 365. Azure AD necessario verificare i domini, noti anche come suffisso UPN, prima che gli utenti vengano sincronizzati. Microsoft consiglia di lasciare l'attributo predefinito userPrincipalName. 

Se l'attributo userPrincipalName non è instradabile e non può essere verificato, è possibile selezionare un altro attributo. È possibile, ad esempio, selezionare posta elettronica come attributo che include l'ID di accesso. Quando si usa un attributo diverso da userPrincipalName, è noto come *ID alternativo*. 

Il valore dell'attributo ID alternativo deve seguire lo standard RFC 822. È possibile usare un ID alternativo con sincronizzazione dell'hash delle password, autenticazione pass-through e Federazione. In Active Directory, l'attributo non può essere definito come multivalore, anche se dispone solo di un singolo valore. Per ulteriori informazioni sull'ID alternativo, vedere [autenticazione pass-through: domande frequenti](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Quando si Abilita l'autenticazione pass-through, è necessario avere almeno un dominio verificato per continuare con il processo di installazione personalizzato.

> [!WARNING]
> Gli ID alternativi non sono compatibili con tutti i carichi di lavoro Microsoft 365. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di ID di accesso alternativi](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Filtro unità organizzativa e dominio
Per impostazione predefinita, tutti i domini e le unità organizzative (OU) sono sincronizzati. Se non si desidera sincronizzare alcuni domini o unità organizzative per Azure AD, è possibile deselezionare le selezioni appropriate.  

![Screenshot che mostra la pagina di filtro dominio e O U.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Questa pagina consente di configurare i filtri basati su dominio e su unità organizzativa. Se si prevede di apportare modifiche, vedere [filtro basato su dominio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) e [filtro basato su unità organizzativa](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Alcune ou sono essenziali per le funzionalità, quindi è necessario lasciarle selezionate.

Se si usa il filtro basato su unità organizzative con una versione di Azure AD Connect precedente a 1.1.524.0, le nuove unità organizzative vengono sincronizzate per impostazione predefinita. Se non si desidera che le nuove unità organizzative vengano sincronizzate, è possibile modificare il comportamento predefinito dopo il passaggio del [filtro basato su unità organizzativa](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) . Per Azure AD Connect 1.1.524.0 o versioni successive, è possibile indicare se si desidera sincronizzare le nuove unità organizzative.

Se si prevede di usare il [filtro basato sui gruppi](#sync-filtering-based-on-groups), assicurarsi che l'unità organizzativa con il gruppo sia inclusa e non sia filtrata usando il filtro unità organizzativa. Il filtro dell'unità organizzativa viene valutato prima della valutazione del filtro basato sui gruppi.

È anche possibile che alcuni domini non siano raggiungibili a causa di restrizioni del firewall. Questi domini sono deselezionati per impostazione predefinita e visualizzano un avviso.  

![Screenshot che mostra i domini non raggiungibili.](./media/how-to-connect-install-custom/unreachable.png)  

Se viene visualizzato questo avviso, assicurarsi che questi domini non siano effettivamente raggiungibili e che l'avviso sia previsto.

### <a name="uniquely-identifying-your-users"></a>Identificazione univoca degli utenti

Nella pagina **Identificazione utenti** scegliere come identificare gli utenti nelle directory locali e come identificarli usando l'attributo sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Selezionare la modalità di identificazione degli utenti nelle directory locali
Utilizzando la funzionalità di *corrispondenza tra foreste* , è possibile definire il modo in cui gli utenti delle foreste Azure AD DS sono rappresentati in Azure ad. Un utente può essere rappresentato solo una volta in tutte le foreste o potrebbe avere una combinazione di account abilitati e disabilitati. In alcune foreste è possibile che l'utente sia rappresentato anche come un contatto.

![Screenshot che mostra la pagina in cui è possibile identificare in modo univoco gli utenti.](./media/how-to-connect-install-custom/unique2.png)

| Impostazione | Descrizione |
| --- | --- |
| [Gli utenti sono rappresentati solo una volta in tutte le foreste](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Tutti gli utenti vengono creati come singoli oggetti in Azure AD. Gli oggetti non vengono uniti nel metaverse. |
| [Attributo di posta](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Questa opzione unisce utenti e contatti se l'attributo di posta ha lo stesso valore in foreste diverse. Usare questa opzione quando i contatti sono stati creati tramite GALSync. Se si sceglie questa opzione, gli oggetti utente il cui attributo di posta elettronica non è popolato non verranno sincronizzati con Azure AD. |
| [Attributi ObjectSID e msExchangeMasterAccountSID/msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Questa opzione unisce un utente abilitato in una foresta di account a un utente disabilitato in una foresta di risorse. In Exchange questa configurazione è definita cassetta postale collegata. È possibile utilizzare questa opzione se si utilizza solo Lync e se Exchange non è presente nella foresta delle risorse. |
| Attributi SAMAccountName e MailNickName |Questa opzione unisce gli attributi in cui si prevede che l'ID di accesso per l'utente sia stato trovato. |
| Scegliere un attributo specifico |Questa opzione consente di selezionare un attributo personale. Se si sceglie questa opzione, gli oggetti utente il cui attributo (selezionato) non è popolato non verranno sincronizzati con Azure AD. **Limitazione:** Per questa opzione sono disponibili solo gli attributi già presenti nel metaverse. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Consente di selezionare la modalità di identificazione degli utenti tramite un ancoraggio di origine
L'attributo *sourceAnchor* non è modificabile durante la durata di un oggetto utente. Si tratta della chiave primaria che collega l'utente locale all'utente in Azure AD.

| Impostazione | Descrizione |
| --- | --- |
| Consentire ad Azure di gestire l'ancoraggio di origine | Selezionare questa opzione se si vuole che Azure AD selezioni automaticamente l'attributo. Se si seleziona questa opzione, Azure AD Connect applica la logica di selezione dell'attributo sourceAnchor descritta in [uso di MS-DS-ConsistencyGuid come sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Al termine dell'installazione personalizzata, viene visualizzato l'attributo selezionato come attributo sourceAnchor. |
| Scegliere un attributo specifico | Selezionare questa opzione se si desidera specificare un attributo di Active Directory esistente come attributo sourceAnchor. |

Poiché non è possibile modificare l'attributo sourceAnchor, è necessario scegliere un attributo appropriato. objectGUID è un candidato valido. Questo attributo non viene modificato a meno che l'account utente non venga spostato tra foreste o domini. Non scegliere attributi che possono cambiare quando un utente si sposa o modifica le assegnazioni. 

Non è possibile usare gli attributi che includono un simbolo di chiocciola (@), quindi non è possibile usare la posta elettronica e userPrincipalName. Anche l'attributo fa distinzione tra maiuscole e minuscole, pertanto quando si sposta un oggetto tra foreste, assicurarsi di mantenere i caratteri maiuscoli e minuscoli. Gli attributi binari sono con codifica Base64, mentre altri tipi di attributo rimangono nello stato non codificato. 

Negli scenari di Federazione e in alcune interfacce di Azure AD, l'attributo sourceAnchor è noto anche come *immutableID*. 

Per altre informazioni sull'ancoraggio di origine, vedere [concetti relativi alla progettazione](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtro di sincronizzazione basato sui gruppi
La funzionalità filtro in gruppi consente di sincronizzare solo un piccolo subset di oggetti per un progetto pilota. Per usare questa funzionalità, creare un gruppo a questo scopo nell'istanza locale di Active Directory. Aggiungere quindi utenti e gruppi da sincronizzare in Azure AD come membri diretti, In un secondo momento è possibile aggiungere utenti o rimuovere gli utenti da questo gruppo per gestire l'elenco di oggetti che devono essere presenti nel Azure AD. 

Tutti gli oggetti che si desidera sincronizzare devono essere membri diretti del gruppo. Utenti, gruppi, contatti e computer o dispositivi devono essere tutti membri diretti. L'appartenenza al gruppo annidato non è stata risolta. Quando si aggiunge un gruppo come membro, viene aggiunto solo il gruppo stesso. I membri non vengono aggiunti.

![Screenshot che mostra la pagina in cui è possibile scegliere come filtrare utenti e dispositivi.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Questa funzionalità è progettata per supportare solo una distribuzione pilota. Non usarla in una distribuzione di produzione completa.
>

In una distribuzione di produzione completa, sarebbe difficile mantenere un singolo gruppo e tutti i relativi oggetti da sincronizzare. Al posto della funzionalità di filtro in gruppi, usare uno dei metodi descritti in configurare il [filtro](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Funzionalità facoltative
Nella pagina successiva è possibile selezionare le funzionalità facoltative per lo scenario.

>[!WARNING]
>Azure AD Connect versioni 1.0.8641.0 e precedenti si basano sul servizio di controllo di accesso di Azure per il writeback delle password.  Il servizio è stato ritirato il 7 novembre 2018.  Se si utilizza una di queste versioni di Azure AD Connect e si Abilita il writeback delle password, è possibile che gli utenti perdano la possibilità di modificare o reimpostare le password quando il servizio viene ritirato. Queste versioni di Azure AD Connect non supportano il writeback delle password.
>
>Per altre informazioni, vedere [eseguire la migrazione dal servizio di controllo di accesso di Azure](../azuread-dev/active-directory-acs-migration.md).
>
>Se si vuole usare il writeback delle password, scaricare la [versione più recente di Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Screenshot che mostra la pagina "funzionalità facoltative".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Se Azure AD Sync o la sincronizzazione diretta (DirSync) è attiva, non attivare le funzionalità di writeback in Azure AD Connect.



| Funzionalità facoltative | Description |
| --- | --- |
| Distribuzione ibrida di Exchange |La funzionalità di distribuzione ibrida di Exchange consente la coesistenza delle cassette postali di Exchange sia in locale che in Microsoft 365. Azure AD Connect sincronizza un set specifico di [attributi](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) da Azure ad di nuovo nella directory locale. |
| Cartelle pubbliche della posta di Exchange | La funzionalità cartelle pubbliche della posta di Exchange consente di sincronizzare gli oggetti cartella pubblica abilitata alla posta elettronica dall'istanza locale di Active Directory Azure AD. |
| Filtro attributi e app di Azure AD |Abilitando Azure AD applicazione e il filtro degli attributi, è possibile personalizzare il set di attributi sincronizzati. Questa opzione aggiunge altre due pagine di configurazione alla procedura guidata. Per altre informazioni, vedere [Filtro attributi e app Azure AD](#azure-ad-app-and-attribute-filtering). |
| Sincronizzazione dell'hash delle password |Se è stata selezionata la Federazione come soluzione di accesso, è possibile abilitare la sincronizzazione dell'hash delle password. Quindi è possibile usarlo come opzione di backup.  </br></br>Se è stata selezionata l'autenticazione pass-through, è possibile abilitare questa opzione per garantire il supporto per i client legacy e per fornire un backup.</br></br> Per altre informazioni, vedere [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md).|
| writeback delle password |Usare questa opzione per assicurarsi che le modifiche apportate alle password che provengono da Azure AD vengano riscritte nella directory locale. Per altre informazioni, vedere [Introduzione alla gestione delle password](../authentication/tutorial-enable-sspr.md). |
| Writeback dei gruppi |Se si usano gruppi di Microsoft 365, è possibile rappresentare i gruppi nell'istanza locale di Active Directory. Questa opzione è disponibile solo se si dispone di Exchange nell'istanza locale di Active Directory. Per ulteriori informazioni, vedere [Azure ad Connect writeback dei gruppi](how-to-connect-group-writeback.md).|
| Writeback dispositivi |Per gli scenari di accesso condizionale, usare questa opzione per eseguire il writeback degli oggetti dispositivo in Azure AD nell'istanza locale di Active Directory. Per altre informazioni, vedere [Abilitazione del writeback dei dispositivi in Azure AD Connect](how-to-connect-device-writeback.md). |
| Sincronizzazione attributi estensione della directory |Selezionare questa opzione per sincronizzare gli attributi specificati con Azure AD. Per altre informazioni, vedere [Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtro attributi e app di Azure AD
Per limitare gli attributi da sincronizzare a Azure AD, iniziare selezionando i servizi usati. Se si modificano le selezioni in questa pagina, è necessario selezionare in modo esplicito un nuovo servizio rieseguendo l'installazione guidata.

![Screenshot che mostra le funzionalità facoltative di Azure A D app.](./media/how-to-connect-install-custom/azureadapps2.png)

In base ai servizi selezionati nel passaggio precedente, in questa pagina vengono visualizzati tutti gli attributi sincronizzati. Questo elenco è costituito da una combinazione di tutti i tipi di oggetto sincronizzati. Se è necessario che alcuni attributi rimangano non sincronizzati, è possibile cancellare la selezione da tali attributi.

![Screenshot che mostra le funzionalità facoltative degli attributi di Azure A D.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> La rimozione degli attributi può influire sulla funzionalità. Per le procedure consigliate e le indicazioni, vedere [attributi da sincronizzare](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Sincronizzazione attributi estensione della directory
È possibile estendere lo schema in Azure AD usando gli attributi personalizzati aggiunti dall'organizzazione o usando altri attributi in Active Directory. Per usare questa funzionalità, nella pagina **funzionalità facoltative** selezionare **sincronizzazione attributo estensione directory**. Nella pagina **estensioni directory** è possibile selezionare altri attributi da sincronizzare.

>[!NOTE]
>Il campo **attributi disponibili** fa distinzione tra maiuscole e minuscole.

![Screenshot che mostra la pagina "estensioni di directory".](./media/how-to-connect-install-custom/extension2.png)

Per altre informazioni, vedere [Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Abilitazione di Single Sign-On
Nella pagina **Single Sign-on** è possibile configurare Single Sign-on per l'uso con la sincronizzazione delle password o l'autenticazione pass-through. Questo passaggio viene eseguito una volta per ogni foresta sincronizzata con Azure AD. La configurazione prevede due passaggi:

1.  Creare l'account computer necessario nell'istanza locale di Active Directory.
2.  Configurare l'area Intranet dei computer client per supportare Single Sign-On.

#### <a name="create-the-computer-account-in-active-directory"></a>Creare l'account computer in Active Directory
Per ogni foresta aggiunta in Azure AD Connect, è necessario fornire le credenziali di amministratore di dominio in modo che l'account computer possa essere creato in ogni foresta. Le credenziali vengono utilizzate solo per creare l'account. Non vengono archiviati o utilizzati per altre operazioni. Aggiungere le credenziali nella pagina **abilita Single Sign-on** , come illustrato nella figura seguente.

![Screenshot che mostra la pagina "Abilita Single Sign-On". Vengono aggiunte le credenziali della foresta.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>È possibile ignorare le foreste in cui non si vuole usare Single Sign-On.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Configurare l'area Intranet per i computer client
Per assicurarsi che il client acceda automaticamente all'area Intranet, assicurarsi che l'URL faccia parte dell'area Intranet. Questo passaggio garantisce che il computer aggiunto al dominio invii automaticamente un ticket Kerberos per Azure AD quando è connesso alla rete aziendale.

In un computer dotato di Criteri di gruppo strumenti di gestione:

1.  Aprire gli strumenti di gestione di Criteri di gruppo.
2.  Modificare i criteri di gruppo che verranno applicati a tutti gli utenti. Ad esempio, il criterio dominio predefinito.
3.  Passare a **Configurazione utente**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **Internet Explorer**  >  pagina sicurezza del**Pannello di controllo Internet**  >  **Security Page**. Selezionare quindi **Elenco di assegnazione siti ad aree**.
4.  Abilitare i criteri. Quindi, nella finestra di dialogo, immettere il nome del valore `https://autologon.microsoftazuread-sso.com` e il valore di `1` . La configurazione dovrebbe avere un aspetto simile all'immagine seguente.
  
    ![Screenshot che mostra le zone Intranet.](./media/how-to-connect-install-custom/sitezone.png)

6.  Selezionare **OK** due volte.

## <a name="configuring-federation-with-ad-fs"></a>Configurazione della federazione con ADFS
È possibile configurare AD FS con Azure AD Connect con pochi clic. Prima di iniziare, è necessario:

* Windows Server 2012 R2 o versione successiva per il server federativo. È necessario abilitare la gestione remota.
* Windows Server 2012 R2 o versione successiva per il server proxy applicazione Web. È necessario abilitare la gestione remota.
* Un certificato TLS/SSL per il nome del servizio federativo che si intende usare (ad esempio, sts.contoso.com).

>[!NOTE]
>È possibile aggiornare un certificato TLS/SSL per la farm AD FS usando Azure AD Connect anche se non viene usato per gestire il trust federativo.

### <a name="ad-fs-configuration-prerequisites"></a>Prerequisiti di configurazione di AD FS
Per configurare la farm di AD FS tramite Azure AD Connect, assicurarsi che WinRM sia abilitato sui server remoti. Assicurarsi di aver completato le altre attività nei [prerequisiti di Federazione](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Assicurarsi inoltre di rispettare i requisiti relativi alle porte elencate nella tabella [Azure ad Connect e server federativi/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) .

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Creare una nuova farm ADFS o usare una farm ADFS esistente
È possibile utilizzare una farm AD FS esistente o crearne una nuova. Se si sceglie di crearne uno nuovo, è necessario fornire il certificato TLS/SSL. Se il certificato TLS/SSL è protetto da una password, viene richiesto di fornire la password.

![Screenshot che mostra la pagina "A D F S Farm"](./media/how-to-connect-install-custom/adfs1.png)

Se si sceglie di utilizzare una farm AD FS esistente, viene visualizzata la pagina in cui è possibile configurare la relazione di trust tra AD FS e Azure AD.

>[!NOTE]
>È possibile utilizzare Azure AD Connect per gestire solo una AD FS farm. Se si dispone di una relazione di trust federativa esistente in cui Azure AD è configurato nella farm AD FS selezionata, Azure AD Connect ricrea nuovamente l'attendibilità da zero.

### <a name="specify-the-ad-fs-servers"></a>Specificare i server ADFS
Specificare i server in cui si desidera installare AD FS. È possibile aggiungere uno o più server, a seconda delle esigenze di capacità. Prima di configurare questa configurazione, aggiungere tutti i server AD FS Active Directory. Questo passaggio non è necessario per i server proxy applicazione Web. 

È consigliabile installare un singolo server AD FS per distribuzioni di test e pilota. Dopo la configurazione iniziale, è possibile aggiungere e distribuire più server per soddisfare le esigenze di scalabilità eseguendo di nuovo Azure AD Connect.

> [!NOTE]
> Prima di configurare questa configurazione, verificare che tutti i server siano aggiunti a un dominio Azure AD.
>


![Screenshot che mostra la pagina "server federativi".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Specificare i server Proxy applicazione Web
Specificare i server proxy applicazione Web. Il server proxy applicazione Web viene distribuito nella rete perimetrale, con la rete Extranet. Supporta le richieste di autenticazione dalla rete Extranet. È possibile aggiungere uno o più server, a seconda delle esigenze di capacità. 

Microsoft consiglia di installare un singolo server proxy applicazione Web per le distribuzioni di test e pilota. Dopo la configurazione iniziale, è possibile aggiungere e distribuire più server per soddisfare le esigenze di scalabilità eseguendo di nuovo Azure AD Connect. È consigliabile disporre di un numero equivalente di server proxy per soddisfare l'autenticazione dalla Intranet.

> [!NOTE]
> - Se l'account usato non è un amministratore locale nei server proxy applicazione Web, vengono richieste le credenziali di amministratore.
> - Prima di specificare i server proxy applicazione Web, verificare che sia presente la connettività HTTP/HTTPS tra il server Azure AD Connect e il server proxy applicazione Web.
> - Verificare che sia presente la connettività HTTP/HTTPS tra il server applicazioni Web e il server AD FS per consentire il flusso delle richieste di autenticazione.
>


![Screenshot che mostra la pagina server proxy applicazione Web.](./media/how-to-connect-install-custom/adfs3.png)

Viene richiesto di immettere le credenziali in modo che il server applicazioni Web possa stabilire una connessione sicura al server AD FS. Queste credenziali devono essere relative a un account amministratore locale nel server AD FS.

![Screenshot che mostra la pagina "credenziali". Le credenziali di amministratore vengono immesse nel campo nome utente e password.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Specificare l'account di servizio per il servizio ADFS
Il servizio AD FS richiede un account del servizio del dominio per autenticare gli utenti e per cercare le informazioni sugli utenti in Active Directory. Supporta due tipi di account di servizio:

* **Account del servizio gestito del gruppo**: questo tipo di account è stato introdotto in servizi di dominio Active Directory da Windows Server 2012. Questo tipo di account fornisce servizi come AD FS. Si tratta di un singolo account in cui non è necessario aggiornare la password regolarmente. Usare questa opzione se il dominio a cui appartengono i server AD FS include già controller di dominio di Windows Server 2012.
* **Account utente di dominio**: questo tipo di account richiede di specificare una password e di aggiornarla periodicamente alla scadenza. Usare questa opzione solo se non sono presenti controller di dominio Windows Server 2012 nel dominio a cui appartengono i server AD FS.

Se è stata selezionata l'opzione **Crea un account del servizio gestito del gruppo** e questa funzionalità non è mai stata usata in Active Directory, immettere le credenziali di amministratore dell'organizzazione. Queste credenziali vengono usate per avviare l'archivio chiavi e abilitare la funzionalità di Active Directory.

> [!NOTE]
> Azure AD Connect controlla se il servizio AD FS è già registrato come nome dell'entità servizio (SPN) nel dominio.  Azure AD DS non consente la registrazione di nomi SPN duplicati nello stesso momento.  Se viene trovato un nome SPN duplicato, non è possibile continuare fino a quando il nome SPN non viene rimosso.

![Screenshot che mostra la pagina "account del servizio D F S".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Selezionare il dominio di Azure AD che si vuole federare
Utilizzare la pagina **Azure ad dominio** per impostare la relazione di federazione tra AD FS e Azure ad. Qui è possibile configurare AD FS per fornire i token di sicurezza da Azure AD. È anche possibile configurare Azure AD per considerare attendibili i token da questa istanza di AD FS. 

In questa pagina è possibile configurare solo un singolo dominio nell'installazione iniziale. È possibile configurare altri domini in un secondo momento, eseguendo di nuovo Azure AD Connect.

![Screenshot che mostra la pagina "Azure A D Domain".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verificare il dominio di Azure AD selezionato per la federazione
Quando si seleziona il dominio di cui si desidera eseguire la Federazione, Azure AD Connect fornisce informazioni che è possibile utilizzare per verificare un dominio non verificato. Per ulteriori informazioni, vedere [aggiungere e verificare il dominio](../fundamentals/add-custom-domain.md).

![Screenshot che mostra la pagina "Azure A D Domain", che include le informazioni che è possibile usare per verificare il dominio.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect tenta di verificare il dominio durante la fase di configurazione. Se non si aggiungono i record di Domain Name System (DNS) necessari, non è possibile completare la configurazione.
>


## <a name="configuring-federation-with-pingfederate"></a>Configurazione della federazione con PingFederate
È possibile configurare PingFederate con Azure AD Connect in pochi clic. Sono richiesti i prerequisiti seguenti:
- PingFederate 8,4 o versione successiva.  Per ulteriori informazioni, vedere [integrazione di PingFederate con Azure Active Directory e Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Un certificato TLS/SSL per il nome del servizio federativo che si intende usare (ad esempio, sts.contoso.com).

### <a name="verify-the-domain"></a>Verificare il dominio
Dopo aver scelto di configurare la Federazione usando PingFederate, viene chiesto di verificare il dominio di cui si vuole eseguire la Federazione.  Selezionare il dominio dal menu a discesa.

![Screenshot che mostra la pagina "Azure A D Domain". Il dominio di esempio "contoso.com" è selezionato.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Esportare le impostazioni di PingFederate


Configurare PingFederate come server federativo per ogni dominio di Azure federato.  Selezionare **Esporta impostazioni** per condividere queste informazioni con l'amministratore di PingFederate.  L'amministratore del server federativo aggiorna la configurazione e quindi fornisce l'URL e il numero di porta del server PingFederate in modo che Azure AD Connect possibile verificare le impostazioni dei metadati.  

![Screenshot che mostra la pagina "impostazioni PingFederate". Il pulsante "Esporta impostazioni" viene visualizzato nella parte superiore della pagina.](./media/how-to-connect-install-custom/ping2.png)

Contattare l'amministratore di PingFederate per risolvere eventuali problemi di convalida.  La figura seguente mostra le informazioni su un server PingFederate che non ha una relazione di trust valida con Azure.

![Screenshot che mostra le informazioni sul server: il server PingFederate è stato trovato, ma la connessione del provider di servizi per Azure è mancante o disabilitata.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Verificare la connettività della federazione
Azure AD Connect tenta di convalidare gli endpoint di autenticazione recuperati dai metadati PingFederate nel passaggio precedente.  Azure AD Connect tenta innanzitutto di risolvere gli endpoint utilizzando i server DNS locali.  Successivamente, tenta di risolvere gli endpoint utilizzando un provider DNS esterno.  Contattare l'amministratore di PingFederate per risolvere eventuali problemi di convalida.  

![Screenshot che mostra la pagina "verifica connettività".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Verificare l'accesso alla Federazione
Infine, è possibile verificare il flusso di accesso federato appena configurato accedendo al dominio federato. Se l'accesso ha esito positivo, la Federazione con PingFederate viene configurata correttamente.

![Screenshot che mostra la pagina "Verifica accesso federato". Un messaggio nella parte inferiore indica un accesso riuscito.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configurare e verificare le pagine
La configurazione viene eseguita nella pagina **Configura** .

> [!NOTE]
> Se è stata configurata la Federazione, assicurarsi che sia stata configurata anche la [risoluzione dei nomi per i server federativi prima di](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) continuare l'installazione.
>



![Screenshot che mostra la pagina "pronto per la configurazione".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Usa modalità di gestione temporanea
È possibile configurare un nuovo server di sincronizzazione in parallelo con la modalità di gestione temporanea. Se si desidera utilizzare questa configurazione, è possibile esportare un solo server di sincronizzazione in una directory nel cloud. Tuttavia, se si desidera passare da un altro server, ad esempio un server che esegue DirSync, è possibile abilitare Azure AD Connect in modalità di gestione temporanea. 

Quando si Abilita l'installazione di gestione temporanea, il motore di sincronizzazione importa e sincronizza i dati come di consueto. Ma non Esporta dati in Azure AD o Active Directory. In modalità di gestione temporanea la funzionalità di sincronizzazione password e la funzionalità di writeback delle password sono disabilitate.

![Screenshot che mostra l'opzione "Abilita modalità di staging".](./media/how-to-connect-install-custom/stagingmode.png)

In modalità di gestione temporanea è possibile apportare le modifiche necessarie al motore di sincronizzazione ed esaminare gli elementi che verranno esportati. Durante la configurazione sembra essere corretta, eseguire nuovamente l'installazione guidata e disattivare la modalità di gestione temporanea. 

I dati vengono ora esportati Azure AD dal server. Assicurarsi di disabilitare l'altro server allo stesso tempo, in modo che soltanto un server esegua l’esportazione in modo attivo.

Per altre informazioni, vedere [Modalità di gestione temporanea](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Verificare la configurazione della federazione
Azure AD Connect verifica le impostazioni DNS quando si seleziona il pulsante **Verifica** . Controlla le impostazioni seguenti:

* **Connettività Intranet**
    * Risolvi FQDN Federazione: Azure AD Connect controlla se il DNS può risolvere il nome di dominio completo della Federazione per garantire la connettività. Se Azure AD Connect non riesce a risolvere il nome di dominio completo, la verifica avrà esito negativo. Per completare la verifica, verificare che sia presente un record DNS per il nome di dominio completo del servizio federativo.
    * Record A DNS: Azure AD Connect controlla se il servizio federativo dispone di un record A. In assenza di un record A, la verifica ha esito negativo. Per completare la verifica, creare un record A (non un record CNAME) per il nome di dominio completo della Federazione.
* **Connettività Extranet**
    * Risolvi FQDN Federazione: Azure AD Connect controlla se il DNS può risolvere il nome di dominio completo della Federazione per garantire la connettività.

      ![Screenshot che mostra la pagina "installazione completata".](./media/how-to-connect-install-custom/completed.png)

      ![Screenshot che mostra la pagina "installazione completata". Un messaggio indica che la configurazione della Intranet è stata verificata.](./media/how-to-connect-install-custom/adfs7.png)

Per convalidare l'autenticazione end-to-end, eseguire manualmente uno o più dei test seguenti:

* Al termine della sincronizzazione, in Azure AD Connect usare l'attività **verifica ulteriore accesso federato** per verificare l'autenticazione per un account utente locale scelto.
* Da un computer aggiunto a un dominio nella Intranet, assicurarsi che sia possibile accedere da un browser. Connettersi a https://myapps.microsoft.com. Usare quindi l'account connesso per verificare l'accesso. L'account predefinito Azure AD DS Administrator non è sincronizzato e non può essere usato per la verifica.
* Verificare che sia possibile accedere da un dispositivo nella rete Extranet. In un computer di casa o in un dispositivo mobile connettersi a https://myapps.microsoft.com . Fornire le credenziali.
* Convalidare l'accesso rich client. Connettersi a https://testconnectivity.microsoft.com. Selezionare quindi **Office 365**  >  **Office 365 single Sign-On test**.

## <a name="troubleshoot"></a>Risolvere problemi
Questa sezione contiene informazioni sulla risoluzione dei problemi che è possibile usare in caso di problemi durante l'installazione di Azure AD Connect.

Quando si Personalizza un'installazione di Azure AD Connect, nella pagina **Installazione componenti richiesti** è possibile selezionare **Usa una SQL Server esistente**. È possibile che venga visualizzato l'errore seguente: "il database ADSync contiene già dati e non può essere sovrascritto. Rimuovere il database esistente e riprovare ".

![Screenshot che mostra la pagina "installazione dei componenti richiesti". Nella parte inferiore della pagina viene visualizzato un errore.](./media/how-to-connect-install-custom/error1.png)

Questo errore viene visualizzato perché esiste già un database denominato *AdSync* nell'istanza SQL di SQL Server specificato.

Questo errore viene in genere visualizzato dopo la disinstallazione di Azure AD Connect.  Il database non viene eliminato dal computer che esegue SQL Server quando si disinstalla Azure AD Connect.

Per risolvere il problema:

1. Controllare il database ADSync che Azure AD Connect utilizzato prima della disinstallazione. Verificare che il database non sia più in uso.

2. Eseguire il backup del database.

3. Eliminare il database:
    1. Utilizzare **Microsoft SQL Server Management Studio** per connettersi all'istanza di SQL. 
    1. Trovare il database **AdSync** e fare clic con il pulsante destro del mouse su di esso.
    1. Scegliere **Elimina**dal menu di scelta rapida.
    1. Selezionare **OK** per eliminare il database.

![Screenshot che Mostra Microsoft SQL Server Management Studio. È stata selezionata una sincronizzazione D.](./media/how-to-connect-install-custom/error2.png)

Dopo aver eliminato il database ADSync, selezionare **Installa** per riprovare l'installazione.

## <a name="next-steps"></a>Passaggi successivi
Al termine dell'installazione, disconnettersi da Windows. Quindi accedere di nuovo prima di usare Synchronization Service Manager o l'editor delle regole di sincronizzazione.

Ora che è stato installato Azure AD Connect, è possibile [verificare l'installazione e assegnare le licenze](how-to-connect-post-installation.md).

Per ulteriori informazioni sulle funzionalità abilitate durante l'installazione, vedere [impedire eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure ad Connect Health](how-to-connect-health-sync.md).

Per altre informazioni su altri argomenti comuni, vedere [Azure ad Connect Sync: Scheduler](how-to-connect-sync-feature-scheduler.md) e [integrare le identità locali con Azure ad](whatis-hybrid-identity.md).
