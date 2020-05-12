---
title: Metodi e funzionalità di autenticazione-Azure Active Directory
description: Informazioni sui diversi metodi e funzionalità di autenticazione disponibili in Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 3947bf0dcad598bf52a742c790a2f99538d6facb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116401"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quali metodi di autenticazione e verifica sono disponibili in Azure Active Directory?

Nell'ambito dell'esperienza di accesso per gli account in Azure Active Directory (Azure AD), è possibile eseguire l'autenticazione di un utente in modi diversi. Un nome utente e una password sono il modo più comune per fornire le credenziali a un utente. Con le funzionalità moderne di autenticazione e sicurezza in Azure AD, la password di base può essere aggiunta o sostituita con metodi di autenticazione aggiuntivi.

Un utente in Azure AD può scegliere di eseguire l'autenticazione usando uno dei metodi di autenticazione seguenti:

* Nome utente e password tradizionali
* Microsoft Authenticator l'accesso senza password dell'app
* Token hardware GIURAto o chiave di sicurezza FIDO2
* Accesso senza password basato su SMS

Molti account in Azure AD sono abilitati per la reimpostazione della password self-service (SSPR) o Azure Multi-Factor Authentication. Queste funzionalità includono metodi di verifica aggiuntivi, ad esempio una telefonata o domande di sicurezza. Si consiglia di richiedere agli utenti di registrare più metodi di verifica. Quando un metodo non è disponibile per un utente, può scegliere di eseguire l'autenticazione con un altro metodo.

La tabella seguente descrive i metodi di autenticazione o di verifica disponibili per i diversi scenari:

