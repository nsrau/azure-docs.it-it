---
title: Restrizioni e criteri password in Azure Active Directory | Microsoft Docs
description: Descrive i criteri che si applicano alle password in Azure Active Directory, inclusi i caratteri consentiti, la lunghezza e la scadenza
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e7cae5cf-e0ee-467b-9a90-db6fdf56cd52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: fc73b667112fe35473872c33bc98423a424c5c65
ms.openlocfilehash: d6bee5afea6afcd756b53f41baef5dd984423c0a
ms.lasthandoff: 02/17/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Restrizioni e criteri password in Azure Active Directory
Questo articolo descrive i criteri password e i requisiti di complessità associati agli account utente archiviati nella directory di Azure AD.

> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
>
>

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Criteri UserPrincipalName che si applicano a tutti gli account utente
Ogni account utente che deve effettuare l'accesso nel sistema di autenticazione di Azure AD deve essere associato a un valore di attributo nome dell'entità utente (UPN) univoco. La tabella seguente descrive i criteri che si applicano sia agli account utente che originano da Active Directory locale (sincronizzati con il cloud) sia agli account utente solo del cloud.

| Proprietà | Requisiti di UserPrincipalName |
| --- | --- |
| Caratteri consentiti |<ul> <li>A-Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caratteri non consentiti |<ul> <li>Qualsiasi '@' carattere che non separa il nome utente dal dominio.</li> <li>Non può contenere un punto "." subito prima del simbolo '@'</li></ul> |
| Vincoli di lunghezza |<ul> <li>La lunghezza totale non deve superare i 113 caratteri</li><li>64 caratteri prima del simbolo ‘@’</li><li>48 caratteri dopo il simbolo ‘@’</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Criteri password che si applicano solo agli account utente del cloud
La tabella seguente descrive le impostazioni di criteri password disponibili che possono essere applicati agli account utente creati e gestiti in Azure AD.

| Proprietà | Requisiti |
| --- | --- |
| Caratteri consentiti |<ul><li>A-Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caratteri non consentiti |<ul><li>Caratteri Unicode</li><li>Spazi</li><li> **Solo password complesse**: non possono contenere un punto "." subito prima del simbolo '@'</li></ul> |
| Restrizioni per le password |<ul><li>minimo 8 caratteri e massimo 16 caratteri</li><li>**Solo le password complesse**: è necessario rispettare 3 su 4 delle istruzioni seguenti:<ul><li>Caratteri minuscoli</li><li>Caratteri maiuscoli</li><li>Numeri (0-9)</li><li>Simboli (vedere le restrizioni per le password sopra citate)</li></ul></li></ul> |
| Durata di validità della password |<ul><li>Valore predefinito: **90** giorni </li><li>Il valore è configurabile mediante il cmdlet Set-MsolPasswordPolicy del modulo di Azure Active Directory per Windows PowerShell.</li></ul> |
| Notifica della scadenza della password |<ul><li>Valore predefinito: **14** giorni (prima della scadenza della password)</li><li>Il valore è configurabile tramite il cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Scadenza della password |<ul><li>Valore predefinito: **false** giorni (indica che la scadenza password è abilitata) </li><li>Il valore può essere configurato per singoli account utente mediante il cmdlet Set-MsolUser. </li></ul> |
| Cronologia di **modifica** della password |L'ultima password **non può** essere riusata alla **modifica** della password. |
| Cronologia di **reimpostazione** della password | L'ultima password **può** essere riusata alla **reimpostazione** della password dimenticata. |
| Blocco account |Dopo 10 tentativi di accesso non riusciti (password errata), l'utente verrà bloccato per un minuto. Ulteriori tentativi di accesso non riusciti bloccheranno l'utente per periodi sempre più lunghi. |

## <a name="next-steps"></a>Passaggi successivi
* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
* [Gestire le password da qualsiasi posizione](active-directory-passwords.md)
* [Funzionamento di Gestione delle password](active-directory-passwords-how-it-works.md)
* [Introduzione alla gestione delle password](active-directory-passwords-getting-started.md)
* [Personalizzare la gestione delle password](active-directory-passwords-customize.md)
* [Procedure consigliate per la gestione delle password](active-directory-passwords-best-practices.md)
* [Come ottenere informazioni dettagliate con i report di gestione delle password](active-directory-passwords-get-insights.md)
* [Domande frequenti sulla gestione delle password](active-directory-passwords-faq.md)
* [Risolvere i problemi relativi alla gestione delle password](active-directory-passwords-troubleshoot.md)
* [Altre informazioni](active-directory-passwords-learn-more.md)

