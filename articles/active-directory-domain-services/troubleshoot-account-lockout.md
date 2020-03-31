---
title: Risolvere i problemi di blocco degli account in Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come risolvere i problemi comuni che causano il blocco degli account utente in Servizi di dominio Azure Active Directory.Learn how to troubleshoot common problems that cause user accounts to be locked out in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 2e274aa353f6c3e485ae10a6a67ee2940eb88b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246320"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Risolvere i problemi di blocco degli account con un dominio gestito di Servizi di dominio Azure ADTroubleshoot account lockout problems with an Azure AD Domain Services managed domain

Per evitare ripetuti tentativi di accesso dannosi, Azure AD DS blocca gli account dopo una soglia definita. Il blocco dell'account può verificarsi anche accidentalmente senza un incidente di attacco di accesso. Ad esempio, se un utente immette ripetutamente la password errata o un servizio tenta di utilizzare una vecchia password, l'account viene bloccato.

In questo articolo sulla risoluzione dei problemi vengono descritti i motivi per cui si verificano i blocchi degli account e come configurare il comportamento e come esaminare i controlli di sicurezza per risolvere gli eventi di blocco.

## <a name="what-is-an-account-lockout"></a>Che cos'è il blocco dell'account?

Un account utente in Servizi di dominio Active Directory di Azure viene bloccato quando viene raggiunta una soglia definita per i tentativi di accesso non riusciti. Questo comportamento di blocco dell'account è progettato per proteggerti da ripetuti tentativi di accesso con forza bruta che possono indicare un attacco digitale automatizzato.

**Per impostazione predefinita, se sono presenti 5 tentativi con password errata in 2 minuti, l'account viene bloccato per 30 minuti.**

Le soglie di blocco account predefinite vengono configurate utilizzando criteri granulari per le password. Se si dispone di un set specifico di requisiti, è possibile ignorare queste soglie di blocco account predefinite. Tuttavia, non è consigliabile aumentare i limiti di soglia per tentare di ridurre il numero di blocchi degli account. Risolvere innanzitutto i problemi relativi all'origine del comportamento di blocco degli account.

### <a name="fine-grained-password-policy"></a>Criteri granulari per le password

I criteri granulari per le password (FGPP) consentono di applicare restrizioni specifiche per i criteri di blocco di password e account a utenti diversi in un dominio. FGPP influisce solo sugli utenti creati in Azure AD DS. Gli utenti cloud e gli utenti di dominio sincronizzati nel dominio gestito di Servizi di dominio Active Directory di Azure da Azure AD non sono interessati dai criteri password.

I criteri vengono distribuiti tramite l'associazione di gruppo nel dominio gestito di Servizi di dominio Active Directory di Azure e le modifiche apportate vengono applicate al successivo accesso utente. La modifica del criterio non sblocca un account utente già bloccato.

Per ulteriori informazioni sui criteri granulari per le password, vedere Configurare i criteri di blocco di [password e account.][configure-fgpp]

## <a name="common-account-lockout-reasons"></a>Motivi comuni di blocco degli account

I motivi più comuni per un account da bloccato, senza alcun intento o fattori dannosi, includono i seguenti scenari:

* **L'utente si è bloccato.**
    * Dopo una recente modifica della password, l'utente ha continuato a utilizzare una password precedente? Il criterio di blocco account predefinito di 5 tentativi non riusciti in 2 minuti può essere causato dall'utente che ritenta inavvertitamente una vecchia password.
* **C'è un'applicazione o un servizio che ha una vecchia password.**
    * Se un account viene utilizzato da applicazioni o servizi, tali risorse potrebbero tentare ripetutamente di accedere utilizzando una vecchia password. Questo comportamento fa sì che l'account venga bloccato.
    * Provare a ridurre al minimo l'utilizzo dell'account in più applicazioni o servizi diversi e registrare dove vengono utilizzate le credenziali. Se viene modificata la password di un account, aggiornare le applicazioni o i servizi associati di conseguenza.
* **La password è stata modificata in un ambiente diverso e la nuova password non è ancora stata sincronizzata.**
    * Se una password dell'account viene modificata all'esterno di Servizi di dominio Active Directory di Azure, ad esempio in un ambiente di Servizi di dominio Active Directory precedente, la sincronizzazione della password tramite Azure AD e Azure AD DS può richiedere alcuni minuti.
    * Un utente che tenta di accedere a una risorsa tramite Servizi di dominio Active Directory di Azure prima del completamento del processo di sincronizzazione delle password causa il blocco dell'account.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Risolvere i problemi relativi ai blocchi degli account con i controlli di sicurezzaTroubleshoot account lockouts with security audits

Per risolvere i problemi relativi agli eventi di blocco degli account e alla provenivienie, abilitare i controlli di sicurezza per Servizi di dominio Active Directory di Azure.To troubleshoot when account lockout events occur and where they's from, [enable security audits for Azure AD DS][security-audit-events]. Gli eventi di controllo vengono acquisiti solo dal momento in cui si abilita la funzionalità. In teoria, è consigliabile abilitare i controlli di sicurezza *prima* che si sia verificato un problema di blocco dell'account da risolvere. Se un account utente presenta ripetutamente problemi di blocco, è possibile abilitare i controlli di sicurezza pronti per la prossima volta che si verifica la situazione.

Dopo aver abilitato i controlli di sicurezza, nelle query di esempio seguenti viene illustrato come esaminare *Account Lockout Events*, codice *4740*.

Visualizzare tutti gli eventi di blocco dell'account degli ultimi sette giorni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Visualizzare tutti gli eventi di blocco dell'account per gli ultimi sette giorni per l'account denominato *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Visualizza tutti gli eventi di blocco degli account tra il 26 giugno 2019 alle ore 9.00. e 1 luglio 2019 mezzanotte, ordinato in ordine crescente per la data e l'ora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui criteri granulari per le password per regolare le soglie di blocco degli account, vedere Configurare i criteri di blocco di [password e account.][configure-fgpp]

Se si verificano ancora problemi durante l'aggiunta della macchina virtuale al dominio gestito di Azure AD DS, trovare la Guida e aprire un ticket di [supporto per Azure Active Directory.][azure-ad-support]

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
