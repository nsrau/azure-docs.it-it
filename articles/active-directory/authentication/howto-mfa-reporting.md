---
title: Dettagli dell'evento di accesso per Azure Multi-Factor Authentication - Azure Active Directory
description: Informazioni su come visualizzare l'attività di accesso per gli eventi e i messaggi di stato di Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 676b8b6fbb56536ec3a49100f5de1419ac417bb6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964146"
---
# <a name="use-the-sign-ins-report-to-review-azure-multi-factor-authentication-events"></a>Usare il report delle attività di accesso per esaminare gli eventi di Azure Multi-Factor Authentication

Per esaminare e comprendere gli eventi di Azure Multi-Factor Authentication, è possibile usare il report delle attività di accesso di Azure Active Directory (Azure AD). In tale report sono contenuti i dettagli relativi all'autenticazione per gli eventi quando a un utente viene richiesta l'autenticazione a più fattori e quando sono in uso criteri di accesso condizionale. Per informazioni dettagliate sul report delle attività di accesso, vedere [Panoramica dei report sulle attività di accesso in Azure AD](../reports-monitoring/concept-sign-ins.md).

Questo articolo illustra come visualizzare il report delle attività di accesso di Azure AD nel portale di Azure e quindi il modulo MSOnline v1 PowerShell.

## <a name="view-the-azure-ad-sign-ins-report"></a>Visualizzare il report delle attività di accesso di Azure AD

Il report sulle attività di accesso fornisce informazioni sull'uso delle applicazioni gestite e sugli accessi da parte dell'utente, tra cui informazioni sull'uso dell'autenticazione a più fattori. I dati sull'autenticazione a più fattori forniscono informazioni approfondite sul funzionamento di questo tipo di autenticazione nell'organizzazione. Consentono di rispondere a domande come le seguenti:

- L'accesso è stato verificato con l'autenticazione a più fattori?
- In che modo l'utente ha completato l'autenticazione a più fattori?
- Perché l'utente non è riuscito a completare l'autenticazione a più fattori?
- Quanti utenti vengono verificati tramite l'autenticazione a più fattori?
- Quanti utenti non riescono a completare la verifica tramite l'autenticazione a più fattori?
- Quali sono i problemi comuni relativi all'autenticazione a più fattori riscontrati dagli utenti finali?

