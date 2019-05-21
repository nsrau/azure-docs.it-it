---
title: Introduzione a registrazione combinata per multi-Factor Authentication (anteprima) - Azure Active Directory e Azure AD SSPR
description: Abilita combinati multi-Factor Authentication di Azure AD e di registrazione (anteprima) di reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc4ff596cdafd348288187b0cd9b32f7b4c2d275
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823381"
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

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinato

Protezione di quando e come eseguire la registrazione degli utenti per Azure multi-Factor Authentication e reimpostazione della password self-service è ora possibile con azioni dell'utente in Criteri di accesso condizionale. Questa funzionalità di anteprima è disponibile per le organizzazioni che hanno attivato il [combinati anteprima registrazione](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità può essere abilitata nelle organizzazioni in cui si desidera che gli utenti la registrazione per Azure multi-Factor Authentication e SSPR da una posizione centrale, ad esempio un percorso di rete attendibili durante l'onboarding delle risorse Umane. Per altre informazioni sulla creazione di percorsi attendibili nell'accesso condizionale, vedere l'articolo [che cos'è la condizione della posizione nell'accesso condizionale di Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibile

Il seguente criterio si applica a tutti gli utenti selezionati, che tentano di registrare con l'esperienza di registrazione combinato e blocca l'accesso a meno che non sono connessi da una posizione contrassegnata come rete attendibile.

![Creare un criterio di accesso Condizionale per controllare la registrazione delle info di sicurezza](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. Nel **portale di Azure**, passare alla **Azure Active Directory** > **accesso condizionale**
1. Selezionare **Nuovi criteri**
1. In nome immettere un nome per questo criterio. Ad esempio, **combinati registrazione Info di sicurezza su reti attendibili**
1. Sotto **assegnazioni**, fare clic su **utenti e gruppi**e selezionare gli utenti e gruppi di questo criterio da applicare a

   > [!WARNING]
   > Gli utenti devono essere abilitati per il [combinati anteprima registrazione](../authentication/howto-registration-mfa-sspr-combined.md).

1. Sotto **Cloud apps o azioni**, selezionare **azioni dell'utente**, selezionare **registrare informazioni di sicurezza (anteprima)**
1. Sotto **condizioni** > **posizioni**
   1. Configurare **Sì**
   1. Includere **qualsiasi posizione**
   1. Escludere **tutte le posizioni attendibili**
   1. Fare clic su **Fatto** nel pannello delle località
   1. Fare clic su **Fatto** nel pannello delle condizioni
1. Sotto **controlli di accesso** > **Grant**
   1. Fare clic su **bloccare l'accesso**
   1. Quindi fare clic su **selezionare**
1. Impostare **attiva criterio** a **su**
1. Quindi fare clic su **Create**

## <a name="next-steps"></a>Passaggi successivi

[Metodi disponibili per multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione password self-service](howto-sspr-deployment.md)

[Configurare Azure multi-Factor Authentication](howto-mfa-getstarted.md)

[Risoluzione dei problemi di registrazione info di sicurezza combinati](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Che cos'è la condizione della posizione nell'accesso condizionale di Azure Active Directory?](../conditional-access/location-condition.md)