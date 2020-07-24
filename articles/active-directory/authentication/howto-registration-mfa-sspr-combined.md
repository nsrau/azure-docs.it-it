---
title: Abilitare la registrazione delle informazioni di sicurezza combinate-Azure Active Directory
description: Informazioni su come semplificare l'esperienza dell'utente finale con Multi-Factor Authentication di Azure AD combinato e la registrazione della reimpostazione della password self-service.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: de76a9138f782ab699bcd6ff56dab09a4e694102
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035520"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Abilitare la registrazione delle informazioni di sicurezza combinate in Azure Active Directory

Prima della registrazione combinata, gli utenti dovevano registrare i metodi di autenticazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service separatamente. Ciò creava confusione negli utenti in quanto si trovavano a dover eseguire la registrazione sia per Multi-Factor Authentication di Azure sia per la reimpostazione della password self-service, con metodi molto simili. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e sfruttare i vantaggi di Azure Multi-Factor Authentication e della reimpostazione della password self-service.

> [!NOTE]
> A partire dal 15 agosto 2020, tutti i nuovi tenant di Azure AD verranno automaticamente abilitati per la registrazione combinata.

Per assicurarsi di aver compreso le funzionalità e gli effetti prima di abilitare la nuova esperienza, vedere i [concetti combinati relativi alla registrazione delle informazioni di sicurezza](concept-registration-mfa-sspr-combined.md).

![Esperienza avanzata di registrazione delle informazioni di sicurezza combinata](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Abilitare la registrazione combinata

Per abilitare la registrazione combinata, completare i passaggi seguenti:

1. Accedere al portale di Azure come amministratore globale o amministratore utenti.
2. Andare a **Azure Active Directory** > **Impostazioni utente** > **Gestione delle impostazioni dell'anteprima delle funzionalità per gli utenti**.
3. In **Gli utenti possono usare l'esperienza di registrazione delle informazioni di sicurezza combinata** scegliere l'abilitazione per un gruppo **selezionato** di utenti o per **tutti** gli utenti.

   ![Abilitare l'esperienza combinata per le informazioni di sicurezza per gli utenti](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Dopo avere abilitato la registrazione combinata, gli utenti che registrano o confermano il numero di telefono o l'app per dispositivi mobili tramite la nuova esperienza potranno usare Azure Multi-Factor Authentication e la reimpostazione della password self-service, se tali metodi sono abilitati nei criteri di Azure Multi-Factor Authentication e di reimpostazione della password self-service.
>
> Se si disabilita questa esperienza, gli utenti che accedono alla pagina di registrazione di SSPR precedente in `https://aka.ms/ssprsetup` sono tenuti a eseguire l'autenticazione a più fattori prima di poter accedere alla pagina.

Se è stato configurato l' *elenco di assegnazione da sito a zona* in Internet Explorer, i siti seguenti devono trovarsi nella stessa zona:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Criteri di accesso condizionale per la registrazione combinata

Per proteggere quando e come gli utenti registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service, è possibile usare le azioni dell'utente nei criteri di accesso condizionale. Questa funzionalità può essere abilitata nelle organizzazioni che vogliono che gli utenti si registrino per Multi-Factor Authentication di Azure e SSPR da una posizione centrale, ad esempio un percorso di rete attendibile durante l'onboarding delle risorse umane.

> [!NOTE]
> Questo criterio si applica solo quando un utente accede a una pagina di registrazione combinata. Questo criterio non impone la registrazione dell'autenticazione a più fattori quando un utente accede ad altre applicazioni.
>
> È possibile creare criteri di registrazione dell'autenticazione a più fattori usando [Azure Identity Protection: configurare i criteri](../identity-protection/howto-identity-protection-configure-mfa-policy.md)di autenticazione a più fattori.

Per altre informazioni sulla creazione di percorsi attendibili nell'accesso condizionale, vedere [Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare criteri per richiedere la registrazione da un percorso attendibile

Completare i passaggi seguenti per creare un criterio applicabile a tutti gli utenti selezionati che tentano di eseguire la registrazione usando l'esperienza di registrazione combinata e blocca l'accesso a meno che non si connettano da un percorso contrassegnato come rete attendibile:

1. Nel **portale di Azure** andare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**
1. Selezionare **+ Nuovi criteri**.
1. Immettere un nome per i criteri, ad esempio *Registrazione delle informazioni di sicurezza combinata su reti attendibili*.
1. In **Assegnazioni** selezionare **Utenti e gruppi**. Scegliere gli utenti e i gruppi a cui si vuole applicare questi criteri, quindi selezionare **Fatto**.

   > [!WARNING]
   > Gli utenti devono essere abilitati per la registrazione combinata.

1. In **App Cloud o azioni** selezionare **Azioni utente**. Controllare **Registra le informazioni di sicurezza**, quindi selezionare **Fatto**.

    ![Creare criteri di accesso condizionale per controllare la registrazione delle informazioni di sicurezza](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. In **Condizioni** > **Posizioni** configurare le opzioni seguenti:
   1. Configurare **Sì**.
   1. Includere **Tutte le località**.
   1. Escludere**tutti i percorsi attendibili**.
1. Selezionare **Fatto** nella finestra *Posizioni*, quindi selezionare **Fatto** nella finestra *Condizioni*.
1. In **Controlli di accesso** > **Concedi** selezionare **Blocca accesso**, quindi **Seleziona**.
1. Impostare **Abilita criteri** su **On**.
1. Per finalizzare i criteri, selezionare **Crea**.

## <a name="next-steps"></a>Passaggi successivi

Per assistenza, vedere la pagina relativa alla [risoluzione dei problemi di registrazione delle informazioni di sicurezza combinate](howto-registration-mfa-sspr-combined-troubleshoot.md) o informazioni [sulla condizione di posizione in Azure ad l'accesso condizionale?](../conditional-access/location-condition.md)

Dopo che gli utenti sono stati abilitati per la registrazione combinata, è possibile [abilitare la reimpostazione della password self-service](tutorial-enable-sspr.md) e [abilitare multi-factor authentication di Azure](tutorial-enable-azure-mfa.md).

Se necessario, informazioni su come [forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
