---
title: Funzionamento Reimpostazione password self-service di Azure AD | Microsoft Docs
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
ms.openlocfilehash: fd9515120049dd3837a43c95de8a9b6822719e19
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Approfondimenti sulla reimpostazione della password self-service in Azure AD

Come funziona la reimpostazione della password self-service? Cosa comporta questa opzione per l'interfaccia? Continuare la lettura per ottenere altre informazioni sulla reimpostazione della password self-service di Azure AD.

## <a name="how-does-the-password-reset-portal-work"></a>Funzionamento del portale di reimpostazione della password

Quando un utente accede al portale di reimpostazione della password, si avvia un flusso di lavoro per determinare:

   * come localizzare la pagina
   * la validità dell'account
   * l'organizzazione a cui l'utente appartiene
   * la modalità di gestione della password dell'utente
   * se l'utente ha una licenza per usare la funzionalità


Leggere i passaggi seguenti per informazioni sulla logica alla base della pagina di reimpostazione della password.

1. L'utente fa clic sul collegamento Problemi di accesso all'account? o accede direttamente a [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
2. In base alle impostazioni locali del browser viene eseguito il rendering dell'esperienza nel linguaggio appropriato. L'esperienza di reimpostazione della password viene localizzata nelle stesse lingue supportate da Office 365.
3. L'utente immette un ID utente e un captcha.
4. Azure AD verifica se l'utente può usare questa funzionalità tramite le operazioni seguenti:
   * Verifica che questa funzionalità sia abilitata per l'utente e che all'utente sia assegnata una licenza Azure AD.
     * Se la funzionalità non è abilitata o se non dispone della licenza, per reimpostare la password l'utente deve contattare il proprio amministratore.
   * Verifica che per l'account dell'utente siano stati definiti i dati di test corretti, in conformità con i criteri dell'amministratore.
     * Se i criteri prevedono un solo test, viene verificato se per l'utente sono definiti i dati appropriati per almeno uno dei test abilitati dai criteri dell'amministratore.
       * Se l'utente non è configurato, verrà invitato a contattare l'amministratore per reimpostare la password.
     * Se i criteri prevedono due test, viene verificato se per l'utente sono definiti i dati appropriati per almeno due dei test abilitati dai criteri dell'amministratore.
       * Se l'utente non è configurato, verrà invitato a contattare l'amministratore per reimpostare la password.
   * Verifica se la gestione della password dell'utente viene eseguita in locale, con federazione o con sincronizzazione degli hash delle password.
     * Se il writeback è stato distribuito e la password dell'utente è gestita in locale, l'utente può continuare con il processo di autenticazione e di reimpostazione della password.
     * Se il writeback non è stato distribuito e la password dell'utente viene gestita in locale, all'utente viene richiesto di contattare l'amministratore per reimpostare la password.
5. Se viene stabilito che l'utente è in grado di reimpostare la password, viene avviata la procedura di reimpostazione tramite procedura guidata.

## <a name="authentication-methods"></a>Metodi di autenticazione

Se è abilitata la reimpostazione della password self-service, SSPR, è necessario selezionare almeno una delle opzioni seguenti per i metodi di autenticazione. Queste opzioni sono a volte denominate "attività di controllo". Si consiglia di scegliere almeno due metodi di autenticazione in modo che gli utenti dispongano di una maggiore flessibilità.

* Email
* Cellulare
* Telefono ufficio
* Domande di sicurezza

![Autenticazione][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Campi usati nella directory per i dati di autenticazione

* Telefono ufficio corrisponde al telefono dell'ufficio
    * Gli utenti non sono in grado di impostare questo campo in modo autonomo, deve essere definito da un amministratore
* Cellulare corrisponde al telefono per l'autenticazione, non visibile al pubblico, o al telefono cellulare, visibile a tutti
    * Il servizio cerca innanzitutto il Telefono per l'autenticazione, quindi esegue il fallback per il Cellulare, se non presente
* Indirizzo e-mail alternativo corrisponde all'indirizzo di posta elettronica per l'autenticazione, non visibile a tutti, o all'indirizzo di posta elettronica alternativo
    * Il servizio cerca innanzitutto l'indirizzo di posta elettronica per l'autenticazione, quindi esegue il failback per l'indirizzo di posta elettronica alternativo

Per impostazione predefinita, solo gli attributi cloud Telefono ufficio e Cellulare vengono sincronizzati con la directory cloud dalla directory locale per i dati di autenticazione.

Gli utenti possono reimpostare la password solo se sono presenti dati nei metodi di autenticazione abilitati e richiesti dall'amministratore.

Se gli utenti desiderano che il proprio numero di cellulare non sia visibile nella directory, desiderano usarlo per la reimpostazione della password, gli amministratori non devono inserirlo nella directory e l'utente deve compilare l'attributo **Telefono per autenticazione** tramite il [portale di registrazione della reimpostazione della password](http://aka.ms/ssprsetup). Gli amministratori possono visualizzare queste informazioni nel profilo dell'utente, ma non vengono pubblicate altrove.

### <a name="number-of-authentication-methods-required"></a>Numero di metodi di autenticazione necessari

Questa opzione determina il numero minimo di metodi di autenticazione o attività di controllo disponibili che un utente deve usare per reimpostare o sbloccare la password e può essere impostata su 1 o 2.

Gli utenti possono scegliere di indicare più metodi di autenticazione, se sono abilitati dall'amministratore.

Se un utente non ha registrato il numero minimo di metodi richiesti, viene visualizzata una pagina di errore che invita a richiedere la reimpostazione della password a un amministratore.

#### <a name="changing-authentication-methods"></a>Modifica dei metodi di autenticazione

Cosa succede se si inizia con un criterio che ha solo un metodo di autenticazione registrato necessario per la reimpostazione o lo sblocco e si passa a due?

| Numero di metodi registrati | Numero di metodi necessari | Risultato |
| :---: | :---: | :---: |
| 1 o più | 1 | **Possibilità** di reimpostare o sbloccare |
| 1 | 2 | **Impossibilità** di reimpostare o sbloccare |
| 2 o più | 2 | **Possibilità** di reimpostare o sbloccare |

Se si modificano i tipi di metodi di autenticazione che un utente può usare, si potrebbe impedire inavvertitamente agli utenti l'uso della reimpostazione password self-service se questi non dispongono della quantità minima di dati.

Esempio: 
1. Criteri originali configurati con 2 metodi di autenticazione necessari usando solo il telefono dell'ufficio e domande di sicurezza. 
2. L'amministratore modifica i criteri in modo da non usare più domande di sicurezza, ma da consentire l'uso del telefono cellulare e di una posta elettronica alternativa.
3. Gli utenti che non inseriscono il numero di telefono cellulare e l'indirizzo di posta elettronica alternativo nei rispettivi campi non possono reimpostare le password.

### <a name="how-secure-are-my-security-questions"></a>Sicurezza delle domande di sicurezza

Se si usano domande di sicurezza, è consigliabile usarle congiuntamente a un altro metodo. Queste possono essere meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande di un altro utente.

> [!NOTE] 
> Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. All'amministratore non è consentito leggere o modificare le domande e le risposte di un utente.
>

### <a name="security-question-localization"></a>Localizzazione della domanda di sicurezza

Tutte le domande predefinite che seguono sono localizzate nel set completo delle lingue di Office 365 in base alle impostazioni locali del browser dell'utente.

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

Le domande di sicurezza personalizzate non sono localizzate per le diverse impostazioni locali. Tutte le domande personalizzate vengono visualizzate nella lingua in cui vengono immesse nell'interfaccia utente di amministrazione, anche se le impostazioni locali del browser dell'utente sono diverse. Se si desidera avere domande localizzate, usare le domande predefinite.

La lunghezza massima di una domanda di sicurezza personalizzata è di 200 caratteri.

### <a name="security-question-requirements"></a>Requisiti della domanda di sicurezza

* Il limite minimo di caratteri della risposta è 3 caratteri
* Il limite massimo di caratteri della risposta è 40 caratteri
* Gli utenti potrebbero non rispondere alla stessa domanda due volte
* Gli utenti potrebbero non dare la stessa risposta a due diverse domande
* Qualsiasi set di caratteri può essere usato per definire domande e risposte, compresi i caratteri Unicode
* Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione

## <a name="registration"></a>Registrazione

### <a name="require-users-to-register-when-signing-in"></a>Richiedere agli utenti di registrarsi all'accesso

Per abilitare questa opzione è necessario che un utente abilitato alla reimpostazione della password completi la registrazione della reimpostazione della password se si effettua l'accesso ad applicazioni che usano Azure AD per l'accesso come quelle che seguono:

* Office 365
* Portale di Azure
* Pannello di accesso
* Applicazioni federate
* Applicazioni personalizzate che usano Azure AD

Se è disabilitata, gli utenti possono comunque registrare manualmente le informazioni di contatto visitando il sito [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) o facendo clic sul collegamento della **registrazione per la reimpostazione della password** nella scheda del profilo nel pannello di accesso.

> [!NOTE]
> Gli utenti possono ignorare il portale di registrazione per la reimpostazione password facendo clic su Annulla o chiudendo la finestra, ma visualizzeranno la richiesta ogni volta che effettuano l'accesso fino al completamento di registrazione.
>
> Questo processo non interromperà la connessione degli utenti che si sono già registrati.

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione

Questa opzione determina il periodo di tempo tra l'impostazione e la conferma delle informazioni di autenticazione ed è disponibile solo se si abilita l'opzione per **richiedere agli utenti di registrarsi all'accesso**.

I valori validi vanno da 0 a 730 giorni, laddove 0 implica che agli utenti non viene richiesto di riconfermare le informazioni di autenticazione

## <a name="notifications"></a>Notifiche

### <a name="notify-users-on-password-resets"></a>Inviare notifiche agli utenti al momento della reimpostazione della password

Se questa opzione è impostata su Sì, l'utente che reimposta la password riceve un messaggio di posta elettronica che segnala che la password è stata modificata tramite il portale di reimpostazione della password self-sevice per gli indirizzi di posta elettronica primario e secondario sul file in Azure AD. La notifica per questo evento di reimpostazione non viene inviata ad altre persone.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Inviare una notifica a tutti gli amministratori quando altri amministratori reimpostano le proprie password

Se questa opzione è impostata su Sì, **tutti gli amministratori** ricevono un messaggio di posta elettronica al proprio indirizzo di posta elettronica primario sul file in Azure AD che notifica la modifica della password tramite il servizio di reimpostazione della password self-service da parte di un altro amministratore.

Esempio: nell'ambiente sono presenti quattro amministratori. L'amministratore "A" reimposta la password tramite il servizio di reimpostazione della password self-service. Gli amministratori B, C e D ricevono un messaggio di posta elettronica che li avvisa di quanto sta accadendo.

## <a name="on-premises-integration"></a>Integrazione locale

Se Azure AD Connect è stato installato, configurato e abilitato, saranno disponibili le opzioni aggiuntive seguenti per le integrazioni locali. Se queste opzioni sono disabilitate, il writeback non è stato configurato correttamente, vedere [Configurazione del writeback delle password](active-directory-passwords-writeback.md#configuring-password-writeback) per altre informazioni.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Writeback delle password nella directory locale

Controlla se l'opzione Writeback password è abilitata per questa directory e, in questo caso, indica lo stato del servizio di writeback locale. Questa opzione è utile se si intende disabilitare temporaneamente il writeback delle password senza riconfigurare Azure AD Connect.

* Se l'opzione è impostata su Sì, il servizio writeback viene abilitato e gli utenti federati e con sincronizzazione degli hash delle password possono reimpostare le password.
* Se l'opzione è impostata su No, il servizio writeback è disabilitato e gli utenti federati e con sincronizzazione degli hash delle password non possono reimpostare le password.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Allow users to unlock accounts without resetting their password

Stabilisce se gli utenti che visitano il portale di reimpostazione della password devono avere la possibilità di sbloccare il proprio account di Active Directory locale senza reimpostare la password. Per impostazione predefinita, Azure AD sblocca gli account quando si esegue una reimpostazione della password. Questa impostazione consente di separare le due operazioni. 

* Se impostata su "Sì", agli utenti viene offerta la possibilità di scegliere se reimpostare la password e sbloccare l'account o se sbloccare l'account senza reimpostare la password.
* Se impostata su "No", gli utenti potranno eseguire le operazioni di reimpostazione della password e di sblocco dell'account solo in abbinamento.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Come funziona la reimpostazione della password per gli utenti B2B?
La modifica e la reimpostazione della password sono completamente supportate con tutte le configurazioni B2B. Per la reimpostazione della password di utenti B2B sono supportati i tre casi seguenti.

1. **Utenti di un'organizzazione partner con un tenant di Azure AD esistente**: se l'organizzazione partner ha un tenant di Azure AD esistente, verranno **rispettati i criteri di reimpostazione della password abilitati in tale tenant**. Affinché la reimpostazione della password funzioni, l'organizzazione partner deve semplicemente assicurarsi che sia abilitata la reimpostazione della password in modalità self-service di AD Azure, senza alcun costo aggiuntivo per i clienti di Office 365. Per l'abilitazione, seguire i passaggi nella guida [Introduzione alla gestione delle password](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords).
2. **Utenti che hanno usato l'[iscrizione self-service](active-directory-self-service-signup.md)**: se l'organizzazione partner ha usato la funzionalità di [iscrizione self-service](active-directory-self-service-signup.md) per accedere a un tenant, gli utenti possono eseguire la reimpostazione con l'indirizzo di posta elettronica registrato.
3. **Utenti B2B**: tutti i nuovi utenti B2B creati usando le nuove [funzionalità B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) potranno anche reimpostare le password con l'indirizzo di posta elettronica registrato durante il processo di invito.

Per testare questo scenario, passare a http://passwordreset.microsoftonline.com con uno di questi utenti partner. Se l'utente ha un indirizzo di posta elettronica alternativo o un indirizzo di posta elettronica per l'autenticazione, la reimpostazione della password funziona come previsto.

> [!NOTE]
> Gli account Microsoft che hanno ottenuto l'accesso guest al tenant di Azure AD, ad esempio quelli di Hotmail.com, Outlook.com o altri indirizzi di posta elettronica personali, non sono in grado di usare la reimpostazione password self-service di Azure AD e dovranno reimpostare la password usando le informazioni contenute nell'articolo [Quando non riesci ad accedere al tuo account Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
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