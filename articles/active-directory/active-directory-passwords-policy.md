---
title: Criteri - Reimpostazione password self-service di Azure AD | Microsoft Docs
description: Opzioni dei criteri di reimpostazione password self-service di Azure AD
services: active-directory
keywords: gestione delle password in Active Directory, gestione delle password, reimpostazione password self-service di Azure AD
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 0c586692720512d2822e67994e22e1f6d50fd921
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restrizioni e criteri password in Azure Active Directory

Questo articolo descrive i criteri password e i requisiti di complessità associati agli account utente archiviati nel tenant di Azure AD.

## <a name="administrator-password-policy-differences"></a>Differenze tra i criteri password degli amministratori

Microsoft consente di applicare criteri avanzati di reimpostazione della password con **doppio controllo** predefiniti per tutti i ruoli di amministratore di Azure, ad esempio amministratore globale, amministratore del supporto tecnico, amministratore password e così via.

In questo modo gli amministratori sono esentati dall'uso di domande di sicurezza e viene richiesta l'applicazione di quanto segue.

I criteri a doppio controllo, che richiedono due elementi di dati di autenticazione (indirizzo e-mail **e** numero di telefono), si applicano nelle situazioni seguenti

* Tutti i ruoli di amministratore di Azure
  * Amministratore del supporto tecnico
  * Amministratore del supporto servizio
  * Amministratore fatturazione
  * Supporto partner - Livello 1
  * Supporto partner - Livello 2
  * Amministratore del servizio Exchange
  * Amministratore del servizio Lync
  * Amministratore account utente
  * Writer di directory
  * Amministratore globale/amministratore aziendale
  * Amministratore del servizio SharePoint
  * Amministratore di conformità
  * Amministratore di applicazioni
  * Amministratore della sicurezza
  * Amministratore dei ruoli con privilegi
  * Amministratore del servizio Intune
  * Amministratore del servizio proxy di applicazione
  * Amministratore del servizio CRM
  * Amministratore del servizio Power BI
  
* 30 giorni trascorsi per una versione di valutazione **OPPURE**
* Presenza di un dominio personalizzato (contoso.com) **OPPURE**
* Identità sincronizzate da Azure AD Connect nella directory locale

### <a name="exceptions"></a>Eccezioni
I criteri a un controllo, che richiedono un elemento di dati di autenticazione (indirizzo e-mail **o** numero di telefono), si applicano nelle situazioni seguenti

* Primi 30 giorni di una versione di valutazione **OPPURE**
* Dominio personalizzato non presente (*.onmicrosoft.com) **E** identità non sincronizzate da Azure AD


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri UserPrincipalName che si applicano a tutti gli account utente

A ogni account utente che deve eseguire l'accesso ad Azure AD è necessario che sia associato un valore di attributo UPN. La tabella seguente illustra i criteri che si applicano sia agli account utente di Active Directory presenti in locale sincronizzati nel cloud sia agli account utente presenti solo nel cloud.

| Proprietà | Requisiti di UserPrincipalName |
| --- | --- |
| Caratteri consentiti |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caratteri non consentiti |<ul> <li>Qualsiasi carattere "@" che non separa il nome utente dal dominio.</li> <li>Non può contenere un punto "." subito prima del simbolo "@"</li></ul> |
| Vincoli di lunghezza |<ul> <li>La lunghezza totale non deve superare i 113 caratteri</li><li>64 caratteri prima del simbolo "@"</li><li>48 caratteri dopo il simbolo "@"</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Criteri password che si applicano solo agli account utente del cloud

La tabella seguente descrive le impostazioni di criteri password disponibili che possono essere applicate agli account utente creati e gestiti in Azure AD.

