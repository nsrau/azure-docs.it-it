---
title: Come funziona la reimpostazione della password self-service di Azure AD | Microsoft Docs
description: Approfondimenti sulla reimpostazione della password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: bb2e1aebc60eee5f94ed486e0efb43265728df6f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Approfondimenti sulla reimpostazione della password self-service in Azure AD

Come funziona la reimpostazione della password self-service? Cosa comporta questa opzione per l'interfaccia? Continuare la lettura per ottenere altre informazioni sulla reimpostazione della password self-service di Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Funzionamento del portale di reimpostazione delle password

Quando un utente accede al portale di reimpostazione della password, si avvia un flusso di lavoro per determinare:

   * come localizzare la pagina
   * la validità dell'account
   * l'organizzazione a cui l'utente appartiene
   * la modalità di gestione della password dell'utente
   * se l'utente ha una licenza per usare la funzionalità

Leggere i passaggi seguenti per informazioni sulla logica alla base della pagina di reimpostazione della password:

1. L'utente seleziona collegamento **Problemi di accesso all'account?** o accede direttamente a [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * In base alle impostazioni locali del browser viene eseguito il rendering dell'esperienza nella lingua appropriata. L'esperienza di reimpostazione della password viene localizzata nelle stesse lingue supportate da Office 365.
2. L'utente immette un ID utente e un captcha.
3. Azure AD verifica se l'utente può usare questa funzionalità tramite le operazioni seguenti:
   * Verifica che questa funzionalità sia abilitata per l'utente e che all'utente sia assegnata una licenza Azure AD.
     * Se la funzionalità non è abilitata o se non dispone della licenza, per reimpostare la password l'utente deve contattare il proprio amministratore.
   * Verifica che per l'account dell'utente siano stati definiti i dati di test corretti, in conformità con i criteri dell'amministratore.
     * Se i criteri prevedono un solo test, viene verificato se per l'utente sono definiti i dati appropriati per almeno uno dei test abilitati dai criteri dell'amministratore.
       * Se la verifica utente non è configurata, l'utente verrà invitato a contattare l'amministratore per reimpostare la password.
     * Se i criteri prevedono due test di verifica, viene verificato se per l'utente sono definiti i dati appropriati per almeno due dei test abilitati dai criteri dell'amministratore.
       * Se la verifica utente non è configurata, l'utente verrà invitato a contattare l'amministratore per reimpostare la password.
   * Verifica se la gestione della password dell'utente viene eseguita in locale, con federazione o con sincronizzazione degli hash delle password.
     * Se il writeback è stato distribuito e la password dell'utente è gestita in locale, l'utente può continuare con il processo di autenticazione e di reimpostazione della password.
     * Se il writeback non è stato distribuito e la password dell'utente viene gestita in locale, all'utente viene richiesto di contattare l'amministratore per reimpostare la password.
4. Se viene stabilito che l'utente è in grado di reimpostare la password, viene avviata la procedura di reimpostazione tramite procedura guidata.

## <a name="authentication-methods"></a>Metodi di autenticazione

Se è abilitata la reimpostazione della password self-service, è necessario selezionare almeno una delle opzioni seguenti per i metodi di autenticazione. Queste opzioni sono a volte denominate "attività di controllo". Si consiglia di scegliere almeno due metodi di autenticazione in modo che gli utenti dispongano di una maggiore flessibilità.

* Email
* Cellulare
* Telefono ufficio
* Domande di sicurezza

![Autenticazione][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Campi usati nella directory per i dati di autenticazione

* **Telefono ufficio**: corrisponde al telefono dell'ufficio.
    * Gli utenti non possono impostare questo campo autonomamente. Deve essere definito da un amministratore.
* **Cellulare**: corrisponde al telefono per l'autenticazione, non visibile al pubblico, o al telefono cellulare, visibile a tutti.
    * Il servizio cerca prima il telefono per l'autenticazione e quindi usa il cellulare, se il telefono per l'autenticazione non è presente.
* **Indirizzo e-mail alternativo**: corrisponde all'indirizzo di posta elettronica per l'autenticazione, non visibile a tutti, o all'indirizzo di posta elettronica alternativo.
    * Il servizio prima cerca l'indirizzo di posta elettronica per l'autenticazione, quindi esegue il failback per l'indirizzo di posta elettronica alternativo.

Per impostazione predefinita, solo gli attributi cloud Telefono ufficio e Cellulare vengono sincronizzati con la directory cloud dalla directory locale per i dati di autenticazione.

Gli utenti possono reimpostare la password solo se sono presenti dati nei metodi di autenticazione abilitati e richiesti dall'amministratore.

Se gli utenti non desiderano che il numero di telefono cellulare sia visibile nella directory, ma vogliono comunque utilizzarlo per la reimpostazione della password, gli amministratori non devono inserire i relativi dati nella directory. Gli utenti devono quindi popolare il proprio attributo **Telefono per autenticazione** tramite il [portale di registrazione per la reimpostazione della password](http://aka.ms/ssprsetup). Gli amministratori possono visualizzare queste informazioni nel profilo dell'utente, ma non vengono pubblicate altrove.

### <a name="the-number-of-authentication-methods-required"></a>Numero di metodi di autenticazione necessari

Questa opzione determina il numero minimo di metodi di autenticazione o attività di controllo disponibili che un utente deve usare per reimpostare o sbloccare la password. Può essere impostata su 1 o 2.

Gli utenti possono scegliere di definire più metodi di autenticazione, se tali metodi sono abilitati dall'amministratore.

Se un utente non ha registrato il numero minimo di metodi richiesti, viene visualizzata una pagina di errore che invita a richiedere la reimpostazione della password a un amministratore.

#### <a name="change-authentication-methods"></a>Modifica dei metodi di autenticazione

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
3. Gli utenti che non inseriscono il numero di telefono cellulare e l'indirizzo di posta elettronica alternativo nei rispettivi campi non possono reimpostare le password.

### <a name="how-secure-are-my-security-questions"></a>Sicurezza delle domande di sicurezza

Se vengono usate le domande di sicurezza, è consigliabile combinare questo metodo con un altro. Le domande di sicurezza possono risultare meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande di un altro utente.

> [!NOTE] 
> Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. All'amministratore non è consentito leggere o modificare le domande e le risposte di un utente.
>

### <a name="security-question-localization"></a>Localizzazione della domanda di sicurezza

Tutte le domande predefinite che seguono sono localizzate nel set completo delle lingue di Office 365 in base alle impostazioni locali del browser dell'utente:

* In quale città hai incontrato tuo marito o il tuo partner?
* In quale città si sono incontrati i tuoi genitori?
* In quale città vive tuo fratello/sorella più vicino/a?
* In quale città è nato tuo padre?
* In quale città hai avuto il tuo primo lavoro?
* In quale città è nata tua madre?
* In quale città hai trascorso il Capodanno del 2000?
* Qual è il cognome del tuo insegnante preferito delle scuole superiori?
* Qual è il nome di una delle università a cui hai inviato una richiesta di iscrizione ma che non hai frequentato?
* In quale luogo si è tenuto il tuo primo ricevimento di matrimonio?
* Qual è il secondo nome di tuo padre?
* Qual è il tuo piatto preferito?
* Qual è il nome e il cognome della nonna materna?
* Qual è il secondo nome di tua madre?
* Qual è l'anno e il mese di nascita di tuo/a fratello/sorella maggiore? Ad esempio, novembre 1985
* Qual è il secondo nome di tuo/a fratello/sorella maggiore?
* Qual è il nome e il cognome del nonno paterno?
* Qual è il secondo nome di tuo/a fratello/sorella minore?
* Quale scuola hai frequentato in prima media?
* Qual è il nome e il cognome del tuo/a migliore amico/a di infanzia?
* Qual è il nome e il cognome del primo/a fidanzato/a?
* Qual è il nome del tuo insegnante preferito delle scuole elementari?
* Qual è la marca e il modello della tua prima auto o moto?
* Qual è il nome della prima scuola che hai frequentato?
* Qual è il nome dell'ospedale in cui sei nato?
* Qual è il nome della via della tua prima casa di infanzia?
* Qual è il nome del tuo supereroe preferito dell'infanzia?
* Qual è il nome del tuo animale di peluche preferito?
* Qual è il nome del primo animale domestico?
* Qual era il tuo soprannome da bambino?
* Qual è il tuo sport preferito alle scuole superiori?
* Qual è stato il tuo primo lavoro?
* Quali erano le ultime quattro cifre del tuo numero di telefono quando eri bambino/a?
* Cosa volevi fare da grande quando eri piccolo?
* Qual è la persona più famosa che hai mai incontrato?

### <a name="custom-security-questions"></a>Domande di sicurezza personalizzate

Le domande di sicurezza personalizzate non sono localizzate per le diverse impostazioni locali. Tutte le domande personalizzate vengono visualizzate nella lingua in cui vengono immesse nell'interfaccia utente di amministrazione, anche se le impostazioni locali del browser dell'utente sono diverse. Se si desidera disporre domande localizzate, è consigliabile usare le domande predefinite.

La lunghezza massima di una domanda di sicurezza personalizzata è di 200 caratteri.

### <a name="security-question-requirements"></a>Requisiti della domanda di sicurezza

* Il limite minimo di caratteri della risposta è 3 caratteri.
* Il limite massimo di caratteri della risposta è 40 caratteri.
* Gli utenti non possono rispondere alla stessa domanda due volte.
* Gli utenti non possono dare la stessa risposta a due diverse domande.
* È possibile usare qualsiasi set di caratteri per definire domande e risposte, compresi i caratteri Unicode.
* Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione.

## <a name="registration"></a>Registrazione

### <a name="require-users-to-register-when-they-sign-in"></a>Richiedere agli utenti di registrarsi all'accesso

Per abilitare questa opzione è necessario che un utente abilitato alla reimpostazione della password completi la registrazione per la reimpostazione della password se effettua l'accesso ad applicazioni tramite Azure AD. Sono inclusi i componenti seguenti:

* Office 365
* Portale di Azure
* Pannello di accesso
* Applicazioni federate
* Applicazioni personalizzate tramite Azure AD

Quando la richiesta di registrazione è disabilitata, gli utenti possono comunque registrare manualmente le informazioni di contatto. È possibile visitare [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) oppure selezionare il collegamento **Registrazione per reimpostazione password** nella scheda **Profilo** nel riquadro di accesso.

> [!NOTE]
> Gli utenti possono chiudere il portale per la registrazione della reimpostazione della password selezionando **Annulla** o chiudendo la finestra. Ogni volta che gli utenti eseguono l'accesso verrà tuttavia visualizzata una richiesta di registrazione finché non viene completato il processo di registrazione.
>
> Questo processo non interromperà la connessione degli utenti che hanno già eseguito l'accesso.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Impostare il numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione

Questa opzione determina il periodo di tempo tra l'impostazione e la conferma delle informazioni di autenticazione ed è disponibile solo se si abilita l'opzione per **Richiedere agli utenti di registrarsi all'accesso**.

I valori validi vanno da 0 a 730 giorni, laddove "0" implica che agli utenti non viene mai richiesto di riconfermare le informazioni di autenticazione.

## <a name="notifications"></a>Notifiche

### <a name="notify-users-on-password-resets"></a>Inviare notifiche agli utenti al momento della reimpostazione della password

Se questa opzione è impostata su **Sì**, l'utente che reimposta la password riceve un messaggio di posta elettronica che segnala che la password è stata modificata. Questo messaggio viene inviato tramite il portale di reimpostazione della password self-service agli indirizzi di posta elettronica primario e alternativo registrati in Azure AD. La notifica per questo evento di reimpostazione non viene inviata ad altre persone.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

Se questa opzione è impostata su **Sì**, *tutti gli amministratori* ricevono un messaggio di posta elettronica al proprio indirizzo di posta elettronica primario sul file in Azure AD. Questo messaggio notifica la modifica della password tramite il servizio di reimpostazione della password self-service da parte di un altro amministratore.

Esempio: nell'ambiente sono presenti quattro amministratori. L'amministratore A reimposta la password tramite il servizio di reimpostazione della password self-service. Gli amministratori B, C e D ricevono un messaggio di posta elettronica che li avvisa della reimpostazione della password.

## <a name="on-premises-integration"></a>Integrazione locale

Se Azure AD Connect è stato installato, configurato e abilitato, saranno disponibili le opzioni aggiuntive seguenti per le integrazioni locali. Se queste opzioni sono disattivate, il writeback non è stato configurato correttamente. Per altre informazioni, vedere [Configurazione del writeback delle password](active-directory-passwords-writeback.md#configure-password-writeback).

![Writeback][Writeback]

Questa pagina fornisce uno stato rapido del client di writeback locale. In base alla configurazione corrente viene visualizzato uno dei seguenti messaggi:

* Il client di writeback locale è attivo e in esecuzione.
* Azure AD è online e connesso al client di writeback locale. Tuttavia sembra che la versione installata di Azure AD Connect non sia aggiornata. Prendere in considerazione l'[aggiornamento di Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) per assicurarsi di disporre delle funzionalità di connettività più recenti e di importanti correzioni di bug.
* Purtroppo non è possibile verificare lo stato del client di writeback locale perché la versione installata di Azure AD Connect non è aggiornata. [Aggiornare Azure AD Connect](./connect/active-directory-aadconnect-upgrade-previous-version.md) per essere in grado di controllare lo stato della connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. [Risoluzione dei problemi di Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) per ripristinare la connessione.
* Purtroppo non è possibile eseguire la connessione al client di writeback locale perché il writeback delle password non è stato configurato correttamente. [Configurare il writeback delle password](active-directory-passwords-writeback.md#configure-password-writeback) per ripristinare la connessione.
* Purtroppo al momento non è possibile connettersi al client di writeback locale. Ciò può essere dovuto a errori temporanei nel sistema. Se il problema persiste, vedere [Risoluzione dei problemi di Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) per ripristinare la connessione.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Writeback delle password nella directory locale

Questo controllo determina se il writeback delle password è abilitato per la directory corrente. Se il writeback è attivato, indica lo stato del servizio writeback locale. Ciò risulta utile se si intende disabilitare temporaneamente il writeback delle password senza dover riconfigurare Azure AD Connect.

* Se l'opzione è impostata su **Sì**, il servizio writeback viene abilitato e gli utenti federati e con sincronizzazione degli hash delle password possono reimpostare le password.
* Se l'opzione è impostata su **No**, il servizio writeback è disabilitato e gli utenti federati e con sincronizzazione degli hash delle password non possono reimpostare le password.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Allow users to unlock accounts without resetting their password

Questo controllo stabilisce se gli utenti che visitano il portale per la reimpostazione della password devono avere la possibilità di sbloccare il proprio account di Active Directory locale senza reimpostare la password. Per impostazione predefinita, Azure AD sblocca gli account quando esegue la reimpostazione di una password. Usare questa impostazione per distinguere queste due operazioni. 

* Se impostata su **Sì**, agli utenti viene offerta la possibilità di scegliere se reimpostare la password e sbloccare l'account o se sbloccare l'account senza reimpostare la password.
* Se impostata su **No**, gli utenti potranno eseguire le operazioni di reimpostazione della password e di sblocco dell'account solo in abbinamento.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Come funziona la reimpostazione della password per gli utenti B2B?
La modifica e la reimpostazione della password sono completamente supportate in tutte le configurazioni B2B. La reimpostazione della password di utenti B2B è supportata nei tre casi seguenti:

   * **Utenti di un'organizzazione partner con un tenant di Azure AD esistente**: se l'organizzazione partner ha un tenant di Azure AD esistente, verranno *rispettati i criteri di reimpostazione della password abilitati in tale tenant*. Per garantire il corretto funzionamento della reimpostazione della password, l'organizzazione partner deve assicurarsi che sia abilitata la reimpostazione delle password self-service di Azure AD. Non sono previsti costi aggiuntivi per i clienti di Office 365. Per abilitare questa funzionalità, seguire i passaggi descritti in [Guida introduttiva: Reimpostazione password self-service di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
   * **Utenti che hanno usato l'iscrizione self-service**: se l'organizzazione partner ha usato la funzionalità di [iscrizione self-service](active-directory-self-service-signup.md) per accedere a un tenant, gli utenti possono eseguire la reimpostazione con l'indirizzo di posta elettronica registrato.
   * **Utenti B2B**: tutti i nuovi utenti B2B creati usando le nuove [funzionalità B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) potranno anche reimpostare le password con l'indirizzo di posta elettronica registrato durante il processo di invito.

Per testare questo scenario, passare a http://passwordreset.microsoftonline.com con uno di questi utenti partner. Se l'utente ha un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione, la reimpostazione della password funziona come previsto.

> [!NOTE]
> Gli account Microsoft a cui è stato concesso l'accesso guest al tenant di Azure AD, ad esempio Hotmail.com, Outlook.com o altri indirizzi di posta elettronica personali, non saranno in grado di utilizzare la funzionalità di reimpostazione delle password self-service di Azure AD. Tali account devono reimpostare le password usando le informazioni riportate nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD:

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md)
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md)
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Metodi di autenticazione di Azure AD disponibili e quantità necessaria"
[Writeback]: ./media/active-directory-passwords-how-it-works/troubleshoot-writeback-running.png "Informazioni sulla risoluzione e la configurazione del writeback delle password di integrazione locale"
