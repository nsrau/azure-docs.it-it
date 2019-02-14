---
title: Criteri di reimpostazione password self-service di Azure AD
description: Configurare le opzioni dei criteri di reimpostazione password self-service di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fed31d07f4bbe9fc47ce0d2c31f45fed288c4c4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218024"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restrizioni e criteri password in Azure Active Directory

Questo articolo descrive i criteri password e i requisiti di complessità associati agli account utente nel tenant di Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Differenze dei criteri di reimpostazione degli amministratori

**Microsoft applica criteri predefiniti di reimpostazione della password basati su una password complessa e *a doppio controllo* per ogni ruolo di amministratore di Azure**; questi criteri possono essere diversi da quelli definiti per gli utenti e non possono essere modificati. È necessario verificare sempre la funzionalità di reimpostazione della password come utente senza ruoli di amministratore di Azure assegnati.

Con il criterio a doppio controllo, gli **amministratori non hanno la possibilità di usare le domande di sicurezza**.

Il criterio a due gate richiede tre tipi di dati di autenticazione, ad esempio un **indirizzo di posta elettronica**, un'**app di autenticazione** o un **numero di telefono**. Un criterio a due gate si applica nelle circostanze seguenti:

* Sono interessati tutti i ruoli di amministratore di Azure seguenti:
  * Amministratore dell'help desk
  * Amministratore del supporto per il servizio
  * Amministratore fatturazione
  * Supporto partner - Livello 1
  * Supporto partner - Livello 2
  * Amministratore del servizio Exchange
  * Amministratore del servizio Lync
  * Amministratore account utente
  * Writer di directory
  * Amministratore globale o amministratore aziendale
  * Amministratore del servizio SharePoint
  * Amministratore di conformità
  * Amministratore di applicazioni
  * Amministratore della sicurezza
  * Amministratore dei ruoli con privilegi
  * Amministratore del servizio Microsoft Intune
  * Amministratore del servizio proxy di applicazione
  * Amministratore del servizio CRM
  * Amministratore del servizio Power BI

* Se sono trascorsi 30 giorni per una sottoscrizione di valutazione o
* È presente un dominio personale, ad esempio contoso.com, o
* Identità sincronizzate da Azure AD Connect nella directory locale

### <a name="exceptions"></a>Eccezioni

Un criterio a un gate richiede un tipo di dati di autenticazione, ad esempio un indirizzo di posta elettronica *o* un numero di telefono. Un criterio a un gate si applica nelle circostanze seguenti:

* Non sono ancora trascorsi i primi 30 giorni per una sottoscrizione di valutazione o
* Non è presente un dominio personale (*.onmicrosoft.com) e
* Azure AD Connect non sincronizza le identità

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri UserPrincipalName che si applicano a tutti gli account utente

A ogni account utente che deve eseguire l'accesso ad Azure AD è necessario che sia associato un valore di attributo UPN. La tabella seguente descrive i criteri che si applicano sia agli account utente Active Directory locali sincronizzati con il cloud sia agli account utente di reti solo cloud:

| Proprietà | Requisiti di UserPrincipalName |
| --- | --- |
| Caratteri consentiti |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caratteri non consentiti |<ul> <li>Qualsiasi carattere "\@\" che non separa il nome utente dal dominio.</li> <li>Non può contenere un punto "." subito prima del simbolo "\@\"</li></ul> |
| Vincoli di lunghezza |<ul> <li>La lunghezza totale non deve superare i 113 caratteri</li><li>Prima del simbolo "\@\" possono essere presenti al massimo 64 caratteri</li><li>Dopo il simbolo "\@\" possono essere presenti al massimo 48 caratteri</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Criteri password che si applicano solo agli account utente del cloud

La tabella seguente descrive le impostazioni disponibili per i criteri password che possono essere applicate agli account utente creati e gestiti in Azure AD:

