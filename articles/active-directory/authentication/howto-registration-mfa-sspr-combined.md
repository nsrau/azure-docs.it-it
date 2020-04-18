---
title: Introduzione alla registrazione combinata - Azure Active Directory
description: Abilitare l'autenticazione a più fattori di Azure AD combinata e la registrazione della reimpostazione della password self-serviceEnable combined Azure AD Multi-Factor Authentication and self-service password reset registration
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639688"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Abilitare la registrazione combinata delle informazioni di sicurezza in Azure Active DirectoryEnable combined security information registration in Azure Active Directory

Prima della registrazione combinata, gli utenti registrare i metodi di autenticazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR) separatamente. Le persone erano confuse sul fatto che metodi simili sono stati utilizzati per Multi-Factor Authentication e SSPR, ma hanno dovuto registrarsi per entrambe le funzionalità. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e ottenere i vantaggi di entrambi Multi-Factor Authentication e SSPR.

Prima di abilitare la nuova esperienza, leggere l'articolo Registrazione combinata delle informazioni di sicurezza per [assicurarsi](concept-registration-mfa-sspr-combined.md) di aver compreso la funzionalità e gli effetti di questa funzionalità.

![Esperienza combinata di registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Completare questi passaggi per abilitare la registrazione combinata:Complete these steps to enable combined registration:

1. Accedere al portale di Azure come amministratore utente o amministratore globale.
2. Passare a Impostazioni utente di **Azure Active Directory** > Gestire**le** > impostazioni di anteprima delle**funzionalità utente.**
3. In Gli utenti possono utilizzare le funzionalità di **anteprima per la registrazione e la gestione delle informazioni**di sicurezza scegliere di abilitare per un gruppo **selezionato** di utenti o per **Tutti** gli utenti.

   ![Abilitare l'esperienza di anteprima delle info di sicurezza combinata per tutti gli utentiEnable the combined security info preview experience for All users](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Dopo aver abilitato la registrazione combinata, gli utenti che registrano o confermano il proprio numero di telefono o app per dispositivi mobili tramite la nuova esperienza di utilizzo possono utilizzarli per Multi-Factor Authentication e SSPR, se tali metodi sono abilitati nei criteri Multi-Factor Authentication e SSPR. Se quindi si disabilita questa esperienza, gli utenti che `https://aka.ms/ssprsetup` accedono alla pagina di registrazione SSPR precedente sarà necessario eseguire l'autenticazione a più fattori prima di poter accedere alla pagina.

Se è stato configurato l'elenco di assegnazione da sito a area in Internet Explorer, i seguenti siti devono trovarsi nella stessa area:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinataConditional Access policies for combined registration

Garantire quando e come gli utenti si registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service è ora possibile con le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità è disponibile per le organizzazioni che hanno abilitato la funzionalità di [registrazione combinata.](../authentication/concept-registration-mfa-sspr-combined.md) Questa funzionalità può essere abilitata nelle organizzazioni in cui desiderano che gli utenti si registrino per Azure Multi-Factor Authentication e SSPR da una posizione centrale, ad esempio un percorso di rete attendibile durante l'onboarding delle risorse umane.

Per altre informazioni sulla creazione di percorsi attendibili in Accesso condizionale, vedere l'articolo [Qual è la condizione del percorso in Azure Active Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibileCreate a policy to require registration from a trusted location

Il criterio seguente si applica a tutti gli utenti selezionati che tentano di registrarsi utilizzando l'esperienza di registrazione combinata e blocca l'accesso a meno che non si connettano da una posizione contrassegnata come rete attendibile.

1. Nel **portale di Azure**passare ad**Accesso condizionale** alla**sicurezza** > di **Azure Active Directory** > 
1. Selezionare **: Nuovo criterio**
1. Immettere un nome per questo criterio, ad esempio Registrazione combinata delle informazioni di *protezione nelle reti attendibili*.
1. In **Assegnazioni** selezionare **Utenti e gruppi**. Scegli gli utenti e i gruppi a cui applicare questo criterio, quindi seleziona **Fine**.

   > [!WARNING]
   > Gli utenti devono essere abilitati per la registrazione combinata.

1. In **Azioni o app cloud**selezionare Azioni **utente**. Selezionare **Registra informazioni di sicurezza**, quindi selezionare **Fatto**.

    ![Creare criteri di accesso condizionale per controllare la registrazione delle informazioni di sicurezzaCreate a conditional access policy to control security info registration](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. In**Percorsi** **condizioni** > configurare le opzioni seguenti:
   1. Configura **Sì**
   1. Includi **qualsiasi posizione**
   1. Escludi **tutti i percorsi attendibili**
1. Selezionare **Fine** nella finestra *Posizioni,* quindi **Selezionare Fine** nella finestra *Condizioni.*
1. In **Controlli di** > accesso**Concedi**, scegliere **Blocca accesso**, quindi **Seleziona**
1. Impostare **Abilita criterio** su **Attivato**
1. Per finalizzare il criterio, selezionare **Crea**

## <a name="next-steps"></a>Passaggi successivi

Per assistenza, vedere come [risolvere i problemi di registrazione combinata delle informazioni](howto-registration-mfa-sspr-combined-troubleshoot.md) di sicurezza o vedere Qual è la condizione di posizione in Azure Active Directory Conditional [Access?](../conditional-access/location-condition.md)

Per abilitare le funzionalità nel tenant di Azure AD, vedere le esercitazioni per abilitare la [reimpostazione della password self-service](tutorial-enable-sspr.md) e [abilitare Azure Multi-Factor Authentication.](tutorial-enable-azure-mfa.md)

Informazioni su come [abilitare la registrazione combinata nel tenant](howto-registration-mfa-sspr-combined.md) o [forzare gli utenti a registrare nuovamente i metodi di autenticazione.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

È anche possibile esaminare i [metodi disponibili per Azure Multi-Factor Authentication e SSPR](concept-authentication-methods.md).