Per visualizzare il report delle attività di accesso nel [portale di Azure](https://portal.azure.com), eseguire queste operazioni. È anche possibile eseguire query sui dati usando l'[API per la creazione di report](../reports-monitoring/concept-reporting-api.md).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account con autorizzazioni di *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Utenti** nel menu a sinistra.
1. In *Attività* nel menu a sinistra selezionare **Accessi**.
1. Viene visualizzato un elenco di eventi di accesso, incluso lo stato. È possibile selezionare un evento per visualizzare altri dettagli.

    La scheda *Dettagli di autenticazione* o *Accesso condizionale* relativa ai dettagli dell'evento mostra il codice dello stato oppure il criterio attivato dalla richiesta dell'autenticazione a più fattori.

    [![Screenshot di un esempio di report delle attività di accesso in Azure Active Directory nel portale di Azure](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Se disponibile, viene visualizzata l'autenticazione, ad esempio un messaggio SMS, la notifica dell'app Microsoft Authenticator o la telefonata.

Per un evento di accesso che indica se la richiesta di autenticazione a più fattori è stata soddisfatta o negata, nella finestra *Dettagli di autenticazione* vengono visualizzati i dati seguenti:

* Se l'autenticazione a più fattori è riuscita, questa colonna fornisce più informazioni riguardo al modo in cui è stata completata.
   * Autenticazione completata nel cloud
   * Autenticazione scaduta a causa dei criteri configurati nel tenant
   * Registrazione richiesta
   * Autenticazione riuscita tramite attestazione nel token
   * Autenticazione riuscita tramite attestazione fornita dal provider esterno
   * Autenticazione riuscita tramite autenticazione avanzata
   * Autenticazione ignorata perché il flusso eseguito è un flusso di accesso del broker Windows
   * Autenticazione ignorata a causa della password di un'app
   * Autenticazione ignorata a causa della posizione
   * Autenticazione ignorata a causa di un dispositivo registrato
   * Autenticazione ignorata a causa di un dispositivo memorizzato
   * Autenticazione completata correttamente

* Se l'autenticazione a più fattori è stata negata, questa colonna fornisce informazioni sul motivo del rifiuto.
   * Autenticazione in corso
   * Tentativo di autenticazione duplicato
   * Immissione di un codice non corretto troppe volte
   * Autenticazione non valida
   * Codice di verifica dell'app per dispositivi mobili non valido
   * Errore di configurazione
   * Chiamata telefonica reindirizzata alla segreteria
   * Numero di telefono in formato non valido
   * Errore del servizio
   * Non è stato possibile raggiungere il telefono dell'utente
   * Non è stato possibile inviare la notifica dell'app per dispositivi mobili al dispositivo
   * Non è stato possibile inviare la notifica dell'app per dispositivi mobili
   * L'utente ha rifiutato l'autenticazione
   * L'utente non ha risposto alla notifica dell'app per dispositivi mobili
   * L'utente non ha registrato alcun metodo di verifica
   * L'utente ha immesso un codice non corretto
   * L'utente ha immesso un PIN non corretto
   * L'utente ha chiuso il telefono senza completare l'autenticazione
   * L'utente è bloccato
   * L'utente non ha mai immesso il codice di verifica
   * Utente non trovato
   * Il codice di verifica è già stato usato una volta

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Report di PowerShell per utenti registrati per l'autenticazione a più fattori

Verificare innanzitutto che il [modulo MSOnline V1 PowerShell](/powershell/azure/active-directory/overview?view=azureadps-1.0) sia installato.

Identificare gli utenti che hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente. Questo set di comandi esclude gli utenti disabilitati perché tali account non possono eseguire l'autenticazione per Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identificare gli utenti che non hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente. Questo set di comandi esclude gli utenti disabilitati perché tali account non possono eseguire l'autenticazione per Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identificare gli utenti e i metodi di output registrati:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Codici di risultato dei report di attività scaricati

La tabella seguente consente di risolvere i problemi relativi agli eventi usando la versione scaricata del report di attività dai passaggi eseguiti nel portale o dai comandi di PowerShell precedenti. Tali codici di risultati non vengono visualizzati direttamente nel portale di Azure.

| Risultato chiamata | Descrizione | Descrizione generale |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN immesso | L'utente ha immesso un PIN.  Se l'autenticazione ha esito positivo, l'utente ha immesso il PIN corretto.  Se l'autenticazione viene negata, è stato immesso un PIN errato o l'utente è impostato sulla modalità standard. |
| SUCCESS_NO_PIN | Immesso solo il carattere # | Se l'utente è impostato in modalità PIN e l'autenticazione viene negata, l'utente non ha immesso il PIN e ha immesso solo #.  Se l'utente è impostato in modalità Standard e l'autenticazione ha esito positivo, l'utente ha immesso solo #, come richiesto in modalità Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # non premuto dopo l'immissione | L'utente non ha inviato le cifre DTMF poiché # non è stato immesso.  Le altre cifre immesse non vengono inviate a meno che non venga immesso # che indica il completamento dell'inserimento. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nessun input telefono - Timeout | La chiamata è stata risposta, ma non c'è stata alcuna risposta vocale.  Indica in genere che la risposta proveniva dalla segreteria telefonica. |
| SUCCESS_PIN_EXPIRED | PIN scaduto e non modificato | Il PIN dell'utente è scaduto ed è stato richiesto di modificarlo, ma non è stata completata la modifica del PIN. |
| SUCCESS_USED_CACHE | Cache usata | L'autenticazione è riuscita senza una chiamata dell'autenticazione a più fattori perché per lo stesso nome utente un'autenticazione precedente è riuscita nell'intervallo di tempo della cache configurato. |
| SUCCESS_BYPASSED_AUTH | Autenticazione ignorata | L'autenticazione ha avuto esito positivo mediante un bypass monouso avviato per l'utente.  Per ulteriori informazioni sul bypass, vedere Report cronologia utenti bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache basata su IP usata | L'autenticazione è riuscita senza una chiamata all'autenticazione a più fattori perché per lo stesso nome utente, tipo di autenticazione, nome dell'applicazione e IP un'autenticazione precedente è riuscita nell'intervallo di tempo della cache configurato. |
| SUCCESS_USED_APP_BASED_CACHE | Cache basata su app usata | L'autenticazione è riuscita senza una chiamata all'autenticazione a più fattori perché per lo stesso nome utente, tipo di autenticazione e nome dell'applicazione un'autenticazione precedente è riuscita nell'intervallo di tempo della cache configurato. |
| SUCCESS_INVALID_INPUT | Input telefono non valido | La risposta inviata dal telefono non è valida.  Potrebbe provenire da un fax o un modem oppure l'utente potrebbe aver immesso * come parte del PIN. |
| SUCCESS_USER_BLOCKED | L'utente è bloccato | Il numero di telefono dell'utente è bloccato.  Un numero bloccato può essere inizializzato dall'utente durante una chiamata di autenticazione o da un amministratore che usa il portale di Azure. <br> NOTA:   un numero bloccato è anche una conseguenza collaterale di un avviso di illecito. |
| SUCCESS_SMS_AUTHENTICATED | SMS autenticato | Per SMS bidirezionale, l'utente ha risposto correttamente con il passcode monouso (OTP) oppure OTP + PIN. |
| SUCCESS_SMS_SENT | SMS inviato | Per SMS, è stato inviato il messaggio SMS contenente il passcode monouso (OTP).  L'utente immette l'OTP oppure OTP + PIN nell'applicazione per completare l'autenticazione. |
| SUCCESS_PHONE_APP_AUTHENTICATED | App mobile autenticata | L'utente ha eseguito l'autenticazione tramite l'app mobile. |
| SUCCESS_OATH_CODE_PENDING | Codice OATH in sospeso | All'utente è stato chiesto il codice OATH, ma non è stata fornita una risposta. |
| SUCCESS_OATH_CODE_VERIFIED | Codice OATH verificato | L'utente ha immesso un codice OATH valido quando richiesto. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Codice OATH di fallback verificato | L'autenticazione è stata negata quando è stato usato il metodo primario di Multi-Factor Authentication e quindi l'utente ha fornito un codice OATH per il fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Domande di sicurezza di fallback risolte | L'autenticazione è stata negata quando è stato usato il metodo primario di Multi-Factor Authentication e quindi l'utente ha risposto correttamente alle domande di sicurezza per il fallback. |
| FAILED_PHONE_BUSY | Autenticazione già in corso | È già incorso un'autenticazione per questo utente.  Questo è spesso causato dai client RADIUS che inviano più richieste di autenticazione durante lo stesso accesso. |
| CONFIG_ISSUE | Telefono irraggiungibile | La chiamata è stata tentata, ma non è stato possibile completarla o non è stata ricevuta alcuna risposta.  Ciò include il segnale di occupato, il segnale di occupato veloce (disconnesso), un segnale a tre toni (numero non più in servizio), timeout durante lo squillo e così via. |
| FAILED_INVALID_PHONENUMBER | Formato numero di telefono non valido | Il formato del numero di telefono non è valido.  I numeri di telefono devono essere numerici e devono essere costituiti da 10 cifre per il codice paese +1 (Stati Uniti e Canada). |
| FAILED_USER_HUNGUP_ON_US | Chiamata disconnessa da utente | L'utente ha risposto al telefono, ma ha riappeso senza premere alcun pulsante. |
| FAILED_INVALID_EXTENSION | Interno non valido | L'interno contiene caratteri non validi.  Sono consentiti solo numeri, virgole, * e #.  È inoltre possibile usare un prefisso @. |
| FAILED_FRAUD_CODE_ENTERED | Codice illecito immesso | L'utente ha scelto di segnalare un illecito durante la chiamata, pertanto l'autenticazione è stata negata e il numero di telefono bloccato.| 
| FAILED_SERVER_ERROR | Impossibile effettuare chiamate | Il servizio Multi-Factor Authentication non è stato in grado di effettuare la chiamata. |
| FAILED_SMS_NOT_SENT | Impossibile inviare SMS. | Impossibile inviare il messaggio SMS.  Autenticazione negata. |
| FAILED_SMS_OTP_INCORRECT | OTP SMS non corretto | L'utente ha immesso un passcode monouso (OTP) errato dal messaggio SMS ricevuto.  Autenticazione negata. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP + PIN SMS non corretti | L'utente ha immesso un passcode monouso (OTP) errato e/o un PIN utente errato.  Autenticazione negata. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | È stato superato il numero massimo di tentativi OTP per SMS | L'utente ha superato il numero massimo di tentativi di inserire un passcode monouso. |
| FAILED_PHONE_APP_DENIED | App mobile negata | L'utente ha negato l'autenticazione nell'app mobile facendo clic sul pulsante Nega. |
| FAILED_PHONE_APP_INVALID_PIN | PIN app mobile non valido | L'utente ha immesso un PIN non valido durante l'autenticazione nell'app mobile. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN app mobile non modificato | L'utente non ha completato una modifica PIN richiesta nell'app mobile. |
| FAILED_FRAUD_REPORTED | Illecito segnalato | L'utente ha segnalato un illecito nell'app mobile. |
| FAILED_PHONE_APP_NO_RESPONSE | Nessuna risposta da app mobile | L'utente non ha risposto alla richiesta di autenticazione dell'app mobile. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Tutti i dispositivi dell'app mobile bloccati | I dispositivi dell'app mobile per l'utente non rispondono più alle notifiche e sono stati bloccati. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Notifica app mobile non riuscita | Si è verificato un errore durante il tentativo di inviare una notifica all'app mobile sul dispositivo dell'utente. |
| FAILED_PHONE_APP_INVALID_RESULT | Risultato app mobile non valido | L'app mobile ha restituito un risultato non valido. |
| FAILED_OATH_CODE_INCORRECT | Codice OATH errato | L'utente ha immesso un codice OATH errato.  Autenticazione negata. |
| FAILED_OATH_CODE_PIN_INCORRECT | Il codice OATH e il PIN non sono validi | L'utente ha immesso un codice OATH errato e/o un PIN utente errato.  Autenticazione negata. |
| FAILED_OATH_CODE_DUPLICATE | Codice OATH duplicato | L'utente ha immesso un codice OATH utilizzato in precedenza.  Autenticazione negata. |
| FAILED_OATH_CODE_OLD | Codice OATH non aggiornato | L'utente ha immesso un codice OATH che precede un codice OATH utilizzato in precedenza.  Autenticazione negata. |
| FAILED_OATH_TOKEN_TIMEOUT | Timeout risultato codice OATH | L'utente ha impiegato troppo tempo per immettere il codice OATH e si è verificato un timeout per il tentativo di Multi-Factor Authentication. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Timeout risultato domande di sicurezza | L'utente ha impiegato troppo tempo per immettere risposte alle domande di sicurezza e per il tentativo di Multi-Factor Authentication si è già verificato il timeout. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout risultato autenticazione | L'utente ha impiegato troppo tempo per completare il tentativo di Multi-Factor Authentication. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticazione limitata | Il tentativo di Multi-Factor Authentication è stato limitato dal servizio. |

## <a name="additional-mfa-reports"></a>Report MFA aggiuntivi

Le informazioni e i report aggiuntivi seguenti sono disponibili per gli eventi di Multi-Factor Authentication, inclusi quelli per il server Multi-Factor Authentication:

| Report | Location | Descrizione |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Sicurezza > MFA > Blocca/Sblocca utenti | Consente di visualizzare la cronologia delle richieste di blocco o sblocco degli utenti. |
| Utilizzo di componenti locali | Azure AD > Sicurezza > MFA > Report attività | Fornisce informazioni sull'uso complessivo di Multi-Factor Authentication tramite l'estensione del server dei criteri di rete, ADFS e il server MFA. |
| Cronologia utenti bypass | Azure AD > Sicurezza > MFA > Bypass monouso | Fornisce una cronologia delle richieste del server MFA per ignorare l'autenticazione a più fattori per un utente. |
| Stato del server | Azure AD > Sicurezza > MFA > Stato del server | Visualizza lo stato dei server MFA associato all'account. |

## <a name="next-steps"></a>Passaggi successivi

Questo articolo fornisce una panoramica del report sulle attività di accesso. Per informazioni più dettagliate sul contenuto del report e per comprendere i dati, vedere [Report delle attività di accesso in Azure AD](../reports-monitoring/concept-sign-ins.md).
