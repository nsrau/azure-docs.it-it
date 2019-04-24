---
title: Introduzione a registrazione combinata per multi-Factor Authentication (anteprima) - Azure Active Directory e Azure AD SSPR
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
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358090"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registrazione di informazioni sicurezza Abilita combinata (anteprima)

Prima di abilitare la nuova esperienza, vedere l'articolo [combinati registrazione di informazioni di sicurezza (anteprima)](concept-registration-mfa-sspr-combined.md) per verificare di conoscere le funzionalità e gli effetti di questa funzionalità.

![Esperienza migliorata la registrazione di informazioni di sicurezza combinato](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Registrazione di informazioni di sicurezza combinato per Azure multi-Factor Authentication e reimpostazione della password self-service di Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Completare questi passaggi per abilitare la registrazione combinata:

1. Accedere al portale di Azure come utente amministratore o amministratore globale.
2. Passare a **Azure Active Directory** > **impostazioni utente** > **gestire le impostazioni per le funzionalità di anteprima di Pannello di accesso**.
3. Sotto **gli utenti possono usare anteprima funzionalità per la registrazione e la gestione delle tue info di sicurezza - aggiornamento**, scegliere di abilitare per una **selezionati** gruppo di utenti o per **tutti** agli utenti.

   ![Abilitare l'esperienza di anteprima di info di sicurezza combinato per tutti gli utenti](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni telefonata non saranno disponibili per multi-Factor Authentication e gli utenti SSPR nei tenant gratuiti/versione di valutazione AD Azure. Messaggi SMS non sono interessati da questa modifica. Le opzioni telefonata continuerà a essere disponibile per gli utenti a pagamento di tenant di Azure AD.

> [!NOTE]
> Dopo aver abilitato combinata registrazione, gli utenti che registrano o confermare che il numero di telefono o app per dispositivi mobili tramite la nuova esperienza possibile usarli per multi-Factor Authentication e SSPR, se tali metodi sono abilitati nella multi-Factor Authentication e SSPR criteri. Se si disabilita questa esperienza, gli utenti che passare per la registrazione SSPR precedente pagina `https:/aka.ms/ssprsetup` sarà necessario eseguire l'autenticazione a più fattori prima di poter accedere la pagina.

Se è stato configurato il sito all'elenco di assegnazione di zona in Internet Explorer, i siti seguenti devono essere nella stessa zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Passaggi successivi

[Metodi disponibili per multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione password self-service](howto-sspr-deployment.md)

[Configurare Azure multi-Factor Authentication](howto-mfa-getstarted.md)

[Risoluzione dei problemi di registrazione info di sicurezza combinati](howto-registration-mfa-sspr-combined-troubleshoot.md)