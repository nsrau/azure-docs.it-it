---
title: Approfondimento sulla reimpostazione della password self-service-Azure Active Directory
description: Funzionamento della reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 393ed336018c7a0272c15adaa72633abd6b95d2f
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377200"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Funzionamento: reimpostazione self-service della password di Azure AD

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di cambiare o reimpostare la password, senza intervento dell'amministratore o dell'help desk. Se un utente dimentica la password o ha l'account bloccato, può seguire le istruzioni per sbloccarlo autonomamente e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione.

> [!IMPORTANT]
> Questo articolo concettuale spiega a un amministratore il funzionamento della reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service ed è necessario tornare al proprio account, passare a [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

## <a name="how-does-the-password-reset-process-work"></a>Come funziona il processo di reimpostazione della password?

Un utente può reimpostare o modificare la password tramite il [portale SSPR](https://aka.ms/sspr). Devono innanzitutto registrare i metodi di autenticazione desiderati. Quando un utente accede al portale di SSPR, la piattaforma Azure considera i fattori seguenti:

* come localizzare la pagina
* la validità dell'account
* l'organizzazione a cui l'utente appartiene
* Dove viene gestita la password dell'utente?
* se l'utente ha una licenza per usare la funzionalità

Quando un utente seleziona il collegamento **non è possibile accedere all'account** da un'applicazione o da una pagina o passa direttamente a [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) , la lingua usata nel portale di SSPR si basa sulle opzioni seguenti:

* Per impostazione predefinita, le impostazioni locali del browser vengono usate per visualizzare il SSPR nella lingua appropriata. L'esperienza di reimpostazione della password viene localizzata nelle stesse lingue [supportate da Office 365](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Se si vuole creare un collegamento a SSPR in una lingua localizzata specifica, aggiungere `?mkt=` alla fine dell'URL di reimpostazione della password insieme alle impostazioni locali obbligatorie.
    * Ad esempio, per specificare le impostazioni locali di spagnolo *es-US* , usare `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Quando il portale di SSPR viene visualizzato nella lingua richiesta, all'utente viene richiesto di immettere un ID utente e di passare un captcha. Azure AD ora verifica che l'utente sia in grado di usare SSPR eseguendo i controlli seguenti:

* Verifica che l'utente disponga di SSPR abilitato e che sia stato assegnato un Azure AD licenza.
  * Se l'utente non è abilitato per SSPR o non dispone di una licenza assegnata, all'utente viene richiesto di contattare l'amministratore per reimpostare la password.
* Verificare che per l'account dell'utente siano stati definiti i metodi di autenticazione corretti, in conformità ai criteri dell'amministratore.
  * Se il criterio richiede un solo metodo, verificare che per l'utente siano stati definiti i dati appropriati per almeno uno dei metodi di autenticazione abilitati dai criteri dell'amministratore.
    * Se i metodi di autenticazione non sono configurati, l'utente è invitato a contattare l'amministratore per reimpostare la password.
  * Se il criterio richiede due metodi, verificare che per l'utente siano stati definiti i dati appropriati per almeno due dei metodi di autenticazione abilitati dai criteri dell'amministratore.
    * Se i metodi di autenticazione non sono configurati, l'utente è invitato a contattare l'amministratore per reimpostare la password.
  * Se un ruolo di amministratore di Azure viene assegnato all'utente, vengono applicati i criteri di password complessa con due attività di controllo. Per altre informazioni, vedere [differenze tra i criteri di reimpostazione dell'amministratore](concept-sspr-policy.md#administrator-reset-policy-differences).
* Verifica se la password dell'utente è gestita in locale, ad esempio se il tenant Azure AD usa l'autenticazione federata, pass-through o la sincronizzazione degli hash delle password:
  * Se il writeback SSPR è configurato e la password dell'utente è gestita in locale, l'utente può procedere con l'autenticazione e la reimpostazione della password.
  * Se il writeback del SSPR non viene distribuito e la password dell'utente viene gestita in locale, all'utente viene richiesto di contattare l'amministratore per reimpostare la password.

Se tutti i controlli precedenti sono stati completati correttamente, l'utente viene guidato nel processo di reimpostazione o modifica della password.

> [!NOTE]
> SSPR può inviare notifiche tramite posta elettronica agli utenti come parte del processo di reimpostazione della password. Questi messaggi di posta elettronica vengono inviati tramite il servizio di inoltro SMTP, che opera in modalità attivo-attivo in diverse aree.
>
> I servizi di inoltro SMTP ricevono ed elaborano il corpo del messaggio di posta elettronica, ma non lo archiviano. Il corpo del messaggio di posta elettronica SSPR che potrebbe potenzialmente contenere informazioni fornite dal cliente non viene archiviato nei log del servizio di inoltro SMTP. I log contengono solo i metadati del protocollo.

Per iniziare a usare SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: abilitazione della reimpostazione della password self-service (SSPR)](tutorial-enable-sspr.md)

## <a name="registration-options"></a>Opzioni di registrazione

Prima che gli utenti possano reimpostare o modificare la password usando SSPR, devono registrarsi e i metodi di autenticazione da usare. Come indicato nella sezione precedente, un utente deve essere registrato per SSPR e disporre di una licenza appropriata applicata.

### <a name="require-users-to-register-when-they-sign-in"></a>Richiedere agli utenti di registrarsi all'accesso

È possibile abilitare l'opzione per richiedere a un utente di completare la registrazione SSPR se accedono a qualsiasi applicazione usando Azure AD. Questo flusso di lavoro include le applicazioni seguenti:

* Office 365
* Portale di Azure
* Pannello di accesso
* Applicazioni federate
* Applicazioni personalizzate che usano Azure AD

Quando non è necessaria la registrazione, gli utenti non vengono richiesti durante l'accesso, ma possono essere registrati manualmente. Gli utenti possono visitare [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) o selezionare il collegamento **registra per la reimpostazione della password** nella scheda **profilo** nel pannello di accesso.

![Opzioni di registrazione per SSPR nella portale di Azure][Registration]

> [!NOTE]
> Gli utenti possono chiudere il portale di registrazione SSPR selezionando **Annulla** o chiudendo la finestra. Tuttavia, viene chiesto di registrarsi ogni volta che accedono fino a quando non completano la registrazione.
>
> Questo interrupt per la registrazione di SSPR non interrompe la connessione dell'utente se è già connesso.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Impostare il numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione

Per assicurarsi che i metodi di autenticazione siano corretti quando sono necessari per reimpostare o modificare la password, è possibile richiedere agli utenti di confermare le informazioni registrate dopo un determinato periodo di tempo. Questa opzione è disponibile solo se si Abilita l'opzione **Richiedi agli utenti di registrarsi all'accesso** .

Valori validi per richiedere all'utente di confermare i metodi registrati da *0* a *730* giorni. L'impostazione di questo valore su *0* indica che agli utenti non viene mai chiesto di confermare le informazioni di autenticazione.

## <a name="authentication-methods"></a>Metodi di autenticazione

Quando un utente è abilitato per SSPR, è necessario che registri almeno un metodo di autenticazione. Si consiglia vivamente di scegliere due o più metodi di autenticazione in modo che gli utenti abbiano una maggiore flessibilità nel caso in cui non siano in grado di accedere a un metodo quando necessario. Per altre informazioni, vedere [che cosa sono i metodi di autenticazione?](concept-authentication-methods.md).

Per SSPR sono disponibili i metodi di autenticazione seguenti:

* Notifica dell'app per dispositivi mobili
* Codice app per dispositivi mobili
* E-mail
* Cellulare
* Telefono ufficio
* Domande di sicurezza

Gli utenti possono reimpostare la password solo se hanno registrato un metodo di autenticazione abilitato dall'amministratore.

> [!WARNING]
> Gli account assegnati ai ruoli di *amministratore* di Azure sono necessari per usare i metodi come definito nella sezione [reimpostazione dei criteri di reimpostazione amministratore](concept-sspr-policy.md#administrator-reset-policy-differences).

![Selezione dei metodi di autenticazione nel portale di Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Numero di metodi di autenticazione necessari

È possibile configurare il numero di metodi di autenticazione disponibili che un utente deve fornire per reimpostare o sbloccare la password. Questo valore può essere impostato su *uno* o *due*.

Gli utenti possono e devono registrare più metodi di autenticazione. Anche in questo caso, è consigliabile che gli utenti registrino due o più metodi di autenticazione in modo che abbiano una maggiore flessibilità nel caso in cui non siano in grado di accedere a un metodo quando necessario.

Se un utente non ha il numero minimo di metodi richiesti registrati quando tenta di usare SSPR, viene visualizzata una pagina di errore che indica che un amministratore ha reimpostato la password. Prestare attenzione se si aumenta il numero di metodi necessari da uno a due se gli utenti esistenti sono registrati per SSPR e non sono in grado di usare la funzionalità. Per ulteriori informazioni, vedere la sezione seguente per [modificare i metodi di autenticazione](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>App per dispositivi mobili e SSPR

Quando si usa un'app per dispositivi mobili come metodo per la reimpostazione della password, ad esempio l'app Microsoft Authenticator, si applicano le considerazioni seguenti:

* Quando gli amministratori richiedono l'uso di un solo metodo per la reimpostazione di una password, il codice di verifica è l'unica opzione disponibile.
* Quando gli amministratori richiedono che vengano usati due metodi per reimpostare una password, gli utenti possono usare il codice di notifica **o** di verifica oltre agli altri metodi abilitati.

| Numero di metodi da reimpostare | Uno | Due |
| :---: | :---: | :---: |
| Funzionalità disponibili delle app per dispositivi mobili | Codice | Codice o notifica |

Gli utenti non hanno la possibilità di registrare l'app per dispositivi mobili durante la registrazione per la reimpostazione della password self-service da [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Gli utenti possono registrare l'app per dispositivi mobili in [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o nella registrazione delle informazioni di sicurezza combinata all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Non è possibile selezionare l'app Authenticator come unico metodo di autenticazione quando è richiesto solo il metodo. Analogamente, non è possibile selezionare l'app Authenticator e un solo metodo aggiuntivo quando sono necessari due metodi.
>
> Quando si configurano i criteri di SSPR che includono l'app Authenticator come metodo, è necessario selezionare almeno un metodo aggiuntivo quando è necessario un metodo ed è necessario selezionare almeno due metodi aggiuntivi quando si configurano due metodi.
>
> Questo requisito è dovuto al fatto che l'esperienza di registrazione di SSPR corrente non include l'opzione per registrare l'app di autenticazione. L'opzione per registrare l'app Authenticator è inclusa nella nuova esperienza di [registrazione combinata](./concept-registration-mfa-sspr-combined.md).
>
> Consentendo ai criteri che usano solo l'app Authenticator (quando è richiesto un metodo) o l'app Authenticator e un solo metodo aggiuntivo (quando sono necessari due metodi), potrebbe impedire agli utenti di registrarsi per SSPR fino a quando non sono configurati per l'uso della nuova esperienza di registrazione combinata.

### <a name="change-authentication-methods"></a>Modifica dei metodi di autenticazione

Cosa succede se si inizia con un criterio che ha solo un metodo di autenticazione registrato necessario per la reimpostazione o lo sblocco e si passa a due?

| Numero di metodi registrati | Numero di metodi necessari | Risultato |
| :---: | :---: | :---: |
| 1 o più | 1 | **Possibilità** di reimpostare o sbloccare |
| 1 | 2 | **Impossibilità** di reimpostare o sbloccare |
| 2 o più | 2 | **Possibilità** di reimpostare o sbloccare |

La modifica dei metodi di autenticazione disponibili può causare anche problemi per gli utenti. Se si modificano i tipi di metodi di autenticazione che un utente può usare, si potrebbe impedire inavvertitamente agli utenti l'uso della reimpostazione password self-service se questi non dispongono della quantità minima di dati.

Si consideri lo scenario di esempio seguente:

1. Il criterio originale è configurato con due metodi di autenticazione necessari. Vengono usati solo il numero di telefono ufficio e le domande di sicurezza.
1. L'amministratore modifica i criteri in modo da non usare più domande di sicurezza, ma da consentire l'uso del telefono cellulare e di un indirizzo di posta elettronica alternativo.
1. Gli utenti senza il telefono cellulare o i campi di posta elettronica alternativi compilati ora non possono reimpostare le password.

## <a name="notifications"></a>Notifiche

Per migliorare la consapevolezza degli eventi relativi alle password, SSPR consente di configurare le notifiche sia per gli utenti che per gli amministratori di identità.

### <a name="notify-users-on-password-resets"></a>Inviare notifiche agli utenti al momento della reimpostazione della password

Se questa opzione è impostata su **Sì**, gli utenti che reimpostano la password riceveranno un messaggio di posta elettronica che informa che la password è stata modificata. Il messaggio di posta elettronica viene inviato tramite il portale di SSPR agli indirizzi di posta elettronica primari e alternativi archiviati in Azure AD. La notifica dell'evento di reimpostazione non viene inviata ad altre persone.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

Se questa opzione è impostata su **Sì**, tutti gli altri amministratori di Azure riceveranno un messaggio di posta elettronica con l'indirizzo di posta elettronica principale archiviato in Azure ad. Questo messaggio notifica la modifica della password tramite il servizio di reimpostazione della password self-service da parte di un altro amministratore.

Si consideri lo scenario di esempio seguente:

* nell'ambiente sono presenti quattro amministratori.
* L'amministratore *A* Reimposta la password usando sspr.
* Gli amministratori *B*, *C*e *D* ricevono un messaggio di posta elettronica per avvisarli della reimpostazione della password.

## <a name="on-premises-integration"></a>Integrazione locale

Se si dispone di un ambiente ibrido, è possibile configurare Azure AD Connect per la scrittura di eventi di modifica della password da Azure AD a una directory locale.

![La convalida del writeback delle password è abilitata e funzionante][Writeback]

Azure AD verifica la connettività ibrida corrente e fornisce uno dei messaggi seguenti nell'portale di Azure:

* Il client di writeback locale è attivo e in esecuzione.
* Azure AD è online e connesso al client di writeback locale. Tuttavia sembra che la versione installata di Azure AD Connect non sia aggiornata. Prendere in considerazione l'[aggiornamento di Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) per assicurarsi di disporre delle funzionalità di connettività più recenti e di importanti correzioni di bug.
* Sfortunatamente, non è possibile controllare lo stato del client di writeback locale perché la versione installata di Azure AD Connect è obsoleta. [Aggiornare Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) per essere in grado di controllare lo stato della connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. [Risoluzione dei problemi di Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) per ripristinare la connessione.
* Purtroppo non è possibile eseguire la connessione al client di writeback locale perché il writeback delle password non è stato configurato correttamente. [Configurare il writeback delle password](./tutorial-enable-sspr-writeback.md) per ripristinare la connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. Ciò può essere dovuto a errori temporanei nel sistema. Se il problema persiste, vedere [Risoluzione dei problemi di Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) per ripristinare la connessione.

Per iniziare a usare il writeback della reimpostazione della password self-service, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Abilitare il writeback della reimpostazione della password self-service](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Writeback delle password nella directory locale

È possibile abilitare il writeback delle password usando il portale di Azure. È anche possibile disabilitare temporaneamente il writeback delle password senza dover riconfigurare Azure AD Connect.

* Se l'opzione è impostata su **Sì**, il writeback è abilitato. Gli utenti federati, con autenticazione pass-through o con sincronizzazione di hash delle password possono reimpostare le password.
* Se l'opzione è impostata su **No**, il writeback è disabilitato. Gli utenti federati, con autenticazione pass-through o con sincronizzazione di hash delle password non possono reimpostare le password.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Allow users to unlock accounts without resetting their password

Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Per offrire flessibilità, è possibile scegliere di consentire agli utenti di sbloccare gli account locali senza dover reimpostare la password. Usare questa impostazione per separare le due operazioni.

* Se impostato su **Sì**, agli utenti viene offerta la possibilità di reimpostare la password e sbloccare l'account oppure di sbloccare l'account senza dover reimpostare la password.
* Se è impostato su **No**, gli utenti saranno in grado di eseguire solo un'operazione combinata di reimpostazione della password e sblocco dell'account.

### <a name="on-premises-active-directory-password-filters"></a>Filtri della password di Active Directory locali

SSPR esegue l'equivalente di una reimpostazione della password avviata dall'amministratore in Active Directory. Se si usa un filtro password di terze parti per applicare regole password personalizzate ed è necessario che il filtro delle password venga controllato durante Azure AD reimpostazione della password self-service, assicurarsi che la soluzione filtro password di terze parti sia configurata per essere applicata allo scenario di reimpostazione della password amministratore. [Azure ad la protezione con password per Active Directory Domain Services](concept-password-ban-bad-on-premises.md) è supportata per impostazione predefinita.

## <a name="password-reset-for-b2b-users"></a>Reimpostazione della password per utenti B2B

La modifica e la reimpostazione della password sono completamente supportate in tutte le configurazioni B2B. La reimpostazione della password di utenti B2B è supportata nei tre casi seguenti:

* **Utenti di un'organizzazione partner con un tenant di Azure ad esistente**: se l'organizzazione partner ha un tenant di Azure ad esistente, verranno rispettati i criteri di reimpostazione della password abilitati in tale tenant. Per garantire il corretto funzionamento della reimpostazione della password, l'organizzazione partner deve assicurarsi che sia abilitata la reimpostazione delle password self-service di Azure AD. Non sono previsti costi aggiuntivi per i clienti di Office 365.
* **Utenti che si iscrivono** all'iscrizione self-service: se l'organizzazione partner ha usato la funzionalità di [iscrizione self-service](../users-groups-roles/directory-self-service-signup.md) per accedere a un tenant, è possibile reimpostare la password con l'indirizzo di posta elettronica registrato.
* **Utenti B2B**: tutti i nuovi utenti B2B creati usando le nuove [funzionalità B2B di Azure ad](../external-identities/what-is-b2b.md) possono anche reimpostare le password con l'indirizzo di posta elettronica registrato durante il processo di invito.

Per testare questo scenario, passare a https://passwordreset.microsoftonline.com con uno di questi utenti partner. Se l'utente ha un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione, la reimpostazione della password funziona come previsto.

> [!NOTE]
> Gli account Microsoft a cui è stato concesso l'accesso Guest al tenant di Azure AD, ad esempio quelli da Hotmail.com, Outlook.com o altri indirizzi di posta elettronica personali, non sono in grado di usare Azure AD SSPR. Tali account devono reimpostare le password usando le informazioni riportate nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: abilitazione della reimpostazione della password self-service (SSPR)](tutorial-enable-sspr.md)

Gli articoli seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Configurare le opzioni di registrazione SSPR nel portale di Azure"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Integrazione locale per SSPR nel portale di Azure"