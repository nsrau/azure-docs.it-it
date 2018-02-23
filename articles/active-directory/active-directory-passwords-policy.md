---
title: Criteri di reimpostazione della password self-service - Azure Active Directory
description: Opzioni dei criteri di reimpostazione password self-service di Azure AD
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione password self-service di Azure AD
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: ade7f1d3c868c2ce6ccedbbf11aaf7dc54706cff
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restrizioni e criteri password in Azure Active Directory

Questo articolo descrive i criteri password e i requisiti di complessità associati agli account utente archiviati nel tenant di Azure Active Directory (Azure AD).

## <a name="administrator-password-policy-differences"></a>Differenze tra i criteri password degli amministratori

Microsoft applica un criterio per la reimpostazione della password predefinita complessa a *due gate* per i ruoli di amministratore di Azure. 

Con il criterio a due gate, gli amministratori non hanno la possibilità di usare le domande di sicurezza.

 Un criterio a due gate richiede due tipi di dati di autenticazione, ad esempio un indirizzo di posta elettronica *e* un numero di telefono. Un criterio a due gate si applica nelle circostanze seguenti:

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
  
* Se sono trascorsi 30 giorni per una sottoscrizione di valutazione

  oppure

* È presente un dominio personale, ad esempio contoso.com

  oppure

* Identità sincronizzate da Azure AD Connect nella directory locale

### <a name="exceptions"></a>Eccezioni
Un criterio a un gate richiede un tipo di dati di autenticazione, ad esempio un indirizzo di posta elettronica *o* un numero di telefono. Un criterio a un gate si applica nelle circostanze seguenti:

* Non sono ancora trascorsi i primi 30 giorni per una sottoscrizione di valutazione

  oppure

* Non è presente un dominio personale (*.onmicrosoft.com) 

  e 

  Azure AD Connect non sincronizza le identità


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri UserPrincipalName che si applicano a tutti gli account utente

A ogni account utente che deve eseguire l'accesso ad Azure AD è necessario che sia associato un valore di attributo UPN. La tabella seguente descrive i criteri che si applicano sia agli account utente Active Directory locali sincronizzati con il cloud sia agli account utente di reti solo cloud:

| Proprietà | Requisiti di UserPrincipalName |
| --- | --- |
| Caratteri consentiti |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caratteri non consentiti |<ul> <li>Qualsiasi carattere "@" che non separa il nome utente dal dominio.</li> <li>Non può contenere un punto "." subito prima del simbolo "@"</li></ul> |
| Vincoli di lunghezza |<ul> <li>La lunghezza totale non deve superare i 113 caratteri</li><li>Prima del simbolo "@" possono esserci al massimo 64 caratteri</li><li>Dopo il simbolo "@" possono esserci al massimo 48 caratteri</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Criteri password che si applicano solo agli account utente del cloud

La tabella seguente descrive le impostazioni disponibili per i criteri password che possono essere applicate agli account utente creati e gestiti in Azure AD:

| Proprietà | Requisiti |
| --- | --- |
| Caratteri consentiti |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caratteri non consentiti |<ul><li>Caratteri Unicode.</li><li>Spazi.</li><li> Solo password complesse: non possono contenere un punto "." subito prima del simbolo "@".</li></ul> |
| Restrizioni per le password |<ul><li>Devono avere un numero di caratteri compreso tra 8 e 16.</li><li>Solo password complesse: è necessario soddisfare tre dei quattro requisiti seguenti:<ul><li>Caratteri minuscoli.</li><li>Caratteri maiuscoli.</li><li>Numeri (da 0 a 9).</li><li>Simboli (vedere le restrizioni per le password elencate sopra).</li></ul></li></ul> |
| Durata di validità della password |<ul><li>Valore predefinito: **90** giorni.</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy` del modulo di Azure Active Directory per Windows PowerShell.</li></ul> |
| Notifica della scadenza della password |<ul><li>Valore predefinito: **14** giorni (prima della scadenza della password).</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Scadenza della password |<ul><li>Valore predefinito: **false** giorni (indica che la scadenza password è abilitata).</li><li>Il valore può essere configurato per singoli account utente con il cmdlet `Set-MsolUser`.</li></ul> |
| Cronologia delle modifiche della password |Al cambio della password, l'utente *non può* usare di nuovo la password più recente. |
| Cronologia delle reimpostazioni della password | Alla reimpostazione di una password dimenticata, l'utente *può* usare di nuovo la password più recente. |
| Blocco dell'account |Dopo 10 tentativi di accesso non riusciti a causa della password errata, l'utente viene bloccato per un minuto. Altri tentativi di accesso non riusciti bloccano l'utente per periodi sempre più lunghi. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Impostare i criteri di scadenza della password in Azure AD

In qualità di amministratore globale per un servizio cloud Microsoft, è possibile usare il Modulo di Microsoft Azure AD per Windows PowerShell per impostare password utente che non scadono. È inoltre possibile usare cmdlet Windows PowerShell per rimuovere la configurazione senza scadenza, o per vedere quali password utente vengono impostate in modo da non scadere mai. 

Queste indicazioni si applicano ad altri provider, ad esempio Intune e Office 365, che si basano sempre su Azure AD per i servizi di identità e directory. La scadenza della password è l'unica parte dei criteri a poter essere modificata.

> [!NOTE]
> Solo le password degli account utente per cui non è usata la sincronizzazione della directory possono essere configurate per non scadere. Per altre informazioni sulla sincronizzazione delle directory, vedere [Connettere AD con Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Impostare o verificare i criteri password con PowerShell

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Al termine dell'installazione è possibile eseguire la procedura seguente per configurare ogni campo.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Come controllare i criteri di scadenza per una password
1. Connettersi a Windows PowerShell usando le credenziali aziendali di amministratore.
2. Eseguire uno di questi comandi:

   * Per vedere se la password di un singolo utente è impostata in modo da non scadere mai, eseguire il cmdlet seguente usando l'UPN (ad esempio, *aprilr@contoso.onmicrosoft.com*) o l'ID dell'utente che si desidera controllare: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Per visualizzare l'impostazione **La password non scade mai** per tutti gli utenti, eseguire il cmdlet seguente: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Impostare una scadenza della password

1. Connettersi a Windows PowerShell usando le credenziali aziendali di amministratore.
2. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che scada, eseguire il cmdlet seguente usando l'UPN o l'ID dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, usare il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza

1. Connettersi a Windows PowerShell usando le credenziali aziendali di amministratore.
2. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che non scada mai, eseguire il cmdlet seguente usando l'UPN o l'ID dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Per impostare le password degli utenti in un'organizzazione in modo che non scadano mai, eseguire il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Le password impostate su `-PasswordNeverExpires $true` diventano comunque obsolete in base all'attributo `pwdLastSet`. Dopo che sono trascorsi più di 90 giorni, le password scadono anche se sono state impostate in modo da non scadere mai. In base all'attributo `pwdLastSet`, se si modifica la scadenza in `-PasswordNeverExpires $false`, all'accesso successivo l'utente sarà tenuto a cambiare tutte le password che hanno un attributo `pwdLastSet` che risale a più di 90 giorni prima. Questa modifica può riguardare un numero elevato di utenti. 

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti offrono altre informazioni sull'uso della reimpostazione della password con Azure AD:

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
* [Domande sulle licenze](active-directory-passwords-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)
