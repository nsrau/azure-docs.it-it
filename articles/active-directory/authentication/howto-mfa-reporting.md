---
title: Rapporto di accesso e uso per Azure MFA | Microsoft Docs
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
ms.openlocfilehash: 8e901f0e5fb1dc2840196bd6a739ff763b54ce8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119225"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication offre diversi report che possono essere usati dall'utente e dall'organizzazione e ai quali è possibile accedere tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Località | DESCRIZIONE |
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

   <center>
   
   ![Cloud](./media/howto-mfa-reporting/report.png)</center>

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

![Cloud](./media/howto-mfa-reporting/sign-in-report.png)

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

## <a name="powershell-reporting"></a>Report di PowerShell

Identificare gli utenti che hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificare gli utenti che non hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Passaggi successivi

* [Per gli utenti](../user-help/multi-factor-authentication-end-user.md)
* [Dove eseguire la distribuzione](concept-mfa-whichversion.md)
