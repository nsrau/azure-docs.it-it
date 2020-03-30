---
title: Report di accesso e utilizzo per Azure MFA - Azure Active DirectoryAccess and usage reports for Azure MFA - Azure Active Directory
description: Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47e6bac031a5f2cffc8734fee976fbf8dadc666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129091"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication offre diversi report che possono essere usati dall'utente e dall'organizzazione e ai quali è possibile accedere tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Location | Descrizione |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Security > l'autenticazione a più fattori > Bloccare/sbloccare gli utentiAzure AD Azure AD Azure AD > Security > MFA > Block/unblock users | Consente di visualizzare la cronologia delle richieste di blocco o sblocco degli utenti. |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |
| Utilizzo di componenti locali | Report attività > azure AD > sicurezza > | Fornisce informazioni sull'utilizzo complessivo di Multi-Factor Authentication tramite l'estensione del server dei criteri di rete, ADFS e il server MFA. |
| Cronologia utenti bypass | Sicurezza > di Azure AD > l'autenticazione utente locale > il bypass monousoAzure AD azure AD > Security > MFA > One-Time bypass | Fornisce una cronologia delle richieste di bypass di Multi-Factor Authentication per un utente. |
| Stato del server | Stato del server > di Azure AD > Security > MFAAzure AD > Security > > MFA > Server status | Consente di visualizzare lo stato dei server di Multi-Factor Authentication associati all'account. |

