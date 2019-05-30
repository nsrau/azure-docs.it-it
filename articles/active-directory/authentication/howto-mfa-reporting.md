---
title: Report di accesso e utilizzo per Azure MFA - Azure Active Directory
description: Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988e8982b6f06fb1210330c5cafdb696892794fe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235523"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication offre diversi report che possono essere usati dall'utente e dall'organizzazione e ai quali è possibile accedere tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Località | Descrizione |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Server MFA > Blocca/Sblocca utenti | Consente di visualizzare la cronologia delle richieste di blocco o sblocco degli utenti. |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |
| Utilizzo di componenti locali | Azure AD > Server MFA > Report attività | Fornisce informazioni sull'utilizzo complessivo di Multi-Factor Authentication tramite l'estensione del server dei criteri di rete, ADFS e il server MFA. |
| Cronologia utenti bypass | Azure AD > Server MFA > Bypass monouso | Fornisce una cronologia delle richieste di bypass di Multi-Factor Authentication per un utente. |
| Stato del server | Azure AD > Server MFA > Stato del server | Consente di visualizzare lo stato dei server di Multi-Factor Authentication associati all'account. |

## <a name="view-mfa-reports"></a>Visualizzare i report MFA

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra, selezionare **Azure Active Directory** > **Server MFA**.
3. Selezionare il report che si vuole visualizzare.

   ![Report Stato server di MFA Server nel portale di Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Report sugli accessi ad Azure AD

Con il **report sulle attività di accesso** nel [portale di Azure](https://portal.azure.com) è possibile ottenere le informazioni necessarie per determinare lo stato e le prestazioni dell'ambiente.

Il report sulle attività di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso, tra cui informazioni sull'utilizzo dell'autenticazione a più fattori. I dati sull'autenticazione a più fattori forniscono informazioni approfondite sul funzionamento di questo tipo di autenticazione nell'organizzazione. Questi dati consentono di rispondere a domande come le seguenti:

- L'accesso è stato verificato con l'autenticazione a più fattori?
- In che modo l'utente ha completato l'autenticazione a più fattori?
- Perché l'utente non è riuscito a completare l'autenticazione a più fattori?
- Quanti utenti vengono verificati tramite l'autenticazione a più fattori?
- Quanti utenti non riescono a completare la verifica tramite l'autenticazione a più fattori?
- Quali sono i problemi comuni relativi all'autenticazione a più fattori riscontrati dagli utenti finali?

Questi dati sono disponibili tramite il [portale di Azure](https://portal.azure.com) e l'[API di creazione report](../reports-monitoring/concept-reporting-api.md).

![Report degli accessi AD Azure nel portale di Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struttura del report sugli accessi

I report delle attività di accesso per l'autenticazione a più fattori permettono di accedere alle informazioni seguenti:

**Autenticazione MFA obbligatoria:** specifica se l'autenticazione a più fattori è obbligatoria o meno per l'accesso. L'autenticazione a più fattori può essere obbligatoria per motivi di autenticazione a più fattori per singoli utenti, accesso condizionale o altri motivi. I valori possibili sono **Sì** o **No**.

**Risultato autenticazione MFA:** altre informazioni che specificano se l'autenticazione a più fattori è riuscita o è stata negata.

- Se l'autenticazione a più fattori è riuscita, questa colonna fornisce più informazioni riguardo al modo in cui è stata completata.
   - Azure Multi-Factor Authentication
      - Autenticazione completata nel cloud
      - Autenticazione scaduta a causa dei criteri configurati nel tenant
      - Registrazione richiesta
      - Autenticazione riuscita tramite attestazione nel token
      - Autenticazione riuscita tramite attestazione fornita dal provider esterno
      - Autenticazione riuscita tramite autenticazione avanzata
      - Autenticazione ignorata perché il flusso eseguito è un flusso di accesso del broker Windows
      - Autenticazione ignorata a causa della password di un'app
      - Autenticazione ignorata a causa della posizione
      - Autenticazione ignorata a causa di un dispositivo registrato
      - Autenticazione ignorata a causa di un dispositivo memorizzato
      - Autenticazione completata correttamente
   - Reindirizzamento al provider esterno per l'autenticazione a più fattori

- Se l'autenticazione a più fattori è stata negata, questa colonna fornisce informazioni sul motivo del rifiuto.
   - Autenticazione di Azure Multi-Factor Authentication negata
      - Autenticazione in corso
      - Tentativo di autenticazione duplicato
      - Immissione di un codice non corretto troppe volte
      - Autenticazione non valida
      - Codice di verifica dell'app per dispositivi mobili non valido
      - Errore di configurazione
      - Chiamata telefonica reindirizzata alla segreteria
      - Numero di telefono in formato non valido
      - Errore del servizio
      - Non è stato possibile raggiungere il telefono dell'utente
      - Non è stato possibile inviare la notifica dell'app per dispositivi mobili al dispositivo
      - Non è stato possibile inviare la notifica dell'app per dispositivi mobili
      - L'utente ha rifiutato l'autenticazione
      - L'utente non ha risposto alla notifica dell'app per dispositivi mobili
      - L'utente non ha registrato alcun metodo di verifica
      - L'utente ha immesso un codice non corretto
      - L'utente ha immesso un PIN non corretto
      - L'utente ha chiuso il telefono senza completare l'autenticazione
      - L'utente è bloccato
      - L'utente non ha mai immesso il codice di verifica
      - Utente non trovato
      - Il codice di verifica è già stato usato una volta

**Metodo autenticazione a più fattori:** metodo di autenticazione usato dall'utente per completare l'autenticazione a più fattori. I valori possibili sono:

- SMS
- Notifica dell'app per dispositivi mobili
- Chiamata telefonica (telefono per l'autenticazione)
- Codice di verifica dell'app per dispositivi mobili
- Chiamata telefonica (telefono ufficio)
- Chiamata telefonica (telefono per l'autenticazione alternativo)

**Dettagli autenticazione a più fattori:** versione parzialmente nascosta del numero di telefono, ad esempio +XX XXXXXXXX64.

**Accesso condizionale:** informazioni sui criteri di accesso condizionale che interessato il tentativo di accesso, tra cui:

- Nome criterio
- Controlli di concessione
- Controlli di sessione
- Risultato

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Creazione di report per utenti registrati per MFA PowerShell

In primo luogo, assicurarsi di avere il [modulo di PowerShell MSOnline V1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) installato.

Identificare gli utenti che hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificare gli utenti che non hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Possibili risultati nel report delle attività

Nella tabella seguente consente di risolvere i problemi di multi-factor authentication tramite alla versione scaricata del rapporto attività di multi-factor authentication. Non verranno visualizzati direttamente nel portale di Azure.

| Risultato della chiamata | Descrizione | Descrizione generale |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN immesso | L'utente ha immesso un PIN.  Se l'autenticazione ha avuto esito positivo l'utente ha immesso il PIN corretto.  Se l'autenticazione viene negata, l'utente ha immesso un PIN errato o l'utente è impostato sulla modalità Standard. |
| SUCCESS_NO_PIN | Solo & immesso | Se l'utente è impostato sulla modalità PIN e l'autenticazione viene negata, questo significa che l'utente non ha immesso il PIN e ha immesso solo #.  Se l'utente è impostato sulla modalità Standard e l'autenticazione ha esito positivo significa che l'utente ha immesso solo # che è la cosa corretta da eseguire in modalità Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Non premuto dopo l'immissione | L'utente non ha inviato le cifre DTMF poiché # non è stato immesso.  Le altre cifre immesse non vengono inviate a meno che non viene immesso # che indica il completamento della voce. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nessun Input telefono - timeout | La chiamata è stata risposta, ma è stata ricevuta alcuna risposta.  Ciò indica in genere che la chiamata è stata prelevata dal messaggio vocale. |
| SUCCESS_PIN_EXPIRED | PIN scaduto e non è stato modificato | Il PIN dell'utente è scaduto ed è stato richiesto di modificarla, ma la modifica del PIN non è stata completata. |
| SUCCESS_USED_CACHE | Cache utilizzata | L'autenticazione ha avuto esito positivo senza una chiamata di multi-Factor Authentication poiché entro l'intervallo cache configurato si è verificato durante una precedente autenticazione riuscita per lo stesso nome utente. |
| SUCCESS_BYPASSED_AUTH | Autenticazione ignorata | Autenticazione ha avuto esito positivo mediante un Bypass monouso avviato per l'utente.  Vedere il Report cronologia utenti bypass per altre informazioni sul bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache basata su IP utilizzata | L'autenticazione ha avuto esito positivo senza una chiamata di multi-Factor Authentication perché una precedente autenticazione riuscita per il nome utente stesso, il tipo di autenticazione, nome dell'applicazione e si è verificato un IP entro l'intervallo cache configurato. |
| SUCCESS_USED_APP_BASED_CACHE | Cache utilizzata basato su App | L'autenticazione ha avuto esito positivo senza una chiamata di multi-Factor Authentication perché una precedente autenticazione riuscita per lo stesso nome utente, tipo di autenticazione e nome dell'applicazione entro l'intervallo cache configurato. |
| SUCCESS_INVALID_INPUT | Input telefono non valido | La risposta inviata dal telefono non è valida.  Potrebbe trattarsi di un fax o modem oppure l'utente potrebbe aver immesso * come parte del proprio PIN. |
| SUCCESS_USER_BLOCKED | L'utente è bloccato | Numero di telefono dell'utente viene bloccato.  Un numero bloccato può essere avviato dall'utente durante una chiamata di autenticazione o da un amministratore tramite il portale di Azure. <br> NOTA:   Un numero bloccato è anche un risultato di un avviso di illecito. |
| SUCCESS_SMS_AUTHENTICATED | SMS autenticato | Per SMS bidirezionale, l'utente ha risposto correttamente con il passcode monouso (OTP) o OTP + PIN. |
| SUCCESS_SMS_SENT | SMS inviato | Per il SMS, è stato inviato il messaggio di testo contenente il passcode monouso (OTP).  L'utente immetterà il OTP o OTP + PIN nell'applicazione per completare l'autenticazione. |
| SUCCESS_PHONE_APP_AUTHENTICATED | App mobile autenticata | L'utente ha eseguito l'autenticazione tramite l'app per dispositivi mobili. |
| SUCCESS_OATH_CODE_PENDING | Codice OATH in sospeso | L'utente è stato richiesto per il proprio codice OATH ma non ha risposto. |
| SUCCESS_OATH_CODE_VERIFIED | Codice OATH verificato | L'utente ha immesso un codice OATH valido quando richiesto. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Codice OATH di fallback verificato | L'utente è stata negata l'autenticazione utilizzando il metodo primario di multi-Factor Authentication e quindi fornito un codice OATH valido per il fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Risposte alle domande di sicurezza di fallback | L'utente è stata negata l'autenticazione utilizzando il metodo primario di multi-Factor Authentication e quindi ha risposto alle domande di sicurezza corretto per il fallback. |
| FAILED_PHONE_BUSY | Autenticazione già In corso | Multi-Factor Authentication sta già elaborando un'autenticazione per questo utente.  Ciò è spesso causato dai client RADIUS che inviano più richieste di autenticazione durante la stessa sign-on. |
| CONFIG_ISSUE | Telefono irraggiungibile | Tentativo di chiamata, ma potrebbe non essere inseriti o non è stata fornita una risposta.  Ciò include il segnale di occupato, segnale di occupato veloce (disconnesso), a tre toni (numero non è più in servizio), un timeout durante far squillare e così via. |
| FAILED_INVALID_PHONENUMBER | Formato numero di telefono non valido | Il numero di telefono ha un formato non valido.  I numeri di telefono devono essere numerici e devono contenere 10 cifre per il codice paese + 1 (Stati Uniti e Canada). |
| FAILED_USER_HUNGUP_ON_US | Utente disconnesso verticale sul telefono | L'utente ha risposto al telefono, ma riappeso senza premere alcun pulsante. |
| FAILED_INVALID_EXTENSION | Estensione non valido | L'estensione contiene caratteri non validi.  Solo cifre, virgole, *, e & sono consentiti.  Un prefisso @ possono inoltre essere utilizzati. |
| FAILED_FRAUD_CODE_ENTERED | Codice illecito immesso | L'utente ha scelto di segnalare un illecito durante la chiamata, pertanto un errore di autenticazione negata e numero di telefono bloccato.| 
| FAILED_SERVER_ERROR | Impossibile effettuare chiamate | Il servizio multi-Factor Authentication non è riuscito a effettuare la chiamata. |
| FAILED_SMS_NOT_SENT | Impossibile inviare SMS | Impossibile inviare il messaggio di testo.  L'autenticazione viene negata. |
| FAILED_SMS_OTP_INCORRECT | OTP SMS non corretta | L'utente ha immesso un passcode monouso errata (OTP) dal messaggio SMS ricevuti.  L'autenticazione viene negata. |
| FAILED_SMS_OTP_PIN_INCORRECT | Testo OTP + PIN SMS non corretto | L'utente ha immesso un passcode monouso errata (OTP) e/o un PIN utente corretto.  L'autenticazione viene negata. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Superato testo massimo tentativi OTP per SMS | L'utente ha superato il numero massimo di tentativi di passcode monouso (OTP). |
| FAILED_PHONE_APP_DENIED | App mobile negata | L'utente ha negato l'autenticazione nell'app mobile facendo clic sul pulsante Nega. |
| FAILED_PHONE_APP_INVALID_PIN | PIN App mobile non valido | L'utente ha immesso un PIN non valido durante l'autenticazione nell'app per dispositivi mobili. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN App mobile non modificato | L'utente non ha completato una modifica PIN richiesta nell'app per dispositivi mobili. |
| FAILED_FRAUD_REPORTED | Illecito segnalato | L'utente ha segnalato un illecito nell'app per dispositivi mobili. |
| FAILED_PHONE_APP_NO_RESPONSE | App per dispositivi mobili Nessuna risposta | L'utente non ha risposto alla richiesta di autenticazione app per dispositivi mobili. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | App tutti i dispositivi Mobile bloccati | I dispositivi app mobile per l'utente non rispondono più alle notifiche e sono stati bloccati. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Notifica dell'App per dispositivi mobili non è riuscita | Si è verificato un errore durante il tentativo di inviare una notifica all'App per dispositivi mobili sul dispositivo dell'utente. |
| FAILED_PHONE_APP_INVALID_RESULT | Risultato App mobile non valido | L'app per dispositivi mobili ha restituito un risultato non è valido. |
| FAILED_OATH_CODE_INCORRECT | Codice OATH errato | L'utente ha immesso un codice OATH errato.  L'autenticazione viene negata. |
| FAILED_OATH_CODE_PIN_INCORRECT | Codice OATH + PIN non corretto | L'utente ha immesso un codice OATH errato e/o un PIN utente corretto.  L'autenticazione viene negata. |
| FAILED_OATH_CODE_DUPLICATE | Codice OATH duplicato | L'utente ha immesso un codice OATH utilizzato in precedenza.  L'autenticazione viene negata. |
| FAILED_OATH_CODE_OLD | Codice OATH non aggiornato | L'utente ha immesso un codice OATH che precede un codice OATH utilizzato in precedenza.  L'autenticazione viene negata. |
| FAILED_OATH_TOKEN_TIMEOUT | Timeout risultato codice OATH | L'utente ha impiegato troppo tempo per immettere il codice OATH e il tentativo di multi-Factor Authentication è già verificato il timeout. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Timeout risultato domande di sicurezza | L'utente ha impiegato troppo tempo per l'immissione risposta alle domande di sicurezza e il tentativo di multi-Factor Authentication è già verificato il timeout. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout risultato autenticazione | L'utente ha impiegato troppo tempo per completare il tentativo di multi-Factor Authentication. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticazione limitata | Il tentativo di multi-Factor Authentication è stato limitato dal servizio. |

## <a name="next-steps"></a>Passaggi successivi

* [Per gli utenti](../user-help/multi-factor-authentication-end-user.md)
* [Dove eseguire la distribuzione](concept-mfa-whichversion.md)
