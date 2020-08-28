---
title: Come gestire un account del marketplace commerciale nel Centro per i partner Microsoft
description: Informazioni su come gestire un account del marketplace commerciale nel Centro per i partner Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 08/27/2020
ms.openlocfilehash: 7438351aa9849e3110952043eff8f7d860b5b7de
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015833"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Come gestire il proprio account del marketplace commerciale nel Centro per i partner

Dopo aver [creato un account del Centro per i partner](./create-account.md), è possibile usare il [dashboard del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) per gestire l'account e le offerte.

Questo articolo illustra come gestire l'account del Centro per i partner, incluse le procedure per:

- [Accedere alle impostazioni dell'account del Centro per i partner](#access-your-account-settings)
- [Trovare l'ID editore, l'ID Symantec, l'ID venditore, l'ID utente, l'ID MPN e i tenant di Azure AD](#account-details)
- [Aggiornare le informazioni di contatto](#contact-info)
- [Configurare i GUID di rilevamento per il monitoraggio dell'utilizzo da parte dei clienti](#tracking-guids)
- [Gestire gli utenti](#manage-users)
- [Gestire i gruppi](#manage-groups)
- [Gestire applicazioni Azure AD](#manage-azure-ad-applications)
- [Definire autorizzazioni e ruoli utente](#define-user-roles-and-permissions)
- [Gestire i tenant di Azure AD (account aziendali)](#manage-tenants)
- [Gestire i contratti del Centro per i partner](#agreements)

## <a name="access-your-account-settings"></a>Accedere alle impostazioni dell'account

Se non è già stato fatto, è consigliabile che l'utente o l'amministratore dell'organizzazione acceda alle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/management) del Centro per i partner per:
- Controllare lo stato di verifica dell'account aziendale
- Confermare l'ID Symantec, l'ID venditore, l'ID Microsoft Partner Network (MPN), l'ID editore e le informazioni di contatto, inclusi il responsabile approvazione della società e il contatto del venditore
- Creare account utente per chiunque usi l'account aziendale nel Centro per i partner

### <a name="open-developer-settings"></a>Aprire le impostazioni modalità sviluppatore

L'opzione **Impostazioni account** si trova nell'angolo in alto a destra del [dashboard del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) nel Centro per i partner. Selezionare l'icona a forma di ingranaggio (nell'angolo in alto a destra del dashboard) e quindi selezionare  **Impostazioni modalità sviluppatore**.

![Menu Impostazioni account nel Centro per i partner](./media/dashboard-developer-settings.png)

In **Impostazioni account** sarà possibile visualizzare:
- **Dettagli dell'account**: tipo di account e stato dell'account
- **ID editore**: ID venditore, ID utente, ID autore, tenant di Azure AD e così via.
- **Informazioni contatto**: nome visualizzato dell'editore, nome contatto del venditore, indirizzo di posta elettronica, telefono e indirizzo
- **GUID di rilevamento**: qualsiasi GUID di rilevamento associato all'account

### <a name="account-details"></a>Dettagli dell'account

Nella sezione Dettagli dell'account è possibile visualizzare le informazioni di base, ad esempio il **tipo di account** (aziendale o personale) e lo **stato verifica** dell'account. Durante il processo di verifica dell'account, queste impostazioni visualizzeranno ogni passaggio necessario, tra cui la verifica dell'indirizzo di posta elettronica, la verifica dell'impiego e verifica dell'azienda. Se necessario, è anche possibile aggiornare l'indirizzo di posta elettronica e inviare nuovamente la verifica.

### <a name="publisher-ids"></a>ID editore

Nella sezione ID editore è possibile visualizzare l'**ID Symantec**, l'**ID venditore**, l'**ID utente**, l'**ID MPN** e i **tenant di Azure AD**. Questi valori vengono assegnati da Microsoft per identificare in modo univoco l'account sviluppatore e non possono essere modificati.

### <a name="contact-info"></a>Informazioni di contatto

Nella sezione Informazioni contatto sono visibili **Nome visualizzato editore**, **Info contatto venditore** (nome del contatto, indirizzo di posta elettronica, numero di telefono e indirizzo del venditore dell'azienda) e **Responsabile approvazione aziendale** (nome, indirizzo di posta elettronica e numero di telefono della persona autorizzata ad approvare le decisioni per l'azienda).

#### <a name="payout-account"></a>Account proventi

Un account proventi è il conto bancario su cui vengono versati gli introiti delle vendite. Questo conto bancario deve trovarsi nello stesso paese o area geografica in cui è stato registrato l'account del Centro per i partner.

Per configurare l'account proventi:

1. Passare alla [pagina di panoramica del marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nel Centro per i partner.
2. Nella sezione Profilo selezionare **Aggiorna** accanto a **Profilo di pagamento**.
3. **Scegliere un metodo di pagamento**: conto bancario o PayPal.
4. **Aggiungere le informazioni di pagamento**: potrebbe essere necessario scegliere un tipo di conto (corrente o di risparmio) e immettere il nome del titolare del conto, il numero di conto, il numero di registrazione, l'indirizzo di fatturazione, il numero di telefono o l'indirizzo di posta elettronica PayPal. *Per altre informazioni sull'uso di PayPal come metodo di pagamento dell'account e per sapere se è supportato in una determinata area di mercato, vedere [Informazioni su PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come avviene per la configurazione iniziale dell'account proventi. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.  

#### <a name="tax-profile"></a>Profilo fiscale

Esaminare lo stato del profilo fiscale corrente, verificando che il **Tipo di entità** e le **Informazioni certificato fiscale** visualizzati siano corretti. Selezionare **Modifica** per aggiornare o completare i moduli necessari.

Per stabilire lo stato fiscale, è necessario specificare il paese o l'area geografica di residenza e la cittadinanza e completare i moduli fiscali appropriati associati al paese o all'area geografica.

Indipendentemente dal paese o dall'area geografica di residenza o dalla cittadinanza, è necessario compilare i moduli fiscali degli Stati Uniti per vendere qualsiasi offerta tramite Microsoft. I partner che soddisfano determinati requisiti di residenza negli Stati Uniti devono compilare un modulo IRS W-9. Gli altri partner al di fuori degli Stati Uniti devono compilare un modulo IRS W-8. Questi moduli possono essere compilati online quando si completa il profilo fiscale.

Non è necessario un codice identificativo del contribuente statunitense per ricevere i pagamenti da Microsoft o per richiedere i vantaggi della convenzione fiscale.

I moduli fiscali possono essere completati e inviati elettronicamente nel Centro per i partner. Nella maggior parte dei casi non è necessario stampare e inviare per posta alcun modulo.

Paesi e aree geografiche diversi hanno requisiti fiscali diversi. L'importo esatto delle imposte da pagare dipende dai paesi e dalle aree geografiche in cui viene venduta l'offerta. In alcuni paesi o aree geografiche Microsoft versa l'IVA e l'imposta sui beni durevoli per conto dei partner. Questi paesi o aree geografiche verranno identificate durante la pubblicazione dell'offerta. In altri paesi o aree geografiche, a seconda della località in cui viene eseguita la registrazione, potrebbe essere necessario versare l'IVA e l'imposta sui beni durevoli direttamente all'autorità fiscale locale. Gli introiti derivanti dalla vendita potrebbero anche essere tassabili come redditi. È consigliabile contattare l'autorità competente per il proprio paese o area geografica, che fornirà tutte le informazioni fiscali necessarie per le transazioni di vendita di Microsoft.

##### <a name="withholding-rates"></a>Aliquote di ritenuta d'acconto

Le informazioni inviate nei moduli fiscali determinano l'aliquota di ritenuta d'acconto appropriata. L'aliquota di ritenuta d'acconto si applica solo alle vendite avvenute negli Stati Uniti. Le vendite in aree diverse dagli Stati Uniti non sono soggette a ritenuta. Le aliquote di ritenuta d'acconto variano, ma per la maggior parte degli sviluppatori registrati al di fuori degli Stati Uniti l'aliquota predefinita è del 30%. Questa aliquota può essere ridotta se il proprio paese o area geografica ha accettato una convenzione fiscale con gli Stati Uniti.

##### <a name="tax-treaty-benefits"></a>Vantaggi della convenzione fiscale

Se non si risiede negli Stati Uniti, è possibile beneficiare dei vantaggi della convenzione fiscale. Tali vantaggi variano a seconda del paese o area geografica e possono ridurre l'importo delle imposte trattenute da Microsoft. È possibile richiedere i vantaggi della convenzione fiscale completando la parte II del modulo W-8BEN. È consigliabile rivolgersi alle autorità competenti del proprio paese o area geografica per determinare l'applicabilità di tali vantaggi alla propria situazione.

[Altre informazioni dettagliate sulle imposte per gli sviluppatori di app/giochi per Windows e per gli editori di Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stato di blocco pagamenti

Per impostazione predefinita, Microsoft invia i pagamenti su base mensile. Tuttavia, è possibile mettere in attesa i pagamenti, in modo da impedire l'invio di pagamenti al proprio account. Se si sceglie di bloccare i pagamenti, i ricavi guadagnati continueranno a essere registrati e i dettagli saranno disponibili nel **Riepilogo proventi**. Tuttavia non verranno inviati pagamenti all'account fino a quando non si rimuoverà lo stato di blocco.

Per bloccare i pagamenti, passare a **Impostazioni account**. In **Dettagli finanziari**, nella sezione **Stato di blocco pagamenti** impostare il dispositivo di scorrimento su **Sì**. È possibile modificare lo stato di blocco dei pagamenti in qualsiasi momento, ma tenere presente che la decisione avrà effetto sul pagamento mensile successivo. Se ad esempio si vuole bloccare il pagamento di aprile, assicurarsi di impostare lo stato di blocco dei pagamenti su **Sì** entro la fine di marzo.

Dopo aver impostato lo stato di blocco dei pagamenti su **Sì**, tutti i pagamenti verranno bloccati finché il dispositivo di scorrimento non verrà nuovamente impostato su **No**. Così facendo, si viene inclusi nel successivo ciclo di pagamento mensile (purché siano state soddisfatte le soglie di pagamento applicabili). Se ad esempio i pagamenti sono stati bloccati, ma si vuole che venga generato un pagamento in giugno, assicurarsi di impostare lo stato di blocco dei pagamenti su **No** entro la fine di maggio.

> [!NOTE]
> La selezione di **Stato di blocco pagamenti** si applica a **tutte** le fonti di ricavi pagate tramite il Centro per i partner Microsoft, inclusi Azure Marketplace, AppSource, Microsoft Store, annunci pubblicitari e così via). Non è possibile selezionare stati di blocco diversi per ogni fonte dei ricavi.

### <a name="devices"></a>Dispositivi

Le impostazioni di gestione dei dispositivi si applicano solo alla pubblicazione UWP. [Altre informazioni](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID di rilevamento

Gli identificatori univoci globali (GUID) sono numeri di riferimento univoci (con 32 cifre esadecimali) che possono essere usati per tenere traccia dell'utilizzo di Azure. 

Per creare un GUID per il rilevamento, è necessario usare un generatore di GUID. Il team di Archiviazione di Azure ha creato un [modulo generatore di GUID](https://aka.ms/StoragePartners) che invierà un'e-mail con un GUID nel formato corretto e riutilizzabile nei vari sistemi di rilevamento.

Si consiglia di creare un GUID univoco per ogni canale di offerta e distribuzione per ogni prodotto. È possibile scegliere di usare un unico GUID per i vari canali di distribuzione del prodotto se non si vuole suddividere la creazione di report.

Se si usa un modello per distribuire un prodotto disponibile su Azure Marketplace e su GitHub, è possibile creare e registrare due GUID distinti:

- Prodotto A su Azure Marketplace
- Prodotto A su GitHub

La creazione di report viene effettuata in base al valore partner (ID partner Microsoft) e ai GUID. È anche possibile tenere traccia dei GUID a un livello più granulare allineandosi a ogni piano dell'offerta.

Per altre informazioni, vedere la pagina relativa alla [Verifica dell'utilizzo dei clienti di Azure con GUID](../azure-partner-customer-usage-attribution.md#faq).

## <a name="create-a-billing-profile"></a>Creare un profilo di fatturazione

Se si pubblica un'offerta [Dynamics 365 for Customer Engagement e Power Apps](./create-new-customer-engagement-offer.md) o [Dynamics 365 for Operations](./create-new-operations-offer.md), è necessario completare il **profilo di fatturazione**.

L'indirizzo di fatturazione viene prepopolato in base alla persona giuridica ed è possibile aggiornarlo in un secondo momento. I campi IVA e Partita IVA sono facoltativi.  Non è possibile modificare il nome del paese o dell'area geografica e il nome della società.

## <a name="multi-user-account-management"></a>Gestione di account multiutente

Il centro per i partner USA [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) per l'accesso e la gestione di account multiutente. L'istanza di Azure AD dell'organizzazione viene automaticamente associata all'account del Centro per i partner durante il processo di registrazione.

## <a name="manage-users"></a>Gestire gli utenti

La sezione **Utenti** del Centro per i partner (in **Impostazioni account**) consente di usare Azure AD per gestire gli utenti, i gruppi e le applicazioni di Azure AD che hanno accesso all'account del Centro per i partner. L'account deve avere autorizzazioni [**a livello di responsabile**](#define-user-roles-and-permissions) per l'[account aziendale (tenant di Azure AD)](./company-work-accounts.md) in cui si vogliono aggiungere o modificare gli utenti. Per gestire gli utenti all'interno di un account aziendale/tenant diverso, è necessario disconnettersi e quindi accedere nuovamente come utente con autorizzazioni di **responsabile** per tale account aziendale/tenant.

Dopo aver eseguito l'accesso con l'account aziendale (tenant di Azure AD), è possibile:

- [Aggiungere o rimuovere utenti](#add-existing-users)
- [Modificare una password utente](#change-a-user-password)
- [Aggiungere o rimuovere gruppi](#manage-groups)
- [Aggiungere o rimuovere applicazioni Azure AD](#add-new-azure-ad-applications)
- [Gestire le chiavi per un'applicazione Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definire autorizzazioni e ruoli utente](#define-user-roles-and-permissions)

Tenere presente che tutti gli utenti del Centro per i partner (inclusi i gruppi e le applicazioni Azure AD) devono avere un account aziendale attivo in un [tenant di Azure AD](#manage-tenants) associato all'account del Centro per i partner.

### <a name="add-existing-users"></a>Aggiungere utenti esistenti

Per aggiungere utenti all'account del Centro per i partner già esistenti nell'[account aziendale (tenant di Azure AD)](./company-work-accounts.md) della società:

1. Passare a **Utenti** (in **Impostazioni account**) e selezionare **Aggiungi utenti**.
2. Selezionare uno o più utenti nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
*Se si seleziona più di un utente da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più utenti con ruoli o autorizzazioni diverse, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Al termine della scelta degli utenti, selezionare **Aggiungi selezionato**.
4. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per gli utenti selezionati.
5. Selezionare **Salva**.

### <a name="create-new-users"></a>Creare nuovi utenti

Per creare nuovi account utente, è necessario avere un account con autorizzazioni di [**amministratore globale**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Passare a **Utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **Crea nuovi utenti**.
1. Immettere un nome, un cognome e un nome utente per ogni nuovo utente. 
1. Per assegnare al nuovo utente un account amministratore globale nella directory dell'organizzazione, selezionare la casella di controllo **Impostare questo utente come amministratore globale nel tenant di Azure AD, con il controllo completo su tutte le risorse della directory**. In questo modo l'utente avrà accesso completo a tutte le funzionalità amministrative nell'istanza di Azure AD dell'azienda e potrà aggiungere e gestire gli utenti nell'account aziendale (tenant di Azure AD) dell'organizzazione, ma non nel Centro per i partner, a meno che non si conceda all'account il ruolo o le autorizzazioni appropriate.
1. Se si è selezionata la casella **Impostare questo utente come amministratore globale**, si dovrà specificare un **indirizzo e-mail per il ripristino della password** per consentire all'utente di recuperare la password, se necessario.
1. Nella sezione **Appartenenza a gruppo** selezionare i gruppi a cui dovrà appartenere il nuovo utente.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

La creazione di un nuovo utente nel Centro per i partner creerà anche un account per tale utente nell'account aziendale (tenant di Azure AD) a cui è stato effettuato l'accesso. Se si apportano modifiche al nome di un utente nel Centro per i partner, le stesse modifiche vengono apportate all'account aziendale dell'organizzazione (tenant di Azure AD).

### <a name="invite-new-users-by-email"></a>Invitare nuovi utenti tramite posta elettronica

Per invitare tramite posta elettronica utenti che attualmente non fanno parte dell'account aziendale (tenant di Azure AD) della società, è necessario un account con autorizzazioni di [**amministratore globale**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Passare a **Utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **Invitare gli utenti tramite posta elettronica**.
2. Immettere uno o più indirizzi di posta elettronica (fino a 10), separati da virgole o punti e virgola.
3. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
4. Selezionare **Salva**.

Gli utenti invitati riceveranno un invito tramite posta elettronica per aggiungere l'account del Centro per i partner. Verrà creato un nuovo account utente guest nell'account aziendale (tenant di Azure AD). Ogni utente dovrà accettare l'invito per poter accedere all'account.

Se è necessario inviare nuovamente un invito, visitare la pagina **Utenti**, trovare l'invito nell'elenco di utenti, selezionare l'indirizzo di posta elettronica (o il testo *Invito in sospeso*), quindi nella parte inferiore della pagina selezionare **Invia di nuovo l'invito**.

> [!NOTE]
> Se l'organizzazione usa l'[integrazione di directory](https://go.microsoft.com/fwlink/p/?LinkID=724033) per sincronizzare il servizio directory locale con l'istanza di Azure AD, non sarà possibile creare nuovi utenti, gruppi o applicazioni Azure AD nel Centro per i partner. È necessario che l'utente (o un altro amministratore nella directory locale) li crei direttamente nella directory locale prima di poterli visualizzare e aggiungere nel Centro per i partner.

### <a name="remove-a-user"></a>Rimuovere un utente

Per rimuovere un utente dall'account aziendale (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare l'utente che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui confermare che si vogliono rimuovere gli utenti selezionati.

### <a name="change-a-user-password"></a>Modificare una password utente

Se uno degli utenti deve modificare la password, può farlo autonomamente se al momento della creazione dell'account si è specificato un **indirizzo e-mail per il ripristino della password**. È anche possibile aggiornare la password di un utente seguendo questa procedura. Per modificare la password di un utente nell'account aziendale della società (tenant di Azure AD), è necessario aver effettuato l'accesso a un account con autorizzazioni di [**amministratore globale**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md). In questo modo verrà modificata la password dell'utente nel tenant di Azure AD, insieme alla password usata per accedere al centro per i partner.

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare il nome dell'account utente che si vuole modificare.
2. Selezionare il pulsante **Reimposta password** nella parte inferiore della pagina.
3. Verrà visualizzata una pagina di conferma per visualizzare le informazioni di accesso per l'utente, inclusa una password temporanea. Assicurarsi di stampare o copiare queste informazioni e di farle avere all'utente, perché non sarà possibile accedere alla password temporanea dopo aver lasciato questa pagina.

## <a name="manage-groups"></a>Gestire i gruppi

I gruppi consentono di controllare contemporaneamente più ruoli utente e autorizzazioni.

### <a name="add-an-existing-group"></a>Aggiungere un gruppo esistente

Per aggiungere un gruppo già esistente nell'account aziendale dell'organizzazione (tenant di Azure AD) all'account del Centro per i partner:

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
2. Selezionare uno o più gruppi dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare gruppi specifici.
Se si seleziona più di un gruppo da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più gruppi con ruoli o autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Al termine della scelta di gruppi, selezionare **Aggiungi selezionato**.
4. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per i gruppi selezionati. Tutti i membri del gruppo potranno accedere all'account del Centro per i partner con le autorizzazioni applicate al gruppo, indipendentemente dai ruoli e dalle autorizzazioni associati ai rispettivi account.
5. Selezionare **Salva**.

Quando si aggiunge un gruppo esistente, ogni utente membro di tale gruppo potrà accedere all'account del Centro per i partner, con le autorizzazioni associate al ruolo assegnato del gruppo.

### <a name="add-a-new-group"></a>Aggiungere un nuovo gruppo

Per aggiungere un nuovo gruppo all'account del Centro per i partner:

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi gruppi**.
2. Nella pagina successiva selezionare **Nuovo gruppo**.
3. Immettere il nome visualizzato del nuovo gruppo.
4. Specificare i ruoli o le autorizzazioni personalizzate per il gruppo. Tutti i membri del gruppo potranno accedere all'account del Centro per i partner con le autorizzazioni applicate qui, indipendentemente dai ruoli o dalle autorizzazioni associati ai rispettivi account.
5. Selezionare gli utenti per il nuovo gruppo nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici.
6. Al termine della selezione degli utenti, selezionare **Aggiungi selezionati** per aggiungerli al nuovo gruppo.
7. Selezionare **Salva**.

Questo nuovo gruppo verrà creato anche nell'account di lavoro dell'organizzazione (tenant Azure AD), non solo nell'account del centro per i partner.

### <a name="remove-a-group"></a>Rimuovere un gruppo

Per rimuovere un gruppo dall'account aziendale (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare il gruppo che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui confermare che si vogliono rimuovere i gruppi selezionati.

## <a name="manage-azure-ad-applications"></a>Gestire applicazioni Azure AD

È possibile consentire alle applicazioni o ai servizi che fanno parte dell'istanza di Azure AD dell'azienda di accedere all'account del Centro per i partner.

### <a name="add-existing-azure-ad-applications"></a>Aggiungere applicazioni Azure AD esistenti

Per aggiungere applicazioni già esistenti nell'istanza di Azure Active Directory dell'azienda:

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi applicazioni Azure AD**.
2. Selezionare una o più applicazioni Azure AD dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare applicazioni Azure AD specifiche. Se si seleziona più di un'applicazione Azure AD da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più applicazioni Azure AD con ruoli o autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
3. Al termine della selezione Azure AD applicazioni, selezionare **Aggiungi selezionato**.
4. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per le applicazioni Azure AD selezionate.
5. Selezionare **Salva**.

### <a name="add-new-azure-ad-applications"></a>Aggiungere nuove applicazioni Azure AD

Per concedere all'account di una nuova applicazione Azure AD l'accesso al Centro per i partner, è possibile crearne uno nella sezione **Utenti**. Verrà creato un nuovo account nell'account aziendale dell'azienda (tenant Azure AD), non solo nell'account del centro per i partner. Se si usa questa applicazione Azure AD soprattutto per l'autenticazione del Centro per i partner e non è necessario che gli utenti vi accedano direttamente, è possibile immettere un indirizzo valido per **URL di risposta** e **URI ID app**, purché tali valori non vengano usati da altre applicazioni Azure AD nella directory.

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi applicazioni Azure AD**.
2. Nella pagina successiva selezionare **Nuova applicazione Azure AD**.
3. Immettere l'**URL di risposta** per la nuova applicazione Azure AD. Si tratta dell'URL dove gli utenti possono accedere e usare l'applicazione Azure AD (noto anche come URL dell'app o URL di accesso). L'**URL di risposta** non può contenere più di 256 caratteri e deve essere univoco all'interno della directory.
4. Immettere l'**URI ID app** per la nuova applicazione Azure AD. Si tratta di un identificatore logico per l'applicazione Azure AD, presentato quando viene inviata ad Azure AD una richiesta di accesso Single Sign-On. L' **URI ID app** deve essere univoco per ogni Azure ad applicazione nella directory. Questo ID può contenere al massimo 256 caratteri. Per altre informazioni sull'URI ID app, vedere [integrazione di applicazioni con Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)).
5. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'applicazione Azure AD.
6. Selezionare **Salva**.

Dopo aver aggiunto o creato un'applicazione Azure AD, è possibile tornare alla sezione **Utenti** e selezionare il nome dell'applicazione per esaminare le impostazioni per l'applicazione, incluso l'ID tenant, l'ID client, l'URL di risposta e l'URI ID app.

### <a name="remove-an-azure-ad-application"></a>Rimuovere un'applicazione Azure AD

Per rimuovere un'applicazione dall'account aziendale (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare l'applicazione che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui confermare che si vogliono rimuovere le applicazioni selezionate.

### <a name="manage-keys-for-an-azure-ad-application"></a>Gestire le chiavi per un'applicazione Azure AD

Se l'applicazione Azure AD legge e scrive i dati in Microsoft Azure AD, sarà necessaria una chiave. È possibile creare chiavi per un'applicazione Azure AD modificandone le informazioni nel Centro per i partner. È anche possibile rimuovere le chiavi che non sono più necessarie.

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare il nome dell'applicazione Azure AD. Verranno visualizzate tutte le chiavi attive per l'applicazione Azure AD, inclusa la data in cui è stata creata la chiave e il momento in cui scadrà 50.
2. Per rimuovere una chiave non più necessaria, selezionare **Rimuovi**.
3. Per aggiungere una nuova chiave, selezionare **Aggiungi nuova chiave**.
4. Verrà visualizzata una schermata con i valori **ID client** e **Chiave**. Assicurarsi di stampare o copiare queste informazioni perché non sarà possibile accedervi di nuovo dopo aver lasciato questa pagina.
5. Per creare altre chiavi, selezionare **Aggiungi un'altra chiave**.

## <a name="define-user-roles-and-permissions"></a>Definire autorizzazioni e ruoli utente

Agli utenti della società è possibile assegnare i seguenti ruoli e autorizzazioni per il programma Commercial Marketplace nel centro per i partner:

- **Proprietario**
  - "Questa è la persona che ha creato l'account e ha l'accesso completo, inclusa la possibilità di creare e modificare tutti gli utenti dell'account e di modificare tutte le impostazioni finanziarie e degli account. Un account ha in genere un solo proprietario. "
- **Responsabile**
  - Può accedere a tutte le funzionalità dell'account Microsoft a eccezione delle impostazioni di imposte e pagamenti
  - Può gestire utenti, ruoli e account aziendali (tenant)
- **Developer**
  - Può gestire e pubblicare le offerte
  - Può visualizzare alcuni report del server di pubblicazione

> [!NOTE]
> Per il programma Marketplace commerciale, i ruoli Amministratore globale, Collaboratore aziendale, Financial Contributor (Collaboratore finanziario) e Addetto al marketing non vengono usati. L'assegnazione di questi ruoli agli utenti non ha alcun effetto. Solo i ruoli Responsabile e Sviluppatore concedono le autorizzazioni agli utenti.

Per altre informazioni sulla gestione di ruoli e autorizzazioni in altre aree del Centro per i partner, ad esempio Azure Active Directory (AD), Cloud Solution Provider (CSP), Control Panel Vendor (CPV) (Fornitore del Pannello di controllo), Utenti guest o Microsoft Partner Network (MPN), vedere [Assegnare autorizzazioni e ruoli utente nel Centro per i partner](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Gestire i tenant

Un tenant di Azure Active Directory (AD), detto anche "account aziendale" in questa serie di documenti, è una rappresentazione dell'organizzazione configurata nel portale di Azure e consente di gestire un'istanza specifica dei servizi cloud Microsoft per gli utenti interni ed esterni. Se l'organizzazione ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Office 365, è stato creato un tenant di Azure AD.

È possibile configurare più tenant da usare con il Centro per i partner. Tutti gli utenti con il ruolo **Responsabile** nell'account del Centro per i partner potranno aggiungere e rimuovere tenant di Azure AD dall'account.  

### <a name="add-an-existing-tenant"></a>Aggiungere un tenant esistente

Per associare un altro tenant di Azure AD all'account del Centro per i partner:

1. Nella pagina **Tenant** (in **Impostazioni account**) selezionare **Associa un altro tenant di Azure AD**.
2. Immettere le credenziali di Azure AD per il tenant che si vuole associare.
3. Controllare l'organizzazione e il nome di dominio per il tenant di Azure AD. Per completare l'associazione, selezionare **Conferma**.

Se l'associazione ha esito positivo, sarà possibile aggiungere e gestire gli utenti degli account nella sezione **Users** del Centro per i partner.

### <a name="create-a-new-tenant"></a>Creare un nuovo tenant

Per creare un altro tenant di Azure AD con l'account del Centro per i partner:

1. Nella pagina **Tenant** (in **Impostazioni account**) selezionare **Crea un nuovo tenant di Azure AD**.
2. Immettere le informazioni sulla directory per la nuova istanza di Azure AD:
    - **Nome di dominio**: nome univoco che verrà usato per il dominio di Azure AD, insieme a ".onmicrosoft.com". Se ad esempio è stato immesso "example", il dominio di Azure AD sarà "example.onmicrosoft.com".
    - **Indirizzo di posta elettronica del contatto**: indirizzo di posta elettronica a cui è possibile essere contattati in merito all'account, se necessario.
    - **Informazioni sull'account utente dell'amministratore globale**: nome, cognome, nome utente e password da usare per il nuovo account amministratore globale.
3. Selezionare **Crea** per confermare il nuovo dominio e le informazioni sull'account.
4. Accedere con il nuovo nome utente e la nuova password di amministratore globale di Azure AD per iniziare ad [aggiungere e gestire gli utenti](#manage-users).

Per altre informazioni sulla creazione di nuovi tenant all'interno del portale di Azure, invece che tramite il portale del Centro per i partner, vedere l'articolo [Creare un nuovo tenant in Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Rimuovere un tenant

Per rimuovere un tenant dall'account del Centro per i partner, trovarne il nome nella pagina **Tenant** (in **Impostazioni account**), quindi selezionare **Rimuovi**. Verrà richiesto di confermare che si vuole rimuovere il tenant. Una volta eseguita questa operazione, nessun utente in tale tenant potrà accedere all'account del Centro per i partner e tutte le autorizzazioni configurate per tali utenti verranno rimosse.

Quando si rimuove un tenant, tutti gli utenti aggiunti all'account del Centro per i partner da tale tenant non potranno più accedere all'account.

> [!TIP]
> Non è possibile rimuovere un tenant se si è attualmente connessi al Centro per i partner con un account nello stesso tenant. Per rimuovere un tenant, è necessario accedere al Centro per i partner come **responsabile** per un altro tenant associato all'account. Se è presente un solo tenant associato all'account, il tenant può essere rimosso solo dopo aver eseguito l'accesso con l'account Microsoft che ha aperto l'account.

## <a name="agreements"></a>Contratti

La sezione **contratti** di centro per i partner (in **Impostazioni account**) consente di visualizzare un elenco dei contratti di pubblicazione autorizzati. Questi contratti sono elencati in base al nome e al numero di versione, inclusa la data di accettazione e il nome dell'utente che ha accettato il contratto.

È possibile che nella parte superiore della pagina venga visualizzato il messaggio **Azioni necessarie** se sono presenti aggiornamenti dei contratti che richiedono attenzione. Per accettare un contratto aggiornato, leggere prima la versione del contratto collegata, quindi selezionare **Accetto contratto**.
