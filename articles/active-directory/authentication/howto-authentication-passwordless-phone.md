---
title: Accesso senza password con l'app Microsoft Authenticator-Azure Active Directory
description: Abilitare l'accesso senza password per Azure AD usando l'app Microsoft Authenticator (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a76aa0ca7cbda3f2db564c220ba12fec60f60509
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381871"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Analogamente alla tecnologia di [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator usa l'autenticazione basata su chiavi per abilitare credenziali utente associate a un dispositivo e basate su un sensore biometrico o un PIN. Questo metodo di autenticazione può essere usato in qualsiasi piattaforma per dispositivi, inclusi i dispositivi mobili e con qualsiasi app o sito Web che si integra con le librerie di autenticazione Microsoft. 

![Esempio di accesso del browser che richiede all'utente di approvare l'accesso](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Anziché visualizzare la richiesta di una password dopo l'immissione di un nome utente, una persona che ha abilitato l'accesso tramite telefono dall'app Microsoft Authenticator visualizzerà un messaggio che informa di toccare un numero nell'app. L'utente dovrà quindi toccare il numero corrispondente nell'app, scegliere l'opzione di approvazione e fornire il codice PIN o la chiave biometrica e l'autenticazione verrà completata.

> [!NOTE]
> Questa funzionalità è stata rilevata nell'app Microsoft Authenticator da marzo 2017, quindi è possibile che, quando il criterio è abilitato per una directory, gli utenti possano riscontrare immediatamente questo flusso e visualizzare un messaggio di errore se non sono stati abilitati dai criteri. Tenere presente queste informazioni e preparare gli utenti per questa modifica.

## <a name="prerequisites"></a>prerequisiti

- Multi-Factor Authentication di Azure, con notifiche push consentite come metodo di verifica 
- Accertarsi che sia installata la versione più recente di Microsoft Authenticator nei dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva.

> [!NOTE]
> Se è stata abilitata la precedente anteprima dell'accesso senza password dell'app Microsoft Authenticator usando Azure AD PowerShell, è stata abilitata per l'intera directory. Se si Abilita utilizzando questo nuovo metodo, i criteri di PowerShell vengono sostituiti. Si consiglia di abilitare per tutti gli utenti nel tenant tramite i nuovi metodi di autenticazione. in caso contrario, gli utenti non presenti nei nuovi criteri non saranno più in grado di accedere a passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Abilita metodi di autenticazione con password

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinata

Le funzionalità di registrazione per i metodi di autenticazione con password si basano sull'anteprima della registrazione combinata. Per abilitare l'anteprima della registrazione combinata, seguire la procedura descritta nell'articolo [abilitare la registrazione delle informazioni di sicurezza combinata (anteprima)](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Abilitare i metodi di autenticazione per l'accesso tramite telefono senza password

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Cercare e selezionare *Azure Active Directory*. Selezionare i **metodi di autenticazione** > **sicurezza** > **criteri metodo di autenticazione (anteprima)**
1. In **accesso con telefono senza password**scegliere le opzioni seguenti
   1. **Abilita** -Sì o no
   1. **Destinazione** -tutti gli utenti o utenti selezionati
1. **Salva** per impostare il nuovo criterio

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrazione e gestione degli utenti dell'app Microsoft Authenticator

1. Passare a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Accedi se non è già stato fatto
1. Aggiungere un'app Authenticator facendo clic su **Aggiungi metodo**, scegliendo **app autenticatore**e facendo clic su **Aggiungi** .
1. Seguire le istruzioni per installare e configurare l'app Microsoft Authenticator nel dispositivo
1. Fare clic su **fine** per completare il flusso di installazione dell'app autenticazione a più fattori. 
1. In **Microsoft Authenticator**scegliere **Abilita l'accesso tramite telefono** dal menu a discesa account
1. Seguire le istruzioni nell'app per completare la registrazione per l'accesso tramite telefono senza password. 

Le organizzazioni possono indirizzare gli utenti all'articolo [accesso con il telefono, non alla password](../user-help/microsoft-authenticator-app-phone-signin-faq.md) per ulteriori interventi di configurazione nell'app Microsoft Authenticator e abilitazione dell'accesso tramite telefono.

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Per l'anteprima pubblica, non è possibile obbligare gli utenti a creare o usare nuove credenziali. Un utente troverà l'accesso senza password solo dopo che un amministratore ha abilitato il tenant **e** l'utente ha aggiornato l'app Microsoft Authenticator per abilitare l'accesso tramite telefono.

Dopo aver digitato il nome utente sul Web e aver selezionato **Avanti**, gli utenti vengono presentati con un numero e vengono richiesti nell'app Microsoft Authenticator per selezionare il numero appropriato da autenticare invece di usare la password. 

![Esempio di accesso del browser con l'app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemi noti

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>L'utente non è abilitato dal criterio ma ha ancora un metodo di accesso telefonico senza password in Microsoft Authenticator

È possibile che un utente abbia creato a un certo punto credenziali di accesso con telefono senza password nell'app Microsoft Authenticator corrente o in un dispositivo precedente. Quando un amministratore abilita i criteri del metodo di autenticazione per l'accesso tramite telefono senza password, qualsiasi utente con una credenziale registrata, inizierà a provare il nuovo prompt di accesso, indipendentemente dal fatto che sia stato abilitato o meno per l'uso del criterio. Se l'utente non è autorizzato a usare le credenziali per criterio, verrà visualizzato un errore dopo aver completato il flusso di autenticazione. 

L'amministratore può scegliere di consentire all'utente di usare l'accesso tramite telefono senza password oppure l'utente deve rimuovere il metodo. Se l'utente non dispone più del dispositivo registrato, può passare a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) e rimuoverlo. Se usano ancora l'autenticatore per l'autenticazione a più fattori, possono scegliere di **disabilitare l'accesso tramite telefono** dall'interno del Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integrazione di AD FS

Se un utente ha abilitato le credenziali senza password di Microsoft Authenticator, per impostazione predefinita l'autenticazione per l'utente invierà sempre una notifica di approvazione. Questa logica impedisce agli utenti di un tenant ibrido di essere reindirizzati ad AD FS per la verifica di accesso senza dover prima fare clic su "Usa la tua password". Questo processo, inoltre, ignorerà eventuali criteri di accesso condizionale e flussi di autenticazione pass-through. 

Se un utente dispone di una verifica di accesso tramite telefono senza password senza risposta in sospeso e tenta di accedere di nuovo, è possibile che l'utente venga immesso in ADFS per immettere una password.  

### <a name="azure-mfa-server"></a>Server di Azure MFA

Gli utenti finali abilitati per l'autenticazione a più fattori tramite il server Azure multi-factor authentication locale di un'organizzazione possono comunque creare e usare una sola credenziale di accesso con telefono senza password. Se l'utente tenta di aggiornare più installazioni (più di 5) di Microsoft Authenticator con le credenziali create, questa modifica può determinare un errore.  

### <a name="device-registration"></a>Registrazione del dispositivo

Uno dei prerequisiti per creare la nuova credenziale avanzata è che il dispositivo, in cui è installata l'app Microsoft Authenticator, deve essere registrato anche all'interno del tenant di Azure AD a un singolo utente. A causa delle restrizioni di registrazione del dispositivo correnti, un dispositivo può essere registrato solo in un singolo tenant. A causa di questa limitazione, nell'app Microsoft Authenticator può essere abilitato per l'accesso tramite telefono un solo account aziendale o dell'istituto di istruzione.

> [!NOTE]
> La registrazione del dispositivo non è identica alla gestione dei dispositivi o alla "MDM". Associa solo un ID dispositivo e un ID utente insieme nella directory Azure AD.  

## <a name="next-steps"></a>Passaggi successivi

[Che cosa sono le password?](concept-authentication-passwordless.md)

[Informazioni sulla registrazione dei dispositivi](../devices/overview.md#getting-devices-in-azure-ad)

[Informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