| Proprietà | Requisiti |
| --- | --- |
| Caratteri consentiti |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caratteri non consentiti |<ul><li>Caratteri Unicode</li><li>Spazi</li><li> **Solo password complesse**: non possono contenere un punto "." subito prima del simbolo "@".</li></ul> |
| Restrizioni per le password |<ul><li>minimo 8 caratteri e massimo 16 caratteri</li><li>**Solo le password complesse**: è necessario rispettare 3 su 4 delle istruzioni seguenti:<ul><li>Caratteri minuscoli</li><li>Caratteri maiuscoli</li><li>Numeri (0-9)</li><li>Simboli (vedere le restrizioni per le password sopra citate)</li></ul></li></ul> |
| Durata di validità della password |<ul><li>Valore predefinito: **90** giorni </li><li>Il valore è configurabile mediante il cmdlet Set-MsolPasswordPolicy del modulo di Azure Active Directory per Windows PowerShell.</li></ul> |
| Notifica della scadenza della password |<ul><li>Valore predefinito: **14** giorni (prima della scadenza della password)</li><li>Il valore è configurabile tramite il cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Scadenza della password |<ul><li>Valore predefinito: **false** giorni (indica che la scadenza password è abilitata) </li><li>Il valore può essere configurato per singoli account utente mediante il cmdlet Set-MsolUser. </li></ul> |
| Cronologia di **modifica** della password |L'ultima password **non può** essere riusata alla **modifica** della password. |
| Cronologia di **reimpostazione** della password | L'ultima password **può** essere riusata alla **reimpostazione** della password dimenticata. |
| Blocco account |Dopo 10 tentativi di accesso non riusciti (password errata), l'utente verrà bloccato per un minuto. Altri tentativi di accesso non riusciti bloccano l'utente per periodi sempre più lunghi. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Impostare i criteri di scadenza della password in Azure Active Directory

In qualità di amministratore globale per un servizio cloud Microsoft, è possibile usare il Modulo di Microsoft Azure Active Directory per Windows PowerShell per impostare password utente che non scadono. È inoltre possibile utilizzare cmdlet Windows PowerShell per rimuovere la configurazione senza scadenza, o per vedere quali password utente vengono impostate senza scadenza. Queste indicazioni si applicano ad altri provider, ad esempio Microsoft Intune e Office 365, che si basano sempre su Microsoft Azure Active Directory per i servizi di identità e directory.

> [!NOTE]
> Solo le password degli account utente per cui non è usata la sincronizzazione della directory possono essere configurate per non scadere. Per altre informazioni sulla sincronizzazione delle directory, vedere [Integrare le directory locali con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Impostare o verificare i criteri password tramite PowerShell

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Al termine dell'installazione è possibile eseguire la procedura seguente per configurare ogni campo.

### <a name="how-to-check-expiration-policy-for-a-password"></a>Come controllare i criteri di scadenza per una password
1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.
2. Eseguire uno di questi comandi:

   * Per vedere se la password di un singolo utente è impostata per non scadere mai, eseguire il cmdlet seguente usando il nome principale utente (UPN) (ad esempio, aprilr@contoso.onmicrosoft.com) o l'ID utente dell'utente che si desidera controllare: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Per visualizzare l'impostazione "Nessuna scadenza per la Password" per tutti gli utenti, eseguire il cmdlet seguente: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Impostare una scadenza della password

1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.
2. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che scada, eseguire questo cmdlet usando il nome dell'entità utente (UPN) o l'ID dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, usare il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza

1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.
2. Eseguire uno di questi comandi:

   * Per impostare la password di un utente senza scadenza, eseguire il cmdlet seguente usando il nome principale utente (UPN) o l'ID utente dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Per impostare le password degli utenti in un'organizzazione in modo che non scadano mai, eseguire il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - Iniziare a usare la gestione self-service delle password di Azure AD 
* [**Licenze**](active-directory-passwords-licensing.md): configurare le licenze di Azure AD
* [**Dati** ](active-directory-passwords-data.md): informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Implementazione**](active-directory-passwords-best-practices.md): pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md): personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Reporting** ](active-directory-passwords-reporting.md): verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - Come Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande di maggiore interesse
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service

