---
title: Criteri di reimpostazione della password self-service - Azure Active Directory
description: Informazioni sulle diverse opzioni dei criteri di reimpostazione della password self-service Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 990d8ef275982b6d70c51819e47b33f543345023
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531276"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Criteri password e limitazioni dell'account in Azure Active Directory

In Azure Active Directory (Azure AD) sono presenti criteri password che definiscono le impostazioni come la complessità, la lunghezza o l'età delle password. Esiste anche un criterio che definisce i caratteri accettabili e la lunghezza per i nomi utente.

Quando si usa la reimpostazione della password self-service (SSPR) per modificare o reimpostare una password in Azure AD, i criteri password vengono controllati. Se la password non soddisfa i requisiti dei criteri, all'utente viene richiesto di riprovare. Gli amministratori di Azure hanno alcune restrizioni sull'uso di SSPR che sono diversi dagli account utente normali.

Questo articolo descrive le impostazioni dei criteri password e i requisiti di complessità associati agli account utente nel tenant di Azure AD e come è possibile usare PowerShell per controllare o impostare le impostazioni di scadenza delle password.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri nome utente

Ogni account che accede a Azure AD deve avere un valore di attributo nome dell'entità utente (UPN) univoco associato all'account. In ambienti ibridi con un ambiente di Active Directory Domain Services locale (AD DS) sincronizzato con Azure AD tramite Azure AD Connect, per impostazione predefinita il Azure AD UPN è impostato sull'UPN locale.

La tabella seguente descrive i criteri del nome utente che si applicano sia agli account Active Directory Domain Services locali che vengono sincronizzati con Azure AD e per gli account utente solo cloud creati direttamente in Azure AD:

| Proprietà | Requisiti di UserPrincipalName |
| --- | --- |
| Caratteri consentiti |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caratteri non consentiti |<ul> <li>Qualsiasi carattere "\@\" che non separa il nome utente dal dominio.</li> <li>Non può contenere un punto "." subito prima del simbolo "\@\"</li></ul> |
| Vincoli di lunghezza |<ul> <li>La lunghezza totale non deve superare i 113 caratteri</li><li>Prima del simbolo "\@\" possono essere presenti al massimo 64 caratteri</li><li>Dopo il simbolo "\@\" possono essere presenti al massimo 48 caratteri</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Criteri password Azure AD

I criteri password vengono applicati a tutti gli account utente creati e gestiti direttamente in Azure AD. Questo criterio password non può essere modificato, anche se è possibile [configurare password vietate personalizzate per Azure ad la protezione delle password](tutorial-configure-custom-password-protection.md).

Il criterio password non si applica agli account utente sincronizzati da un ambiente Active Directory Domain Services locale usando Azure AD Connect, a meno che non si abiliti EnforceCloudPasswordPolicyForPasswordSyncedUsers.

Sono definite le seguenti opzioni relative ai criteri password:

| Proprietà | Requisiti |
| --- | --- |
| Caratteri consentiti |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [] {} &#124; \:',. ? / \` ~ " ( ) ;</li> <li>spazio vuoto</li></ul> |
| Caratteri non consentiti | Caratteri Unicode. |
| Restrizioni per le password |<ul><li>Un minimo di 8 caratteri e un massimo di 256 caratteri.</li><li>è necessario soddisfare tre dei quattro requisiti seguenti:<ul><li>Caratteri minuscoli.</li><li>Caratteri maiuscoli.</li><li>Numeri (da 0 a 9).</li><li>Simboli (vedere le restrizioni per le password elencate sopra).</li></ul></li></ul> |
| Durata scadenza password (validità massima password) |<ul><li>Valore predefinito: **90** giorni.</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy` del modulo di Azure Active Directory per Windows PowerShell.</li></ul> |
| Notifica della scadenza della password (quando gli utenti ricevono una notifica della scadenza della password) |<ul><li>Valore predefinito: **14** giorni (prima della scadenza della password).</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Scadenza password (Let password non scadono mai) |<ul><li>Valore predefinito: **false** (indica che la password ha una data di scadenza).</li><li>Il valore può essere configurato per singoli account utente con il cmdlet `Set-MsolUser`.</li></ul> |
| Cronologia delle modifiche della password | Al cambio della password, l'utente *non può* usare di nuovo la password più recente. |
| Cronologia delle reimpostazioni della password | Alla reimpostazione di una password dimenticata, l'utente *può* usare di nuovo la password più recente. |
| Blocco dell'account | Dopo 10 tentativi di accesso non riusciti a causa della password errata, l'utente viene bloccato per un minuto. Altri tentativi di accesso non riusciti bloccano l'utente per periodi sempre più lunghi. Il [blocco intelligente](howto-password-smart-lockout.md) tiene traccia degli ultimi tre hash delle password non validi per evitare l'incremento del contatore dei blocchi per la stessa password. Se un utente immette la stessa password errata più volte, questo comportamento non comporterà il blocco dell'account. |

## <a name="administrator-reset-policy-differences"></a>Differenze dei criteri di reimpostazione degli amministratori

Per impostazione predefinita, gli account amministratore sono abilitati per la reimpostazione della password self-service e viene applicato un criterio di reimpostazione della password a *due Gate* predefinito sicuro. Questo criterio può essere diverso da quello definito per gli utenti e questo criterio non può essere modificato. È necessario verificare sempre la funzionalità di reimpostazione della password come utente senza ruoli di amministratore di Azure assegnati.

Con il criterio a due gate, gli amministratori non hanno la possibilità di usare le domande di sicurezza.

Il criterio a due gate richiede tre tipi di dati di autenticazione, ad esempio un indirizzo di posta elettronica, un'app di autenticazione o un numero di telefono. Un criterio a due gate si applica nelle circostanze seguenti:

* Sono interessati tutti i ruoli di amministratore di Azure seguenti:
  * Amministratore dell'help desk
  * Amministratore del supporto per il servizio
  * Amministratore fatturazione
  * Supporto partner - Livello 1
  * Supporto partner - Livello 2
  * Amministratori di Exchange
  * Amministratore di Skype for Business
  * Amministratore utenti
  * Writer di directory
  * Amministratore globale o amministratore aziendale
  * Amministratore di SharePoint
  * Amministratore di conformità
  * Amministratore di applicazioni
  * Amministratore della sicurezza
  * Amministratore dei ruoli con privilegi
  * Amministratore di Intune
  * Amministratore del servizio proxy di applicazione
  * Amministratore di Dynamics 365
  * Amministratore del servizio Power BI
  * Amministratore dell'autenticazione
  * Amministratore di autenticazione con privilegi

* Se sono trascorsi 30 giorni per una sottoscrizione di valutazione o
* Un dominio personalizzato è stato configurato per il tenant di Azure AD, ad esempio *contoso.com*; o
* Identità sincronizzate da Azure AD Connect nella directory locale

È possibile disabilitare l'uso di SSPR per gli account Administrator usando il cmdlet di PowerShell [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) . Il `-SelfServePasswordResetEnabled $False` parametro Disabilita SSPR per gli amministratori.

### <a name="exceptions"></a>Eccezioni

Un criterio a un gate richiede un tipo di dati di autenticazione, ad esempio un indirizzo di posta elettronica o un numero di telefono. Un criterio a un gate si applica nelle circostanze seguenti:

* Non sono ancora trascorsi i primi 30 giorni per una sottoscrizione di valutazione o
* Un dominio personalizzato non è stato configurato per il tenant di Azure AD, quindi usa il valore predefinito **. onmicrosoft.com*. Il dominio predefinito **. onmicrosoft.com* non è consigliato per l'uso in ambiente di produzione; e
* Azure AD Connect non sincronizza le identità

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Criteri di scadenza delle password

Un amministratore *globale* o un *amministratore utente* può utilizzare il [modulo di Microsoft Azure ad per Windows PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) per impostare le password utente in modo che non scadano.

È anche possibile usare i cmdlet di PowerShell per rimuovere la configurazione never-expires o per vedere quali password utente sono impostate in modo da non scadere mai.

Queste linee guida si applicano ad altri provider, ad esempio Intune e Microsoft 365, che si basano anche su Azure AD per i servizi di identità e directory. La scadenza della password è l'unica parte dei criteri a poter essere modificata.

> [!NOTE]
> Solo le password per gli account utente che non sono sincronizzati tramite Azure AD Connect possono essere configurate in modo da non scadere. Per altre informazioni sulla sincronizzazione delle directory, vedere [Connettere AD con Azure AD](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Impostare o verificare i criteri password con PowerShell

Per iniziare, [scaricare e installare il modulo Azure ad PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) e [connetterlo al tenant di Azure ad](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples).

Dopo l'installazione del modulo, attenersi alla procedura seguente per completare ogni attività in base alle esigenze.

### <a name="check-the-expiration-policy-for-a-password"></a>Controllare i criteri di scadenza per una password

1. Aprire un prompt di PowerShell e [connettersi al tenant di Azure ad](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) usando un account amministratore *globale* o *amministratore utente* .
1. Eseguire uno dei comandi seguenti per un singolo utente o per tutti gli utenti:

   * Per verificare se la password di un singolo utente è impostata in modo da non scadere mai, eseguire il cmdlet seguente. Sostituire `<user ID>` con l'ID utente dell'utente che si vuole controllare, ad esempio *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Per visualizzare l'impostazione **La password non scade mai** per tutti gli utenti, eseguire il cmdlet seguente: 

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Impostare una scadenza della password

1. Aprire un prompt di PowerShell e [connettersi al tenant di Azure ad](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) usando un account amministratore *globale* o *amministratore utente* .
1. Eseguire uno dei comandi seguenti per un singolo utente o per tutti gli utenti:

   * Per impostare la password di un utente in modo che scada la password, eseguire il cmdlet seguente. Sostituire `<user ID>` con l'ID utente dell'utente che si vuole controllare, ad esempio *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, usare il cmdlet seguente: 

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza

1. Aprire un prompt di PowerShell e [connettersi al tenant di Azure ad](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) usando un account amministratore *globale* o *amministratore utente* .
1. Eseguire uno dei comandi seguenti per un singolo utente o per tutti gli utenti:

   * Per impostare la password di un utente in modo che non scada mai, eseguire il cmdlet seguente. Sostituire `<user ID>` con l'ID utente dell'utente che si vuole controllare, ad esempio *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Per impostare le password di tutti gli utenti di un'organizzazione in modo che non scadano mai, eseguire il seguente cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Le password impostate su `-PasswordPolicies DisablePasswordExpiration` diventano comunque obsolete in base all'attributo `pwdLastSet`. In base all'attributo `pwdLastSet`, se si modifica la scadenza in `-PasswordPolicies None`, all'accesso successivo l'utente sarà tenuto a cambiare tutte le password che hanno un attributo `pwdLastSet` che risale a più di 90 giorni prima. Questa modifica può riguardare un numero elevato di utenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare SSPR, vedere [esercitazione: consentire agli utenti di sbloccare l'account o reimpostare le password con Azure Active Directory la reimpostazione della password self-service](tutorial-enable-sspr.md).

Se gli utenti hanno problemi con SSPR, vedere risolvere i problemi di [reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
