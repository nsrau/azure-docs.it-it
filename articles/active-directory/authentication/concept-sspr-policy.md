---
title: Criteri di reimpostazione della password self-service - Azure Active Directory
description: Informazioni sulle diverse opzioni dei criteri di reimpostazione della password self-service di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393044"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Criteri e restrizioni di reimpostazione della password self-service in Azure Active Directory

Questo articolo descrive i criteri password e i requisiti di complessità associati agli account utente nel tenant di Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Differenze dei criteri di reimpostazione degli amministratori

Microsoft applica un criterio di **reimpostazione della password a *due porte* predefinito per qualsiasi ruolo**di amministratore di Azure. Questo criterio potrebbe essere diverso da quello definito per gli utenti e non può essere modificato. È necessario verificare sempre la funzionalità di reimpostazione della password come utente senza ruoli di amministratore di Azure assegnati.

Con il criterio a doppio controllo, gli **amministratori non hanno la possibilità di usare le domande di sicurezza**.

Il criterio a due gate richiede tre tipi di dati di autenticazione, ad esempio un *indirizzo di posta elettronica*, un'*app di autenticazione* o un *numero di telefono*. Un criterio a due gate si applica nelle circostanze seguenti:

* Sono interessati tutti i ruoli di amministratore di Azure seguenti:
  * Amministratore dell'help desk
  * Amministratore del supporto per il servizio
  * Amministratore fatturazione
  * Supporto partner - Livello 1
  * Supporto partner - Livello 2
  * Amministratore di Exchange
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
  * Amministratore dell'autenticazione con privilegi

* Se sono trascorsi 30 giorni per una sottoscrizione di valutazione o
* È stato configurato un dominio personalizzato per il tenant di Azure AD, ad esempio *contoso.com;* O
* Identità sincronizzate da Azure AD Connect nella directory locale

### <a name="exceptions"></a>Eccezioni

Un criterio a un gate richiede un tipo di dati di autenticazione, ad esempio un indirizzo di posta elettronica o un numero di telefono. Un criterio a un gate si applica nelle circostanze seguenti:

* Non sono ancora trascorsi i primi 30 giorni per una sottoscrizione di valutazione o
* Un dominio personalizzato non è stato configurato per il tenant di Azure AD, pertanto viene usato il valore predefinito ,*ovvero onmicrosoft.com*. Il dominio predefinito con*estensione onmicrosoft.com* non è consigliato per l'utilizzo in produzione. E
* Azure AD Connect non sincronizza le identità

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri UserPrincipalName che si applicano a tutti gli account utente

A ogni account utente che deve eseguire l'accesso ad Azure AD è necessario che sia associato un valore di attributo UPN. Nella tabella seguente vengono descritti i criteri che si applicano sia agli account utente di Servizi di dominio Active Directory locali sincronizzati con il cloud e agli account utente solo cloud:

| Proprietà | Requisiti di UserPrincipalName |
| --- | --- |
| Caratteri consentiti |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Caratteri non consentiti |<ul> <li>Qualsiasi carattere "\@\" che non separa il nome utente dal dominio.</li> <li>Non può contenere un punto "." subito prima del simbolo "\@\"</li></ul> |
| Vincoli di lunghezza |<ul> <li>La lunghezza totale non deve superare i 113 caratteri</li><li>Prima del simbolo "\@\" possono essere presenti al massimo 64 caratteri</li><li>Dopo il simbolo "\@\" possono essere presenti al massimo 48 caratteri</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Criteri password che si applicano solo agli account utente del cloud

The following table describes the password policy settings applied to user accounts that are created and managed in Azure AD:

