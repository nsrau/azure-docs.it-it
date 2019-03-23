---
title: Introduzione a registrazione combinata per MFA (anteprima) - Azure Active Directory e Azure AD SSPR
description: Abilita combinati multi-Factor Authentication di Azure AD e di registrazione (anteprima) di reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d7f9623a7594aaa79c4cff187486360b1befc8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369021"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registrazione di informazioni sicurezza Abilita combinata (anteprima)

Prima di abilitare la nuova esperienza, vedere l'articolo [combinati registrazione di informazioni di sicurezza (anteprima)](concept-registration-mfa-sspr-combined.md) per far comprendere la funzionalità e l'impatto di questa funzionalità.

![Esperienza migliorata la registrazione di informazioni di sicurezza combinato](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Registrazione di informazioni di sicurezza combinato per la reimpostazione password self-service di Azure multi-Factor Authentication e Azure AD è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Completare la procedura seguente per abilitare la registrazione combinata:

1. Accedere al portale di Azure come utente amministratore o amministratore globale.
2. Passare ad **Azure Active Directory** > **Impostazioni utente** > **Gestisci le impostazioni per le funzionalità in anteprima del pannello di accesso**.
3. Sotto **gli utenti possono usare anteprima funzionalità per la registrazione e la gestione delle tue info di sicurezza - aggiornamento**, scegliere di abilitare per una **selezionati** gruppo di utenti o per **tutti** agli utenti.

![Abilitare l'esperienza di anteprima di info di sicurezza combinato per tutti gli utenti](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partire da marzo del 2019 le opzioni telefonata non sarà disponibile per gli utenti MFA e SSPR nei tenant gratuiti/versione di valutazione AD Azure. Messaggi SMS non sono interessati da questa modifica. Chiamata telefonica continuerà a essere disponibile per gli utenti a pagamento di tenant di Azure AD. Questa modifica interessa solo i tenant gratuiti/versione di valutazione AD Azure.

> [!NOTE]
> In seguito all'attivazione combinata registrazione, gli utenti che registrano o confermare che il numero di telefono o app per dispositivi mobili tramite la nuova esperienza possibile usarli per l'autenticazione a più fattori e SSPR, se tali metodi sono abilitati nelle politiche di MFA e SSPR. Se si disabilita questa esperienza, gli utenti che passare per la registrazione SSPR precedente pagina `https:/aka.ms/ssprsetup` sarà necessario eseguire l'autenticazione a più fattori prima di poter accedere la pagina.

Se è stato configurato il sito all'elenco di assegnazione di zona in Internet Explorer, i siti seguenti devono essere nella stessa zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Passaggi successivi

[Metodi disponibili per l'autenticazione a più fattori e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione password self-service](howto-sspr-deployment.md)

[Configurare Azure multi-Factor Authentication](howto-mfa-getstarted.md)

[Risoluzione dei problemi di registrazione info di sicurezza combinati](howto-registration-mfa-sspr-combined-troubleshoot.md)