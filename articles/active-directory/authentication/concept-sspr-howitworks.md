---
title: Approfondimento sulla reimpostazione della password self-service-Azure Active Directory
description: Funzionamento della reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fcac058a510b8b7fed5b3967bbbf439dd4c0f71
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786742"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Funzionamento: reimpostazione self-service della password di Azure AD

Come funziona la reimpostazione della password self-service? Cosa comporta questa opzione per l'interfaccia? Continuare la lettura per ottenere altre informazioni sulla reimpostazione della password self-service di Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Funzionamento del portale di reimpostazione delle password

Quando un utente accede al portale di reimpostazione della password, si avvia un flusso di lavoro per determinare:

   * come localizzare la pagina
   * la validità dell'account
   * l'organizzazione a cui l'utente appartiene
   * la modalità di gestione della password dell'utente
   * se l'utente ha una licenza per usare la funzionalità

Leggere i passaggi seguenti per informazioni sulla logica alla base della pagina di reimpostazione della password:

1. L'utente seleziona il collegamento **Problemi di accesso all'account?** o accede direttamente a [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * In base alle impostazioni locali del browser viene eseguito il rendering dell'esperienza nella lingua appropriata. L'esperienza di reimpostazione della password viene localizzata nelle stesse lingue supportate da Office 365.
   * Per visualizzare il portale di reimpostazione della password localizzato in un'altra lingua, accodare"?mkt=" alla fine dell'URL di reimpostazione della password seguendo questo esempio di localizzazione verso lo spagnolo [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. L'utente immette un ID utente e un captcha.
3. Azure AD verifica se l'utente può usare questa funzionalità tramite le operazioni seguenti:
   * Verifica che questa funzionalità sia abilitata per l'utente e che all'utente sia assegnata una licenza Azure AD.
     * Se la funzionalità non è abilitata o se non dispone della licenza, per reimpostare la password l'utente deve contattare il proprio amministratore.
   * Verificare che per l'account dell'utente siano stati definiti i metodi di autenticazione corretti, in conformità ai criteri dell'amministratore.
     * Se i criteri prevedono un solo metodo, viene verificato se per l'utente sono definiti i dati appropriati per almeno uno dei metodi di autenticazione abilitati dai criteri dell'amministratore.
       * Se i metodi di autenticazione non sono configurati, l'utente verrà invitato a contattare l'amministratore per reimpostare la password.
     * Se i criteri prevedono due metodi, viene verificato se per l'utente sono definiti i dati appropriati per almeno due dei metodi di autenticazione abilitati dai criteri dell'amministratore.
       * Se i metodi di autenticazione non sono configurati, l'utente verrà invitato a contattare l'amministratore per reimpostare la password.
     * Se un ruolo di amministratore di Azure viene assegnato all'utente, vengono applicati i criteri di password complessa con due attività di controllo. Altre informazioni su questi criteri sono disponibili nella sezione [Differenze dei criteri di reimpostazione degli amministratori](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Verifica se la gestione della password dell'utente viene eseguita in locale, con federazione, autenticazione pass-through o con sincronizzazione degli hash delle password.
     * Se il writeback è stato distribuito e la password dell'utente è gestita in locale, l'utente può continuare con il processo di autenticazione e di reimpostazione della password.
     * Se il writeback non è stato distribuito e la password dell'utente viene gestita in locale, all'utente viene richiesto di contattare l'amministratore per reimpostare la password.
4. Se viene stabilito che l'utente è in grado di reimpostare la password, viene avviata la procedura di reimpostazione tramite procedura guidata.

## <a name="authentication-methods"></a>Metodi di autenticazione

Se è abilitata la reimpostazione della password self-service, è necessario selezionare almeno una delle opzioni seguenti per i metodi di autenticazione. Queste opzioni sono a volte denominate "attività di controllo". È consigliabile **scegliere due o più metodi di autenticazione** in modo che gli utenti abbiano una maggiore flessibilità nel caso in cui non siano in grado di accedere a uno di questi in un dato momento. Altre informazioni sui metodi elencati di seguito sono disponibili nell'articolo informazioni sui [metodi di autenticazione](concept-authentication-methods.md).

* Notifica dell'app per dispositivi mobili
* Codice dell'app per dispositivi mobili
* Indirizzo di posta elettronica
* Cellulare
* Telefono ufficio
* Domande di sicurezza

Gli utenti possono reimpostare la password solo se sono presenti dati nei metodi di autenticazione abilitati dall'amministratore.

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni per le telefonate non saranno disponibili per gli utenti di multi-factor authentication e SSPR in un tenant gratuito/di valutazione Azure AD. Questa modifica non ha alcun effetto sui messaggi SMS. La telefonata continuerà a essere disponibile per gli utenti in tenant Azure AD a pagamento. Questa modifica ha effetto solo sui tenant di Azure AD gratuiti/di valutazione.

> [!WARNING]
> Gli account assegnati i ruoli di amministratore di Azure verrà richiesto di utilizzare i metodi definiti nella sezione [Differenze dei criteri di reimpostazione degli amministratori](concept-sspr-policy.md#administrator-reset-policy-differences).

![Selezione dei metodi di autenticazione nel portale di Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Numero di metodi di autenticazione necessari

Questa opzione determina il numero minimo di metodi di autenticazione o attività di controllo disponibili che un utente deve usare per reimpostare o sbloccare la password. Può essere impostata su 1 o 2.

Gli utenti possono scegliere di definire più metodi di autenticazione, se tali metodi sono abilitati dall'amministratore.

Se un utente non ha registrato il numero minimo di metodi richiesti, viene visualizzata una pagina di errore che invita a richiedere la reimpostazione della password a un amministratore.

#### <a name="mobile-app-and-sspr"></a>App per dispositivi mobili e SSPR

Quando si usa un'app per dispositivi mobili, ad esempio l'app Microsoft Authenticator, come un metodo per la reimpostazione della password, è necessario essere a conoscenza delle seguenti avvertenze:

* Quando gli amministratori richiedono l'uso di un solo metodo per la reimpostazione di una password, il codice di verifica è l'unica opzione disponibile.
* Quando gli amministratori richiedono l'uso di due metodi per la reimpostazione della password, gli utenti possono **usare** la notifica **OPPURE** il codice di verifica, oltre a qualsiasi altro metodo abilitato.

| Numero di metodi da reimpostare | Uno | Due |
| :---: | :---: | :---: |
| Funzionalità disponibili delle app per dispositivi mobili | Codice | Codice o notifica |

Gli utenti non hanno la possibilità di registrare l'app per dispositivi mobili durante la registrazione per la reimpostazione della password in modalità self-service da [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Gli utenti possono registrare l'app per dispositivi mobili al link in [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o nella nuova anteprima di registrazione delle informazioni di sicurezza alla pagina [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> È necessario abilitare la [registrazione convergente per la reimpostazione password self-service e Azure Multi-Factor Authentication (anteprima pubblica)](concept-registration-mfa-sspr-converged.md) prima che gli utenti siano in grado di accedere alla nuova esperienza in [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> Non è possibile selezionare l'app Authenticator come unico metodo di autenticazione durante la configurazione di un criterio a 1 Gate. Analogamente, non è possibile selezionare l'app Authenticator e un solo metodo aggiuntivo durante la configurazione di un criterio a 2 porte.
> Quindi, quando si configurano i criteri di SSPR che includono l'app Authenticator come metodo, è necessario selezionare almeno un metodo aggiuntivo durante la configurazione di un criterio a 1 Gate ed è necessario selezionare almeno due metodi aggiuntivi durante la configurazione di un criterio a 2 porte.
> Il motivo di questo requisito è dovuto al fatto che l'esperienza di registrazione SSPR corrente non include l'opzione per registrare l'app di autenticazione. L'opzione per registrare l'app Authenticator è inclusa nella nuova [registrazione convergente per la reimpostazione della password self-service e multi-factor authentication di Azure (anteprima pubblica)](concept-registration-mfa-sspr-converged.md).
> Consentendo ai criteri che usano solo l'app Authenticator (per i criteri a 1 Gate) o l'app Authenticator e un solo metodo aggiuntivo (per i criteri a 2 Gate), potrebbe impedire agli utenti di registrarsi per SSPR fino a quando non sono stati configurati per l'uso del nuovo Esperienza di registrazione.

### <a name="change-authentication-methods"></a>Modifica dei metodi di autenticazione

Cosa succede se si inizia con un criterio che ha solo un metodo di autenticazione registrato necessario per la reimpostazione o lo sblocco e si passa a due?

| Numero di metodi registrati | Numero di metodi necessari | Risultato |
| :---: | :---: | :---: |
| 1 o più | 1 | **Possibilità** di reimpostare o sbloccare |
| 1 | 2 | **Impossibilità** di reimpostare o sbloccare |
| 2 o più | 2 | **Possibilità** di reimpostare o sbloccare |

Se si modificano i tipi di metodi di autenticazione che un utente può usare, si potrebbe impedire inavvertitamente agli utenti l'uso della reimpostazione password self-service se questi non dispongono della quantità minima di dati.

Esempio:
1. Il criterio originale è configurato con due metodi di autenticazione necessari. Vengono usati solo il numero di telefono ufficio e le domande di sicurezza. 
2. L'amministratore modifica i criteri in modo da non usare più domande di sicurezza, ma da consentire l'uso del telefono cellulare e di un indirizzo di posta elettronica alternativo.
3. Gli utenti che non inseriscono il numero di telefono cellulare o l'indirizzo di posta elettronica alternativo nei rispettivi campi non possono reimpostare le password.

## <a name="registration"></a>Registrazione

### <a name="require-users-to-register-when-they-sign-in"></a>Richiedere agli utenti di registrarsi all'accesso

L'abilitazione di questa opzione richiede che un utente completi la registrazione di reimpostazione della password se effettua l'accesso a tutte le applicazioni con Azure AD. Questo flusso di lavoro include le applicazioni seguenti:

* Office 365
* Portale di Azure
* Pannello di accesso
* Applicazioni federate
* Applicazioni personalizzate che usano Azure AD

Quando la richiesta di registrazione è disabilitata, gli utenti possono eseguire la registrazione manualmente. È possibile visitare [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) oppure selezionare il collegamento **Registrazione per reimpostazione password** nella scheda **Profilo** nel pannello di accesso.

> [!NOTE]
> Gli utenti possono chiudere il portale per la registrazione della reimpostazione della password selezionando **Annulla** o chiudendo la finestra. Ogni volta che gli utenti eseguono l'accesso verrà tuttavia visualizzata una richiesta di registrazione finché non viene completato il processo di registrazione.
>
> Questa interruzione non interromperà la connessione degli utenti che hanno già eseguito l'accesso.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Impostare il numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione

Questa opzione determina il periodo di tempo tra l'impostazione e la conferma delle informazioni di autenticazione ed è disponibile solo se si abilita l'opzione per **Richiedere agli utenti di registrarsi all'accesso**.

I valori validi vanno da 0 a 730 giorni, laddove "0" implica che agli utenti non viene mai richiesto di riconfermare le informazioni di autenticazione.

## <a name="notifications"></a>Notifiche

### <a name="notify-users-on-password-resets"></a>Inviare notifiche agli utenti al momento della reimpostazione della password

Se questa opzione è impostata su **Sì**, l'utente che reimposta la password riceve un messaggio di posta elettronica che segnala che la password è stata modificata. Questo messaggio viene inviato tramite il portale di reimpostazione della password self-service agli indirizzi di posta elettronica primario e alternativo registrati in Azure AD. La notifica dell'evento di reimpostazione non viene inviata ad altre persone.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

Se questa opzione è impostata su **Sì**, *tutti gli amministratori* ricevono un messaggio di posta elettronica al proprio indirizzo di posta elettronica primario sul file in Azure AD. Questo messaggio notifica la modifica della password tramite il servizio di reimpostazione della password self-service da parte di un altro amministratore.

Esempio: nell'ambiente sono presenti quattro amministratori. L'amministratore A reimposta la password tramite il servizio di reimpostazione della password self-service. Gli amministratori B, C e D ricevono un messaggio di posta elettronica che li avvisa della reimpostazione della password.

## <a name="on-premises-integration"></a>Integrazione locale

Se Azure AD Connect è stato installato, configurato e abilitato, saranno disponibili le opzioni aggiuntive seguenti per le integrazioni locali. Se queste opzioni sono disattivate, il writeback non è stato configurato correttamente. Per altre informazioni, vedere [Configurazione del writeback delle password](howto-sspr-writeback.md).

![La convalida del writeback delle password è abilitata e funzionante][Writeback]

Questa pagina fornisce uno stato rapido del client di writeback locale. In base alla configurazione corrente viene visualizzato uno dei seguenti messaggi:

* Il client di writeback locale è attivo e in esecuzione.
* Azure AD è online e connesso al client di writeback locale. Tuttavia sembra che la versione installata di Azure AD Connect non sia aggiornata. Prendere in considerazione l'[aggiornamento di Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) per assicurarsi di disporre delle funzionalità di connettività più recenti e di importanti correzioni di bug.
* Purtroppo non è possibile verificare lo stato del client di writeback locale perché la versione installata di Azure AD Connect non è aggiornata. [Aggiornare Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) per essere in grado di controllare lo stato della connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. [Risoluzione dei problemi di Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) per ripristinare la connessione.
* Purtroppo non è possibile eseguire la connessione al client di writeback locale perché il writeback delle password non è stato configurato correttamente. [Configurare il writeback delle password](howto-sspr-writeback.md) per ripristinare la connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. Ciò può essere dovuto a errori temporanei nel sistema. Se il problema persiste, vedere [Risoluzione dei problemi di Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) per ripristinare la connessione.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Writeback delle password nella directory locale

Questo controllo determina se il writeback delle password è abilitato per la directory corrente. Se il writeback è attivato, indica lo stato del servizio writeback locale. Questo controllo è utile se si intende disabilitare temporaneamente il writeback delle password senza dover riconfigurare Azure AD Connect.

* Se l'opzione è impostata su **Sì**, il servizio writeback viene abilitato e gli utenti federati, con autenticazione pass-through e con sincronizzazione degli hash delle password possono reimpostare le password.
* Se l'opzione è impostata su **No**, il servizio writeback viene disabilitato e gli utenti federati, con autenticazione pass-through e con sincronizzazione degli hash delle password non possono reimpostare le password.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Allow users to unlock accounts without resetting their password

Questo controllo stabilisce se gli utenti che visitano il portale per la reimpostazione della password devono avere la possibilità di sbloccare il proprio account di Active Directory locale senza reimpostare la password. Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Usare questa impostazione per distinguere queste due operazioni.

* Se impostata su **Sì**, agli utenti viene offerta la possibilità di scegliere se reimpostare la password e sbloccare l'account o se sbloccare l'account senza reimpostare la password.
* Se impostata su **No**, gli utenti potranno eseguire le operazioni di reimpostazione della password e di sblocco dell'account solo in abbinamento.

### <a name="on-premises-active-directory-password-filters"></a>Filtri della password di Active Directory locali

Reimpostazione della password self-service di Azure AD esegue l'equivalente di una reimpostazione della password avviata dall'amministratore in Active Directory. Se si usa un filtro delle password di terze parti per applicare le regole di password personalizzate, ed è necessario che questo filtro delle password venga verificato durante la reimpostazione della password self-service di Azure AD, assicurarsi che la soluzione di filtro della password di terze parti sia configurata per essere applicata nello scenario di reimpostazione della password da parte dell'amministratore. [Protezione password di Azure AD per Windows Server Active Directory](concept-password-ban-bad-on-premises.md) è supportata per impostazione predefinita.

## <a name="password-reset-for-b2b-users"></a>Reimpostazione della password per utenti B2B

La modifica e la reimpostazione della password sono completamente supportate in tutte le configurazioni B2B. La reimpostazione della password di utenti B2B è supportata nei tre casi seguenti:

* **Utenti di un'organizzazione partner con un tenant di Azure AD esistente**: se l'organizzazione partner ha un tenant di Azure AD esistente, verranno *rispettati i criteri di reimpostazione della password abilitati in tale tenant*. Per garantire il corretto funzionamento della reimpostazione della password, l'organizzazione partner deve assicurarsi che sia abilitata la reimpostazione delle password self-service di Azure AD. Non sono previsti costi aggiuntivi per i clienti di Office 365. Per abilitare questa funzionalità, seguire i passaggi descritti in [Guida introduttiva: Reimpostazione password self-service di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
* **Utenti che hanno usato l'iscrizione self-service**: se l'organizzazione partner ha usato la funzionalità di [iscrizione self-service](../users-groups-roles/directory-self-service-signup.md) per accedere a un tenant, gli utenti possono eseguire la reimpostazione con l'indirizzo di posta elettronica registrato.
* **Utenti B2B**: tutti i nuovi utenti B2B creati usando le nuove [funzionalità B2B di Azure AD](../active-directory-b2b-what-is-azure-ad-b2b.md) potranno anche reimpostare le password con l'indirizzo di posta elettronica registrato durante il processo di invito.

Per testare questo scenario, passare a https://passwordreset.microsoftonline.com con uno di questi utenti partner. Se l'utente ha un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione, la reimpostazione della password funziona come previsto.

> [!NOTE]
> Gli account Microsoft a cui è stato concesso l'accesso guest al tenant di Azure AD, ad esempio Hotmail.com, Outlook.com o altri indirizzi di posta elettronica personali, non saranno in grado di utilizzare la funzionalità di reimpostazione delle password self-service di Azure AD. Tali account devono reimpostare le password usando le informazioni riportate nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD:

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Domande sulle licenze](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Credo che qualcosa sia rotto. Ricerca per categorie risolvere i problemi di SSPR?](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Informazioni sulla risoluzione e la configurazione del writeback delle password di integrazione locale"
