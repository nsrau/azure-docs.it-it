---
title: Accesso senza password con l'app Microsoft Authenticator (anteprima)-Azure Active Directory
description: Accesso ad Azure AD tramite l'app Microsoft Authenticator senza usare la password (anteprima pubblica)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c464874708c7b93ec5620cc9ae253912ce1a4790
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357131"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Accesso tramite telefono senza password con l'app Microsoft Authenticator (anteprima pubblica)

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Analogamente alla tecnologia di [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator usa l'autenticazione basata su chiavi per abilitare credenziali utente associate a un dispositivo e basate su un sensore biometrico o un PIN.

![Esempio di accesso del browser che richiede all'utente di approvare l'accesso](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Se un utente ha abilitato l'accesso tramite telefono nell'app Microsoft Authenticator, dopo l'immissione del nome utente non verrà visualizzata la richiesta di inserimento della password, ma verrà visualizzato un messaggio che chiederà all'utente di toccare un numero nell'app. L'utente dovrà quindi toccare il numero corrispondente nell'app, scegliere l'opzione di approvazione e fornire il codice PIN o la chiave biometrica e l'autenticazione verrà completata.

## <a name="enable-my-users"></a>Abilitare gli utenti desiderati

### <a name="tenant-prerequisites"></a>Prerequisiti per il tenant

* Azure Active Directory
* Utenti finali abilitati per Azure multi-Factor Authentication
* Possibilità per gli utenti di registrare i propri dispositivi

### <a name="steps-to-enable"></a>Procedura di abilitazione

Seguire i passaggi descritti nell'articolo [abilitare l'accesso senza password per Azure ad](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods)per abilitare i metodi di autenticazione senza password nella directory.

> [!NOTE]
> Se in precedenza è stata abilitata questa funzionalità per il tenant usando uno script di PowerShell, l'impostazione di un nuovo criterio per utenti e gruppi sovrascrive i criteri esistenti a livello di tenant. 
>

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Come possono accedere tramite telefono gli utenti finali?

Per l'anteprima pubblica, non è possibile obbligare gli utenti a creare o usare nuove credenziali. Un utente finale incontrerà l'accesso senza password solo dopo che un amministratore ha abilitato il tenant e l'utente ha aggiornato l'app Microsoft Authenticator per abilitare l'accesso tramite telefono.

> [!NOTE]
> Questa funzionalità è inclusa nell'app da marzo 2017 ed è quindi possibile che, se per un tenant sono stati abilitati i criteri appropriati, gli utenti possano usufruire subito di questa possibilità. Tenere presente queste informazioni e preparare gli utenti per questa modifica.
>

1. Eseguire la registrazione in Azure Multi-Factor Authentication
1. Accertarsi che sia installata la versione più recente di Microsoft Authenticator nei dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva.
1. Aggiungere all'app un account aziendale o dell'istituto di istruzione con le notifiche push. La documentazione per l'utente finale è disponibile all'indirizzo [https://aka.ms/authappstart](https://aka.ms/authappstart).

Dopo aver configurato nell'app Microsoft Authenticator un account MFA con le notifiche push, l'utente può seguire la procedura descritta nell'articolo [Accedere con il telefono, non con la password](../user-help/microsoft-authenticator-app-phone-signin-faq.md) per completare la registrazione per l'accesso tramite telefono.

## <a name="known-issues"></a>Problemi noti

### <a name="ad-fs-integration"></a>Integrazione con AD FS

Se un utente ha abilitato le credenziali senza password di Microsoft Authenticator, per impostazione predefinita l'autenticazione per l'utente invierà sempre una notifica di approvazione. Questa logica impedisce agli utenti di un tenant ibrido di essere reindirizzati ad AD FS per la verifica di accesso senza dover prima fare clic su "Usa la tua password". Questo processo, inoltre, ignorerà eventuali criteri di accesso condizionale e flussi di autenticazione pass-through. L'eccezione a questo processo è rappresentata dal caso in cui venga specificato un login_hint, un utente verrà inoltrato in modo autoAD FS e ignorerà l'opzione per l'utilizzo delle credenziali con password.

### <a name="azure-mfa-server"></a>Server di Azure MFA

Gli utenti finali abilitati per MFA tramite un server di Azure MFA locale di un'organizzazione possono comunque creare e usare credenziali di accesso tramite telefono senza password. Se l'utente tenta di aggiornare più installazioni (più di 5) di Microsoft Authenticator con le credenziali create, questa modifica può determinare un errore.  

### <a name="device-registration"></a>Registrazione del dispositivo

Uno dei prerequisiti per creare queste nuove credenziali sicure è che il dispositivo in cui si trovano sia registrato nel tenant di Azure AD per un singolo utente. A causa delle limitazioni relative alla registrazione dei dispositivi, un dispositivo può essere registrato solo in un singolo tenant. A causa di questa limitazione, nell'app Microsoft Authenticator può essere abilitato per l'accesso tramite telefono un solo account aziendale o dell'istituto di istruzione.

## <a name="next-steps"></a>Passaggi successivi

[Che cosa sono le password?](concept-authentication-passwordless.md)

[Informazioni sulla registrazione dei dispositivi](../devices/overview.md#getting-devices-in-azure-ad)

[Informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
