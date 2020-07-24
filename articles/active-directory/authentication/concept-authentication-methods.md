---
title: Metodi e funzionalità di autenticazione - Azure Active Directory
description: Informazioni sui diversi metodi e le diverse funzionalità di autenticazione disponibili in Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 4c224f40996625ead4592a3bf50e02f0877c76db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051316"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quali metodi di autenticazione e verifica sono disponibili in Azure Active Directory?

Nell'ambito dell'esperienza di accesso degli account in Azure Active Directory (Azure AD) è possibile eseguire l'autenticazione di un utente in modi diversi. Il modo più comune in cui un utente inserisce le credenziali è tradizionalmente l'uso di un nome utente e di una password. Con le moderne funzionalità di autenticazione e sicurezza in Azure AD, la comune password può essere integrata o sostituita con metodi di autenticazione aggiuntivi.

Un utente in Azure AD può scegliere di eseguire l'autenticazione usando uno dei metodi di autenticazione seguenti:

* Nome utente e password tradizionali
* Accesso senza password con l'app Microsoft Authenticator
* Token hardware OATH o chiave di sicurezza FIDO2
* Accesso senza password basato su SMS

Molti account di Azure AD sono abilitati per la reimpostazione della password self-service e Azure Multi-Factor Authentication. Queste funzionalità includono metodi di verifica aggiuntivi quali una telefonata o domande di sicurezza. Si consiglia di richiedere agli utenti di registrare più di un metodo di verifica. Quando un metodo non è disponibile, un utente può scegliere di eseguire l'autenticazione con un altro metodo.

La tabella seguente delinea i metodi disponibili per l'autenticazione primaria o secondaria:

