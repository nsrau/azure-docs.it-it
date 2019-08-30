---
title: Come gestire un account Marketplace commerciale nel centro per i partner
description: Informazioni su come gestire un account Marketplace commerciale nel centro per i partner.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: f0557f21e791dd22df16451003d5efcfc72c56b4
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164726"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Come gestire l'account del Marketplace commerciale nel centro per i partner 

Dopo aver [creato un account del centro](./create-account.md)per i partner, è possibile gestire l'account e le offerte usando il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Questo articolo illustra come gestire l'account del centro per i partner, inclusa la procedura per: 

- [Accedere alle impostazioni dell'account del centro per i partner](#access-your-account-settings)
- [Trovare l'ID editore, l'ID venditore, l'ID utente e i tenant di Azure AD](#account-details)
- [Aggiornare le informazioni di contatto](#contact-info)
- [Gestisci i dettagli finanziari (account di pagamento, profilo fiscale, stato di attesa dei pagamenti)](#financial-details)
- [Configurare i GUID di rilevamento per il monitoraggio dell'utilizzo dei clienti](#tracking-guids)
- [Gestire gli utenti](#manage-users)
- [Gestire i gruppi](#manage-groups)
- [Gestire applicazioni Azure AD](#manage-azure-ad-applications)
- [Definire i ruoli utente e le autorizzazioni](#define-user-roles-and-permissions)
- [Gestire i tenant di Azure AD (account di lavoro)](#manage-tenants)
- [Gestisci contratti con il centro per i partner](#agreements)


## <a name="access-your-account-settings"></a>Accedere alle impostazioni dell'account

Se non è ancora stato fatto, l'utente o l'amministratore dell'organizzazione deve accedere alle impostazioni dell' [account](https://partner.microsoft.com/dashboard/account/management) per l'account del centro per i partner, al fine di:
- verificare lo stato di verifica dell'account aziendale
- confermare l'ID venditore, l'ID MPN, l'ID editore e le informazioni di contatto, inclusi il responsabile approvazione della società e il contatto del venditore
- configurare i dettagli finanziari dell'azienda, incluse le esenzioni fiscali, se appropriato
- creare account utente per chiunque usi il proprio account aziendale nel centro per i partner

### <a name="open-developer-settings"></a>Apri impostazioni sviluppatore

Le impostazioni dell'account si trovano nell'angolo superiore destro del [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) nel centro per i partner. Selezionare l'icona a forma di ingranaggio nell'angolo in alto a destra del dashboard e quindi selezionare **Impostazioni sviluppatore**. 

![Menu delle impostazioni dell'account nel centro per i partner](./media/dashboard-developer-settings.png)

All'interno **delle impostazioni dell'account**, sarà possibile visualizzare:
- **Dettagli account**: Stato dell'account e del tipo di account
- **ID editore**: ID venditore, ID utente, ID editore, tenant Azure AD e così via.
- **Informazioni contatto**: Nome visualizzato dell'editore, nome contatto del venditore, indirizzo di posta elettronica, telefono e indirizzo
- **Dettagli finanziari**: Stato dell'account, del profilo fiscale e del pagamento
- **Dispositivi**: Qualsiasi dispositivo di test associato all'account
- **GUID di rilevamento**: Qualsiasi GUID di rilevamento associato all'account

### <a name="account-details"></a>Dettagli dell'account

Nella sezione dettagli account è possibile visualizzare le informazioni di base, ad esempio il **tipo di account** (società o utente singolo) e lo stato di **Verifica** dell'account. Durante il processo di verifica dell'account, queste impostazioni visualizzeranno ogni passaggio necessario, tra cui verifica della posta elettronica, verifica dell'utilizzo e verifica dell'attività. Se necessario, è anche possibile aggiornare la posta elettronica e inviare nuovamente la verifica. 

### <a name="publisher-ids"></a>ID editore

Nella sezione ID editore è possibile visualizzare l'ID **venditore**, l' **ID MPN**e l' **ID editore**. Questi valori vengono assegnati da Microsoft per identificare in modo univoco l'account sviluppatore e non possono essere modificati.

### <a name="contact-info"></a>Informazioni contatto

Nella sezione informazioni di contatto è possibile visualizzare il **nome visualizzato dell'editore**, le **informazioni di contatto del venditore** (il nome del contatto, la posta elettronica, il numero di telefono e l'indirizzo del venditore della società) e il **responsabile approvazione della società** (nome, indirizzo di posta elettronica e numero di telefono del utente con autorità per approvare le decisioni per l'azienda. 

### <a name="financial-details"></a>Dettagli finanziari

Nella sezione dettagli finanziari è possibile fornire o aggiornare le informazioni finanziarie se si pubblicano app a pagamento, componenti aggiuntivi o servizi. 

Se si prevede di elencare le offerte gratuite, non è necessario configurare un account di pagamento o compilare eventuali moduli fiscali. Se in seguito si cambia idea e si decide di voler vendere tramite Microsoft, è possibile configurare l'account di pagamento e compilare i moduli fiscali in quel momento. 

#### <a name="payout-account"></a>Account di pagamento

Un account di pagamento è il conto bancario a cui vengono inviati i proseguimenti dalle vendite. Questo account bancario deve trovarsi nello stesso paese in cui è stato registrato l'account del centro per i partner.

Per configurare l'account di pagamento, è necessario **associare l'account Microsoft**:
1. In **Impostazioni account**, nella sezione **dettagli finanziari** , selezionare **associa il proprio account Microsoft**. 
2. Quando richiesto, accedere con il proprio account Microsoft (MSA). Questo account non può essere già associato a un altro account del centro per i partner. 
3. Per completare la configurazione dell'account di pagamento, accedere completamente al centro per i partner, quindi accedere nuovamente con l'account Microsoft, anziché con l'account aziendale. 

Ora che l'account Microsoft è associato, per aggiungere un account di pagamento, sarà necessario:
- **Scegliere un metodo di pagamento**: Conto bancario o PayPal
- **Aggiungere le informazioni di pagamento**: Ciò può includere la scelta di un tipo di account (controllo o risparmio), l'immissione del nome del titolare dell'account, il numero di account, il numero di routing, l'indirizzo di fatturazione, il numero di telefono o l'indirizzo di posta elettronica PayPal. \* Per altre informazioni sull'uso di PayPal come metodo di pagamento dell'account e per sapere se è supportato nell'area del mercato, vedere [informazioni su PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, proprio come quando si configura prima l'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.  

#### <a name="tax-profile"></a>Profilo fiscale

Esaminare lo stato corrente del profilo fiscale, verificare che vengano visualizzati il **tipo di entità** e le **informazioni sui certificati fiscali** corretti. Selezionare **modifica** per aggiornare o completare i moduli richiesti.

Per stabilire lo stato di imposta, è necessario specificare il paese di residenza e cittadinanza e completare i moduli fiscali appropriati associati al paese/area geografica.

Indipendentemente dal paese di residenza o cittadinanza, è necessario compilare Stati Uniti moduli fiscali per vendere eventuali offerte tramite Microsoft. I partner che soddisfano determinati requisiti di residenza di Stati Uniti devono compilare un modulo IRS W-9. Altri partner all'esterno del Stati Uniti devono compilare un modulo IRS W-8. È possibile compilare questi moduli online quando si completa il profilo fiscale.

Non è necessario un Stati Uniti numero di identificazione del contribuente singolo (o it) per ricevere i pagamenti da Microsoft o per rivendicare i vantaggi del trattato fiscale.

È possibile completare e inviare i moduli fiscali elettronicamente nel centro per i partner; nella maggior parte dei casi non è necessario stampare e inviare moduli.

Paesi e regioni diversi presentano requisiti fiscali diversi. La quantità esatta di cui è necessario pagare le tasse dipende dai paesi e dalle aree in cui si vendono le offerte. Microsoft è responsabile delle vendite e dell'utilizzo delle imposte per conto dell'utente in alcuni paesi. Questi paesi verranno identificati nel processo di visualizzazione dell'offerta. In altri paesi, a seconda della posizione in cui viene effettuata la registrazione, potrebbe essere necessario ricorrere alle vendite e utilizzare le imposte per le vendite direttamente all'autorità di tassazione locale. Inoltre, le vendite effettuate dall'utente possono essere imposte come reddito. Si consiglia vivamente di contattare l'autorità pertinente per il paese o l'area geografica che può aiutare a determinare le informazioni fiscali corrette per le transazioni di vendita Microsoft.

##### <a name="withholding-rates"></a>Tariffe ritenute
Le informazioni inviate nei moduli fiscali determinano il tasso di ritenuta fiscale appropriato. Il tasso di ritenuta si applica solo alle vendite effettuate nell'Stati Uniti; le vendite effettuate in posizioni diverse dagli Stati Uniti non sono soggette a ritenute. Le percentuali di ritenzione variano, ma per la maggior parte degli sviluppatori che si registrano all'esterno del Stati Uniti, la tariffa predefinita è il 30%. È possibile ridurre questa frequenza se il paese ha accettato un accordo fiscale sul reddito con la Stati Uniti.

##### <a name="tax-treaty-benefits"></a>Vantaggi del trattato fiscale
Se si è al di fuori dell'Stati Uniti, è possibile sfruttare i vantaggi del trattato fiscale. Questi vantaggi variano a seconda del paese e possono consentire di ridurre la quantità di imposte ritenute da Microsoft. È possibile rivendicare i vantaggi del trattato fiscale completando la parte 2 del modulo W-8BEN. È consigliabile comunicare con le risorse appropriate del paese o dell'area geografica per determinare se questi vantaggi si applicano all'utente.

[Altre informazioni sui dettagli delle imposte per gli sviluppatori di app e giochi di Windows e per gli editori di Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stato di attesa del pagamento

Per impostazione predefinita, Microsoft invia i pagamenti su base mensile. Tuttavia, è possibile mettere in attesa i pagamenti, evitando così di inviare i pagamenti al proprio account. Se si sceglie di mettere in attesa i pagamenti, si continuerà a registrare i ricavi acquisiti e fornire i dettagli nel **Riepilogo dei pagamenti**. Tuttavia, non verrà inviato alcun pagamento all'account fino a quando non viene rimossa l'attesa. 

Per inserire i pagamenti in attesa, passare a **Impostazioni account**. In **dettagli finanziari**, nella sezione **stato di attesa dei pagamenti** , impostare il dispositivo di scorrimento **su**attivato. È possibile modificare lo stato di mantenimento dei pagamenti in qualsiasi momento, ma tenere presente che la decisione avrà un effetto sui prossimi pagamenti mensili. Se ad esempio si desidera mantenere il pagamento di aprile, assicurarsi di impostare lo stato di mantenimento dei pagamenti **su on** prima della fine di marzo.

Dopo aver impostato lo stato di mantenimento dei pagamenti **su on**, tutti i pagamenti saranno in attesa fino a quando il dispositivo di scorrimento nonviene disattivato. Quando si esegue questa operazione, si verrà inclusi durante il successivo ciclo di pagamento mensile (purché siano state soddisfatte le soglie di pagamento applicabili). Se, ad esempio, sono stati rilevati i pagamenti in attesa, ma si vuole che venga generato un pagamento in giugno, assicurarsi di impostare lo stato di mantenimento dei pagamenti su **off** prima della fine di maggio.

> [!NOTE]
> La selezione **dello stato di mantenimento dei pagamenti** si applica a **tutte le** origini dei ricavi pagate tramite il centro per i partner Microsoft, tra cui Azure Marketplace, AppSource, Microsoft Store, pubblicità e così via. Non è possibile selezionare Stati di attesa diversi per ogni origine dei ricavi.

### <a name="devices"></a>Dispositivi

Le impostazioni di gestione del dispositivo si applicano solo alla pubblicazione UWP. [Altre informazioni](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID di rilevamento

Gli identificatori univoci globali (GUID) sono numeri di riferimento univoci (con cifre esadecimali 32) che possono essere usati per tenere traccia dell'utilizzo di Azure. 

Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare 2 GUID distinti:

*   Prodotto A su Azure Marketplace
*   Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. È anche possibile tenere traccia dei GUID a un livello più granulare allineando a ogni piano all'interno dell'offerta.

Per altre informazioni, vedere le [domande frequenti su come tenere traccia dell'utilizzo dei clienti di Azure con GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gestione degli account multiutente

Il centro per i partner utilizza [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure ad) per l'accesso e la gestione di account multiutente. Il Azure AD dell'organizzazione viene automaticamente associato all'account del centro per i partner come parte del processo di registrazione. 

## <a name="manage-users"></a>Gestisci utenti

La sezione **utenti** del centro per i partner (in **Impostazioni account**) consente di usare Azure ad per gestire gli utenti, i gruppi e le applicazioni Azure ad che hanno accesso all'account del centro per i partner. Si noti che per gestire gli utenti, è necessario aver eseguito l'accesso con l' [account](./company-work-accounts.md) aziendale (il tenant Azure ad associato). Per gestire gli utenti all'interno di un account aziendale o tenant diverso, è necessario disconnettersi e quindi accedere di nuovo come utente con autorizzazioni di **gestione** per tale account di lavoro/tenant. 

Dopo aver eseguito l'accesso con l'account aziendale (Azure AD tenant), è possibile:
- [Aggiunta o rimozione di utenti](#add-or-remove-users)
- [Modificare una password utente](#change-a-user-password)
- [Aggiungi o Rimuovi gruppi](#add-or-remove-users)
- [Aggiunta o rimozione di applicazioni Azure AD](#add-new-azure-ad-applications)
- [Gestire le chiavi per un'applicazione Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definire i ruoli utente e le autorizzazioni](#define-user-roles-and-permissions)


Tenere presente che tutti gli utenti del centro per i partner (inclusi gruppi e applicazioni Azure AD) devono avere un account di lavoro attivo in un [tenant di Azure ad](#manage-tenants) associato all'account del centro per i partner. 

### <a name="add-or-remove-users"></a>Aggiungere o rimuovere utenti

L'account deve disporre delle autorizzazioni a [**livello di gestione**](#define-user-roles-and-permissions) per l' [account aziendale (tenant Azure ad)](./company-work-accounts.md) in cui si desidera aggiungere o modificare gli utenti.

#### <a name="add-existing-users"></a>Aggiungi utenti esistenti

Per aggiungere utenti all'account del centro per i partner già esistenti nell'account aziendale dell'azienda [(Azure ad tenant)](./company-work-accounts.md):

1. Passare a **utenti** (in **Impostazioni account**) e selezionare **Aggiungi utenti**.
2. Selezionare uno o più utenti nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
\* Se si seleziona più di un utente da aggiungere all'account del centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più utenti con ruoli o autorizzazioni diverse, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3.  Al termine della scelta degli utenti, fare clic su **Aggiungi selezionato**.
5.  Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per gli utenti selezionati.
6.  Selezionare **Salva**.

#### <a name="create-new-users"></a>Creazione di nuovi utenti

Per creare nuovi account utente, è necessario disporre di un account con autorizzazioni di [**amministratore globale**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . 

1. Passare a **utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **Crea nuovi utenti**.
1. Immettere il nome, il cognome e il nome utente per ogni nuovo utente. 
1. Se si desidera che il nuovo utente disponga di un account amministratore globale nella directory dell'organizzazione, selezionare la casella Assegna a **questo utente un amministratore globale nel Azure ad, con il controllo completo su tutte le risorse della directory**. In questo modo si concede all'utente l'accesso completo a tutte le funzionalità amministrative del Azure AD dell'azienda. Potranno aggiungere e gestire gli utenti nell'account aziendale (Azure AD tenant) dell'organizzazione, anche se non nel centro per i partner, a meno che non si conceda all'account il ruolo o le autorizzazioni appropriate. 
1. Se è stata selezionata la casella per **rendere l'utente un amministratore globale**, è necessario fornire un **messaggio di posta elettronica** per il ripristino della password per consentire all'utente di ripristinare la password, se necessario.
1. Nella sezione **appartenenza a gruppo** selezionare tutti i gruppi a cui si desidera che il nuovo utente appartenga.
1. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

La creazione di un nuovo utente nel centro per i partner creerà anche un account per tale utente nell'account aziendale (tenant Azure AD) a cui è stato effettuato l'accesso. Se si apportano modifiche al nome di un utente nel centro per i partner, le stesse modifiche vengono apportate all'account aziendale dell'organizzazione (Azure AD tenant).

#### <a name="invite-new-users-by-email"></a>Invita nuovi utenti tramite posta elettronica

Per invitare gli utenti che non fanno parte dell'account aziendale aziendale (Azure AD tenant) tramite posta elettronica, è necessario disporre di un account con autorizzazioni di [**amministratore globale**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . 

1. Passare a **utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **invita utenti per posta elettronica**.
2. Immettere uno o più indirizzi di posta elettronica (fino a dieci), separati da virgole o punti e virgola.
3. Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
4. Selezionare **Salva**.

Gli utenti invitati riceveranno un invito tramite posta elettronica per aggiungere l'account del centro per i partner. Verrà creato un nuovo account utente guest nell'account aziendale (Azure AD tenant). Ogni utente dovrà accettare l'invito per poter accedere all'account.

Se è necessario inviare di nuovo un invito, visitare la pagina **utenti** , trovare l'invito nell'elenco degli utenti, selezionare l'indirizzo di posta elettronica o il testo che indica l' *invito in sospeso*. Quindi, nella parte inferiore della pagina, selezionare Invia di nuovo l' **invito**.
 

> [!NOTE]
> Se l'organizzazione usa l' [integrazione della directory](https://go.microsoft.com/fwlink/p/?LinkID=724033) per sincronizzare il servizio directory locale con la Azure ad, non sarà possibile creare nuovi utenti, gruppi o applicazioni Azure ad nel centro per i partner. È necessario che l'utente (o un altro amministratore nella directory locale) la crei direttamente nella directory locale prima di poterlo visualizzare e aggiungere al centro per i partner.

#### <a name="remove-a-user"></a>Rimuovere un utente

Per rimuovere un utente dall'account aziendale (Azure AD tenant), passare a **utenti** (in **Impostazioni account**), selezionare l'utente che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui è possibile confermare che si desidera rimuovere gli utenti selezionati.

#### <a name="change-a-user-password"></a>Modificare una password utente

Se è necessario modificare la password di uno degli utenti, è possibile farlo autonomamente se è stato fornito un **messaggio di posta elettronica** per il ripristino della password durante la creazione dell'account utente. È anche possibile aggiornare la password di un utente attenendosi alla procedura descritta di seguito. Per modificare la password di un utente nell'account aziendale (Azure AD tenant), è necessario eseguire l'accesso con un account con autorizzazioni di [**amministratore globale**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) . Si noti che in questo modo verrà modificata la password dell'utente nel tenant di Azure AD, insieme alla password usata per accedere al centro per i partner.

1.  Nella pagina **utenti** (in **Impostazioni account**) selezionare il nome dell'account utente che si desidera modificare.
2.  Selezionare il pulsante **Reimposta password** nella parte inferiore della pagina.
3.  Verrà visualizzata una pagina di conferma che mostra le informazioni di accesso per l'utente, inclusa una password temporanea. Assicurarsi di stampare o copiare queste informazioni e di fornirle all'utente, perché non sarà possibile accedere alla password temporanea dopo aver lasciato questa pagina.


## <a name="manage-groups"></a>Gestisci gruppi

I gruppi consentono di controllare contemporaneamente più ruoli utente e autorizzazioni.

#### <a name="add-an-existing-group"></a>Aggiungi un gruppo esistente

Per aggiungere un gruppo già esistente nell'account di lavoro dell'organizzazione (tenant Azure AD) all'account del centro per i partner: 

1.  Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
2.  Selezionare uno o più gruppi dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare gruppi specifici.
Se si seleziona più di un gruppo da aggiungere all'account del centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più gruppi con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3.  Al termine della scelta di gruppi, fare clic su **Aggiungi selezionato**.
4.  Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per il gruppo o i gruppi selezionati. Tutti i membri del gruppo saranno in grado di accedere all'account del centro per i partner con le autorizzazioni valide per il gruppo, indipendentemente dai ruoli e dalle autorizzazioni associate al proprio account.
5.  Selezionare **Salva**.

Quando si aggiunge un gruppo esistente, ogni utente membro di tale gruppo sarà in grado di accedere all'account del centro per i partner, con le autorizzazioni associate al ruolo assegnato del gruppo.

#### <a name="add-a-new-group"></a>Aggiungere un nuovo gruppo

Per aggiungere un nuovo gruppo all'account del centro per i partner: 

1.  Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
2.  Nella pagina successiva selezionare **nuovo gruppo**.
3.  Immettere il nome visualizzato per il nuovo gruppo.
4.  Specificare i ruoli o le autorizzazioni personalizzate per il gruppo. Tutti i membri del gruppo saranno in grado di accedere all'account del centro per i partner con le autorizzazioni applicate, indipendentemente dai ruoli o dalle autorizzazioni associate al rispettivo account.
5.  Selezionare gli utenti per il nuovo gruppo nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
6.  Al termine della selezione degli utenti, fare clic su **Aggiungi selezionati** per aggiungerli al nuovo gruppo.
7.  Selezionare **Salva**.

Si noti che questo nuovo gruppo verrà creato anche nell'account di lavoro dell'organizzazione (tenant Azure AD), non solo nell'account del centro per i partner.

#### <a name="remove-a-group"></a>Rimuovere un gruppo

Per rimuovere un gruppo dall'account aziendale (Azure AD tenant), passare a **utenti** (in **Impostazioni account**), selezionare il gruppo che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui è possibile confermare che si desidera rimuovere i gruppi selezionati.

## <a name="manage-azure-ad-applications"></a>Gestire applicazioni Azure AD

È possibile consentire alle applicazioni o ai servizi che fanno parte del Azure AD dell'azienda di accedere all'account del centro per i partner. 

#### <a name="add-existing-azure-ad-applications"></a>Aggiungere applicazioni Azure AD esistenti 

Per aggiungere applicazioni già presenti nel Azure Active Directory della società: 

1.  Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi Azure ad applicazioni**.
2.  Selezionare una o più applicazioni Azure AD dall'elenco visualizzato. È possibile utilizzare la casella di ricerca per cercare specifiche applicazioni Azure AD. Se si seleziona più di un'applicazione Azure AD da aggiungere all'account del centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più applicazioni Azure AD con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3.  Al termine della selezione di Azure AD applicazioni, fare clic su **Aggiungi selezionato**.
5.  Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per le applicazioni Azure ad selezionate.
6.  Selezionare **Salva**.

#### <a name="add-new-azure-ad-applications"></a>Aggiungi nuove applicazioni Azure AD 

Se si desidera concedere l'accesso al centro per i partner a un nuovo account dell'applicazione Azure AD, è possibile crearne uno nella sezione **utenti** . Si noti che in questo modo verrà creato un nuovo account nell'account aziendale aziendale (tenant Azure AD), non solo nell'account del centro per i partner. Se si usa principalmente questa applicazione Azure AD per l'autenticazione del centro per i partner e non è necessario che gli utenti accedano direttamente, è possibile immettere qualsiasi indirizzo valido per l' **URL di risposta** e l' **URI ID app**, purché tali valori non vengano usati da altri Azure Applicazione AD nella directory.

1.  Nella pagina **utenti** (in **Impostazioni account**) selezionare **Aggiungi Azure ad applicazioni**.
2.  Nella pagina successiva selezionare **nuovo Azure ad applicazione**.
3.  Immettere l' **URL di risposta** per la nuova applicazione Azure ad. Si tratta dell'URL in cui gli utenti possono accedere e usare l'applicazione Azure AD (talvolta nota anche come URL dell'app o URL di accesso). L' **URL di risposta** non può contenere più di 256 caratteri e deve essere univoco nella directory.
4.  Immettere l' **URI ID app** per la nuova applicazione Azure ad. Si tratta di un identificatore logico per l'applicazione Azure AD presentata quando viene inviata una richiesta di Single Sign-On a Azure AD. Si noti che l' **URI ID app** deve essere univoco per ogni Azure ad applicazione nella directory. Questo ID non può contenere più di 256 caratteri. Per altre informazioni sull'URI ID app, vedere [integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  Nella sezione **ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'applicazione Azure ad.
6.  Selezionare **Salva**.

Dopo aver aggiunto o creato un'applicazione Azure AD, è possibile tornare alla sezione **utenti** e selezionare il nome dell'applicazione per rivedere le impostazioni per l'applicazione, inclusi ID tenant, ID client, URL di risposta e URI ID app.

#### <a name="remove-an-application"></a>Rimuovere un'applicazione

Per rimuovere un'applicazione dall'account aziendale (Azure AD tenant), passare a **utenti** (in **Impostazioni account**), selezionare l'applicazione che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** dal azioni disponibili. Verrà visualizzata una finestra popup in cui è possibile confermare che si desidera rimuovere le applicazioni selezionate.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gestire le chiavi per un'applicazione Azure AD

Se l'applicazione Azure AD legge e scrive i dati in Microsoft Azure AD, sarà necessaria una chiave. È possibile creare chiavi per un'applicazione Azure AD modificando le relative informazioni nel centro per i partner. È anche possibile rimuovere le chiavi che non sono più necessarie.

1.  Nella pagina **utenti** (in **Impostazioni account**) selezionare il nome dell'applicazione Azure ad. Verranno visualizzate tutte le chiavi attive per l'applicazione Azure AD, inclusa la data di creazione e di scadenza della chiave. 
2. Per rimuovere una chiave non più necessaria, selezionare **Rimuovi**.
3.  Per aggiungere una nuova chiave, selezionare **Aggiungi nuova chiave**.
4.  Viene visualizzata una schermata che mostra l' **ID client** e i **valori della chiave**. Assicurarsi di stampare o copiare queste informazioni poiché non sarà possibile accedervi di nuovo dopo aver lasciato questa pagina.
4.  Se si desidera creare più chiavi, selezionare **Aggiungi un'altra chiave**.


### <a name="define-user-roles-and-permissions"></a>Definire i ruoli utente e le autorizzazioni

Agli utenti della società è possibile assegnare i seguenti ruoli e autorizzazioni per il programma Commercial Marketplace nel centro per i partner. 

|**Ruolo**|**Autorizzazioni**|
|----------------------------------|:---------------------------------|

| Manager | • può accedere a tutte le funzionalità di account Microsoft ad eccezione delle impostazioni fiscali e di pagamento |      | • È in grado di gestire utenti, ruoli e account di lavoro (tenant) | | Developer | • è in grado di gestire e pubblicare le offerte |      | • È possibile visualizzare alcuni report del server di pubblicazione | | Amministratore globale | • non pertinente per il Marketplace commerciale | | Collaboratore aziendale | • non pertinente per Marketplace commerciale | | Collaboratore finanziario | • non pertinente per il Marketplace commerciale | | Marketer | • non pertinente per il Marketplace commerciale |

Per ulteriori informazioni sulla gestione dei ruoli e delle autorizzazioni in altre aree del centro per i partner, ad esempio Azure Active Directory (AD), Cloud Solution Provider (CSP), pannello di controllo Fornitore (CPV), utenti guest o Microsoft Partner Network (MPN), vedere [assegnare ruoli agli utenti. e autorizzazioni nel centro per i partner](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gestisci tenant

Un tenant di Azure Active Directory (AD), detto anche "account di lavoro" in questa documentazione, è una rappresentazione dell'organizzazione configurata nel portale di Azure e consente di gestire un'istanza specifica dei servizi cloud Microsoft per l'interno e utenti esterni. Se l'organizzazione ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, è stato stabilito un tenant di Azure AD. 

È possibile configurare più tenant da usare con il centro per i partner. Qualsiasi utente con il ruolo di **responsabile** nell'account del centro per i partner avrà la possibilità di aggiungere e rimuovere Azure ad tenant dall'account.  

### <a name="add-an-existing-tenant"></a>Aggiungi un tenant esistente

Per associare un altro tenant Azure AD all'account del centro per i partner:

1.  Nella pagina **tenant** (in **Impostazioni account**) selezionare **associa un altro Azure ad tenant**.
2. Immettere le credenziali di Azure AD per il tenant che si vuole associare.
3. Esaminare l'organizzazione e il nome di dominio per il tenant del Azure AD. Per completare l'associazione, selezionare **conferma**.

Se l'associazione ha esito positivo, sarà possibile aggiungere e gestire gli utenti degli account nella sezione **utenti** del centro per i partner.

### <a name="create-a-new-tenant"></a>Creare un nuovo tenant

Per creare un nuovo tenant di Azure AD con l'account del centro per i partner:

1.  Nella pagina **tenant** (in **Impostazioni account**) selezionare **Crea un nuovo tenant Azure ad**.
2. Immettere le informazioni sulla directory per la nuova Azure AD:
    - **Nome di dominio**: Nome univoco che verrà usato per il dominio Azure AD, insieme a ". onmicrosoft.com". Ad esempio, se è stato immesso "example", il dominio Azure AD sarà "example.onmicrosoft.com".
    - **Indirizzo di posta elettronica di contatto**: Un indirizzo di posta elettronica in cui è possibile contattare l'utente per l'account, se necessario.
    - **Informazioni sull'account utente dell'amministratore globale**: Il nome, il cognome, il nome utente e la password che si desidera utilizzare per il nuovo account amministratore globale.
3. Selezionare **Crea** per confermare il nuovo dominio e le informazioni sull'account.
4. Accedere con il nuovo nome utente e la password dell'amministratore globale di Azure AD per iniziare ad [aggiungere e gestire gli utenti](#manage-users).

Per ulteriori informazioni sulla creazione di nuovi tenant all'interno del portale di Azure, anziché tramite il portale del centro per i partner, vedere l'articolo [creare un nuovo tenant in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Rimuovere un tenant

Per rimuovere un tenant dall'account del centro per i partner, trovarne il nome nella pagina **tenant** (in **Impostazioni account**), quindi selezionare **Rimuovi**. Verrà richiesto di confermare che si vuole rimuovere il tenant. Una volta eseguita questa operazione, nessun utente in tale tenant potrà accedere all'account del centro per i partner e tutte le autorizzazioni configurate per tali utenti verranno rimosse.

Quando si rimuove un tenant, tutti gli utenti aggiunti all'account del centro per i partner da tale tenant non saranno più in grado di accedere all'account.

> [!TIP]
> Non è possibile rimuovere un tenant se è stato eseguito l'accesso al centro per i partner usando un account nello stesso tenant. Per rimuovere un tenant, è necessario accedere al centro per i partner come **responsabile** di un altro tenant associato all'account. Se è presente un solo tenant associato all'account, il tenant può essere rimosso solo dopo aver eseguito l'accesso con la account Microsoft che ha aperto l'account.


## <a name="agreements"></a>Accordi

La sezione **contratti** di centro per i partner (in **Impostazioni account**) consente di visualizzare un elenco dei contratti di pubblicazione autorizzati. Questi contratti sono elencati in base al nome e al numero di versione, inclusa la data di accettazione e il nome dell'utente che ha accettato l'accordo. 

Le **azioni necessarie** possono essere visualizzate nella parte superiore della pagina se sono presenti aggiornamenti del contratto che richiedono l'attenzione dell'utente. Per accettare un contratto aggiornato, leggere prima la versione del contratto collegato, quindi selezionare **accetta accordo**. 


## <a name="next-steps"></a>Passaggi successivi

- [Creare una nuova offerta SaaS](./create-new-saas-offer.md)