| Metodo | Usare al momento dell'accesso | Usare durante la verifica |
| --- | --- | --- |
| [Password](#password) | Sì | Autenticazione a più fattori e SSPR |
| [App Microsoft Authenticator](#microsoft-authenticator-app) | Sì (anteprima) | Autenticazione a più fattori e SSPR |
| [Chiavi di sicurezza FIDO2 (anteprima)](#fido2-security-keys) | Sì | Solo autenticazione a più fattori |
| [Token hardware del GIURAmento (anteprima)](#oath-hardware-tokens) | Sì | SSPR ed autenticazione a più fattori |
| [sms](#phone-options) | Sì (anteprima) | Autenticazione a più fattori e SSPR |
| [Chiamata vocale](#phone-options) | No | Autenticazione a più fattori e SSPR |
| [Domande di sicurezza](#security-questions) | No | Solo SSPR |
| [Indirizzo di posta elettronica](#email-address) | No | Solo SSPR |
| [Password dell'app](#app-passwords) | No | Autenticazione a più fattori solo in alcuni casi |

In questo articolo vengono illustrati i diversi metodi di autenticazione e di verifica disponibili in Azure AD ed eventuali limitazioni o limitazioni specifiche.

![Metodi di autenticazione in uso nella schermata di accesso](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Password

Una password Azure AD è spesso uno dei metodi di autenticazione principali. Non è possibile disabilitare il metodo di autenticazione della password.

Anche se si usa un metodo di autenticazione, ad esempio l' [accesso basato su SMS](howto-authentication-sms-signin.md) quando l'utente non usa la password per firmare, una password rimane come metodo di autenticazione disponibile.

## <a name="microsoft-authenticator-app"></a>App Microsoft Authenticator

L'app Authenticator offre un livello di sicurezza aggiuntivo per l'account aziendale o dell'Istituto di istruzione Azure AD o l'account Microsoft ed è disponibile per [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Con l'app Microsoft Authenticator, gli utenti possono eseguire l'autenticazione senza password durante l'accesso o come opzione di verifica aggiuntiva durante la reimpostazione della password self-service (SSPR) o gli eventi di Azure Multi-Factor Authentication.

Gli utenti possono ricevere una notifica tramite l'app per dispositivi mobili per approvare o negare o usare l'app Authenticator per generare un codice di verifica del GIURAmento che può essere inserito in un'interfaccia di accesso. Se si Abilita sia un codice di notifica che di verifica, gli utenti che registrano l'app Authenticator possono usare uno dei due metodi per verificarne l'identità.

Per usare l'app Authenticator a una richiesta di accesso anziché una combinazione di nome utente e password, vedere [abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Gli utenti non hanno la possibilità di registrare l'app per dispositivi mobili quando abilitano SSPR. Al contrario, gli utenti possono registrare l'app per dispositivi mobili in [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o come parte della registrazione di informazioni di sicurezza combinata all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Notifica tramite app per dispositivi mobili

L'app Authenticator consente di impedire l'accesso non autorizzato agli account e di arrestare le transazioni fraudolente effettuando il push di una notifica sullo smartphone o sul tablet. Gli utenti visualizzano la notifica e, se sono legittimi, selezionare **Verifica**. In caso contrario, è possibile selezionare **Nega**.

![Screenshot della richiesta del browser Web di esempio per la notifica dell'app Authenticator per completare il processo di accesso](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se l'organizzazione dispone di personale che lavora o viaggia in Cina, la notifica tramite il metodo dell' *app mobile* nei dispositivi Android non funziona in quel paese. I metodi di autenticazione alternativi devono essere resi disponibili per tali utenti.

### <a name="verification-code-from-mobile-app"></a>Codice di verifica dall'app per dispositivi mobili

L'app Authenticator può essere usata come token software per generare un codice di verifica del GIURAmento. Dopo l'immissione del nome utente e della password, immettere il codice fornito dall'app Authenticator nell'interfaccia di accesso. Il codice di verifica offre una seconda forma di autenticazione.

Gli utenti possono avere una combinazione di un massimo di cinque token hardware o applicazioni di autenticazione, ad esempio l'app Microsoft Authenticator, configurati per l'uso in qualsiasi momento.

> [!WARNING]
> Per garantire il massimo livello di sicurezza per la reimpostazione della password self-service quando è necessario un solo metodo per la reimpostazione, un codice di verifica è l'unica opzione disponibile per gli utenti.
>
> Quando sono necessari due metodi, gli utenti possono reimpostare utilizzando un codice di notifica o di verifica, oltre a tutti gli altri metodi abilitati.

## <a name="fido2-security-keys"></a>Chiavi di sicurezza FIDO2

L'Alleanza FIDO (Fast IDentity online) aiuta a promuovere standard di autenticazione aperti e a ridurre l'utente delle password come una forma di autenticazione. FIDO2 è lo standard più recente che incorpora lo standard di autenticazione Web (webauthn).

Per usare le chiavi di sicurezza di FIDO2 a una richiesta di accesso anziché una combinazione di nome utente e password, vedere [abilitare l'accesso con chiave di sicurezza FIDO2 senza password (anteprima)](howto-authentication-passwordless-security-key.md).

Gli utenti possono registrarsi e quindi selezionare una chiave di sicurezza FIDO2 nell'interfaccia di accesso come mezzo di autenticazione principale. Queste chiavi di sicurezza di FIDO2 sono in genere dispositivi USB, ma possono anche usare Bluetooth o NFC. Con un dispositivo hardware che gestisce l'autenticazione, la sicurezza di un account viene aumentata poiché non esiste una password che può essere esposta o indovinata.

Le chiavi di sicurezza FIDO2 in Azure AD sono attualmente in anteprima. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-hardware-tokens"></a>Token hardware OATH

OATH è uno standard aperto che specifica come vengono generati i codici per password monouso (OTP, One-Time Password). Azure AD supporta l'uso di token SHA-1 per il GIURAmento-TOTP della varietà di 30 secondi o di 60 secondi. I clienti possono acquistare questi token dal fornitore di loro scelta.

Le chiavi segrete sono limitate a 128 caratteri, che potrebbero non essere compatibili con tutti i token. La chiave privata può contenere solo i caratteri *a-z* , *a-z* e digits *1-7*e deve essere codificata in *Base32*.

I token hardware del GIURAmento nel Azure AD sono attualmente in anteprima. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Caricamento dei token del GIURAmento nella finestra token del GIURAmento dell'autenticazione a più fattori](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Dopo aver acquisito i token, è necessario caricarli in un formato di file con valori delimitati da virgole (CSV), tra cui l'UPN, il numero di serie, la chiave privata, l'intervallo di tempo, il produttore e il modello, come illustrato nell'esempio seguente:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Assicurarsi di includere la riga di intestazione nel file CSV.

Una volta formattato correttamente come file CSV, un amministratore può quindi accedere al portale di Azure, passare a **Azure Active Directory**token di autenticazione a più fattori di  >  **sicurezza**  >  **MFA**  >  **OATH tokens**e caricare il file CSV risultante.

A seconda delle dimensioni del file CSV, potrebbero essere richiesti alcuni minuti per l'elaborazione. Selezionare il pulsante **Aggiorna** per ottenere lo stato corrente. Se sono presenti errori nel file, è possibile scaricare un file CSV in cui sono elencati gli eventuali errori da risolvere. I nomi dei campi nel file CSV scaricato sono diversi dalla versione caricata.

Una volta risolti gli errori, l'amministratore può attivare ogni chiave selezionando **Activate** per il token e immettendo il OTP visualizzato nel token.

Gli utenti possono avere una combinazione di un massimo di cinque token hardware o applicazioni di autenticazione, ad esempio l'app Microsoft Authenticator, configurati per l'uso in qualsiasi momento.

## <a name="phone-options"></a>Opzioni telefono

Per l'autenticazione diretta tramite SMS, è possibile [configurare e abilitare gli utenti per l'autenticazione basata su SMS (anteprima)](howto-authentication-sms-signin.md). L'accesso basato su SMS è ideale per i ruoli di lavoro in primo piano. Con l'accesso basato su SMS, non è necessario che gli utenti conoscano un nome utente e una password per accedere alle applicazioni e ai servizi. L'utente immette invece il numero di telefono cellulare registrato, riceve un SMS con un codice di verifica e lo immette nell'interfaccia di accesso.

Gli utenti possono inoltre verificare se stessi utilizzando un telefono cellulare o un telefono dell'ufficio come forma secondaria di autenticazione utilizzata durante la Multi-Factor Authentication di Azure o la reimpostazione della password self-service (SSPR).

Per il corretto funzionamento, i numeri di telefono devono essere nel formato *+ CountryCode PhoneNumber*, ad esempio *+ 1 4251234567*.

> [!NOTE]
> È necessario uno spazio tra il prefisso internazionale e il numero di telefono.
>
> La reimpostazione della password non supporta le estensioni telefono. Anche nel formato *+ 1 4251234567X12345* , le estensioni vengono rimosse prima che venga effettuata la chiamata.

### <a name="mobile-phone-verification"></a>Verifica del cellulare

Per Multi-Factor Authentication di Azure o SSPR, gli utenti possono scegliere di ricevere un SMS con un codice di verifica da immettere nell'interfaccia di accesso o ricevere una telefonata con una richiesta di immissione del codice pin definito.

Se gli utenti non desiderano che il numero di telefono cellulare sia visibile nella directory, ma si desidera utilizzarlo per la reimpostazione della password, gli amministratori non devono popolare il numero di telefono nella directory. Gli utenti devono invece popolare l'attributo **Phone di autenticazione** tramite la registrazione delle informazioni di sicurezza combinata all'indirizzo [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Gli amministratori possono visualizzare queste informazioni nel profilo dell'utente, ma non vengono pubblicate altrove.

![Screenshot del portale di Azure che mostra i metodi di autenticazione con un numero di telefono popolato](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft non garantisce il recapito di messaggi di richiesta di Multi-Factor Authentication di Azure coerenti con lo stesso numero. Per quanto riguarda gli utenti, Microsoft può aggiungere o rimuovere codici brevi in qualsiasi momento, in quanto vengono apportate modifiche alla route per migliorare la distribuzione di SMS. Microsoft non supporta i codici brevi per i paesi/aree geografiche oltre al Stati Uniti e al Canada.

#### <a name="text-message-verification"></a>Verifica SMS

Con la verifica dei messaggi di testo durante SSPR o Azure Multi-Factor Authentication, viene inviato un SMS al numero di telefono cellulare contenente un codice di verifica. Per completare il processo di accesso, il codice di verifica fornito viene immesso nell'interfaccia di accesso.

#### <a name="phone-call-verification"></a>Verifica telefonata

Con la verifica tramite telefonata durante SSPR o Azure Multi-Factor Authentication, viene effettuata una chiamata vocale automatizzata al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di immettere il numero PIN seguito da # sul tastierino.

### <a name="office-phone-verification"></a>Verifica del telefono dell'ufficio

L'attributo telefono ufficio viene gestito dall'amministratore di Azure AD e non può essere registrato da un utente.

Con la verifica tramite telefonata durante SSPR o Azure Multi-Factor Authentication, viene effettuata una chiamata vocale automatizzata al numero di telefono registrato dall'utente. Per completare il processo di accesso, all'utente viene richiesto di immettere il numero PIN seguito da # sul tastierino.

### <a name="troubleshooting-phone-options"></a>Risoluzione dei problemi delle opzioni telefono

In caso di problemi con l'autenticazione tramite telefono per Azure AD, esaminare i passaggi per la risoluzione dei problemi seguenti:

* ID chiamante bloccato in un singolo dispositivo.
   * Esaminare i numeri bloccati configurati nel dispositivo.
* Numero di telefono errato o codice paese errato oppure confusione tra il numero di telefono personale e il numero di telefono dell'ufficio.
   * Risolvere i problemi relativi all'oggetto utente e ai metodi di autenticazione configurati. Verificare che siano registrati i numeri di telefono corretti.
* PIN errato immesso.
   * Verificare che l'utente abbia usato il PIN corretto come registrato per il proprio account.
* Chiamata trasmessa al messaggio vocale.
   * Verificare che l'utente disponga del telefono acceso e che il servizio sia disponibile nella propria area oppure utilizzare un metodo alternativo.
* L'utente è bloccato
   * Chiedere a un amministratore Azure AD di sbloccare l'utente nella portale di Azure.
* SMS non è sottoscritto sul dispositivo.
   * Chiedere all'utente di modificare i metodi o attivare SMS sul dispositivo.
* Sono stati rilevati provider di telecomunicazioni difettosi, ad esempio non sono stati rilevati input telefono, problemi di toni DTMF mancanti, ID chiamante bloccato su più dispositivi o SMS bloccati tra più dispositivi.
   * Microsoft usa più provider di telecomunicazioni per instradare telefonate e messaggi SMS per l'autenticazione. Se viene visualizzato uno dei problemi descritti in precedenza, chiedere a un utente di usare il metodo almeno cinque volte in 5 minuti e di avere a disposizione le informazioni dell'utente per contattare il supporto tecnico Microsoft.

## <a name="security-questions"></a>Domande di sicurezza

Le domande di sicurezza non vengono usate come metodo di autenticazione durante un evento di accesso. Al contrario, è possibile utilizzare le domande di sicurezza durante il processo di reimpostazione della password self-service (SSPR) per confermare l'utente. Gli account amministratore non possono usare domande di sicurezza come metodo di verifica con SSPR.

Quando gli utenti si registrano per SSPR, viene richiesto di scegliere i metodi di autenticazione da usare. Se scelgono di usare domande di sicurezza, scelgono da un set di domande da richiedere e quindi forniscono risposte personalizzate.

![Screenshot del portale di Azure che mostra i metodi di autenticazione e le opzioni per le domande di sicurezza](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Le domande di sicurezza vengono archiviate privatamente e in modo sicuro in un oggetto utente nella directory e gli utenti possono rispondere a tali domande solo durante la registrazione. Un amministratore non è in grado di leggere o modificare le domande o le risposte di un utente.

Le domande di sicurezza possono risultare meno sicure rispetto ad altri metodi perché alcuni utenti potrebbero conoscere le risposte alle domande di un altro utente. Se si usano domande di sicurezza con SSPR, è consigliabile usarle insieme a un altro metodo. A un utente può essere richiesto di usare l'app Microsoft Authenticator o l'autenticazione tramite telefono per verificarne l'identità durante il processo di SSPR e scegliere le domande di sicurezza solo se non dispongono del telefono o del dispositivo registrato.

### <a name="predefined-questions"></a>Domande predefinite

Le domande di sicurezza predefinite seguenti sono disponibili per l'uso come metodo di verifica con SSPR. Tutte queste domande di sicurezza vengono convertite e localizzate nel set completo di lingue di Office 365 in base alle impostazioni locali del browser dell'utente:

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

Le domande di sicurezza personalizzate non vengono localizzate automaticamente come con le domande di sicurezza predefinite. Tutte le domande personalizzate vengono visualizzate nella stessa lingua in cui vengono immesse nell'interfaccia utente di amministrazione, anche se le impostazioni locali del browser dell'utente sono diverse. Se si desidera disporre domande localizzate, è consigliabile usare le domande predefinite.

### <a name="security-question-requirements"></a>Requisiti della domanda di sicurezza

Per le domande di sicurezza predefinite e personalizzate, si applicano i requisiti e le limitazioni seguenti:

* Il limite minimo di caratteri della risposta è 3 caratteri.
* Il limite massimo di caratteri della risposta è 40 caratteri.
* Gli utenti non possono rispondere alla stessa domanda due volte.
* Gli utenti non possono dare la stessa risposta a due diverse domande.
* È possibile usare qualsiasi set di caratteri per definire domande e risposte, compresi i caratteri Unicode.
* Il numero di domande definite deve essere maggiore o uguale al numero di domande necessarie per la registrazione.

## <a name="email-address"></a>Indirizzo di posta elettronica

Non è possibile usare un indirizzo di posta elettronica come metodo di autenticazione diretta. L'indirizzo di posta elettronica è disponibile solo come opzione di verifica per la reimpostazione della password self-service (SSPR). Quando l'indirizzo di posta elettronica viene selezionato durante SSPR, viene inviato un messaggio di posta elettronica all'utente per completare il processo di autenticazione/verifica.

Durante la registrazione per SSPR, un utente fornisce l'indirizzo di posta elettronica da usare. Si consiglia di usare un account di posta elettronica diverso rispetto al proprio account aziendale per assicurarsi che possano accedervi durante SSPR.

## <a name="app-passwords"></a>Password dell'app

Alcune app meno recenti non basate su browser non comprendono pause o interruzioni nel processo di autenticazione. Se un utente è abilitato per l'autenticazione a più fattori e tenta di usare una di queste app non basate su browser, non è in genere possibile autenticarsi correttamente. Una password dell'app consente agli utenti di continuare a eseguire l'autenticazione con le app precedenti non basate su browser senza interruzioni.

Per impostazione predefinita, gli utenti non possono creare password dell'app. Se è necessario consentire agli utenti di creare password dell'app, selezionare l'impostazione **Consenti agli utenti di creare password dell'app per accedere alle app non basate su browser** in *Impostazioni servizio per le* proprietà multi-factor authentication di Azure dell'utente.

![Screenshot del portale di Azure che mostra le impostazioni del servizio per l'autenticazione a più fattori per consentire all'utente di password dell'app](media/concept-authentication-methods/app-password-authentication-method.png)

Se si applica Azure Multi-Factor Authentication usando i criteri di accesso condizionale e non tramite l'autenticazione a più fattori per utente, non è possibile creare password di app. Le applicazioni moderne che usano i criteri di accesso condizionale per controllare l'accesso non necessitano di password di app.

Se l'organizzazione è federata per Single Sign-On (SSO) con Azure AD e si usa Multi-Factor Authentication di Azure, si applicano le considerazioni seguenti:

* La password dell'app viene verificata da Azure AD, quindi ignora la Federazione. La federazione viene usata solo quando si configura la password dell'app. Per gli utenti federati (SSO) le password vengono archiviate nell'ID dell'organizzazione. Se l'utente lascia l'azienda, tali informazioni devono essere trasmesse nell'ID organizzazione tramite DirSync. La sincronizzazione degli eventi di disabilitazione o eliminazione dell'account può richiedere fino a tre ore, il che ritarda la disabilitazione o l'eliminazione delle password di app in Azure AD.
* Le impostazioni locali di controllo dell'accesso client non vengono rispettate dalle password dell'app.
* Per le password dell'app non è disponibile alcuna funzionalità di controllo o registrazione dell'autenticazione locale.
* Alcuni progetti architetturali avanzati possono richiedere l'uso di una combinazione di nome utente e password dell'organizzazione e password dell'app quando si usa l'autenticazione a più fattori, a seconda di dove si autenticano.
    * Per i client che si autenticano con un'infrastruttura locale, verranno usati un nome utente e una password dell'organizzazione.
    * Per i client che eseguono l'autenticazione con AD Azure, verrà usata la password dell'app.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l'[esercitazione sulla reimpostazione della password self-service][tutorial-sspr] e [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti relativi a SSPR, vedere [come funziona Azure ad la reimpostazione della password self-service][concept-sspr].

Per altre informazioni sui concetti relativi a multi-factor authentication, vedere funzionamento di [Azure multi-factor authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md