| Metodo | Autenticazione primaria | Autenticazione secondaria |
| --- | --- | --- |
| [Password](#password) | Sì | |
| [App Microsoft Authenticator](#microsoft-authenticator-app) | Sì (anteprima) | Autenticazione a più fattori e SSPR |
| [Chiavi di sicurezza FIDO2 (anteprima)](#fido2-security-keys) | Sì | Solo MFA |
| [Token software OATH](#oath-software-tokens) | No | MFA |
| [Token hardware OATH (anteprima)](#oath-hardware-tokens-preview) | Sì | MFA |
| [SMS](#phone-options) | Sì (anteprima) | Autenticazione a più fattori e SSPR |
| [Chiamata vocale](#phone-options) | No | Autenticazione a più fattori e SSPR |
| [Domande di sicurezza](#security-questions) | No | Solo reimpostazione della password self-service |
| [Indirizzo di posta elettronica](#email-address) | No | Solo reimpostazione della password self-service |
| [Password dell'app](#app-passwords) | No | Autenticazione a più fattori solo in alcuni casi |

Questo articolo illustra i diversi metodi di autenticazione e verifica disponibili in Azure AD ed eventuali limitazioni o restrizioni specifiche.

![Metodi di autenticazione in uso nella schermata di accesso](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Password

La password di Azure AD è spesso uno dei principali metodi di autenticazione. Non è possibile disabilitare il metodo di autenticazione con password.

Anche se si usa un metodo di autenticazione come ad esempio l'[accesso basato su SMS](howto-authentication-sms-signin.md) quando l'utente non usa la propria password per accedere, questa rimane disponibile come metodo di autenticazione.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

L'app Authenticator offre un livello di sicurezza aggiuntivo a un account aziendale o dell'istituto di istruzione Azure AD o all'account Microsoft ed è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Con l'app Microsoft Authenticator, gli utenti possono eseguire l'autenticazione senza password durante l'accesso o come opzione di verifica aggiuntiva durante la reimpostazione della password self-service o gli eventi di Azure Multi-Factor Authentication.

Gli utenti possono ricevere una notifica tramite l'app per dispositivi mobili da approvare o rifiutare, oppure possono usare l'app Authenticator per generare un codice di verifica OATH che può essere inserito in un'interfaccia di accesso. Se si abilitano sia la notifica che il codice di verifica, gli utenti che registrano l'app Authenticator possono usare uno dei due metodi per verificare la propria identità.

Per usare l'app Authenticator in un prompt di accesso invece di una combinazione di nome utente e password, vedere [Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Gli utenti non possono registrare l'app per dispositivi mobili quando abilitano la reimpostazione della password self-service. Possono invece registrarla all'indirizzo [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o come parte della registrazione delle informazioni di sicurezza combinate alla pagina [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

### <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

L'app Authenticator consente di impedire l'accesso non autorizzato agli account e di arrestare le transazioni illecite eseguendo il push di una notifica allo smartphone o al tablet dell'utente. Gli utenti visualizzano la notifica e, se legittima, selezionano **Verifica**. Altrimenti possono selezionare **Nega**.

![Screenshot di un esempio di prompt del Web browser per la notifica dell'app Authenticator per completare il processo di accesso](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se il personale dell'organizzazione lavora o viaggia verso la Cina, il metodo di *notifica tramite l'app per dispositivi mobili* nei dispositivi Android non funziona in quel paese o in quell'area geografica. Per questi utenti è necessario rendere disponibili metodi di autenticazione alternativi.

### <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

L'app Authenticator può essere usata come token software per generare un codice di verifica OATH. Dopo aver inserito il nome utente e la password, si immette il codice inviato dall'app Authenticator nell'interfaccia di accesso. Il codice di verifica offre una seconda forma di autenticazione.

Gli utenti possono avere una combinazione composta da fino a cinque token hardware OATH o applicazioni di autenticazione, quali l'app Microsoft Authenticator, configurate per l'uso in qualsiasi momento.

> [!WARNING]
> Per garantire il livello di sicurezza più elevato per la reimpostazione della password self-service quando viene richiesto un solo metodo di ripristino, il codice di verifica rappresenta l'unica opzione disponibile per gli utenti.
>
> Quando vengono richiesti due metodi, gli utenti possono eseguire la reimpostazione usando la notifica o il codice di verifica, oltre a un altro metodo abilitato.

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

FIDO (Fast Identity Online) Alliance promuove gli standard di autenticazione aperti e limita l'uso delle password come forma di autenticazione. FIDO2 è lo standard più recente che incorpora lo standard di autenticazione Web (WebAuthn).

Per usare le chiavi di sicurezza di FIDO2 in un prompt di accesso anziché una combinazione di nome utente e password, vedere [Abilitare l'accesso con chiave di sicurezza FIDO2 senza password (anteprima)](howto-authentication-passwordless-security-key.md).

Gli utenti possono registrarsi e quindi selezionare una chiave di sicurezza FIDO2 nell'interfaccia di accesso come mezzo di autenticazione principale. Le chiavi di sicurezza di FIDO2 sono in genere dispositivi USB, ma possono anche usare il Bluetooth o l'NFC. Grazie a un dispositivo hardware che gestisce l'autenticazione, la sicurezza di un account viene aumentata poiché non vi è una password che può essere esposta o individuata.

Le chiavi di sicurezza FIDO2 in Azure AD sono attualmente in anteprima. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-tokens"></a>Token OATH

OATH TOTP (Time-Based One-Time Passwords) è uno standard aperto che specifica come vengono generati i codici per password monouso (OTP). OATH TOTP può essere implementato usando un software o un hardware per generare i codici. Azure AD non supporta OATH HOTP, uno standard di generazione del codice diverso.

### <a name="oath-software-tokens"></a>Token software OATH

I token software OATH sono in genere applicazioni quali l'app Microsoft Authenticator e altre app di autenticazione. Azure AD genera la chiave privata, o il seme, che rappresenta l'input nell'app ed è usato per generare ciascun OTP.

L'app Authenticator genera automaticamente i codici quando è configurata per generare notifiche push, in modo che un utente abbia un backup anche se il dispositivo non è connesso. Possono essere usate anche applicazioni di terze parti che usano OATH TOTP per generare i codici.

Alcuni token hardware OATH TOTP sono programmabili, ovvero non contengono una chiave privata o un seme pre-programmato. Questi token hardware programmabili possono essere configurati usando la chiave privata o il seme ottenuto dal flusso di configurazione del token software. I clienti possono acquistare i token dal fornitore di propria scelta e usare la chiave privata o il seme nel processo di configurazione del fornitore.

### <a name="oath-hardware-tokens-preview"></a>Token hardware OATH (anteprima)

Azure AD supporta l'uso di token SHA-1 OATH-TOTP che aggiornano i codici ogni 30 o 60 secondi. I clienti possono acquistare questi token dal fornitore preferito.

I token hardware OATH TOTP sono in genere dotati di una chiave privata o di un seme pre-programmato nel token. Queste chiavi devono essere inserite in Azure AD come descritto nella procedura seguente. Le chiavi private sono limitate a 128 caratteri e ciò potrebbe non essere compatibile con tutti i token. La chiave privata può contenere solo i caratteri *a-z* o *A-Z* e i numeri *1-7* e deve essere codificata in *Base32*.

I token hardware OATH TOTP programmabili di cui è possibile generare un nuovo seme possono anche essere configurati con Azure AD nel flusso di configurazione del token software.

I token hardware OATH sono supportati come parte di un'anteprima pubblica. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Pannello di caricamento dei token OATH nei token OATH di MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Dopo aver acquisito i token, è necessario caricarli in un formato di file con valori delimitati da virgole (CSV) includendo l'UPN, il numero di serie, la chiave privata, l'intervallo di tempo, il produttore e il modello come illustrato nell'esempio seguente:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Assicurarsi di includere la riga di intestazione nel file CSV.

Dopo aver formattato correttamente il file CSV, un amministratore può accedere al portale di Azure e passare ad **Azure Active Directory > Sicurezza > MFA > Token OATH** e caricare il file CSV risultante.

A seconda delle dimensioni del file CSV, potrebbero essere richiesti alcuni minuti per l'elaborazione. Per ottenere lo stato corrente, selezionare il pulsante **Aggiorna**. Se sono presenti errori nel file, è possibile scaricare un file CSV che elenca gli eventuali errori da risolvere. I nomi dei campi nel file CSV scaricato sono diversi da quelli della versione caricata.

Dopo aver risolto eventuali errori, l'amministratore può quindi attivare ogni chiave selezionando **Attiva** per il token da attivare e immettendo l'OTP visualizzato nel token.

Gli utenti possono avere una combinazione composta da fino a cinque token hardware OATH o applicazioni di autenticazione, quali l'app Microsoft Authenticator, configurate per l'uso in qualsiasi momento.

## <a name="phone-options"></a>Opzioni con il telefono

Per l'autenticazione diretta tramite SMS, è possibile [configurare e abilitare gli utenti per l'autenticazione basata su SMS (anteprima)](howto-authentication-sms-signin.md). L'accesso basato su SMS è ideale per chi lavora a contatto con il pubblico. Con l'accesso basato su SMS, non è necessario che l'utente conosca nome utente e password per accedere alle applicazioni e ai servizi. Può immettere invece il numero di telefono cellulare registrato, ricevere un SMS con un codice di verifica e inserirlo nell'interfaccia di accesso.

Gli utenti possono autenticarsi anche usando un telefono cellulare o un telefono dell'ufficio come forma di autenticazione secondaria durante l'autenticazione a più fattori di Azure o la reimpostazione della password self-service.

Per garantirne il corretto funzionamento, i numeri di telefono devono essere nel formato *+PrefissoInternazionale NumeroTelefonico*, ad esempio *+1 4251234567*.

> [!NOTE]
> È necessario uno spazio tra il prefisso internazionale o della regione e il numero di telefono.
>
> La reimpostazione della password non supporta le estensioni del telefono. Anche nel formato *+1 4251234567X12345*, le estensioni vengono rimosse prima della chiamata.

### <a name="mobile-phone-verification"></a>Verifica tramite telefono cellulare

Per Azure Multi-Factor Authentication o la reimpostazione della password self-service, gli utenti possono scegliere di ricevere un SMS con un codice di verifica da immettere nell'interfaccia di accesso oppure una telefonata in cui si richiedere di immettere il codice PIN definito.

Se gli utenti non desiderano che il numero di telefono cellulare sia visibile nella directory, ma vogliono comunque usarlo per la reimpostazione della password, gli amministratori non devono inserirlo nella directory. Gli utenti devono invece popolare il proprio attributo **Telefono per l'autenticazione** tramite la registrazione delle informazioni di sicurezza combinate all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Gli amministratori possono visualizzare queste informazioni nel profilo dell'utente, ma non vengono pubblicate altrove.

![Screenshot del portale di Azure che mostra i metodi di autenticazione con un numero di telefono inserito](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft non garantisce l'invio coerente di prompt Azure Multi-Factor Authentication via SMS o tramite messaggio vocale allo stesso numero. Nell'interesse degli utenti, Microsoft può aggiungere o rimuovere codici brevi in qualsiasi momento per eseguire modifiche di route e migliorare il recapito degli SMS. Microsoft non supporta i codici brevi in paesi o aree geografiche diversi da Stati Uniti e Canada.

#### <a name="text-message-verification"></a>Verifica tramite SMS

Con la verifica tramite SMS durante la reimpostazione della password self-service o l'uso di Azure Multi-Factor Authentication, viene inviato un SMS al numero di telefono cellulare contenente un codice di verifica. Per completare il processo di accesso, il codice di verifica inviato viene immesso nell'interfaccia di accesso.

#### <a name="phone-call-verification"></a>Verifica tramite telefonata

Con la verifica tramite telefonata durante la reimpostazione della password self-service o l'uso di Azure Multi-Factor Authentication, viene eseguita una chiamata vocale automatica al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di immettere il numero PIN seguito da # sul tastierino.

### <a name="office-phone-verification"></a>Verifica tramite telefono dell'ufficio

L'attributo telefono dell'ufficio viene gestito dall'amministratore di Azure AD e non può essere registrato dall'utente stesso.

Con la verifica tramite telefonata durante la reimpostazione della password self-service o l'uso di Azure Multi-Factor Authentication, viene eseguita una chiamata vocale automatica al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di immettere il numero PIN seguito da # sul tastierino.

### <a name="troubleshooting-phone-options"></a>Risoluzione dei problemi delle opzioni che si servono del telefono

In caso di problemi con l'autenticazione tramite telefono in Azure AD, esaminare la procedura per la risoluzione dei problemi seguente:

* ID del chiamante bloccato su un solo dispositivo.
   * Esaminare i numeri bloccati configurati sul dispositivo.
* Numero di telefono errato o codice internazionale o dell'area errato, oppure confusione tra il numero di telefono personale e il numero di telefono dell'ufficio.
   * Risolvere i problemi relativi all'oggetto utente e ai metodi di autenticazione configurati. Assicurarsi che siano registrati i numeri di telefono corretti.
* PIN immesso errato.
   * Verificare che l'utente abbia usato il PIN corretto registrato per il proprio account.
* Chiamata inoltrata alla segreteria telefonica.
   * Verificare che il telefono dell'utente sia acceso e che il servizio sia disponibile nella propria area oppure usare un altro metodo.
* L'utente è bloccato
   * Chiedere a un amministratore di Azure AD di sbloccare l'utente nel portale di Azure.
* Il metodo con SMS non è stato sottoscritto sul dispositivo.
   * Chiedere all'utente di modificare i metodi o di attivare il metodo con SMS sul dispositivo.
* Servizi di telecomunicazione non funzionanti correttamente, ad esempio input del telefono mancanti, problemi di toni DTMF mancanti, ID del chiamante bloccato oppure SMS bloccati in più dispositivi.
   * Microsoft usa più provider di telecomunicazioni per instradare telefonate e messaggi SMS per l'autenticazione. Se si nota uno dei problemi descritti in precedenza, chiedere all'utente di provare a usare il metodo almeno cinque volte in 5 minuti e recuperare le informazioni dell'utente prima di contattare il supporto tecnico Microsoft.

## <a name="security-questions"></a>Domande di sicurezza

Le domande di sicurezza non vengono usate come metodo di autenticazione durante un evento di accesso. Possono invece essere usate durante il processo di reimpostazione della password self-service per confermare l'identità dell'utente. Gli account amministratore non possono usare le domande di sicurezza come metodo di verifica con la reimpostazione della password self-service.

Quando gli utenti si registrano per la reimpostazione della password self-service, viene richiesto loro di scegliere i metodi di autenticazione da usare. Se decidono di usare le domande di sicurezza, scelgono tra una serie di domande e quindi inseriscono le proprie risposte.

![Screenshot del portale di Azure che mostra i metodi di autenticazione e le opzioni per le domande di sicurezza](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. All'amministratore non è consentito leggere o modificare le domande e le risposte di un utente.

Le domande di sicurezza possono risultare meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande di un altro utente. Se vengono usate le domande di sicurezza con la reimpostazione della password self-service, è consigliabile combinare questo metodo con un altro. A un utente può essere richiesto di usare l'app Microsoft Authenticator oppure l'autenticazione tramite telefono per confermare la propria identità durante il processo di reimpostazione della password self-service e di scegliere le domande di sicurezza solo se non ha il telefono o il dispositivo registrato a portata di mano.

### <a name="predefined-questions"></a>Domande predefinite

Le domande di sicurezza predefinite seguenti sono disponibili per essere usate come metodo di verifica con la reimpostazione della password self-service. Tutte le domande di sicurezza sono tradotte e localizzate nel set completo delle lingue di Office 365 in base alle impostazioni locali del browser dell'utente:

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
* Qual è l'anno e il mese di nascita di tuo/a fratello/sorella maggiore? (ad esempio, novembre 1985)
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

Per una maggiore flessibilità, è possibile definire domande di sicurezza personalizzate. La lunghezza massima di una domanda di sicurezza personalizzata è di 200 caratteri.

Le domande di sicurezza personalizzate non vengono localizzate automaticamente come avviene con le domande di sicurezza predefinite. Tutte le domande personalizzate vengono visualizzate nella lingua in cui vengono immesse nell'interfaccia utente di amministrazione, anche se le impostazioni locali del browser dell'utente sono diverse. Se si desidera disporre domande localizzate, è consigliabile usare le domande predefinite.

### <a name="security-question-requirements"></a>Requisiti della domanda di sicurezza

Sia per le domande di sicurezza predefinite che per quelle personalizzate, si applicano i requisiti e le limitazioni seguenti:

* Il limite minimo di caratteri della risposta è 3 caratteri.
* Il limite massimo di caratteri della risposta è 40 caratteri.
* Gli utenti non possono rispondere alla stessa domanda due volte.
* Gli utenti non possono dare la stessa risposta a due diverse domande.
* È possibile usare qualsiasi set di caratteri per definire domande e risposte, compresi i caratteri Unicode.
* Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione.

## <a name="email-address"></a>Indirizzo di posta elettronica

Non è possibile usare un indirizzo di posta elettronica come metodo di autenticazione diretta. L'indirizzo di posta elettronica è disponibile solo come opzione di verifica per la reimpostazione della password self-service. Quando l'indirizzo di posta elettronica viene selezionato durante la reimpostazione della password self-service, viene inviato un messaggio di posta elettronica all'utente per completare il processo di autenticazione/verifica.

Durante la registrazione per la reimpostazione della password self-service, un utente inserisce l'indirizzo di posta elettronica da usare. È consigliabile usare un account di posta elettronica diverso dal proprio account aziendale per assicurarsi di potervi accedere durante la reimpostazione della password self-service.

## <a name="app-passwords"></a>Password dell'app

Alcune app meno recenti non basate su browser non comprendono le pause o le interruzioni nel processo di autenticazione. Se un utente è abilitato per l'autenticazione a più fattori e tenta di usare una di queste app meno recenti non basate su browser, in genere non riesce a eseguire l'autenticazione. Una password dell'app consente agli utenti di continuare a eseguire correttamente l'autenticazione con le app meno recenti non basate su browser senza interruzioni.

Per impostazione predefinita, gli utenti non possono creare password dell'app. Se si desidera consentire agli utenti di creare password dell'app, selezionare l'opzione **Allow users to create app passwords to sign into non-browser apps** (Consenti agli utenti di creare password dell'app per accedere alle app non basate su browser) in *Impostazioni del servizio* per le proprietà di Azure Multi-Factor Authentication dell'utente.

![Screenshot del portale di Azure che mostra le impostazioni del servizio per l'autenticazione a più fattori per consentire all'utente di creare password dell'app](media/concept-authentication-methods/app-password-authentication-method.png)

Se si applica Azure Multi-Factor Authentication usando i criteri di accesso condizionale e non tramite MFA per utente, è possibile creare le password dell'app. Le applicazioni moderne che usano i criteri di accesso condizionale per controllare l'accesso non hanno bisogno di password dell'app.

Se l'organizzazione è federata per l'accesso Single Sign-On (SSO) con Azure AD e si usa Azure Multi-Factor Authentication, tenere presente le considerazioni seguenti:

* La password dell'app è verificata da Azure AD e di conseguenza ignora la federazione. La federazione viene usata solo quando si configura la password dell'app. Per gli utenti federati (SSO) le password vengono archiviate nell'ID dell'organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync. Gli eventi di disabilitazione o eliminazione dell'account possono richiedere fino a 3 ore per la sincronizzazione, ritardando la disabilitazione o l'eliminazione delle password dell'app in Azure AD.
* Le impostazioni locali di controllo di accesso client non vengono rispettate dalle password dell'app.
* Per le password dell'app non è disponibile alcuna funzionalità di registrazione o controllo dell'autenticazione in locale.
* Alcune architetture avanzate possono richiedere una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa l'autenticazione a più fattori, a seconda della posizione in cui viene eseguita l'autenticazione.
    * Per i client che si autenticano con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione.
    * Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'[esercitazione sulla reimpostazione della password self-service][tutorial-sspr] e [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Per altre informazioni sui concetti di MFA, vedere [Come funziona Azure Multi-Factor Authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md