| Proprietà | Requisiti |
| --- | --- |
| Caratteri consentiti |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ " ( ) ;</li></ul> |
| Caratteri non consentiti |<ul><li>Caratteri Unicode.</li><li>Spazi.</li><li> Solo password complesse: non possono contenere un punto "." subito prima del simbolo "\@\"".</li></ul> |
| Restrizioni per le password |<ul><li>Devono avere un numero di caratteri compreso tra 8 e 16.</li><li>Solo password complesse: è necessario soddisfare tre dei quattro requisiti seguenti:<ul><li>Caratteri minuscoli.</li><li>Caratteri maiuscoli.</li><li>Numeri (da 0 a 9).</li><li>Simboli (vedere le restrizioni per le password elencate sopra).</li></ul></li></ul> |
| Durata di validità della password |<ul><li>Valore predefinito: **90** giorni.</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy` del modulo di Azure Active Directory per Windows PowerShell.</li></ul> |
| Notifica della scadenza della password |<ul><li>Valore predefinito: **14** giorni (prima della scadenza della password).</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Scadenza della password |<ul><li>Valore predefinito: **false** giorni (indica che la scadenza password è abilitata).</li><li>Il valore può essere configurato per singoli account utente con il cmdlet `Set-MsolUser`.</li></ul> |
| Cronologia delle modifiche della password |Al cambio della password, l'utente *non può* usare di nuovo la password più recente. |
| Cronologia delle reimpostazioni della password | Alla reimpostazione di una password dimenticata, l'utente *può* usare di nuovo la password più recente. |
| Blocco dell'account |Dopo 10 tentativi di accesso non riusciti a causa della password errata, l'utente viene bloccato per un minuto. Altri tentativi di accesso non riusciti bloccano l'utente per periodi sempre più lunghi. Il [blocco intelligente](howto-password-smart-lockout.md) tiene traccia degli ultimi tre hash delle password non validi per evitare l'incremento del contatore dei blocchi per la stessa password. Se un utente immette la stessa password errata più volte, questo comportamento non comporterà il blocco dell'account. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Impostare i criteri di scadenza della password in Azure AD

In qualità di amministratore globale per un servizio cloud Microsoft, è possibile usare il Modulo di Microsoft Azure AD per Windows PowerShell per impostare password utente che non scadono. È inoltre possibile usare cmdlet Windows PowerShell per rimuovere la configurazione senza scadenza, o per vedere quali password utente vengono impostate in modo da non scadere mai. 

Queste indicazioni si applicano ad altri provider, ad esempio Intune e Office 365, che si basano sempre su Azure AD per i servizi di identità e directory. La scadenza della password è l'unica parte dei criteri a poter essere modificata.

> [!NOTE]
> Solo le password degli account utente per cui non è usata la sincronizzazione della directory possono essere configurate per non scadere. Per altre informazioni sulla sincronizzazione delle directory, vedere [Connettere AD con Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Impostare o verificare i criteri password con PowerShell

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Al termine dell'installazione è possibile eseguire la procedura seguente per configurare ogni campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Controllare i criteri di scadenza per una password

1. Connettersi a Windows PowerShell usando le credenziali aziendali di amministratore.
1. Eseguire uno di questi comandi:

   * Per vedere se la password di un singolo utente è impostata in modo da non scadere mai, eseguire il cmdlet seguente usando l'UPN (ad esempio, *aprilr@contoso.onmicrosoft.com*) o l'ID dell'utente che si desidera controllare: `Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`
   * Per visualizzare l'impostazione **La password non scade mai** per tutti gli utenti, eseguire il cmdlet seguente: `Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`

### <a name="set-a-password-to-expire"></a>Impostare una scadenza della password

1. Connettersi a Windows PowerShell usando le credenziali aziendali di amministratore.
1. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che scada, eseguire il cmdlet seguente usando l'UPN o l'ID dell'utente: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None`
   * Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, usare il cmdlet seguente: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None`

### <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza

1. Connettersi a Windows PowerShell usando le credenziali aziendali di amministratore.
1. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che non scada mai, eseguire il cmdlet seguente usando l'UPN o l'ID dell'utente: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration`
   * Per impostare le password degli utenti in un'organizzazione in modo che non scadano mai, eseguire il cmdlet seguente: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration`

   > [!WARNING]
   > Le password impostate su `-PasswordPolicies DisablePasswordExpiration` diventano comunque obsolete in base all'attributo `pwdLastSet`. Dopo che sono trascorsi più di 90 giorni, le password scadono anche se sono state impostate in modo da non scadere mai. In base all'attributo `pwdLastSet`, se si modifica la scadenza in `-PasswordPolicies None`, all'accesso successivo l'utente sarà tenuto a cambiare tutte le password che hanno un attributo `pwdLastSet` che risale a più di 90 giorni prima. Questa modifica può riguardare un numero elevato di utenti. 

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti offrono altre informazioni sull'uso della reimpostazione della password con Azure AD:

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md).
* [Domande sulle licenze](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)
