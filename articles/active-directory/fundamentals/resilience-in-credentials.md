---
title: Resilienza della compilazione con la gestione delle credenziali in Azure Active Directory
description: Una guida per architetti e amministratori IT sulla creazione di una strategia di credenziale resiliente.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5fd5cf419adb137df5c578e3b17b88749215be5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919620"
---
# <a name="build-resilience-with-credential-management"></a>Resilienza della compilazione con la gestione delle credenziali

Quando una credenziale viene presentata Azure AD in una richiesta di token, esistono più dipendenze che devono essere disponibili per la convalida. Il primo fattore di autenticazione si basa sull'autenticazione Azure AD e, in alcuni casi, sull'infrastruttura locale. Per altre informazioni sulle architetture di autenticazione ibrida, vedere [resilienza di compilazione nell'infrastruttura ibrida](resilience-in-hybrid.md). 

Se si implementa un secondo fattore, le dipendenze per il secondo fattore vengono aggiunte alle dipendenze per la prima. Se, ad esempio, il primo fattore è tramite PTA e il secondo fattore è SMS, le dipendenze sono:

* Servizi di autenticazione Azure AD

* Servizio autenticazione a più fattori di Azure

* Infrastruttura locale

* Gestore telefonico

* Il dispositivo dell'utente (non illustrato)

 
![Immagine dei metodi e delle dipendenze di autenticazione](./media/resilience-in-credentials/admin-resilience-credentials.png)

La strategia delle credenziali deve prendere in considerazione le dipendenze di ogni tipo di autenticazione ed eseguire il provisioning di metodi che evitano un singolo punto di errore. 

Poiché i metodi di autenticazione hanno dipendenze diverse, è consigliabile consentire agli utenti di eseguire la registrazione per il maggior numero possibile di opzioni di secondo fattore. Assicurarsi di includere i secondi fattori con dipendenze diverse, se possibile. Ad esempio, chiamata vocale e SMS come secondo fattore condividono le stesse dipendenze, quindi la loro presenza come uniche opzioni non mitiga il rischio.

La strategia di credenziale più resiliente consiste nell'usare l'autenticazione con password. Le chiavi di sicurezza di Windows Hello for business e FIDO 2,0 hanno meno dipendenze rispetto all'autenticazione avanzata con due fattori distinti. Le chiavi di sicurezza app Microsoft Authenticator, Windows Hello for business e Fido 2,0 sono le più sicure. 

Per i secondi, l'app Microsoft Authenticator o altre app di autenticazione che usano i token di accesso monouso basato sul tempo (TOTP) o i token hardware del GIURAmento hanno il minor numero di dipendenze e sono pertanto più resilienti.

## <a name="how-do-multiple-credentials-help-resilience"></a>In che modo più credenziali contribuiscono alla resilienza?

Il provisioning di più tipi di credenziali offre agli utenti opzioni che soddisfano le preferenze e i vincoli ambientali. Di conseguenza, l'autenticazione interattiva in cui agli utenti viene richiesto di eseguire l'autenticazione a più fattori sarà più resiliente alle dipendenze specifiche non disponibili al momento della richiesta. È possibile [ottimizzare le richieste di riautenticazione per la funzionalità autenticazione a più fattori](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Oltre alla resilienza di singoli utenti descritta in precedenza, le aziende devono pianificare le contingenze per interruzioni su larga scala, ad esempio errori operativi che introducono una configurazione errata, un'emergenza naturale o un'interruzione di risorse a livello aziendale a un servizio federativo locale (soprattutto se usato per l'autenticazione a più fattori). 

## <a name="how-do-i-implement-resilient-credentials"></a>Ricerca per categorie implementare le credenziali resilienti?

* Distribuire le [credenziali password](../authentication/howto-authentication-passwordless-deployment.md) , ad esempio Windows Hello for business, l'autenticazione telefono e le chiavi di sicurezza FIDO2 per ridurre le dipendenze.

* Distribuire l' [App Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) come secondo fattore.

* Abilitare la [sincronizzazione dell'hash delle password](../hybrid/whatis-phs.md) per gli account ibridi sincronizzati da Windows Server Active Directory. Questa opzione può essere abilitata insieme ai servizi federativi, ad esempio AD FS e fornisce un fallback in caso di errore del servizio federativo.

* [Analizzare l'utilizzo dei metodi di autenticazione](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) a più fattori per migliorare l'esperienza degli utenti.

* [Implementare una strategia di controllo degli accessi resiliente](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza di compilazione con gli Stati dei dispositivi](resilience-with-device-states.md)

* [Resilienza della compilazione usando la valutazione di accesso continuo (CAE)](resilience-with-continuous-access-evaluation.md)

* [Resilienza di compilazione nell'autenticazione utente esterna](resilience-b2b-authentication.md)

* [Resilienza di compilazione nell'autenticazione ibrida](resilience-in-hybrid.md)

* [Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione](resilience-on-premises-access.md)

Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)
