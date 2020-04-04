---
title: Introduzione alla registrazione combinata - Azure Active Directory
description: Abilitare Azure AD Multi-Factor Authentication combinato e la registrazione della reimpostazione della password self-service (anteprima)Enable combined Azure AD Multi-Factor Authentication and self-service password reset registration (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f454b0296a3463d7346c2945b21162e5a38c0eb7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652515"
---
# <a name="enable-combined-security-information-registration-preview"></a>Abilita registrazione combinata delle informazioni di sicurezza (anteprima)Enable combined security information registration (preview)

Prima di abilitare la nuova esperienza, leggere l'articolo Registrazione combinata delle informazioni di [sicurezza (anteprima)](concept-registration-mfa-sspr-combined.md) per assicurarsi di comprendere le funzionalità e gli effetti di questa funzionalità.

![Esperienza combinata di registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| La registrazione combinata delle informazioni di sicurezza per Azure Multi-Factor Authentication e la reimpostazione della password self-service di Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure AD. Per altre informazioni sulle anteprime, vedere Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure .For more information about previews, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Le organizzazioni che hanno abilitato l'anteprima precedente per la registrazione e la gestione delle info di sicurezza devono completare i passaggi seguenti per abilitare l'esperienza di anteprima avanzata. Per le organizzazioni che non effettuano il passaggio, l'8 ottobre 2019, Microsoft passerà gli utenti dell'anteprima precedente per la registrazione e la gestione delle info di sicurezza all'esperienza avanzata. 
> 
> Se non è stata abilitata alcuna versione dell'anteprima, l'organizzazione non sarà interessata.

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Completare questi passaggi per abilitare la registrazione combinata:Complete these steps to enable combined registration:

1. Accedere al portale di Azure come amministratore utente o amministratore globale.
2. Passare a Impostazioni utente di **Azure Active Directory** > Gestire**le** > impostazioni di anteprima delle**funzionalità utente.**
3. In Gli utenti possono utilizzare le funzionalità di **anteprima per la registrazione e la gestione delle informazioni**di sicurezza scegliere di abilitare per un gruppo **selezionato** di utenti o per **Tutti** gli utenti.

   ![Abilitare l'esperienza di anteprima delle info di sicurezza combinata per tutti gli utentiEnable the combined security info preview experience for All users](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> A partire da marzo 2019, le opzioni di chiamata telefonica non saranno disponibili per gli utenti di Multi-Factor Authentication e SSPR nei tenant di Azure AD di prova/gratuita. I messaggi SMS non sono interessati da questa modifica. Le opzioni di chiamata telefonica saranno comunque disponibili per gli utenti nei tenant di Azure AD a pagamento.

> [!NOTE]
> Dopo aver abilitato la registrazione combinata, gli utenti che registrano o confermano il proprio numero di telefono o app per dispositivi mobili tramite la nuova esperienza di utilizzo possono utilizzarli per Multi-Factor Authentication e SSPR, se tali metodi sono abilitati nei criteri Multi-Factor Authentication e SSPR. Se quindi si disabilita questa esperienza, gli utenti che `https://aka.ms/ssprsetup` accedono alla pagina di registrazione SSPR precedente sarà necessario eseguire l'autenticazione a più fattori prima di poter accedere alla pagina.

Se è stato configurato l'elenco di assegnazione da sito a area in Internet Explorer, i seguenti siti devono trovarsi nella stessa area:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinataConditional Access policies for combined registration

Garantire quando e come gli utenti si registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service è ora possibile con le azioni dell'utente nei criteri di accesso condizionale. Questa funzione di anteprima è disponibile per le organizzazioni che hanno abilitato [l'anteprima di registrazione combinata.](../authentication/concept-registration-mfa-sspr-combined.md) Questa funzionalità può essere abilitata nelle organizzazioni in cui desiderano che gli utenti si registrino per Azure Multi-Factor Authentication e SSPR da una posizione centrale, ad esempio un percorso di rete attendibile durante l'onboarding delle risorse umane. Per altre informazioni sulla creazione di percorsi attendibili in Accesso condizionale, vedere l'articolo [Qual è la condizione del percorso in Azure Active Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibileCreate a policy to require registration from a trusted location

Il criterio seguente si applica a tutti gli utenti selezionati, che tentano di registrarsi utilizzando l'esperienza di registrazione combinata, e bloccano l'accesso a meno che non si connettano da una posizione contrassegnata come rete attendibile.

![Creare un criterio CA per controllare la registrazione delle informazioni di sicurezzaCreate a CA policy to control security info registration](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Nel **portale di Azure**passare ad**Accesso condizionale** alla**sicurezza** > di **Azure Active Directory** > 
1. Selezionare **Nuovo criterio**
1. In Nome immettere un Nome per il criterio. Ad esempio, Registrazione combinata delle informazioni di **sicurezza su reti attendibili**
1. In **Assegnazioni**fare clic su **Utenti e gruppi**e selezionare gli utenti e i gruppi a cui si desidera applicare il criterio

   > [!WARNING]
   > Gli utenti devono essere abilitati per [l'anteprima di registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

1. In **App o azioni cloud**selezionare Azioni **utente**, selezionare Registra informazioni di **sicurezza (anteprima)**
1. In **Condizioni** > **Posizioni**
   1. Configura **Sì**
   1. Includi **qualsiasi posizione**
   1. Escludi **tutti i percorsi attendibili**
   1. Fare clic su **Fine** nel pannello Posizioni
   1. Fare clic su **Fine** nel pannello Condizioni
1. In **Controlli di** > accesso**Concedi**
   1. Fare clic su **Blocca accesso**
   1. Quindi fare clic su **Seleziona**
1. Impostare **Abilita criterio** su **Attivato**
1. Quindi fare clic su **Crea**

## <a name="next-steps"></a>Passaggi successivi

[Forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Metodi disponibili per Multi-Factor Authentication e SSPR](concept-authentication-methods.md)

[Configurare la reimpostazione della password self-service](howto-sspr-deployment.md)

[Configurazione di Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Risoluzione dei problemi di registrazione combinata delle informazioni di sicurezzaTroubleshooting combined security info registration](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qual è la condizione di posizione in Accesso condizionale di Azure Active Directory?](../conditional-access/location-condition.md)