## <a name="view-mfa-reports"></a>Visualizzare i report MFA

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. A sinistra, selezionare **Azure Active Directory** > **Security** > **MFA**.
3. Selezionare il report che si vuole visualizzare.

   ![Rapporto sullo stato del server MFA Server nel portale di Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Report sugli accessi ad Azure AD

Con il **report sulle attività di accesso** nel [portale di Azure](https://portal.azure.com) è possibile ottenere le informazioni necessarie per determinare lo stato e le prestazioni dell'ambiente.

Il report sulle attività di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso, tra cui informazioni sull'utilizzo dell'autenticazione a più fattori. I dati sull'autenticazione a più fattori forniscono informazioni approfondite sul funzionamento di questo tipo di autenticazione nell'organizzazione. Questi dati consentono di rispondere a domande come le seguenti:

- L'accesso è stato verificato con l'autenticazione a più fattori?
- In che modo l'utente ha completato l'autenticazione a più fattori?
- Perché l'utente non è riuscito a completare l'autenticazione a più fattori?
- Quanti utenti vengono verificati tramite l'autenticazione a più fattori?
- Quanti utenti non riescono a completare la verifica tramite l'autenticazione a più fattori?
- Quali sono i problemi comuni relativi all'autenticazione a più fattori riscontrati dagli utenti finali?

Questi dati sono disponibili tramite il [portale di Azure](https://portal.azure.com) e l'API per la creazione di [report.](../reports-monitoring/concept-reporting-api.md)

![Report di accesso di Azure AD nel portale di AzureAzure AD sign ins report in the Azure portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struttura del report sugli accessi

I report delle attività di accesso per l'autenticazione a più fattori permettono di accedere alle informazioni seguenti:

**Autenticazione MFA obbligatoria:** specifica se l'autenticazione a più fattori è o meno obbligatoria per l'accesso. L'autenticazione a più fattori può essere necessaria a causa dell'autenticazione a più fattori per utente, dell'accesso condizionale o di altri motivi. I valori possibili sono **Sì** o **No**.

**Risultato autenticazione MFA:** altre informazioni che specificano se l'autenticazione a più fattori è riuscita o è stata negata:

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

**Accesso condizionale** Informazioni sui criteri di accesso condizionale che hanno interessato il tentativo di accesso, tra cui:Find information about Conditional Access policies that affected the sign-in tenta including:

- Nome criteri
- Controlli di concessione
- Controlli di sessione
- Risultato

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell reporting on users registered for MFA

Verificare innanzitutto che sia installato il modulo PowerShell di [MSOnline V1.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)

Identificare gli utenti che hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente. Questo set di comandi esclude gli utenti disabilitati poiché questi account non possono eseguire l'autenticazione in Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identificare gli utenti che non hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente. Questo set di comandi esclude gli utenti disabilitati poiché questi account non possono eseguire l'autenticazione in Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identificare gli utenti e i metodi di output registrati. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Possibili risultati nei rapporti attività

La tabella seguente può essere utilizzata per risolvere i problemi di autenticazione a più fattori utilizzando la versione scaricata del report attività di autenticazione a più fattori. Non verranno visualizzati direttamente nel portale di Azure.They will not appear directly in the Azure portal.

| Risultato chiamata | Descrizione | Descrizione ampia |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN immesso | L'utente ha immesso un PIN.  Se l'autenticazione ha esito positivo, l'utente ha immesso il PIN corretto. Se l'autenticazione viene negata, quindi hanno immesso un PIN non corretto o l'utente è impostato sulla modalità Standard. |
| SUCCESS_NO_PIN | Solo : è stato immesso | Se l'utente è impostato in modalità PIN e l'autenticazione viene negata, l'utente non ha immesso il PIN e ha immesso solo #.   Se l'utente è impostato in modalità Standard e l'autenticazione ha esito positivo, l'utente ha immesso solo #, come richiesto in modalità Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Non premuto dopo l'immissione | L'utente non ha inviato le cifre DTMF poiché # non è stato immesso.  Le altre cifre immesse non vengono inviate a meno che non viene immesso # che indica il completamento della voce. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nessun input telefono - Timeout | La chiamata è stata risposta, ma non c'è stata alcuna risposta.  Indica in genere che la chiamata è stata risposta dal messaggio vocale. |
| SUCCESS_PIN_EXPIRED | PIN scaduto e non modificato | Il PIN dell'utente è scaduto ed è stato richiesto di modificarlo, ma non è stata completata la modifica del PIN. |
| SUCCESS_USED_CACHE | Cache utilizzata | L'autenticazione è riuscita senza una chiamata di Autenticazione a più fattori poiché un'autenticazione riuscita precedente per lo stesso nome utente si è verificata nell'intervallo di tempo della cache configurata. |
| SUCCESS_BYPASSED_AUTH | Autenticazione ignorata | L'autenticazione ha avuto esito positivo mediante un bypass monouso avviato per l'utente. Per ulteriori informazioni sul bypass, vedere Report cronologia utenti bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache basata su IP utilizzata | L'autenticazione è riuscita senza una chiamata di Autenticazione a più fattori poiché un'autenticazione riuscita precedente per lo stesso nome utente, tipo di autenticazione, nome dell'applicazione e IP si è verificata all'interno dell'intervallo di tempo della cache configurata. |
| SUCCESS_USED_APP_BASED_CACHE | Cache basata su app utilizzata | L'autenticazione è riuscita senza una chiamata di Autenticazione a più fattori dopo una precedente autenticazione riuscita per lo stesso nome utente, tipo di autenticazione e nome dell'applicazione all'interno dell'intervallo di tempo della cache configurata. |
| SUCCESS_INVALID_INPUT | Input telefono non valido | La risposta inviata dal telefono non è valida. Potrebbe prodovreitenere da un fax o da un modem o che l'utente abbia immesso il numero di computer come PIN. |
| SUCCESS_USER_BLOCKED | Utente bloccato | Il numero di telefono dell'utente è bloccato. Un numero bloccato può essere avviato dall'utente durante una chiamata di autenticazione o da un amministratore tramite il portale di Azure.A blocked number can be initiated by the user during an authentication call or by an administrator using the Azure portal. <br> NOTA: Un numero bloccato è anche un sottoprodotto di un avviso di frode. |
| SUCCESS_SMS_AUTHENTICATED | SMS autenticato | Per SMS bidirezionale, l'utente ha risposto correttamente con il passcode monouso (OTP) o OTP + PIN. |
| SUCCESS_SMS_SENT | SMS inviato | Per SMS, è stato inviato il messaggio SMS contenente il passcode monouso (OTP). Per completare l'autenticazione, l'utente inserirà il PIN OTP o OTP nell'applicazione. |
| SUCCESS_PHONE_APP_AUTHENTICATED | App mobile autenticata | L'utente ha eseguito l'autenticazione tramite l'app mobile. |
| SUCCESS_OATH_CODE_PENDING | Codice OATH in sospeso | All'utente è stato chiesto il codice OATH ma non ha risposto. |
| SUCCESS_OATH_CODE_VERIFIED | Codice OATH verificato | L'utente ha immesso un codice OATH valido quando richiesto. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Codice OATH di fallback verificato | All'utente è stata negata l'autenticazione utilizzando il metodo primario di Multi-Factor Authentication e quindi ha fornito un codice OATH per il fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Domande di sicurezza di fallback risposte | All'utente è stata negata l'autenticazione utilizzando il metodo primario di Multi-Factor Authentication e quindi ha risposto correttamente alle domande di sicurezza per il fallback. |
| FAILED_PHONE_BUSY | Autenticazione già in corso | Multi-Factor Authentication sta già elaborando un'autenticazione per questo utente. Ciò è spesso causato dai client RADIUS che inviano più richieste di autenticazione durante lo stesso accesso. |
| CONFIG_ISSUE | Telefono irraggiungibile | La chiamata è stata tentata, ma non è stato possibile o non ha ricevuto risposta. Questo include il segnale di occupato, il segnale di occupato veloce (disconnesso), i tritoni (numero non più in servizio), il timeout durante lo squillo, ecc. |
| FAILED_INVALID_PHONENUMBER | Formato numero di telefono non valido | Il formato del numero di telefono non è valido. I numeri di telefono devono essere numerici e devono essere composti da 10 cifre per il prefisso internazionale n. 1 (Stati Uniti & Canada). |
| FAILED_USER_HUNGUP_ON_US | Chiamata disconnessa da utente | L'utente ha risposto al telefono ma ha riappeso senza premere alcun pulsante. |
| FAILED_INVALID_EXTENSION | Interno non valido | L'interno contiene caratteri non validi. Sono consentite solo le cifre, le virgole, i caratteri .  È inoltre possibile utilizzare un prefisso @. |
| FAILED_FRAUD_CODE_ENTERED | Codice illecito immesso | L'utente ha scelto di segnalare un illecito durante la chiamata, pertanto l'autenticazione è stata negata e il numero di telefono bloccato.| 
| FAILED_SERVER_ERROR | Impossibile effettuare chiamate | Il servizio Multi-Factor Authentication non è riuscito a effettuare la chiamata. |
| FAILED_SMS_NOT_SENT | Impossibile inviare l'SMS. | Impossibile inviare il messaggio di testo.  Autenticazione negata. |
| FAILED_SMS_OTP_INCORRECT | OTP SMS non corretta | L'utente ha immesso un passcode monouso (OTP) non corretto dal messaggio di testo ricevuto.  Autenticazione negata. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP + PIN SMS non corretti | L'utente ha immesso un codice OTP (One-Time Passcode) non corretto e/o un PIN utente non corretto.  Autenticazione negata. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Superato il numero massimo di tentativi OTP dei messaggi di testo | L'utente ha superato il numero massimo di tentativi di passcode Monocumo (OTP). |
| FAILED_PHONE_APP_DENIED | App mobile negata | L'utente ha negato l'autenticazione nell'app mobile facendo clic sul pulsante Nega. |
| FAILED_PHONE_APP_INVALID_PIN | PIN app mobile non valido | L'utente ha immesso un PIN non valido durante l'autenticazione nell'app mobile. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN app mobile non modificato | L'utente non ha completato una modifica PIN richiesta nell'app mobile. |
| FAILED_FRAUD_REPORTED | Illecito segnalato | L'utente ha segnalato un illecito nell'app mobile. |
| FAILED_PHONE_APP_NO_RESPONSE | Nessuna risposta da app mobile | L'utente non ha risposto alla richiesta di autenticazione dell'app per dispositivi mobili. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Tutti i dispositivi app mobile bloccati | I dispositivi app mobile per l'utente non rispondono più alle notifiche e sono stati bloccati. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Notifica app mobile non riuscita | Si è verificato un errore durante il tentativo di inviare una notifica all'app mobile sul dispositivo dell'utente. |
| FAILED_PHONE_APP_INVALID_RESULT | Risultato app mobile non valido | L'app mobile ha restituito un risultato non valido. |
| FAILED_OATH_CODE_INCORRECT | Codice OATH errato | L'utente ha immesso un codice OATH errato.   Autenticazione negata. |
| FAILED_OATH_CODE_PIN_INCORRECT | Codice OATH - PIN errato | L'utente ha immesso un codice OATH non corretto e/o un PIN utente non corretto.   Autenticazione negata. |
| FAILED_OATH_CODE_DUPLICATE | Codice OATH duplicato | L'utente ha immesso un codice OATH utilizzato in precedenza.   Autenticazione negata. |
| FAILED_OATH_CODE_OLD | Codice OATH non aggiornato | L'utente ha immesso un codice OATH che precede un codice OATH utilizzato in precedenza.   Autenticazione negata. |
| FAILED_OATH_TOKEN_TIMEOUT | Timeout risultato codice OATH | L'utente ha impiegato troppo tempo per inserire il codice OATH e il tentativo di autenticazione a più fattori era già sforato. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Sicurezza Domande Risultato Timeout | L'utente ha impiegato troppo tempo per immettere la risposta alle domande di sicurezza e il tentativo di autenticazione a più fattori era già stime. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout risultato autenticazione | L'utente ha impiegato troppo tempo per completare il tentativo di autenticazione a più fattori. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticazione limitata | Il tentativo di autenticazione a più fattori è stato limitato dal servizio. |

## <a name="next-steps"></a>Passaggi successivi

* [Report di utilizzo e approfondimenti di SSPR e MFA](howto-authentication-methods-usage-insights.md)
* [Per gli utenti](../user-help/multi-factor-authentication-end-user.md)
* [Dove eseguire la distribuzione](concept-mfa-whichversion.md)