| Proprietà | Requisiti |
| --- | --- |
| Caratteri consentiti |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! &#124; : ', . ? / \`~ " ( ) ;</li> <li>spazio vuoto</li></ul> |
| Caratteri non consentiti | Caratteri Unicode. |
| Restrizioni per le password |<ul><li>Un minimo di 8 caratteri e un massimo di 256 caratteri.</li><li>è necessario soddisfare tre dei quattro requisiti seguenti:<ul><li>Caratteri minuscoli.</li><li>Caratteri maiuscoli.</li><li>Numeri (da 0 a 9).</li><li>Simboli (vedere le restrizioni per le password elencate sopra).</li></ul></li></ul> |
| Durata scadenza password (validità massima password) |<ul><li>Valore predefinito: **90** giorni.</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy` del modulo di Azure Active Directory per Windows PowerShell.</li></ul> |
| Notifica di scadenza della password (quando gli utenti ricevono una notifica di scadenza della password) |<ul><li>Valore predefinito: **14** giorni (prima della scadenza della password).</li><li>Il valore può essere configurato con il cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Scadenza password (non lasciare che le password scadano mai) |<ul><li>Valore predefinito: **false** (indica che la password ha una data di scadenza).</li><li>Il valore può essere configurato per singoli account utente con il cmdlet `Set-MsolUser`.</li></ul> |
| Cronologia delle modifiche della password | Al cambio della password, l'utente *non può* usare di nuovo la password più recente. |
| Cronologia delle reimpostazioni della password | Alla reimpostazione di una password dimenticata, l'utente *può* usare di nuovo la password più recente. |
| Blocco dell'account | Dopo 10 tentativi di accesso non riusciti a causa della password errata, l'utente viene bloccato per un minuto. Altri tentativi di accesso non riusciti bloccano l'utente per periodi sempre più lunghi. Il [blocco intelligente](howto-password-smart-lockout.md) tiene traccia degli ultimi tre hash delle password non validi per evitare l'incremento del contatore dei blocchi per la stessa password. Se qualcuno immette la stessa password errata più volte, questo comportamento non causerà la chiusura dell'account. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Impostare i criteri di scadenza della password in Azure AD

Un *amministratore globale* o un amministratore *utente* per un servizio cloud Microsoft può usare il modulo Microsoft Azure AD per *Windows PowerShell per* impostare le password utente in modo che non scadano. È inoltre possibile usare cmdlet Windows PowerShell per rimuovere la configurazione senza scadenza, o per vedere quali password utente vengono impostate in modo da non scadere mai.

Queste indicazioni si applicano ad altri provider, ad esempio Intune e Office 365, che si basano sempre su Azure AD per i servizi di identità e directory. La scadenza della password è l'unica parte dei criteri a poter essere modificata.

> [!NOTE]
> Solo le password degli account utente per cui non è usata la sincronizzazione della directory possono essere configurate per non scadere. Per altre informazioni sulla sincronizzazione delle directory, vedere [Connettere AD con Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Impostare o verificare i criteri password con PowerShell

Per iniziare, [scaricare e installare il modulo Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Dopo aver installato il modulo, utilizzare la procedura seguente per configurare ogni campo.

### <a name="check-the-expiration-policy-for-a-password"></a>Controllare i criteri di scadenza per una password

1. Connettersi a Windows PowerShell utilizzando le credenziali dell'amministratore utente o dell'amministratore aziendale.
1. Eseguire uno dei comandi seguenti:

   * Per verificare se la password di un singolo utente è impostata in modo che non scada mai, eseguire il cmdlet seguente utilizzando l'UPN (ad esempio, *aprilr\@contoso.onmicrosoft.com*) o l'ID utente dell'utente che si desidera controllare:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Per visualizzare l'impostazione **La password non scade mai** per tutti gli utenti, eseguire il cmdlet seguente: 

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Impostare una scadenza della password

1. Connettersi a Windows PowerShell utilizzando le credenziali dell'amministratore utente o dell'amministratore aziendale.
1. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che scada, eseguire il cmdlet seguente usando l'UPN o l'ID dell'utente: 

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, usare il cmdlet seguente: 

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza

1. Connettersi a Windows PowerShell utilizzando le credenziali dell'amministratore utente o dell'amministratore aziendale.
1. Eseguire uno di questi comandi:

   * Per impostare la password di un utente in modo che non scada mai, eseguire il cmdlet seguente usando l'UPN o l'ID dell'utente: 

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

Per iniziare a usare SSPR, vedere [Esercitazione: Consentire agli utenti di sbloccare l'account o reimpostare le password usando la reimpostazione della password self-service](tutorial-enable-sspr.md)di Azure Active Directory.

Se l'utente o gli utenti hanno problemi con SSPR, vedere Risolvere i problemi relativi alla [reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
