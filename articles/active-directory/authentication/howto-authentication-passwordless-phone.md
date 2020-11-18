---
title: Accesso senza password con l'app Microsoft Authenticator-Azure Active Directory
description: Abilitare l'accesso senza password per Azure AD usando l'app Microsoft Authenticator (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9da0c00bd8498e3f43d5f8258308fbc010a6a274
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839523"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Microsoft Authenticator usa l'autenticazione basata su chiave per abilitare le credenziali utente associate a un dispositivo, in cui il dispositivo usa un PIN o biometrico. [Windows Hello for business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) usa una tecnologia simile.

Questa tecnologia di autenticazione può essere usata in qualsiasi piattaforma per dispositivi, incluso quello per dispositivi mobili. Questa tecnologia può essere usata anche con qualsiasi app o sito Web che si integra con le librerie di autenticazione Microsoft.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Esempio di accesso del browser che richiede all'utente di approvare l'accesso.":::

Gli utenti che hanno abilitato l'accesso tramite telefono dall'app Microsoft Authenticator visualizzano un messaggio che chiede di toccare un numero nell'app. Non viene richiesto alcun nome utente o password. Per completare il processo di accesso nell'app, un utente deve eseguire le azioni seguenti:

1. Corrisponde al numero.
2. Scegliere **Approva**.
3. Fornire il PIN o la biometrica.

## <a name="prerequisites"></a>Prerequisiti

Per usare l'accesso tramite telefono senza password con l'app Microsoft Authenticator, è necessario soddisfare i prerequisiti seguenti:

- Azure AD Multi-Factor Authentication, con notifiche push consentite come metodo di verifica.
- Accertarsi che sia installata la versione più recente di Microsoft Authenticator nei dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva.

> [!NOTE]
> Se è stata abilitata Microsoft Authenticator l'anteprima di accesso senza password usando Azure AD PowerShell, è stata abilitata per l'intera directory. Se si Abilita utilizzando questo nuovo metodo, il criterio di PowerShell viene sostituito. Si consiglia di abilitare per tutti gli utenti nel tenant tramite il menu nuovi *metodi di autenticazione* . in caso contrario, gli utenti non inclusi nei nuovi criteri non saranno più in grado di accedere senza una password.

## <a name="enable-passwordless-authentication-methods"></a>Abilita metodi di autenticazione con password

Per usare l'autenticazione senza password in Azure AD, abilitare prima di tutto l'esperienza di registrazione combinata, quindi abilitare gli utenti per il metodo less per la password.

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione con password si basano sulla funzionalità di registrazione combinata. Per consentire agli utenti di completare la registrazione combinata, attenersi alla procedura per [abilitare la registrazione delle informazioni di sicurezza combinata](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Abilitare i metodi di autenticazione per l'accesso tramite telefono senza password

Azure AD consente di scegliere i metodi di autenticazione che possono essere usati durante il processo di accesso. Gli utenti registrano quindi i metodi da usare.

Per abilitare il metodo di autenticazione per l'accesso tramite telefono senza password, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account *amministratore globale* .
1. Cercare e selezionare *Azure Active Directory*, quindi passare a metodi di autenticazione **sicurezza**  >  **Authentication methods**  >  **criteri metodo di autenticazione (anteprima)**
1. In **accesso con telefono senza password** scegliere le opzioni seguenti:
   1. **Abilita** -Sì o no
   1. **Destinazione** -tutti gli utenti o utenti selezionati
1. Per applicare il nuovo criterio, selezionare **Salva**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Registrazione e gestione degli utenti Microsoft Authenticator

Gli utenti si registrano per il metodo di autenticazione con password di Azure AD usando la procedura seguente:

1. Passare a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Accedere, quindi aggiungere l'app Authenticator selezionando **Aggiungi metodo > app Authenticator**, quindi **Aggiungi**.
1. Seguire le istruzioni per installare e configurare l'app Microsoft Authenticator nel dispositivo.
1. Selezionare **fine** per completare la configurazione dell'autenticatore.
1. In **Microsoft Authenticator** scegliere **Abilita l'accesso tramite telefono** dal menu a discesa per l'account registrato.
1. Seguire le istruzioni nell'app per completare la registrazione dell'account per l'accesso tramite telefono senza password.

Un'organizzazione può indirizzare gli utenti all'accesso con i loro telefoni, senza usare una password. Per ulteriori informazioni sulla configurazione dell'app Microsoft Authenticator e sull'abilitazione dell'accesso tramite telefono, vedere [accedere agli account tramite l'app Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Gli utenti che non sono consentiti dal criterio per l'uso dell'accesso tramite telefono non sono più in grado di abilitarlo all'interno dell'app Microsoft Authenticator.

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Un utente può iniziare a usare l'accesso senza password dopo il completamento di tutte le azioni seguenti:

- Un amministratore ha abilitato il tenant dell'utente.
- L'utente ha aggiornato l'app Microsoft Authenticator per abilitare l'accesso tramite telefono.

La prima volta che un utente avvia il processo di accesso tramite telefono, l'utente esegue i passaggi seguenti:

1. Immette il nome nella pagina di accesso.
2. Seleziona **Avanti**.
3. Se necessario, seleziona **altri modi per eseguire l'accesso**.
4. Seleziona **approva una richiesta nell'app Microsoft Authenticator**.

L'utente viene quindi visualizzato con un numero. L'app richiede all'utente di eseguire l'autenticazione selezionando il numero appropriato, anziché immettendo una password.

Dopo che l'utente ha usato l'accesso tramite telefono senza password, l'app continua a guidare l'utente tramite questo metodo. Tuttavia, l'utente visualizzerà l'opzione per scegliere un altro metodo.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Esempio di accesso del browser con l'app Microsoft Authenticator.":::

## <a name="known-issues"></a>Problemi noti

I problemi noti seguenti sono presenti nell'esperienza di anteprima corrente.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Opzione non visualizzato per l'accesso tramite telefono senza password

In uno scenario, un utente può avere una verifica di accesso tramite telefono senza password senza risposta in sospeso. Tuttavia, l'utente potrebbe provare a eseguire di nuovo l'accesso. Quando si verifica questa situazione, l'utente potrebbe visualizzare solo l'opzione per immettere una password.

Per risolvere questo scenario, è possibile usare i passaggi seguenti:

1. Aprire l'app Microsoft Authenticator.
2. Rispondere a qualsiasi richiesta di notifica.

L'utente può quindi continuare a usare l'accesso tramite telefono senza password.

### <a name="federated-accounts"></a>Account federati

Quando un utente ha abilitato qualsiasi credenziale senza password, il processo di accesso Azure AD smette di utilizzare l'hint di accesso \_ . Pertanto, il processo non accelera più l'utente verso un percorso di accesso federato.

Questa logica impedisce in genere a un utente in un tenant ibrido di essere indirizzato a Active Directory servizi federati (AD FS) per la verifica dell'accesso. Tuttavia, l'utente mantiene la possibilità di fare clic su **Usa la password**.

### <a name="azure-mfa-server"></a>Server di Azure MFA

Un utente finale può essere abilitato per l'autenticazione a più fattori tramite un server Azure multi-factor authentication locale. L'utente può comunque creare e usare una sola credenziale di accesso con telefono senza password.

Se l'utente tenta di aggiornare più installazioni (5 +) dell'app Microsoft Authenticator con le credenziali di accesso con telefono senza password, questa modifica potrebbe causare un errore.

### <a name="device-registration"></a>Registrazione del dispositivo

Prima di poter creare le nuove credenziali complesse, esistono prerequisiti. Un prerequisito è che il dispositivo in cui è installata l'app Microsoft Authenticator deve essere registrato nel tenant di Azure AD a un singolo utente.

Attualmente, un dispositivo può essere registrato solo in un singolo tenant. A causa di questa limitazione, nell'app Microsoft Authenticator può essere abilitato per l'accesso tramite telefono un solo account aziendale o dell'istituto di istruzione.

> [!NOTE]
> La registrazione del dispositivo non è identica alla gestione dei dispositivi o alla gestione di dispositivi mobili (MDM). La registrazione del dispositivo associa solo un ID dispositivo e un ID utente, nella directory Azure AD.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'autenticazione Azure AD e sui metodi con password, vedere gli articoli seguenti:

- [Informazioni sul funzionamento dell'autenticazione con password](concept-authentication-passwordless.md)
- [Informazioni sulla registrazione dei dispositivi](../devices/overview.md#getting-devices-in-azure-ad)
- [Informazioni sulle Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
