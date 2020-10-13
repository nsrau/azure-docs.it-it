---
title: Accesso senza password con l'app Microsoft Authenticator-Azure Active Directory
description: Abilitare l'accesso senza password per Azure AD usando l'app Microsoft Authenticator (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964826"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Analogamente alla tecnologia di [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator usa l'autenticazione basata su chiavi per abilitare credenziali utente associate a un dispositivo e basate su un sensore biometrico o un PIN. Questo metodo di autenticazione può essere usato in qualsiasi piattaforma per dispositivi, inclusi i dispositivi mobili e con qualsiasi app o sito Web che si integra con le librerie di autenticazione Microsoft.

![Esempio di accesso del browser che richiede all'utente di approvare l'accesso](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Anziché visualizzare la richiesta di una password dopo l'immissione di un nome utente, una persona che ha abilitato l'accesso tramite telefono dall'app Microsoft Authenticator Visualizza un messaggio che chiede di toccare un numero nell'app. Per completare il processo di accesso nell'app, l'utente deve corrispondere al numero, scegliere **approva**, quindi specificare il PIN o la biometria.

## <a name="prerequisites"></a>Prerequisiti

Per usare l'accesso tramite telefono senza password con l'app Microsoft Authenticator, è necessario soddisfare i prerequisiti seguenti:

- Azure Multi-Factor Authentication, con notifiche push consentite come metodo di verifica.
- Accertarsi che sia installata la versione più recente di Microsoft Authenticator nei dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva.

> [!NOTE]
> Se è stata abilitata Microsoft Authenticator anteprima dell'accesso senza password dell'app usando Azure AD PowerShell, è stata abilitata per l'intera directory. Se si Abilita utilizzando questo nuovo metodo, il criterio di PowerShell viene sostituito. Si consiglia di abilitare per tutti gli utenti nel tenant tramite il menu nuovi *metodi di autenticazione* . in caso contrario, gli utenti non inclusi nei nuovi criteri non saranno più in grado di accedere senza una password.

## <a name="enable-passwordless-authentication-methods"></a>Abilita metodi di autenticazione con password

Per usare l'autenticazione senza password in Azure AD, abilitare prima di tutto l'esperienza di registrazione combinata, quindi abilitare gli utenti per il metodo less per la password.

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione con password si basano sulla funzionalità di registrazione combinata. Per consentire agli utenti di completare la registrazione combinata, attenersi alla procedura per [abilitare la registrazione delle informazioni di sicurezza combinata](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Abilitare i metodi di autenticazione per l'accesso tramite telefono senza password

Azure AD consente di scegliere i metodi di autenticazione che possono essere usati durante il processo di accesso. Gli utenti registrano quindi i metodi da usare.

Per abilitare il metodo di autenticazione per l'accesso tramite telefono senza password, completare i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account *amministratore globale* .
1. Cercare e selezionare *Azure Active Directory*, quindi passare a metodi di autenticazione **sicurezza**  >  **Authentication methods**  >  **criteri metodo di autenticazione (anteprima)**
1. In **accesso con telefono senza password**scegliere le opzioni seguenti:
   1. **Abilita** -Sì o no
   1. **Destinazione** -tutti gli utenti o utenti selezionati
1. Per applicare il nuovo criterio, selezionare **Salva**.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrazione e gestione degli utenti dell'app Microsoft Authenticator

Con il metodo di autenticazione senza password disponibile per l'uso in Azure AD, gli utenti devono ora registrarsi per il metodo di autenticazione senza password attenendosi alla procedura seguente:

1. Passare a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Accedere, quindi aggiungere l'app Authenticator selezionando **Aggiungi metodo > app Authenticator**, quindi **Aggiungi**.
1. Seguire le istruzioni per installare e configurare l'app Microsoft Authenticator nel dispositivo.
1. Selezionare **fine** per completare la configurazione dell'autenticatore.
1. Nell'app **Microsoft Authenticator** scegliere **Abilita l'accesso tramite telefono** dal menu a discesa per l'account registrato.
1. Seguire le istruzioni nell'app per completare la registrazione dell'account per l'accesso tramite telefono senza password.

Le organizzazioni possono indirizzare gli utenti all' [accesso con il telefono, non alla password](../user-help/user-help-auth-app-sign-in.md) per un'ulteriore assistenza per la configurazione dell'app Microsoft Authenticator e l'abilitazione dell'accesso tramite telefono.

> [!NOTE]
> Gli utenti che non sono consentiti dal criterio per l'uso dell'accesso tramite telefono non sono più in grado di abilitarlo all'interno dell'app Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Un utente può iniziare a usare l'accesso senza password dopo che un amministratore ha abilitato il tenant e l'utente ha aggiornato l'app Microsoft Authenticator per abilitare l'accesso tramite telefono.

Per iniziare a usare l'accesso tramite telefono, dopo aver digitato un nome utente nella pagina di accesso e selezionando **Avanti**, è possibile che gli utenti debbano selezionare **altri modi per eseguire l'accesso**, quindi **approvare una richiesta nell'app Microsoft Authenticator**. Gli utenti vengono quindi presentati con un numero e vengono richiesti nell'app Microsoft Authenticator per selezionare il numero appropriato da autenticare invece di usare la password. Una volta che gli utenti hanno utilizzato l'accesso tramite telefono senza password, viene richiesto di riutilizzarlo, fino a quando non viene utilizzato scegliere un altro metodo.

![Esempio di accesso del browser con l'app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemi noti

I problemi noti seguenti sono presenti nell'esperienza di anteprima corrente.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Opzione non visualizzato per l'accesso tramite telefono senza password

Se un utente dispone di una verifica di accesso tramite telefono senza password senza risposta in sospeso e tenta di eseguire nuovamente l'accesso, l'utente può visualizzare solo un'opzione per immettere una password. Aprire il Microsoft Authenticator e rispondere alle richieste di notifica per continuare a usare l'accesso tramite telefono senza password.

### <a name="federated-accounts"></a>Account federati

Quando un utente ha abilitato le credenziali senza password, Azure AD account di accesso smetteranno di usare login_hint per accelerare l'utente a un percorso di accesso federato. Questa logica impedisce agli utenti di un tenant ibrido di essere indirizzati a AD FS per la verifica dell'accesso senza che l'utente stia eseguendo un passaggio aggiuntivo per fare clic su "usa la password".

### <a name="azure-mfa-server"></a>Server di Azure MFA

Gli utenti finali abilitati per l'autenticazione a più fattori tramite il server di autenticazione a più fattori di Azure locale di un'organizzazione possono comunque creare e usare una sola credenziale di accesso con telefono senza password. Se l'utente tenta di aggiornare più installazioni (più di 5) di Microsoft Authenticator con le credenziali create, questa modifica può determinare un errore.  

### <a name="device-registration"></a>Registrazione del dispositivo

Uno dei prerequisiti per creare la nuova credenziale avanzata è che il dispositivo, in cui è installata l'app Microsoft Authenticator, deve essere registrato anche all'interno del tenant di Azure AD a un singolo utente. A causa delle restrizioni di registrazione del dispositivo correnti, un dispositivo può essere registrato solo in un singolo tenant. A causa di questa limitazione, nell'app Microsoft Authenticator può essere abilitato per l'accesso tramite telefono un solo account aziendale o dell'istituto di istruzione.

> [!NOTE]
> La registrazione del dispositivo non è identica alla gestione dei dispositivi o alla "MDM". Associa solo un ID dispositivo e un ID utente insieme nella directory Azure AD.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'autenticazione Azure AD e sui metodi con password, vedere gli articoli seguenti:

* [Informazioni sul funzionamento dell'autenticazione con password](concept-authentication-passwordless.md)
* [Informazioni sulla registrazione dei dispositivi](../devices/overview.md#getting-devices-in-azure-ad)
* [Informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
