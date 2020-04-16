---
title: Come gestire un account di mercato commerciale nel Centro per i partner Microsoft
description: Scopri come gestire un account di mercato commerciale nel Centro per i partner Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 256ea1a03cd22df443362623177f91893f28b664
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419154"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gestire l'account del marketplace commerciale nel Centro per i partner

Dopo aver [creato un account del Centro per](./create-account.md)i partner, è possibile utilizzare il dashboard del marketplace [commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) per gestire l'account e le offerte.

In questo articolo, approfondiremo come gestire il tuo account del Centro per i partner, incluso come:

- [Accedere alle impostazioni dell'account del Centro per i partner](#access-your-account-settings)
- [Trovare l'ID editore, l'ID Symantec, l'ID venditore, l'ID utente, l'ID MPN e i tenant di Azure AD](#account-details)
- [Aggiornare le informazioni di contatto](#contact-info)
- [Configurare i GUID di monitoraggio per il monitoraggio dell'utilizzo dei clienti](#tracking-guids)
- [Gestisci utenti](#manage-users)
- [Gestire i gruppi](#manage-groups)
- [Gestire le applicazioni di Azure ADManage Azure AD applications](#manage-azure-ad-applications)
- [Definire ruoli utente e autorizzazioni](#define-user-roles-and-permissions)
- [Gestire i tenant di Azure AD (account di lavoro)Manage Azure AD tenants (work accounts)](#manage-tenants)
- [Gestire gli accordi del Centro per i partner](#agreements)

## <a name="access-your-account-settings"></a>Accedere alle impostazioni dell'account

Se non è già stato fatto, l'utente (o l'amministratore dell'organizzazione) deve accedere alle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/management) per l'account del Centro per i partner per:
- Controllare lo stato di verifica dell'account dell'azienda
- Confermare l'ID Symantec, l'ID venditore, l'ID Microsoft Partner Network (MPN) e le informazioni di contatto dell'editore, inclusi l'approvatore della società e il contatto del venditore
- Creare account utente per tutti coloro che utilizzeranno il proprio account aziendale nel Centro per i partner

### <a name="open-developer-settings"></a>Aprire le impostazioni dello sviluppatore

**Le impostazioni dell'account** si trovano nell'angolo in alto a destra del dashboard del [marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) nel Centro per i partner. Selezionare l'icona a forma di ingranaggio (nell'angolo superiore destro del dashboard) e quindi selezionare **Impostazioni sviluppatore**.

![Menu Impostazioni account nel Centro per i partner](./media/dashboard-developer-settings.png)

All'interno **Impostazioni account,** potrai visualizzare i tuoi:
- **Dettagli account**: Tipo di conto e stato dell'account
- **ID editore:** ID venditore, ID utente, ID editore, tenant di Azure AD e così via.
- **Informazioni di contatto**: Nome visualizzato dell'editore, nome del contatto del venditore, e-mail, telefono e indirizzo
- **GUID di monitoraggio**: Qualsiasi GUI di monitoraggio associato al tuo account

### <a name="account-details"></a>Dettagli dell'account

Nella sezione Dettagli account puoi visualizzare informazioni di base, come il **tipo di account** (Società o Individuale) e lo stato di **verifica** dell'account. Durante il processo di verifica dell'account, queste impostazioni visualizzeranno ogni passaggio richiesto, tra cui la verifica dell'email, la verifica dell'impiego e la verifica aziendale. Puoi anche aggiornare la tua email qui e inviare di nuovo la verifica, se necessario.

### <a name="publisher-ids"></a>ID editore

Nella sezione ID editore è possibile visualizzare **l'ID Symantec**, l'ID **venditore**, **l'ID utente**, **l'ID MPN**e i tenant di **Azure AD.** Questi valori vengono assegnati da Microsoft per identificare in modo univoco l'account sviluppatore e non possono essere modificati.

### <a name="contact-info"></a>Informazioni di contatto

Nella sezione Informazioni di contatto è possibile visualizzare il **nome visualizzato dell'editore**, le **informazioni** di contatto del venditore (il nome del contatto, l'indirizzo e-mail, il numero di telefono e l'indirizzo del venditore della società) e **l'approvatore della società** (il nome, l'indirizzo e-mail e il numero di telefono della persona con autorità per approvare le decisioni per la società).

#### <a name="payout-account"></a>Account per i proventi

Un conto di pagamento è il conto bancario a cui vengono inviati i proventi dalle vendite. Questo conto bancario deve trovarsi nello stesso paese in cui hai registrato il tuo conto del Centro per i partner.

Per configurare l'account di pagamento, è necessario **associare l'account Microsoft:**
1. Vai alla [pagina di panoramica](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) del marketplace commerciale nel Centro per i partner.
2. Nella sezione Profilo selezionare **Associa account Microsoft**.
3. Quando richiesto, accedere con l'account Microsoft (MSA). Questo account non può essere già associato a un altro account del Centro per i partner.
4. Per completare la configurazione dell'account di pagamento, esci dal Centro per i partner, quindi accedi di nuovo con il tuo account Microsoft (anziché con il tuo account di lavoro).

Ora che l'account Microsoft è associato, per aggiungere un account di pagamento, è necessario:
- **Scegli un metodo di pagamento**: Conto bancario o PayPal
- **Aggiungi informazioni di pagamento**: Può includere la scelta di un tipo di account (controllo o risparmio), l'immissione del nome del titolare dell'account, il numero di conto e il numero di routing, l'indirizzo di fatturazione, il numero di telefono o l'indirizzo e-mail PayPal. Per ulteriori informazioni sull'utilizzo di PayPal come metodo di pagamento dell'account e per scoprire se è supportato nella tua regione di mercato, consulta Info su [PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché dobbiamo verificare la modifica dell'account, proprio come facciamo quando configuriamo per la prima volta l'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.  

#### <a name="tax-profile"></a>Profilo fiscale

Esaminare lo stato corrente del profilo fiscale, confermando che vengano visualizzati il tipo di **entità** corretto e le informazioni sul **certificato fiscale.** Selezionare **Modifica** per aggiornare o completare i moduli necessari.

Per stabilire il tuo status fiscale, devi specificare il tuo paese di residenza e la cittadinanza e compilare i moduli fiscali appropriati associati al tuo paese/regione.

Indipendentemente dal paese di residenza o dalla cittadinanza, è necessario compilare i moduli fiscali degli Stati Uniti per vendere qualsiasi offerta tramite Microsoft. I partner che soddisfano determinati requisiti di residenza negli Stati Uniti devono compilare un modulo IRS W-9. Altri partner al di fuori degli Stati Uniti devono compilare un modulo W-8 dell'IRS. Puoi compilare questi moduli online non appena completi il tuo profilo fiscale.

Non è necessario un codice ITIN (Individual Taxpayer Identification Number) degli Stati Uniti per ricevere pagamenti da Microsoft o richiedere agevolazioni fiscali.

È possibile compilare e inviare i moduli fiscali elettronicamente nel Centro per i partner; nella maggior parte dei casi, non è necessario stampare e inviare alcun modulo.

Paesi e regioni diversi hanno requisiti fiscali diversi. L'importo esatto che devi pagare in tasse dipende dai paesi e dalle regioni in cui vendi le tue offerte. Microsoft rimette le vendite e utilizza l'imposta per conto dell'utente in alcuni paesi. Questi paesi saranno identificati nel processo di inserzione della tua offerta. In altri paesi, a seconda di dove sei registrato, potrebbe essere necessario rimettere le vendite e utilizzare l'imposta per le vendite direttamente all'autorità fiscale locale. Inoltre, i proventi delle vendite ricevuti possono essere imponibili come reddito. Ti consigliamo vivamente di contattare l'autorità competente per il tuo paese o la tua area geografica che può aiutarti a determinare le giuste informazioni fiscali per le tue transazioni di vendita Microsoft.

##### <a name="withholding-rates"></a>Tassi delle trattenute
Le informazioni che fornisci nei moduli fiscali determinano il tasso di trattenuta fiscale appropriato. Il tasso di trattenuta fiscale si applica solo alle vendite effettuate negli Stati Uniti. Le vendite effettuate in altri paesi non sono soggette a trattenuta. I tassi di trattenuta possono variare, ma per la maggior parte degli sviluppatori registrati al di fuori degli Stati Uniti il tasso predefinito è del 30%. Hai la possibilità di ridurre questo tasso se il tuo paese ha stipulato un accordo di agevolazioni fiscali sul reddito con gli Stati Uniti.

##### <a name="tax-treaty-benefits"></a>Agevolazioni fiscali
Se non risiedi negli Stati Uniti, potresti usufruire di agevolazioni fiscali Questi vantaggi variano da paese a paese e possono consentire di ridurre l'importo delle imposte trattenute da Microsoft. Puoi richiedere agevolazioni fiscali compilando la parte II del modulo W-8BEN. Consigliamo di consultare gli organi preposti nel tuo paese o nella tua area geografica per stabilire se hai diritto a tali agevolazioni.

[Altre informazioni sui dettagli fiscali per gli sviluppatori di app/giochi di Windows e per gli editori](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)di Azure Marketplace.

#### <a name="payout-hold-status"></a>Stato dei proventi in attesa

Per impostazione predefinita, Microsoft invia i pagamenti su base mensile. Tuttavia, hai la possibilità di mettere i pagamenti in attesa, che impediranno l'invio di pagamenti al tuo account. Se scegli di mettere i pagamenti in sospeso, continueremo a registrare le entrate che guadagni e a fornire i dettagli nel riepilogo dei **pagamenti.** Tuttavia, non invieremo pagamenti al tuo account finché non rimuovi la sospensione. 

Per mettere i tuoi pagamenti in attesa, vai a **Impostazioni account**. In **Dettagli finanziari**, nella sezione **Stato dei proventi in attesa** imposta il dispositivo di scorrimento sullo stato **attivato**. Puoi modificare lo stato dei proventi in attesa in qualsiasi momento, ma tieni presente che la tua decisione avrà effetto sui prossimi proventi mensili. Ad esempio, se desideri mantenere il pagamento di April, assicurati di impostare lo stato di sospensione dei pagamenti **su Attivato** prima della fine di marzo.

Una volta che hai impostato lo stato dei proventi in attesa come **attivato**, tutti i proventi saranno in attesa finché non imposterai di nuovo il dispositivo di scorrimento sullo stato **disattivato**. In questo caso, sarai incluso durante il successivo ciclo di pagamento mensile (a condizione che siano state rispettate tutte le soglie di pagamento applicabili). Ad esempio, se hai sospeso i pagamenti, ma desideri che venga generato un pagamento a giugno, assicurati di impostare lo stato di sospensione dei pagamenti su **Disattivato** prima della fine di maggio.

> [!NOTE]
> La selezione dello stato di sospensione dei **pagamenti** si applica a **tutte le** fonti di reddito pagate tramite il Centro per i partner Microsoft, inclusi Azure Marketplace, AppSource, Microsoft Store, pubblicità e così via. Non puoi selezionare stati di attesa diversi per ogni fonte di ricavi.

### <a name="devices"></a>Dispositivi

Le impostazioni di gestione dei dispositivi si applicano solo alla pubblicazione UWP. [Altre informazioni](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID di rilevamento

Gli identificatori univoci globali (GUID, Globally Unique Identifier) sono numeri di riferimento univoci (con 32 cifre esadecimali) che possono essere usati per tenere traccia dell'utilizzo di Azure.Globally Unique Identifiers (GUIDs) are unique reference numbers (with 32 hexadecimal digits) that can be used for tracking your Azure usage. 

Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si distribuisce un prodotto usando un modello ed è disponibile sia in Azure Marketplace che in GitHub, è possibile creare e registrare due GUID distinti:If you deploy a product by using a template and it is available on both the Azure Marketplace and on GitHub, you can create and register two distinct GUIDS:

*    Prodotto A su Azure Marketplace
*    Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. È inoltre possibile tenere traccia dei GUID a un livello più granulare allineati a ogni piano all'interno dell'offerta.

Per altre informazioni, vedere Domande [frequenti sul rilevamento dell'utilizzo dei clienti](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq)di Azure con i GUID.

## <a name="create-a-billing-profile"></a>Creare un profilo di fatturazione

Se si pubblica un'offerta [Dynamics 365 for Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) o Dynamics [365 for Operations,](./create-new-operations-offer.md) è necessario completare il profilo di **fatturazione.**

L'indirizzo di fatturazione viene precompilato dalla persona giuridica ed è possibile aggiornarlo in un secondo momento. I campi TAX e VAT sono facoltativi.  Il nome del paese e il nome della società non possono essere modificati.

## <a name="multi-user-account-management"></a>Gestione degli account multiutente

Il Centro per i partner sfrutta [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) per l'accesso e la gestione di account multiutente. Azure AD dell'organizzazione viene associato automaticamente all'account del Centro per i partner come parte del processo di registrazione.

## <a name="manage-users"></a>Gestire gli utenti

La sezione **Utenti** del Centro per i partner (in **Impostazioni account**) consente di usare Azure AD per gestire gli utenti, i gruppi e le applicazioni di Azure AD che hanno accesso all'account del Centro per i partner. L'account deve disporre di autorizzazioni a livello di [**responsabile**](#define-user-roles-and-permissions) per l'account di lavoro (tenant di [Azure AD)](./company-work-accounts.md) in cui si desidera aggiungere o modificare gli utenti. Per gestire gli utenti all'interno di un account di lavoro /tenant diverso, è necessario disconnettersi e quindi accedere nuovamente come utente con autorizzazioni **di amministratore** per tale account di lavoro/tenant.

Dopo aver eseguito l'accesso con l'account aziendale (tenant di Azure AD), è possibile:Once you are signed in with your work account (Azure AD tenant), you can:

- [Aggiungere o rimuovere utenti](#add-existing-users)
- [Modifica una password utente](#change-a-user-password)
- [Aggiungere o rimuovere gruppi](#manage-groups)
- [Aggiungere o rimuovere applicazioni di Azure ADAdd or remove Azure AD applications](#add-new-azure-ad-applications)
- [Gestire le chiavi per un'applicazione Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definire ruoli utente e autorizzazioni](#define-user-roles-and-permissions)

Tenere presente che tutti gli utenti del Centro per i partner (inclusi i gruppi e le applicazioni di Azure AD) devono disporre di un account di lavoro attivo in un tenant di [Azure AD](#manage-tenants) associato all'account del Centro per i partner.

### <a name="add-existing-users"></a>Aggiungere utenti esistenti

Per aggiungere utenti all'account del Centro per i partner già presenti nell'account di lavoro dell'azienda (tenant di [Azure AD):](./company-work-accounts.md)

1. Passare a **Utenti** (in **Impostazioni account)** e selezionare **Aggiungi utenti**.
2. Seleziona uno o più utenti nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare utenti specifici.
Se si selezionano più utenti da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più utenti con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Dopo aver scelto gli utenti, fare clic su **Aggiungi selezionato**.
4. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per gli utenti selezionati.
5. Selezionare **Salva**.

### <a name="create-new-users"></a>Creare nuovi utenti

Per creare nuovi account utente, è necessario disporre di un account con autorizzazioni [**di amministratore globale.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Vai a **Utenti** (in **Impostazioni account**) seleziona **Aggiungi utenti**, quindi scegli Crea **nuovi utenti.**
1. Immettere un nome, un cognome e un nome utente per ogni nuovo utente. 
1. Se si desidera che il nuovo utente disponga di un account amministratore globale nella directory dell'organizzazione, selezionare la casella **Rendi l'utente un amministratore globale in Azure AD, con il controllo completo su tutte le risorse della directory**. In questo modo l'utente avrà accesso completo a tutte le funzionalità amministrative nella directory Azure AD della tua azienda. Saranno in grado di aggiungere e gestire utenti nell'account di lavoro dell'organizzazione (tenant di Azure AD), anche se non nel Centro per i partner, a meno che non si conceda all'account il ruolo o le autorizzazioni appropriate.
1. Se hai selezionato la casella **Rendi questo utente un amministratore globale,** dovrai fornire un'e-mail di recupero **password** per l'utente per recuperare la password, se necessario.
1. Nella sezione **Appartenenza al gruppo** seleziona i gruppi a cui vuoi che appartenga il nuovo utente.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

La creazione di un nuovo utente nel Centro per i partner creerà anche un account per tale utente nell'account di lavoro (tenant di Azure AD) a cui è stato eseguito l'accesso. Apportare modifiche al nome di un utente nel Centro per i partner, apportare le stesse modifiche nell'account di lavoro dell'organizzazione (tenant di Azure AD).

### <a name="invite-new-users-by-email"></a>Invitare nuovi utenti tramite posta elettronica

Per invitare gli utenti che non fanno attualmente parte dell'account di lavoro aziendale (tenant di Azure AD) tramite posta elettronica, è necessario disporre di un account con autorizzazioni [**di amministratore globale.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Vai a **Utenti** (in **Impostazioni account**) seleziona **Aggiungi utenti**, quindi scegli **Invita utenti tramite e-mail.**
2. Immetti uno o più indirizzi e-mail (fino a dieci), separati da virgole o punti e virgola.
3. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
4. Selezionare **Salva**.

Gli utenti invitati riceverai un invito via email a iscriversi al tuo account del Centro per i partner. Verrà creato un nuovo account utente guest nell'account di lavoro (tenant di Azure AD). Ogni utente dovrà accettare l'invito per poter accedere all'account.

Se è necessario inviare di nuovo un invito, visitare la pagina **Utenti,** trovare l'invito nell'elenco degli utenti, selezionare il relativo indirizzo di posta elettronica (o il testo *Invito in sospeso*). Quindi, nella parte inferiore della pagina, selezionare **Invia di nuovo l'invito**.

> [!NOTE]
> Se l'organizzazione usa [l'integrazione](https://go.microsoft.com/fwlink/p/?LinkID=724033) della directory per sincronizzare il servizio directory locale con Azure AD, non sarà possibile creare nuovi utenti, gruppi o applicazioni Azure AD nel Centro per i partner. L'utente (o un altro amministratore nella directory locale) dovrà crearli direttamente nella directory locale prima di poterli visualizzare e aggiungere nel Centro per i partner.

### <a name="remove-a-user"></a>Rimuovere un utente

Per rimuovere un utente dall'account di lavoro (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare l'utente che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** dalle azioni disponibili. Verrà visualizzata una finestra popup per confermare che si desidera rimuovere gli utenti selezionati.

### <a name="change-a-user-password"></a>Modifica una password utente

Se uno degli utenti deve modificare la password, può farlo autonomamente se è stata fornita **un'e-mail** di recupero password durante la creazione dell'account utente. Puoi anche aggiornare la password di un utente seguendo i passaggi seguenti. Per modificare la password di un utente nell'account aziendale (tenant di Azure AD), è necessario accedere con un account con autorizzazioni di [**amministratore globale.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Si noti che in questo modo verrà modificata la password dell'utente nel tenant di Azure AD, insieme alla password usata per accedere al Centro per i partner.

1. Nella pagina **Utenti,** in **Impostazioni account,** selezionare il nome dell'account utente che si desidera modificare.
2. Selezionare il pulsante **Reimposta password** nella parte inferiore della pagina.
3. Apparirà una pagina di conferma che mostra le informazioni di accesso per l'utente, inclusa una password temporanea. Assicurati di stampare o copiare queste informazioni e fornirle all'utente, in quanto non potrai accedere alla password temporanea dopo aver lasciato questa pagina.

## <a name="manage-groups"></a>Gestire i gruppi

I gruppi consentono di controllare più ruoli utente e autorizzazioni tutti insieme.

### <a name="add-an-existing-group"></a>Aggiungere un gruppo esistente

Per aggiungere un gruppo già esistente nell'account di lavoro dell'organizzazione (tenant di Azure AD) all'account del Centro per i partner:To add a group that already exists in your organization's work account (Azure AD tenant) to your Partner Center account:

1. Nella pagina **Utenti** (in **Impostazioni account)** selezionare **Aggiungi gruppi**.
2. Seleziona uno o più gruppi nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare gruppi specifici.
Se si selezionano più gruppi da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più gruppi con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Dopo aver scelto i gruppi, fare clic su **Aggiungi selezionati**.
4. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per i gruppi selezionati. Tutti i membri del gruppo potranno accedere all'account del Centro per i partner con le autorizzazioni applicate al gruppo, indipendentemente dai ruoli e dalle autorizzazioni associati al proprio account individuale.
5. Selezionare **Salva**.

Quando si aggiunge un gruppo esistente, ogni utente membro di tale gruppo sarà in grado di accedere all'account del Centro per i partner, con le autorizzazioni associate al ruolo assegnato al gruppo.

### <a name="add-a-new-group"></a>Aggiungere un nuovo gruppo

Per aggiungere un gruppo nuovo di zecca al tuo account del Centro per i partner:

1. Nella pagina **Utenti** (in **Impostazioni account)** selezionare **Aggiungi gruppi**.
2. Nella pagina successiva selezionare **Nuovo gruppo**.
3. Immetti il nome visualizzato per il nuovo gruppo.
4. Specificare i ruoli o le autorizzazioni personalizzate per il gruppo. Tutti i membri del gruppo potranno accedere al tuo account del Centro per i partner con le autorizzazioni che applichi qui, indipendentemente dai ruoli/autorizzazioni associati al loro account individuale.
5. Selezionare gli utenti per il nuovo gruppo dall'elenco visualizzato. Puoi usare la casella di ricerca per cercare utenti specifici.
6. Dopo aver selezionato gli utenti, fare clic su **Aggiungi selezionato** per aggiungerli al nuovo gruppo.
7. Selezionare **Salva**.

Si noti che questo nuovo gruppo verrà creato anche nell'account di lavoro dell'organizzazione (tenant di Azure AD), non solo nell'account del Centro per i partner.

### <a name="remove-a-group"></a>Rimuovere un gruppo

Per rimuovere un gruppo dall'account di lavoro (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare il gruppo che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** dalle azioni disponibili. Verrà visualizzata una finestra popup per confermare che si desidera rimuovere i gruppi selezionati.

## <a name="manage-azure-ad-applications"></a>Gestire le applicazioni di Azure ADManage Azure AD applications

È possibile consentire alle applicazioni o ai servizi che fanno parte di Azure AD dell'azienda di accedere all'account del Centro per i partner.

### <a name="add-existing-azure-ad-applications"></a>Aggiungere applicazioni di Azure AD esistentiAdd existing Azure AD applications

Per aggiungere applicazioni già esistenti in Azure Active Directory dell'azienda:To add applications that already exist in your company's Azure Active Directory:

1. Nella pagina **Utenti** (in **Impostazioni account)** selezionare **Aggiungi applicazioni di Azure AD.**
2. Seleziona una o più applicazioni Azure AD nell'elenco visualizzato. Puoi usare la casella di ricerca per cercare applicazioni Azure AD specifiche. Se si selezionano più applicazioni di Azure AD da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più applicazioni Azure AD con ruoli/autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Dopo aver selezionato le applicazioni Azure AD, fai clic su **Aggiungi selezione**.
4. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per le applicazioni di Azure AD selezionate.
5. Selezionare **Salva**.

### <a name="add-new-azure-ad-applications"></a>Aggiungere nuove applicazioni di Azure ADAdd new Azure AD applications

Se si vuole concedere al Centro per i partner l'accesso a un nuovo account dell'applicazione Azure AD, è possibile crearne uno nella sezione **Utenti.If** you want to grant Partner Center access to a brand-new Azure AD application account, you can create one in the Users section. Si noti che verrà creato un nuovo account nell'account di lavoro aziendale (tenant di Azure AD), non solo nell'account del Centro per i partner. Se si usa principalmente questa applicazione Azure AD per l'autenticazione del Centro per i partner e non è necessario che gli utenti vi accedano direttamente, è possibile immettere qualsiasi indirizzo valido per **l'URL** di risposta e l'URI **dell'ID app,** purché tali valori non vengano usati da altre applicazioni di Azure AD nella directory.

1. Nella pagina **Utenti** (in **Impostazioni account)** selezionare **Aggiungi applicazioni di Azure AD.**
2. Nella pagina successiva selezionare **Nuova applicazione Azure AD**.
3. Immetti l’indirizzo **URL di risposta** per la nuova applicazione Azure AD. Questo è l'URL da cui gli utenti possono eseguire l'accesso e usare la tua applicazione Azure AD (noto anche come URL dell'app o URL di accesso). **L'URL** di risposta non può essere più lungo di 256 caratteri e deve essere univoco all'interno della directory.
4. Immetti l’**URI ID app** per la nuova applicazione Azure AD. Si tratta di un identificatore logico per l'applicazione Azure AD che viene presentato quando una richiesta Single Sign-On viene inviata ad Azure AD. Si noti che **l'URI DELL'ID app** deve essere univoco per ogni applicazione Azure AD nella directory. Questo ID non può essere più lungo di 256 caratteri. Per altre info sull'URI dell'ID app, vedere [Integrazione di applicazioni con Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts)
5. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'applicazione Azure AD.
6. Selezionare **Salva**.

Dopo aver aggiunto o creato un'applicazione Azure AD, è possibile tornare alla sezione **Utenti** e selezionare il nome dell'applicazione per esaminare le impostazioni per l'applicazione, inclusi l'ID tenant, l'ID client, l'URL di risposta e l'URI dell'ID app.

### <a name="remove-an-azure-ad-application"></a>Rimuovere un'applicazione Azure ADRemove an Azure AD application

Per rimuovere un'applicazione dall'account di lavoro (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare l'applicazione che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** dalle azioni disponibili. Verrà visualizzata una finestra popup per confermare che si desidera rimuovere le applicazioni selezionate.

### <a name="manage-keys-for-an-azure-ad-application"></a>Gestire le chiavi per un'applicazione Azure AD

Se la tua applicazione Azure AD legge e scrive dati in Microsoft Azure AD, servirà una chiave. È possibile creare chiavi per un'applicazione Azure AD modificandone le informazioni nel Centro per i partner. Puoi anche rimuovere le chiavi non più necessarie.

1. Nella pagina **Utenti,** in **Impostazioni account,** selezionare il nome dell'applicazione Azure AD. Verranno visualizzate tutte le chiavi attive per l'applicazione Azure AD, inclusa la data in cui la chiave è stata creata e la data di scadenza. 
2. Per rimuovere una chiave non più necessaria, selezionare **Rimuovi**.
3. Per aggiungere una nuova chiave, selezionare **Aggiungi nuova chiave**.
4. Verrà visualizzata una schermata che mostra i valori **ID client** e **Chiave**. Assicurarsi di stampare o copiare queste informazioni, in quanto non sarà possibile accedervi nuovamente dopo aver lasciato questa pagina.
5. Se si desidera creare più chiavi, selezionare **Aggiungi un'altra chiave**.

## <a name="define-user-roles-and-permissions"></a>Definire ruoli utente e autorizzazioni

Agli utenti della tua azienda possono essere assegnati i ruoli e le autorizzazioni seguenti per il programma Commercial Marketplace nel Centro per i partner:

- **Manager**
  - Può accedere a tutte le funzionalità dell'account Microsoft, ad eccezione delle impostazioni fiscali e di pagamento
  - Può gestire utenti, ruoli e account di lavoro (tenant)
- **Developer**
  - Può gestire e pubblicare le offerte
  - Può visualizzare alcuni rapporti del publisher

> [!NOTE]
> Per il programma Commercial Marketplace, i ruoli Amministratore globale, Collaboratore aziendale, Collaboratore finanziario e Marketing non vengono utilizzati. L'assegnazione di questi ruoli agli utenti non ha alcun effetto. Solo i ruoli Manager e Sviluppatore concedono autorizzazioni agli utenti.

Per altre informazioni sulla gestione di ruoli e autorizzazioni in altre aree del Centro per i partner, ad esempio Azure Active Directory (AD), Cloud Solution Provider (CSP), Fornitore del Pannello di controllo (CPV), Utenti guest o Microsoft Partner Network (MPN), vedere [Assegnare ruoli e autorizzazioni degli utenti nel Centro per](https://docs.microsoft.com/partner-center/permissions-overview)i partner .

## <a name="manage-tenants"></a>Gestire i tenant

Un tenant di Azure Active Directory (AD), noto anche come "account di lavoro" in questa documentazione, è una rappresentazione dell'organizzazione configurata nel portale di Azure e consente di gestire un'istanza specifica dei servizi cloud Microsoft per gli utenti interni ed esterni. Se l'organizzazione ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, è stato stabilito automaticamente un tenant di Azure AD.

È possibile configurare più tenant da usare con il Centro per i partner. Qualsiasi utente con il ruolo **Manager** nell'account del Centro per i partner avrà la possibilità di aggiungere e rimuovere tenant di Azure AD dall'account.  

### <a name="add-an-existing-tenant"></a>Aggiungere un tenant esistente

Per associare un altro tenant di Azure AD all'account del Centro per i partner:To associate another Azure AD tenant with your Partner Center account:

1. Nella pagina **Tenant** (in **Impostazioni account)** selezionare **Associa un altro tenant di Azure AD.**
2. Immettere le credenziali di Azure AD per il tenant che si vuole associare.
3. Esaminare l'organizzazione e il nome di dominio per il tenant di Azure AD. Per completare l'associazione, selezionare **Conferma**.

Se l'associazione ha esito positivo, sarà quindi possibile aggiungere e gestire gli utenti dell'account nella sezione **Utenti** nel Centro per i partner.

### <a name="create-a-new-tenant"></a>Creare un nuovo tenantCreate a new tenant

Per creare un tenant di Azure AD nuovo e personalizzato con l'account del Centro per i partner:To create a brand new Azure AD tenant with your Partner Center account:

1. Nella pagina **Tenant** (in **Impostazioni account)** selezionare **Crea un nuovo tenant di Azure AD.**
2. Immetti le informazioni sulla directory per la nuova istanza di Azure AD:
    - **Nome di dominio**: Il nome univoco che useremo per il dominio di Azure AD, insieme a ".onmicrosoft.com". Ad esempio, se è stato immesso "esempio", il dominio di Azure AD sarà "example.onmicrosoft.com".
    - **Indirizzo di posta elettronica**: indirizzo di posta elettronica a cui possiamo contattarti per questioni relative all'account se necessario.
    - **Informazioni sull'account utente dell'amministratore globale:** nome, cognome, nome utente e password che si desidera utilizzare per il nuovo account amministratore globale.
3. Seleziona **Crea** per confermare le nuove informazioni sul dominio e sull'account.
4. Accedere con il nuovo nome utente e la nuova password di amministratore globale di Azure AD per iniziare [ad aggiungere e gestire gli utenti.](#manage-users)

Per altre informazioni sulla creazione di nuovi tenant all'interno del portale di Azure, anziché tramite il portale Del Centro per i partner, vedere l'articolo [Creare un nuovo tenant in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

### <a name="remove-a-tenant"></a>Rimuovere un tenant

Per rimuovere un tenant dall'account del Centro per i partner, individuarne il nome nella pagina **Tenant** (in **Impostazioni account**), quindi selezionare **Rimuovi**. Verrà richiesto di confermare che si desidera rimuovere il tenant. Una volta eseguito questa operazione, nessun utente in tale tenant sarà in grado di accedere all'account del Centro per i partner e tutte le autorizzazioni configurate per tali utenti verranno rimosse.

Quando si rimuove un tenant, tutti gli utenti aggiunti all'account del Centro per i partner da tale tenant non saranno più in grado di accedere all'account.

> [!TIP]
> Non è possibile rimuovere un tenant se è stato effettuato l'accesso al Centro per i partner utilizzando un account nello stesso tenant. Per rimuovere un tenant, è necessario accedere al Centro per i partner come **Manager** per un altro tenant associato all'account. Se all'account è associato un solo tenant, tale tenant può essere rimosso solo dopo aver eseguito l'accesso con l'account Microsoft che ha aperto l'account.

## <a name="agreements"></a>Contratti

La sezione **Contratti** del Centro per i partner (in **Impostazioni account)** consente di visualizzare un elenco degli accordi di pubblicazione autorizzati. Questi accordi sono elencati in base al nome e al numero di versione, inclusa la data in cui è stato accettato e il nome dell'utente che ha accettato l'accordo.

**Le azioni necessarie** possono essere visualizzate nella parte superiore di questa pagina se sono presenti aggiornamenti del contratto che richiedono la tua attenzione. Per accettare un accordo aggiornato, leggere prima la versione dell'accordo collegata, quindi selezionare **Accetta accordo**.
