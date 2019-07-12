---
title: Come gestire un account di Marketplace commerciale nel centro per i Partner
description: Informazioni su come gestire un account di Marketplace commerciale nel centro per i Partner.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e0c9f1fcf2d9d7e806645f1bdf9e8a6c74245a60
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619327"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Come gestire l'account di Marketplace commerciale nel centro per i Partner 

Dopo aver [creato un account del centro per i Partner](./create-account.md), è possibile gestire l'account e le offerte tramite il [dashboard commerciale Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

In questo articolo verrà esaminato come gestire l'account del centro per i Partner, tra cui: 

- [Accedere alle impostazioni di account Partner Center](#access-your-account-settings)
- [Trovare l'ID dell'editore, ID del venditore, l'ID utente e tenant di Azure AD](#account-details)
- [Aggiornare le informazioni di contatto](#contact-info)
- [Gestire dettagli finanziari (account di pagamento, profilo fiscale, lo stato di sospensione dei proventi)](#financial-details)
- [Configurare rilevamento GUID per il monitoraggio dell'utilizzo dei clienti](#tracking-guids)
- [Gestione utenti](#manage-users)
- [Gruppi di gestione](#manage-groups)
- [Applicazioni di gestione Azure AD](#manage-azure-ad-applications)
- [Definire le autorizzazioni e ruoli utente](#define-user-roles-and-permissions)
- [Gestire il tenant di Azure AD (account aziendali)](#manage-tenants)
- [Centro per i Partner Manager contratti](#agreements)


## <a name="access-your-account-settings"></a>Le impostazioni dell'account di accesso

Se non è già stato fatto, è consigliabile accedere è (o l'amministratore dell'organizzazione) il [impostazioni account](https://partner.microsoft.com/dashboard/account/management) per l'account del centro per i Partner al fine di:
- controllare lo stato di verifica account della società
- confermare l'ID del venditore, l'ID MPN, ID editore e recapiti, tra cui il contatto di venditore e responsabile approvazione aziendale
- impostare i dettagli finanziari dell'azienda, tra cui esenzione se appropriato
- creare account utente per tutti gli utenti che usano l'account aziendale nel centro per i Partner

### <a name="open-developer-settings"></a>Aprire le impostazioni per sviluppatori

Le impostazioni dell'account si trova in alto a destra delle [dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) nel centro per i Partner. Selezionare l'icona dell'ingranaggio (nell'angolo superiore destro del dashboard) e quindi selezionare **impostazioni modalità sviluppatore**. 

![Menu Impostazioni account nel centro per i Partner](./media/dashboard-developer-settings.png)

All'interno **impostazioni Account**, sarà possibile visualizzare il:
- **Dettagli account**: Tipo di account e lo stato dell'Account
- **Gli ID editore**: ID del venditore, ID utente, ID editore, i tenant di Azure AD, e così via.
- **Informazioni di contatto**: Nome visualizzato dell'autore, nome contatto del venditore, posta elettronica, telefono e indirizzo
- **Finanziari dettagli**: Account di pagamento, profilo fiscale e lo stato di sospensione dei proventi
- **Dispositivi**: I dispositivi di test associati all'account
- **GUID di rilevamento**: Qualsiasi rilevamento GUID associato all'account

### <a name="account-details"></a>Dettagli dell'account

Nella sezione dei dettagli Account, è possibile visualizzare le informazioni di base, ad esempio il **tipo di Account** (aziendale o singoli) e il **lo stato di verifica** dell'account. Durante il processo di verifica account, queste impostazioni verranno visualizzato ogni passaggio necessario, tra cui verifica tramite posta elettronica, verifica dell'occupazione e la verifica di business. È anche possibile aggiornare il messaggio di posta elettronica e inviare di nuovo la verifica se necessario. 

### <a name="publisher-ids"></a>ID editore

Nella sezione gli ID editore, è possibile vedere le **ID del venditore**, **ID MPN**, e **ID editore**. Questi valori vengono assegnati da Microsoft per identificare in modo univoco l'account per sviluppatore e non possono essere modificati.

### <a name="contact-info"></a>Informazioni di contatto

Nella sezione informazioni sul contatto, è possibile vedere le **nome visualizzato dell'editore**, **informazioni di contatto per il venditore** (il nome del contatto, indirizzo di posta elettronica, numero di telefono e indirizzo per il venditore della società) e il **aziendale responsabile approvazione** (il nome, indirizzo di posta elettronica e numero di telefono della persona che ha autorità per approvare le decisioni per la società). 

### <a name="financial-details"></a>Finanziari dettagli

Nella sezione dei dettagli finanziari, è possibile specificare o aggiornare le tue informazioni se si pubblica l'App a pagamento, componenti aggiuntivi o servizi. 

Se si prevede solo visualizzare l'elenco di offerte gratuite, non devi configurare un account di pagamento oppure compilare eventuali moduli d'imposta. Se si cambia idea e decide di vendita tramite il Microsoft, è possibile configurare l'account di pagamento e compilare i moduli d'imposta in quel momento. 

#### <a name="payout-account"></a>Account di pagamento

Un account di pagamento è il conto bancario a cui viene eseguito viene inviati dalle vendite. Il conto bancario deve essere nello stesso paese in cui è registrata l'account Partner Center.

Per configurare l'account di pagamento, è necessario **associare il proprio Account Microsoft**:
1. In **impostazioni Account**, sotto il **informazioni finanziarie** sezione, selezionare **associare il proprio Account Microsoft**. 
2. Quando richiesto, accedere con l'Account Microsoft (MSA). Questo account non può essere già associato a un altro account Partner Center. 
3. Per completare la configurazione dell'account di pagamento, disconnettersi completamente Centro per i Partner, quindi accedere nuovamente con l'Account Microsoft (anziché l'account aziendale). 

Ora che è associato l'Account Microsoft, per aggiungere un account di pagamento, è necessario:
- **Scegliere un metodo di pagamento**: Conto bancario o PayPal
- **Aggiungere le informazioni di pagamento**: Può trattarsi di scelta di un tipo di account (deposito o risparmio), immettere il nome del titolare dell'account, il numero di account e routing numero, indirizzo di fatturazione, numero di telefono o indirizzo di posta elettronica di PayPal. \* Per altre informazioni sull'uso di PayPal come metodo di pagamento di account e per scoprire se è supportato nella propria area di immissione sul mercato, vedere [PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché dobbiamo verificare la modifica dell'account, proprio come facciamo quando si configura prima l'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.  

#### <a name="tax-profile"></a>Profilo fiscale

Visualizzare il proprio stato di profilo fiscale corrente, che conferma la corretta **tipo di entità** e **informazioni sul certificato IVA** viene visualizzato. Selezionare **modifica** per aggiornare o completare eventuali necessarie form.

Per stabilire il tuo stato di imposte, è necessario specificare il paese di residenza e citizenship e completare i moduli disponibili fiscale appropriata per il paese/regione.

Indipendentemente dal proprio paese di residenza o citizenship, è necessario compilare moduli d'imposta Italia vendere eventuali offerte tramite Microsoft. I partner che soddisfano determinati requisiti di residenza degli Stati Uniti devono compilare un modulo w-9 IRS. Altri partner di fuori degli Stati Uniti deve compilare un modulo w-8 IRS. È possibile compilare questi moduli online dopo il completamento del profilo fiscale.

Una United States singoli codice fiscale o grafici, non sono necessario per ricevere i pagamenti da Microsoft o fiscali trattato vantaggi di attestazione.

È possibile completare e inviare i moduli d'imposta elettronicamente nel centro per i Partner; Nella maggior parte dei casi, non è necessario stampare e tutti i moduli di posta elettronica.

Paesi e regioni diverse hanno requisiti fiscali diversi. La quantità esatta che è necessario prestare imposte dipende i paesi e aree geografiche in cui si vendere le offerte. Microsoft rinvii sales e l'uso di imposte per tuo conto in alcuni paesi. Questi paesi verranno identificati nel processo di inserzione dell'offerta. In altri paesi, a seconda di dove sono registrati, occorre verserà le imposte sulle vendite e usare imposte per le vendite direttamente all'autorità fiscale pertinente, procurandosi locale. Inoltre, i ricavi delle vendite che viene visualizzato potrebbero essere soggetti come reddito. Ti invitiamo a contattare l'autorità rilevante per il paese o area geografica più utili per determinare le informazioni fiscali a destra per le transazioni di vendita Microsoft.

##### <a name="withholding-rates"></a>Rifiutando tariffe
Le informazioni di che invio nei tuoi moduli di imposte determina frequenza ritenuta appropriata. Si applicano le tariffe ritenuta solo per le vendite effettuate in Stati Uniti. le vendite effettuate in posizioni non statunitensi non sono soggetti a ritenuta d'. Le tariffe ritenute variano, ma per la maggior parte degli sviluppatori, la registrazione di fuori degli Stati Uniti, la frequenza predefinita è 30 %). È possibile scegliere di ridurre questa velocità se ha accettato il paese di trattato un reddito con gli Stati Uniti.

##### <a name="tax-treaty-benefits"></a>Vantaggi di trattato fiscali
Se si è fuori dagli Stati Uniti, potrebbe essere in grado di sfruttare i vantaggi dei redditi vantaggi trattato. Questi vantaggi variano da paese a paese e consentano di ridurre la quantità di imposte che Microsoft trattiene. È possibile richiedere tax trattato vantaggi completando parte II del modulo W-8BEN. È consigliabile che comunicare con le risorse appropriate nel proprio paese o regione per determinare se questi vantaggi si applicano all'utente.

[Altre informazioni sui dettagli fiscali per gli sviluppatori di app/gioco Windows e i server di pubblicazione di Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stato di sospensione dei proventi

Per impostazione predefinita, Microsoft invia i pagamenti con cadenza mensile. Tuttavia, è possibile inserire proventi in attesa, che impedisce l'invio di pagamenti al proprio account. Se si sceglie di mettere i proventi in attesa, continueremo a registrare tutti i ricavi che si ottengono e fornire i dettagli nel **riepilogo proventi**. Tuttavia, Microsoft non invierà i pagamenti al proprio account finché non verrà rimosso lo stato di attesa. 

Per inserire i pagamenti in sospeso, passare a **impostazioni Account**. Sotto **dettagli finanziari**, nella **relative ai proventi mantenere lo stato di** sezione, attivare o disattivare il dispositivo di scorrimento **su**. È possibile modificare lo stato in sospeso relative ai proventi in qualsiasi momento, ma tenere presente che la decisione influirà il pagamento mensile successivo. Ad esempio, se si vuole salvare sui proventi di aprile, assicurarsi di impostare lo stato di attesa dei proventi **su** prima della fine del mese di marzo.

Dopo aver impostato i proventi mantenere lo stato su **sul**, tutti i proventi saranno in attesa finché non si attiva/disattiva il dispositivo di scorrimento al **Off**. Quando si esegue questa operazione, sarà incluso durante il successivo ciclo di pagamento mensile (a condizione che tutte le soglie pagamento applicabili siano stati soddisfatti). Ad esempio, se è state proventi in attesa, ma potrebbe avere un pagamento generato nel mese di giugno, quindi assicurarsi di attivare o disattivare i proventi contengono lo stato su **disattivata** prima della fine del mese di maggio.

> [!NOTE]
> il **relative ai proventi stato di sospensione** selezione vale per **tutti i** fonti di ricavi che sono a pagamento tramite Microsoft Partner Center, tra cui Azure Marketplace, AppSource, Microsoft Store, pubblicità e così via.). Non è possibile selezionare gli stati di sospensione diverso per ogni origine di ricavi.

### <a name="devices"></a>Dispositivi

Impostazioni di gestione del dispositivo si applicano solo alla pubblicazione di piattaforma UWP. [Altre informazioni](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID di rilevamento

Gli identificatori univoci (globali GUID) a livello globale sono numeri di riferimento univoco (con 32 cifre esadecimali) che possono essere utilizzati per tenere traccia dell'utilizzo di Azure. 

Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare 2 GUID distinti:

*   Prodotto A su Azure Marketplace
*   Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. È anche possibile rilevare i GUID a un livello più granulare di allineamento per ogni piano all'interno dell'offerta.

Per altre informazioni, vedere la [dai clienti di Azure di rilevamento con domande frequenti sul GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gestione degli account multi-utente di

Centro per i partner si avvale [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) per la gestione e accesso all'account multiutente. L'organizzazione AD Azure viene automaticamente associato all'account Partner Center come parte del processo di registrazione. 

## <a name="manage-users"></a>Manage Users

Il **gli utenti** sezione del centro per i Partner (sotto **impostazioni Account**) utilizziamo Azure AD per gestire utenti, gruppi e applicazioni di Azure AD che hanno accesso all'account del centro per i Partner. Si noti che per gestire utenti, è necessario accedere con il [account aziendale](./company-work-accounts.md) (tenant di Azure AD associato). Per gestire gli utenti all'interno di un account di lavoro diverso o tenant, sarà necessario disconnettersi e quindi accedere nuovamente come un utente con **Manager** autorizzazioni su tale account di lavoro / tenant. 

Dopo aver effettuato con l'account aziendale (tenant di Azure AD), è possibile:
- [Aggiungere o rimuovere utenti](#add-or-remove-users)
- [Modificare una password utente](#change-a-user-password)
- [Aggiungere o rimuovere gruppi](#add-or-remove-users)
- [Aggiungere o rimuovere le applicazioni Azure AD](#add-new-azure-ad-applications)
- [Gestione delle chiavi per un'applicazione Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definire le autorizzazioni e ruoli utente](#define-user-roles-and-permissions)


Tenere presente che tutti gli utenti Partner Center (inclusi i gruppi e applicazioni di Azure AD) devono avere un account di lavoro attivo in un [tenant di Azure AD](#manage-tenants) associato all'account Partner Center. 

### <a name="add-or-remove-users"></a>Aggiungere o rimuovere utenti

L'account deve avere [ **a livello di gestione** ](#define-user-roles-and-permissions) le autorizzazioni per il [account (tenant di Azure AD) aziendale](./company-work-accounts.md) in cui si desidera aggiungere o modificare gli utenti.

#### <a name="add-existing-users"></a>Aggiungere gli utenti esistenti

Per aggiungere utenti all'account Partner Center già presenti in azienda [account (tenant di Azure AD) aziendale](./company-work-accounts.md):

1. Passare a **gli utenti** (sotto **impostazioni Account**) e selezionare **aggiungere utenti**.
2. Selezionare uno o più utenti dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
\* Se si seleziona più di un utente da aggiungere all'account del centro per i Partner, è necessario assegnare loro il ruolo stesso o un set di autorizzazioni personalizzate. Per aggiungere più utenti con ruoli diversi o le autorizzazioni, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3.  Dopo aver scelto gli utenti, fare clic **Aggiungi selezionato**.
5.  Nel **ruoli** sezione, specificare i ruoli o le autorizzazioni personalizzate per gli utenti selezionati.
6.  Selezionare **Salva**.

#### <a name="create-new-users"></a>Creare nuovi utenti

Per creare nuovo account utente, è necessario un account con [ **amministratore globale** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) autorizzazioni. 

1. Passare a **gli utenti** (sotto **impostazioni Account**), selezionare **aggiungere utenti**, quindi scegliere **creare nuovi utenti**.
1. Immettere un nome, cognome e nome utente per ogni nuovo utente. 
1. Se si desidera che il nuovo utente di un account di amministratore globale nella directory dell'organizzazione, selezionare la casella **Rendi questo utente amministratore globale in Azure AD, con controllo completo su tutte le risorse di directory**. Questo fornirà all'utente l'accesso completo a tutte le funzionalità amministrative dell'azienda di Azure AD. Sarà possibile aggiungere e gestire gli utenti nell'account di lavoro dell'organizzazione (tenant di Azure AD), anche se non in Partner Center, a meno che non è concedere all'account il ruolo o le autorizzazioni appropriate. 
1. Se è stata selezionata la casella **Rendi questo utente amministratore globale**, è necessario fornire un **posta elettronica di ripristino Password** l'utente può ripristinare la password, se necessario.
1. Nel **appartenenza al gruppo** , selezionare tutti i gruppi a cui si desidera il nuovo utente appartenere.
1. Nel **ruoli** sezione, specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

Creazione di un nuovo utente in Partner Center creerà anche un account per l'utente dell'account aziendale (tenant di Azure AD) a cui si è connessi. Apportare modifiche al nome dell'utente in Partner Center verranno apportare le stesse modifiche nell'account di lavoro dell'organizzazione (tenant di Azure AD).

#### <a name="invite-new-users-by-email"></a>Invitare nuovi utenti tramite posta elettronica

Per invitare gli utenti che non fanno attualmente parte dell'account di lavoro di società (tenant di Azure AD) tramite posta elettronica, è necessario un account con [ **amministratore globale** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) autorizzazioni. 

1. Passare a **gli utenti** (sotto **impostazioni Account**), selezionare **aggiungere utenti**, quindi scegliere **invitare gli utenti tramite posta elettronica**.
2. Immettere l'indirizzo di posta elettronica uno o più indirizzi (fino a dieci volte), separati da virgole o punti e virgola.
3. Nel **ruoli** sezione, specificare i ruoli o le autorizzazioni personalizzate per l'utente.
4. Selezionare **Salva**.

Gli utenti invitati otterrà un invito tramite posta elettronica ad aggiungere l'account del centro per i Partner. Verrà creato un nuovo account utente guest nel tuo account aziendale (tenant di Azure AD). Ogni utente dovrà accettare l'invito prima di poter accedere all'account.

Se è necessario inviare di nuovo un invito, visitare il **gli utenti** pagina, trovare l'invito dell'elenco di utenti, selezionare l'indirizzo di posta elettronica (o il testo con la dicitura *invito in sospeso*). Quindi, nella parte inferiore della pagina, selezionare **inviare di nuovo invito**.
 

> [!NOTE]
> Se l'organizzazione Usa [integrazione di directory](https://go.microsoft.com/fwlink/p/?LinkID=724033) per sincronizzare il servizio di directory locali con Azure AD, sarà possibile creare nuovi utenti, gruppi o applicazioni Azure AD nel centro per i Partner. Utente (o un altro amministratore della directory in locale) sarà necessario crearli direttamente nella directory in locale sarà in grado di vedere e aggiungerli nel centro per i Partner.

#### <a name="remove-a-user"></a>Rimuovere un utente

Per rimuovere un utente dall'account di lavoro (tenant di Azure AD), passare a **gli utenti** (sotto **impostazioni Account**), selezionare l'utente che si desidera rimuovere utilizzando la casella di controllo nella colonna all'estrema destra, quindi scegliere  **Rimuovere** fra le azioni disponibili. Verrà visualizzata una finestra a comparsa si chiede di confermare che si desidera rimuovere gli utenti selezionati.

#### <a name="change-a-user-password"></a>Modificare una password utente

Se un utente deve cambiare la password, è possibile farlo autonomamente se è stata specificata una **posta elettronica di ripristino Password** durante la creazione dell'account utente. È anche possibile aggiornare password la seguendo la procedura seguente. Per modificare la password dell'utente nell'account di lavoro di società (tenant di Azure AD), è necessario eseguire l'accesso un account con [ **amministratore globale** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) autorizzazioni. Si noti che questa operazione modificherà la password dell'utente nel tenant di Azure AD, con la password utilizzata per accedere a Centro per i Partner.

1.  Dal **gli utenti** pagina (sotto **impostazioni Account**), selezionare il nome dell'account utente che si desidera modificare.
2.  Selezionare il **la reimpostazione della password** nella parte inferiore della pagina.
3.  Verrà visualizzata una pagina di conferma che mostra le informazioni di accesso per l'utente, tra cui una password temporanea. Assicurarsi di stampare o copiare queste info e offrirlo all'utente, come sarà possibile accedere alla password temporanea, dopo aver chiuso questa pagina.


## <a name="manage-groups"></a>Gestire i gruppi

I gruppi consentono di controllare più ruoli utente e le autorizzazioni tutti insieme.

#### <a name="add-an-existing-group"></a>Aggiungere un gruppo esistente

Per aggiungere che un gruppo già esistente nell'account di lavoro dell'organizzazione (tenant di Azure AD) per l'account Partner Center: 

1.  Dal **gli utenti** pagina (sotto **impostazioni Account**), selezionare **aggiungere gruppi**.
2.  Selezionare uno o più gruppi dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare gruppi specifici.
Se si seleziona più di un gruppo da aggiungere al proprio account Partner Center, è necessario assegnare loro il ruolo stesso o un set di autorizzazioni personalizzate. Per aggiungere più gruppi con diversi ruoli o le autorizzazioni, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3.  Dopo aver scelto i gruppi, fare clic **Aggiungi selezionato**.
4.  Nel **ruoli** sezione, specificare i ruoli o le autorizzazioni personalizzate per i gruppi selezionati. Tutti i membri del gruppo potranno accedere all'account del centro per i Partner con le autorizzazioni che applicano al gruppo, indipendentemente dai ruoli e autorizzazioni associate al proprio account singoli.
5.  Selezionare **Salva**.

Quando si aggiunge un gruppo esistente, ogni utente che è un membro di tale gruppo potranno accedere all'account Centro per i Partner, con le autorizzazioni associate al ruolo assegnato del gruppo.

#### <a name="add-a-new-group"></a>Aggiungere un nuovo gruppo

Per aggiungere un nuovo gruppo all'account del centro per i Partner: 

1.  Dal **gli utenti** pagina (sotto **impostazioni Account**), selezionare **aggiungere gruppi**.
2.  Nella pagina successiva, selezionare **nuovo gruppo**.
3.  Immettere il nome visualizzato per il nuovo gruppo.
4.  Specificare i ruoli o le autorizzazioni personalizzate per il gruppo. Tutti i membri del gruppo potranno accedere all'account del centro per i Partner con le autorizzazioni che applicano in questo caso, indipendentemente dalle autorizzazioni o ruoli associate con l'account singoli.
5.  Utenti selezionati per il nuovo gruppo di nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
6.  Dopo avere selezionato gli utenti, fare clic **Aggiungi selezionato** per aggiungerli al nuovo gruppo.
7.  Selezionare **Salva**.

Si noti che questo nuovo gruppo verrà creato nell'account di lavoro dell'organizzazione (tenant di Azure AD), non solo nel proprio account Partner Center.

#### <a name="remove-a-group"></a>Rimuovere un gruppo

Per rimuovere un gruppo dal proprio account aziendale (tenant di Azure AD), passare a **gli utenti** (sotto **impostazioni Account**), selezionare il gruppo che si desidera rimuovere utilizzando la casella di controllo nella colonna all'estrema destra, quindi scegliere  **Rimuovere** fra le azioni disponibili. Verrà visualizzata una finestra a comparsa si chiede di confermare che si desidera rimuovere i gruppi selezionati.

## <a name="manage-azure-ad-applications"></a>Gestire le applicazioni di Azure AD

È possibile consentire le applicazioni o servizi che fanno parte di Azure dell'azienda AD accedere all'account del centro per i Partner. 

#### <a name="add-existing-azure-ad-applications"></a>Aggiungere le applicazioni esistenti di Azure AD 

Per aggiungere le applicazioni che già esistono in Azure Active Directory aziendale: 

1.  Dal **gli utenti** pagina (sotto **impostazioni Account**), selezionare **aggiungere applicazioni di Azure AD**.
2.  Selezionare uno o più applicazioni di Azure AD dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare specifiche delle applicazioni Azure AD. Se si seleziona più di un'applicazione di Azure AD per aggiungere l'account Partner Center, è necessario assegnare loro il ruolo stesso o un set di autorizzazioni personalizzate. Per aggiungere più applicazioni di Azure AD con diversi ruoli o le autorizzazioni, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3.  Dopo aver selezionato le applicazioni Azure AD, fare clic **Aggiungi selezionato**.
5.  Nel **ruoli** sezione, specificare i ruoli o le autorizzazioni personalizzate per il profilo selezionato o più applicazioni di Azure AD.
6.  Selezionare **Salva**.

#### <a name="add-new-azure-ad-applications"></a>Aggiungere di nuovo di Azure le applicazioni di AD 

Se si desidera concedere l'accesso Partner Center a Azure completamente nuovo account di Active Directory dell'applicazione, è possibile crearne uno nel **utenti** sezione. Si noti che questo creerà un nuovo account nell'account di lavoro di società (tenant di Azure AD), non solo nel proprio account Partner Center. Se si usa principalmente l'applicazione di Azure AD per l'autenticazione del centro per i Partner e non necessario agli utenti di accedervi direttamente, è possibile immettere qualsiasi indirizzo valido per il **URL di risposta** e **URI ID App**, a condizione come tali valori non vengono utilizzati da un'altra applicazione di Azure AD nella directory.

1.  Dal **gli utenti** pagina (sotto **impostazioni Account**), selezionare **aggiungere applicazioni di Azure AD**.
2.  Nella pagina successiva, selezionare **New Azure AD application**.
3.  Immettere il **URL di risposta** per la nuova applicazione Azure AD. Si tratta dell'URL in cui gli utenti possono accedere e usare l'applicazione di Azure AD (talvolta noto anche come l'URL dell'App o l'URL Sign-On). Il **URL di risposta** non può superare i 256 caratteri e deve essere univoco all'interno della directory.
4.  Immettere il **URI ID App** per la nuova applicazione Azure AD. Questo è un identificatore logico per l'applicazione di Azure AD che viene visualizzata quando viene inviata una richiesta single sign-on di Azure ad. Si noti che il **URI ID App** deve essere univoco per ogni applicazione di Azure AD nella directory. Questo ID non può superare i 256 caratteri. Per altre informazioni sull'URI ID App, vedi [integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  Nel **ruoli** sezione, specificare i ruoli o le autorizzazioni personalizzate per l'applicazione Azure AD.
6.  Selezionare **Salva**.

Dopo aver aggiunto o creare un'applicazione Azure AD, è possibile restituire per il **utenti** sezione e selezionare il nome dell'applicazione per verificare le impostazioni per l'applicazione, inclusi l'ID Tenant, ID Client, URL di risposta e URI ID App.

#### <a name="remove-an-application"></a>Rimuovere un'applicazione

Per rimuovere un'applicazione dal proprio account aziendale (tenant di Azure AD), passare a **gli utenti** (sotto **impostazioni Account**), selezionare l'applicazione che si desidera rimuovere utilizzando la casella di controllo nella colonna all'estrema destra, quindi scegliere **rimuovere** fra le azioni disponibili. Verrà visualizzata una finestra a comparsa si chiede di confermare che si desidera rimuovere le applicazioni selezionate.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gestione delle chiavi per un'applicazione Azure AD

Se l'applicazione Azure AD legge e scrive i dati in Microsoft Azure AD, che è necessario una chiave. È possibile creare chiavi per un'applicazione Azure AD, modificando le informazioni nel centro per i Partner. È anche possibile rimuovere le chiavi che non sono più necessari.

1.  Dal **gli utenti** pagina (sotto **impostazioni Account**), selezionare il nome dell'applicazione Azure AD. Scoprirai tutti i tasti attivi per l'applicazione di Azure AD, tra cui la data in cui è stata creata la chiave e quando scadrà. 
2. Per rimuovere una chiave che non è più necessario, selezionare **rimuovere**.
3.  Per aggiungere una nuova chiave, selezionare **Aggiungi nuova chiave**.
4.  Si noterà una schermata con il **ID Client** e **valori di chiave**. Assicurarsi di stampare o copiare le informazioni di questo tipo, come sarà possibile accedervi nuovamente dopo aver chiuso questa pagina.
4.  Se si desidera creare più chiavi, selezionare **aggiungere un'altra chiave**.


### <a name="define-user-roles-and-permissions"></a>Definire le autorizzazioni e ruoli utente

Agli utenti della società possono essere assegnati i ruoli e autorizzazioni nel programma commerciale Marketplace nel centro per i Partner seguenti. 

Si noti come ruoli tenant di Azure Active Directory (AAD) includono amministratore globale, Amministratore utenti e ruoli CSP. Non-AAD ruoli sono tali che non si gestisce il tenant e includono amministratore MPN, admin profilo business, amministrazione di referral, admin incentivi e incentivi utente.


|**Ruolo**|**Autorizzazioni**|
|----------------------------------|:---------------------------------|
|Amministratore globale|• Possono accedere a tutti i Microsoft account/servizi con privilegi completi
|      |• Creare ticket di supporto per il centro per i Partner
||• Visualizzare gli accordi sui listini prezzi e offerte
||• Visualizzare, creare e gestire gli utenti partner|
|Manager|• Possono accedere a tutte le funzionalità di account Microsoft, ad eccezione delle impostazioni relative a pagamenti e fiscali
|      |• È possibile gestire utenti, ruoli e utilizzare account (tenant)|
|Sviluppatore|• Possono caricare pacchetti, inviare le App e componenti aggiuntivi e consente di visualizzare il report di utilizzo per i dettagli di dati di telemetria
|      |• Non è possibile accedere alle impostazioni di account o le informazioni finanziarie|
|Collaboratore di Business|• È possibile accedere a informazioni finanziarie e impostare i dettagli sui prezzi
|      |• Non è possibile creare o inviare nuove app e componenti aggiuntivi|
|Finanziari per i collaboratori|• Consente di visualizzare report sui proventi
|      |• Non è possibile apportare modifiche alle impostazioni o le app|
|Esperti di marketing|• Possono rispondere ai commenti dei clienti e ai report non finanziaria
|      |• Non è possibile apportare modifiche alle impostazioni o le app|

Per altre informazioni sulla gestione dei ruoli e autorizzazioni in altre aree del centro per i Partner, ad esempio Azure Active Directory (Active Directory), Cloud Solution Provider (CSP), il fornitore di Pannello di controllo (CPV), gli utenti Guest o Microsoft Partner Network (MPN), vedere [assegnare ruoli degli utenti e autorizzazioni nel centro per i Partner](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gestire i tenant

Un tenant di Azure Active Directory (AD), detto anche "account di lavoro" in questa documentazione è una rappresentazione della propria organizzazione configurazione nel portale di Azure e consente di gestire un'istanza specifica di servizi cloud Microsoft per uso interno e gli utenti esterni. Se l'organizzazione ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, tenant di Azure AD è stato stabilito per l'utente. 

È possibile configurare più tenant da usare con Centro per i Partner. Tutti gli utenti con il **Manager** ruolo nell'account di Centro per i Partner avranno la possibilità di aggiungere e rimuovere tenant di Azure AD dall'account.  

### <a name="add-an-existing-tenant"></a>Aggiungere un tenant esistente

Per associare un altro tenant di Azure AD con l'account del centro per i Partner:

1.  Dal **tenant** pagina (sotto **impostazioni Account**), selezionare **associare un altro tenant di Azure AD**.
2. Immettere le credenziali di Azure AD per il tenant che si desidera associare.
3. Esaminare il nome dell'organizzazione e di dominio per il tenant di Azure AD. Per completare l'associazione, selezionare **Confirm**.

Se l'associazione ha esito positivo, sarà quindi possibile aggiungere e gestire gli utenti dell'account di **utenti** sezione nel centro per i Partner.

### <a name="create-a-new-tenant"></a>Creare un nuovo tenant

Per creare un tenant di marchio nuovo Azure AD con l'account del centro per i Partner:

1.  Dal **tenant** pagina (sotto **impostazioni Account**), selezionare **crea un nuovo tenant di Azure AD**.
2. Immettere le informazioni di directory per il nuovo Azure AD:
    - **Nome di dominio**: Il nome univoco che si userà per il dominio di Azure AD, insieme a ". onmicrosoft.com". Ad esempio, se è stato immesso "example", il dominio di Azure AD sarà "example.onmicrosoft.com".
    - **Posta elettronica di contatto**: Un indirizzo di posta elettronica dove possiamo contattarti riguardo al proprio account se necessario.
    - **Informazioni sull'account utente di amministratore globale**: Il nome, ultimo nome, nome utente e password che si desidera utilizzare per il nuovo account di amministratore globale.
3. Selezionare **Create** per confermare le informazioni di dominio e account di nuovo.
4. Accedere con il nuovo Azure AD amministratore globale username e password per iniziare [aggiunta e gestione degli utenti](#manage-users).

Per altre informazioni sulla creazione di nuovi tenant all'interno del portale di Azure, invece che tramite il portale Centro per i Partner, vedere l'articolo [creare un nuovo tenant in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Rimuovere un tenant

Per rimuovere un tenant dall'account di Centro per i Partner, trovare il nome sul **tenant** pagina (nella **impostazioni Account**), quindi selezionare **Rimuovi**. Verrà chiesto di confermare che si desidera rimuovere il tenant. Una volta in questo caso, nessun utente nel tenant saranno in grado di accedere all'account del centro per i Partner e tutte le autorizzazioni che è configurato per tali utenti verranno rimossi.

Quando si rimuove un tenant, tutti gli utenti che sono stati aggiunti all'account del centro per i Partner di tenant non saranno in grado di accedere all'account.

> [!TIP]
> Se si è attualmente connessi al centro per i Partner con un account nello stesso tenant, è possibile rimuovere un tenant. Per rimuovere un tenant, è necessario accedere al centro per i Partner come un **Manager** un altro tenant di cui è associato all'account. Se è presente un solo tenant associati all'account, i tenant può essere rimossi solo dopo l'accesso con l'account Microsoft che ha aperto l'account.


## <a name="agreements"></a>Contratti

Il **contratti** sezione del centro per i Partner (sotto **impostazioni Account**) si è visualizzare un elenco dei contratti di pubblicazione che è stato autorizzato. Questi accordi sono elencati in base al nome e numero di versione, inclusa la data che viene accettata e il nome dell'utente che ha accettato il contratto. 

**Le azioni necessarie** venga visualizzato nella parte superiore della pagina se sono presenti aggiornamenti del contratto che richiedono attenzione. Per accettare un contratto aggiornato, innanzitutto leggere la versione contratto collegata, quindi selezionare **accetta contratto**. 

Per informazioni sui contratti di Cloud Solution Provider (CSP) nel centro per i Partner, visitare [contratti di Cloud di Microsoft per regione e lingua](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Passaggi successivi

- [Creare una nuova offerta SaaS](./create-new-saas-offer.md)
