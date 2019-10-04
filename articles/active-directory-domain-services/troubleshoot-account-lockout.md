---
title: Risolvere i problemi di blocco degli account in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni che comportano la blocco degli account utente in Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 29789f299f266c86d719d56cfbf8e262907f7264
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827085"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Risolvere i problemi di blocco degli account con un Azure AD Domain Services dominio gestito

Per evitare tentativi di accesso dannosi ripetuti, Azure AD DS blocca gli account dopo una soglia definita. Questo blocco degli account può verificarsi anche in caso di incidente senza una richiesta di attacco di accesso. Se, ad esempio, un utente immette ripetutamente la password errata o un servizio tenta di usare una vecchia password, l'account viene bloccato.

Questo articolo sulla risoluzione dei problemi illustra i motivi per cui si verificano i blocchi degli account e il modo in cui è possibile configurare il comportamento e come esaminare i controlli di sicurezza per risolvere i problemi relativi agli eventi di blocco.

## <a name="what-is-an-account-lockout"></a>Che cos'è il blocco di un account?

Un account utente in Azure AD DS viene bloccato quando viene soddisfatta una soglia definita per i tentativi di accesso non riusciti. Questo comportamento di blocco degli account è progettato per proteggere i tentativi di accesso con forza bruta ripetuta che potrebbero indicare un attacco digitale automatizzato.

**Per impostazione predefinita, se sono presenti 5 tentativi di accesso con password errata in 2 minuti, l'account viene bloccato per 30 minuti.**

Le soglie di blocco degli account predefinite vengono configurate utilizzando criteri granulari per le password. Se si dispone di un set specifico di requisiti, è possibile eseguire l'override di queste soglie di blocco degli account predefinite. Tuttavia, non è consigliabile aumentare i limiti di soglia per provare a ridurre il numero di blocchi degli account. Risolvere innanzitutto l'origine del comportamento di blocco dell'account.

### <a name="fine-grained-password-policy"></a>Criteri granulari per le password

I criteri granulari per le password (vengono mostrate) consentono di applicare restrizioni specifiche per i criteri di blocco di password e account a utenti diversi in un dominio. FGPP influiscono solo sugli utenti creati in Azure AD DS. Gli utenti del cloud e gli utenti del dominio sincronizzati nel dominio gestito di Azure AD DS da Azure AD non sono interessati dai criteri per le password.

I criteri vengono distribuiti tramite l'associazione di gruppo nel dominio gestito di Azure AD DS e tutte le modifiche apportate vengono applicate al successivo accesso dell'utente. La modifica dei criteri non comporta lo sblocco di un account utente già bloccato.

Per altre informazioni sui criteri granulari per le password, vedere [configurare i criteri per la password e il blocco degli account][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Motivi comuni di blocco degli account

I motivi più comuni per il blocco di un account, senza finalità o fattori dannosi, includono gli scenari seguenti:

* **L'utente si è bloccato.**
    * Dopo una modifica recente della password, l'utente ha continuato a usare una password precedente? Il criterio di blocco account predefinito di 5 tentativi non riusciti in 2 minuti può essere causato dall'utente che ha inavvertitamente ritentato una vecchia password.
* **Esiste un'applicazione o un servizio con una vecchia password.**
    * Se un account viene usato da applicazioni o servizi, è possibile che le risorse provino ripetutamente ad accedere con una vecchia password. Questo comportamento causa la blocco dell'account.
    * Provare a ridurre al minimo l'utilizzo dell'account tra più applicazioni o servizi diversi e registrare le credenziali utilizzate. Se viene modificata la password di un account, aggiornare di conseguenza le applicazioni o i servizi associati.
* **La password è stata modificata in un ambiente diverso e la nuova password non è ancora stata sincronizzata.**
    * Se la password di un account viene modificata al di fuori di Azure AD DS, ad esempio in un ambiente di servizi di dominio Active Directory locale, potrebbero essere necessari alcuni minuti prima che la modifica della password venga sincronizzata tramite Azure AD e in Azure AD DS.
    * Un utente che tenta di accedere a una risorsa tramite Azure AD DS prima del completamento del processo di sincronizzazione delle password causa il blocco dell'account.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Risolvere i problemi di blocco degli account con i controlli di sicurezza

Per risolvere i problemi quando si verificano eventi di blocco degli account e da dove provengono, [abilitare i controlli di sicurezza per Azure AD DS (attualmente in anteprima)][security-audit-events]. Gli eventi di controllo vengono acquisiti solo dal momento in cui si Abilita la funzionalità. Idealmente, è consigliabile abilitare i controlli di sicurezza *prima* che si verifichi un problema di blocco dell'account per la risoluzione dei problemi. Se un account utente presenta ripetutamente problemi di blocco, è possibile abilitare i controlli di sicurezza pronti per la prossima volta che si verifica la situazione.

Dopo aver abilitato i controlli di sicurezza, le seguenti query di esempio illustrano come esaminare *gli eventi di blocco degli account*, codice *4740*.

Visualizzare tutti gli eventi di blocco degli account per gli ultimi sette giorni:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Visualizzare tutti gli eventi di blocco degli account per gli ultimi sette giorni per l'account denominato *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Visualizza tutti gli eventi di blocco degli account tra il 26 giugno 2019 alle 9.00 e il 1 ° luglio 2019 mezzanotte, ordinati in ordine crescente in base alla data e all'ora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui criteri granulari per le password per modificare le soglie di blocco degli account, vedere [configurare i criteri di blocco][configure-fgpp]degli account e delle password.

Se si verificano ancora problemi di aggiunta della macchina virtuale al dominio gestito di Azure AD DS, [consultare la guida e aprire un ticket di supporto per Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
