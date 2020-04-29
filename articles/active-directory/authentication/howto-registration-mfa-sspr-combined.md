---
title: Introduzione alla registrazione combinata-Azure Active Directory
description: Abilita Multi-Factor Authentication di Azure AD combinato e la registrazione della reimpostazione della password self-service
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639688"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Abilitare la registrazione delle informazioni di sicurezza combinate in Azure Active Directory

Prima della registrazione combinata, gli utenti registravano i metodi di autenticazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR) separatamente. Gli utenti erano confusi che venivano usati metodi simili per Multi-Factor Authentication e SSPR, ma dovevano registrarsi per entrambe le funzionalità. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e sfruttare i vantaggi di Multi-Factor Authentication e SSPR.

Prima di abilitare la nuova esperienza, vedere l'articolo [registrazione delle informazioni di sicurezza combinata](concept-registration-mfa-sspr-combined.md) per assicurarsi di comprendere la funzionalità e gli effetti di questa funzionalità.

![Esperienza ottimizzata per la registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Completare questi passaggi per abilitare la registrazione combinata:

1. Accedere al portale di Azure come amministratore utente o amministratore globale.
2. Passare a **Azure Active Directory** > **Impostazioni** > utente**Gestisci impostazioni di anteprima funzionalità utente**.
3. In **utenti possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza**, scegliere di abilitare per un gruppo **selezionato** di utenti o per **tutti** gli utenti.

   ![Abilitare l'esperienza combinata di anteprima delle informazioni di sicurezza per tutti gli utenti](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Dopo aver abilitato la registrazione combinata, gli utenti che registrano o confermano il numero di telefono o l'app per dispositivi mobili tramite la nuova esperienza possono usarli per Multi-Factor Authentication e SSPR, se tali metodi sono abilitati nei criteri Multi-Factor Authentication e SSPR. Se si disabilita questa esperienza, gli utenti che accedono alla pagina di registrazione di SSPR `https://aka.ms/ssprsetup` precedente a dovranno eseguire l'autenticazione a più fattori prima di poter accedere alla pagina.

Se è stato configurato l'elenco di assegnazione da sito a zona in Internet Explorer, i siti seguenti devono trovarsi nella stessa zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinata

È ora possibile proteggere quando e come gli utenti si registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service con le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità è disponibile per le organizzazioni che hanno abilitato la [funzionalità di registrazione combinata](../authentication/concept-registration-mfa-sspr-combined.md). Questa funzionalità può essere abilitata nelle organizzazioni in cui si vuole che gli utenti si registrino per Multi-Factor Authentication di Azure e SSPR da una posizione centrale, ad esempio un percorso di rete attendibile durante l'onboarding delle risorse umane.

Per ulteriori informazioni sulla creazione di percorsi attendibili nell'accesso condizionale, vedere l'articolo [Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibile

I criteri seguenti si applicano a tutti gli utenti selezionati che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata e bloccano l'accesso a meno che non si connettano da un percorso contrassegnato come rete attendibile.

1. Nella **portale di Azure**individuare **Azure Active Directory** > **Security** > **accesso condizionale** di sicurezza
1. Seleziona **+ nuovo criterio**
1. Immettere un nome per il criterio, ad esempio la *registrazione delle informazioni di sicurezza combinata su reti attendibili*.
1. In **Assegnazioni** selezionare **Utenti e gruppi**. Scegliere gli utenti e i gruppi a cui si vuole applicare questo criterio, quindi **fare**clic su fine.

   > [!WARNING]
   > Gli utenti devono essere abilitati per la registrazione combinata.

1. In **azioni o app Cloud**selezionare **azioni utente**. Controllare **registra informazioni di sicurezza**, quindi selezionare **fine**.

    ![Creare un criterio di accesso condizionale per controllare la registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. In **Conditions** > **percorsi**condizioni configurare le opzioni seguenti:
   1. Configura **Sì**
   1. Includi **qualsiasi posizione**
   1. Escludi **tutti i percorsi attendibili**
1. Selezionare **fatto** nella finestra *percorsi* , quindi fare clic su **fine** nella finestra *condizioni* .
1. In **controllo** > di accesso**concedere**scegliere **Blocca accesso**, quindi **selezionare**
1. Imposta **Abilita criterio** **su on**
1. Per finalizzare il criterio, selezionare **Crea**

## <a name="next-steps"></a>Passaggi successivi

Per assistenza, vedere come [risolvere i problemi relativi alla registrazione di informazioni di sicurezza combinate](howto-registration-mfa-sspr-combined-troubleshoot.md) o informazioni [sulla condizione di posizione in Azure Active Directory l'accesso condizionale?](../conditional-access/location-condition.md)

Per abilitare le funzionalità nel tenant di Azure AD, vedere le esercitazioni per [abilitare la reimpostazione self-service delle password](tutorial-enable-sspr.md) e [abilitare Azure multi-factor authentication](tutorial-enable-azure-mfa.md).

Informazioni su come [abilitare la registrazione combinata nel tenant](howto-registration-mfa-sspr-combined.md) o [forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

È anche possibile esaminare i [metodi disponibili per multi-factor authentication di Azure e SSPR](concept-authentication-methods.md).
