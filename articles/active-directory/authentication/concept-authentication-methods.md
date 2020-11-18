---
title: Metodi e funzionalità di autenticazione - Azure Active Directory
description: Informazioni sui diversi metodi e le diverse funzionalità di autenticazione disponibili in Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: a166a451c405c2321453e02751baad91c2a14c60
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840018"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quali metodi di autenticazione e verifica sono disponibili in Azure Active Directory?

Nell'ambito dell'esperienza di accesso degli account in Azure Active Directory (Azure AD) è possibile eseguire l'autenticazione di un utente in modi diversi. Il modo più comune in cui un utente inserisce le credenziali è tradizionalmente l'uso di un nome utente e di una password. Con le funzionalità moderne di autenticazione e sicurezza in Azure AD, la password di base deve essere completata o sostituita con metodi di autenticazione più protetti.

![Tabella dei punti di forza e dei metodi di autenticazione preferiti in Azure AD](media/concept-authentication-methods/authentication-methods.png)

I metodi di autenticazione senza password come Windows Hello, le chiavi di sicurezza FIDO2 e l'app Microsoft Authenticator forniscono gli eventi di accesso più sicuri.

Azure AD Multi-Factor Authentication aggiunge una maggiore sicurezza solo tramite una password quando un utente esegue l'accesso. All'utente possono essere richieste altre forme di autenticazione, ad esempio per rispondere a una notifica push, immettere un codice da un token software o hardware oppure rispondere a un SMS o a una telefonata.

Per semplificare l'esperienza di onboarding dell'utente e registrarsi per l'autenticazione a più fattori e SSPR, è consigliabile [abilitare la registrazione delle informazioni di sicurezza combinate](howto-registration-mfa-sspr-combined.md). Per garantire la resilienza, è consigliabile richiedere agli utenti di registrare più metodi di autenticazione. Quando un metodo non è disponibile per un utente durante l'accesso o SSPR, può scegliere di eseguire l'autenticazione con un altro metodo. Per altre informazioni, vedere [creare una strategia di gestione del controllo di accesso resiliente in Azure ad](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Sicurezza e sicurezza del metodo di autenticazione

Quando si distribuiscono funzionalità come Azure AD Multi-Factor Authentication all'interno dell'organizzazione, esaminare i metodi di autenticazione disponibili. Scegliere i metodi che soddisfano o superano i requisiti in termini di sicurezza, usabilità e disponibilità. Laddove possibile, usare i metodi di autenticazione con il massimo livello di sicurezza.

Nella tabella seguente vengono descritte le considerazioni sulla sicurezza per i metodi di autenticazione disponibili. La disponibilità indica che l'utente è in grado di usare il metodo di autenticazione, non della disponibilità del servizio in Azure AD:

| Metodo di autenticazione          | Sicurezza | Usabilità | Disponibilità |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | Alta     | Alta      | Alta         |
| App Microsoft Authenticator    | Alta     | Alta      | Alta         |
| Chiave di sicurezza FIDO2 (anteprima)   | Alta     | Alta      | Alta         |
| Token hardware OATH (anteprima) | Livello medio   | Livello medio    | Alta         |
| Token software OATH           | Livello medio   | Livello medio    | Alta         |
| sms                            | Media   | Alto      | Medio       |
| Chiamata vocale                          | Livello medio   | Livello medio    | Livello medio       |
| Password                       | Basso      | Alto      | Alta         |

Per altre informazioni sulla sicurezza, vedere [vulnerabilità di autenticazione e vettori di attacco](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> Per flessibilità e usabilità, è consigliabile usare l'app Microsoft Authenticator. Questo metodo di autenticazione offre la migliore esperienza utente e più modalità, ad esempio la password, le notifiche push con autenticazione a più fattori e i codici di GIURAmento.

## <a name="how-each-authentication-method-works"></a>Funzionamento di ogni metodo di autenticazione

Alcuni metodi di autenticazione possono essere usati come fattore principale quando si accede a un'applicazione o a un dispositivo, ad esempio usando una chiave di sicurezza FIDO2 o una password. Altri metodi di autenticazione sono disponibili solo come fattore secondario quando si usa Azure AD Multi-Factor Authentication o SSPR.

La tabella seguente illustra quando è possibile usare un metodo di autenticazione durante un evento di accesso:

| Metodo                         | Autenticazione primaria | Autenticazione secondaria  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | Sì                    | MFA                       |
| App Microsoft Authenticator    | Sì (anteprima)          | Autenticazione a più fattori e SSPR              |
| Chiave di sicurezza FIDO2 (anteprima)   | Sì                    | MFA                       |
| Token hardware OATH (anteprima) | No                     | MFA                       |
| Token software OATH           | No                     | MFA                       |
| sms                            | Sì (anteprima)          | Autenticazione a più fattori e SSPR              |
| Chiamata vocale                     | No                     | Autenticazione a più fattori e SSPR              |
| Password                       | Sì                    |                           |

Tutti questi metodi di autenticazione possono essere configurati nella portale di Azure e sempre più usando l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Per altre informazioni sul funzionamento di ogni metodo di autenticazione, vedere gli articoli concettuali distinti seguenti:

* [Windows Hello for business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [App Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Chiave di sicurezza FIDO2 (anteprima)](concept-authentication-passwordless.md#fido2-security-keys)
* [Token hardware OATH (anteprima)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Token software OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Accesso SMS (anteprima)](howto-authentication-sms-signin.md) e [Verifica](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verifica della chiamata vocale](concept-authentication-phone-options.md)
* Password

> [!NOTE]
> In Azure AD, una password è spesso uno dei metodi di autenticazione principali. Non è possibile disabilitare il metodo di autenticazione con password. Se si usa una password come fattore di autenticazione principale, aumentare la sicurezza degli eventi di accesso usando Azure AD Multi-Factor Authentication.

In determinati scenari è possibile utilizzare i seguenti metodi di verifica aggiuntivi:

* [Password dell'app](howto-mfa-app-passwords.md) : usate per le applicazioni obsolete che non supportano l'autenticazione moderna e possono essere configurate per l'multi-factor authentication Azure ad per utente.
* [Domande di sicurezza](concept-authentication-security-questions.md) : usate solo per SSPR
* [Indirizzo di posta elettronica](concept-sspr-howitworks.md#authentication-methods) : usato solo per SSPR

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere l' [esercitazione per la reimpostazione della password self-service (SSPR)][tutorial-sspr] e [Azure ad multi-factor authentication][tutorial-azure-mfa].

Per altre informazioni sui concetti di reimpostazione della password self-service, vedere [Come funziona la reimpostazione della password self-service di Azure AD][concept-sspr].

Per altre informazioni sui concetti relativi a multi-factor authentication, vedere funzionamento di [Azure AD multi-factor authentication][concept-mfa].

Altre informazioni sulla configurazione dei metodi di autenticazione con l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Per esaminare i metodi di autenticazione in uso, vedere [Azure ad l'analisi del metodo di autenticazione multi-factor authentication con PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
