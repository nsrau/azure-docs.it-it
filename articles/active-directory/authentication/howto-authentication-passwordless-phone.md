---
title: Accesso senza password con l'app Microsoft Authenticator - Azure Active Directory
description: Abilitare l'accesso senza password ad Azure AD usando l'app Microsoft Authenticator (anteprima)Enable passwordless sign-in to Azure AD using the Microsoft Authenticator app (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853e7143834a340b870b71ef1a287dab136e2783
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654065"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)Enable passwordless sign-in with the Microsoft Authenticator app (preview)

L'app Microsoft Authenticator consente di accedere a qualsiasi account di Azure AD senza usare la password. Analogamente alla tecnologia di [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator usa l'autenticazione basata su chiavi per abilitare credenziali utente associate a un dispositivo e basate su un sensore biometrico o un PIN. Questo metodo di autenticazione può essere utilizzato su qualsiasi piattaforma del dispositivo, inclusi i dispositivi mobili, e con qualsiasi app o sito Web che si integra con le librerie di autenticazione Microsoft.This authentication method can be used on any device platform, including mobile, and with any app or website that integrates with Microsoft authentication libraries. 

![Esempio di accesso al browser che richiede all'utente di approvare l'accesso](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Invece di visualizzare una richiesta di password dopo aver immesso un nome utente, una persona che ha abilitato l'accesso telefonico dall'app Microsoft Authenticator visualizzerà un messaggio che informa di toccare un numero nell'app. L'utente dovrà quindi toccare il numero corrispondente nell'app, scegliere l'opzione di approvazione e fornire il codice PIN o la chiave biometrica e l'autenticazione verrà completata.

> [!NOTE]
> Questa funzionalità è stata nell'app Microsoft Authenticator dal marzo del 2017, pertanto è possibile che quando il criterio è abilitato per una directory, gli utenti possono incontrare immediatamente questo flusso e visualizzare un messaggio di errore se non sono stati abilitati dai criteri. Tenere presente queste informazioni e preparare gli utenti per questa modifica.

## <a name="prerequisites"></a>Prerequisiti

- Azure Multi-Factor Authentication, con notifiche push consentite come metodo di verificaAzure Multi-Factor Authentication, with push notifications allowed as a verification method 
- Accertarsi che sia installata la versione più recente di Microsoft Authenticator nei dispositivi che eseguono iOS 8.0 o versione successiva oppure Android 6.0 o versione successiva.

> [!NOTE]
> Se è stata abilitata l'anteprima di accesso senza password dell'app Microsoft Authenticator precedente con Azure AD PowerShell, è stata abilitata per l'intera directory. Se si abilita l'uso di questo nuovo metodo, verrà sostituito il criterio di PowerShell.If you enable using this new method, it will supercede the PowerShell policy. È consigliabile abilitare per tutti gli utenti nel tenant tramite i nuovi metodi di autenticazione, altrimenti gli utenti non nel nuovo criterio non saranno più in grado di accedere senza password. 

## <a name="enable-passwordless-authentication-methods"></a>Abilitare i metodi di autenticazione senza passwordEnable passwordless authentication methods

### <a name="enable-the-combined-registration-experience"></a>Abilitare l'esperienza di registrazione combinataEnable the combined registration experience

Le funzionalità di registrazione per i metodi di autenticazione senza password si basano sull'anteprima combinata della registrazione. Seguire i passaggi nell'articolo Abilitare la registrazione combinata delle informazioni di [sicurezza (anteprima)](howto-registration-mfa-sspr-combined.md)per abilitare l'anteprima combinata della registrazione.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Abilitare i metodi di autenticazione per l'accesso telefonico senza passwordEnable passwordless phone sign-in authentication methods

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Cercare e selezionare *Azure Active Directory*. Selezionare**Metodi di autenticazione** > di sicurezza Criteri metodo di**autenticazione (anteprima)Select** **Security** > Authentication methods Authentication method policy (Preview)
1. In **Accesso al telefono senza password**scegliere le opzioni seguenti
   1. **Abilita** - Sì o No
   1. **Destinazione** - Tutti gli utenti o Seleziona utenti
1. **Salva** per impostare il nuovo criterio

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrazione e gestione degli utenti dell'app Microsoft Authenticator

1. Passare a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Accedi se non è già
1. Aggiungere un'app di autenticazione facendo clic su **Aggiungi metodo**, scegliendo **Applicazione autenticatore**e facendo clic su **Aggiungi**
1. Seguire le istruzioni per installare e configurare l'app Microsoft Authenticator nel dispositivo
1. Fare clic su **Fine** per completare il flusso di configurazione dell'app Authenticator MFA. 
1. In **Microsoft Authenticator**scegliere **Abilita accesso telefonico** dal menu a discesa dell'account
1. Segui le istruzioni nell'app per completare la registrazione per l'accesso al telefono senza password. 

Le organizzazioni possono indirizzare gli utenti all'articolo [Accedere con il telefono, non la password](../user-help/microsoft-authenticator-app-phone-signin-faq.md) per ulteriore assistenza nella configurazione dell'app Microsoft Authenticator e per l'abilitazione dell'accesso tramite telefono. Per applicare queste impostazioni, potrebbe essere necessario disconnettersi e tornare al tenant. 

## <a name="sign-in-with-passwordless-credential"></a>Accedi con credenziali senza password

Per l'anteprima pubblica, non è possibile obbligare gli utenti a creare o usare nuove credenziali. Un utente incontrerà l'accesso senza password solo dopo che un amministratore ha abilitato il tenant **e** l'utente ha aggiornato l'app Microsoft Authenticator per abilitare l'accesso tramite telefono.

Dopo aver digitato il nome utente sul Web e aver selezionato **Avanti**, agli utenti viene visualizzato un numero e nell'app Microsoft Authenticator viene richiesto di selezionare il numero appropriato da autenticare anziché utilizzare la password. 

![Esempio di accesso al browser con l'app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemi noti

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>L'utente non è abilitato dai criteri, ma ha ancora il metodo di accesso telefonico senza password in Microsoft Authenticator

È possibile che un utente abbia a un certo punto creato una credenziale di accesso al telefono senza password nell'app Microsoft Authenticator corrente o in un dispositivo precedente. Una volta che un amministratore abilita il criterio del metodo di autenticazione per l'accesso telefonico senza password, qualsiasi utente con una credenziale registrata inizierà a sperimentare la nuova richiesta di accesso, indipendentemente dal fatto che sia stato abilitato all'utilizzo o meno del criterio. Se all'utente non è stato consentito utilizzare le credenziali in base ai criteri, verrà visualizzato un errore dopo il completamento del flusso di autenticazione. 

L'amministratore può scegliere di abilitare l'utente all'accesso al telefono senza password oppure l'utente deve rimuovere il metodo. Se l'utente non ha più il [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) dispositivo registrato, può andare a rimuoverlo e rimuoverlo. Se usano ancora l'autenticatore per l'autenticazione a più fattori, possono scegliere **Disattiva accesso telefonico** dall'interno di Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integrazione con ADFSAD FS integration

Se un utente ha abilitato le credenziali senza password di Microsoft Authenticator, per impostazione predefinita l'autenticazione per l'utente invierà sempre una notifica di approvazione. Questa logica impedisce agli utenti in un tenant ibrido di essere indirizzati ad ADFS per la verifica dell'accesso senza che l'utente esetrae un passaggio aggiuntivo per fare clic su "Usa la password". Questo processo, inoltre, ignorerà eventuali criteri di accesso condizionale e flussi di autenticazione pass-through. 

Se un utente dispone di una verifica di accesso del telefono senza password senza risposta in sospeso e tenta di accedere nuovamente, l'utente può essere portato ad ADFS per immettere una password.  

### <a name="azure-mfa-server"></a>Server di Azure MFA

Gli utenti finali abilitati per l'autenticazione a più fattori tramite il server Azure MFA locale di un'organizzazione possono comunque creare e usare una singola credenziale di accesso tramite password. Se l'utente tenta di aggiornare più installazioni (più di 5) di Microsoft Authenticator con le credenziali create, questa modifica può determinare un errore.  

### <a name="device-registration"></a>Registrazione del dispositivo

Uno dei prerequisiti per creare questa nuova credenziale forte è che il dispositivo in cui è installata l'app Microsoft Authenticator deve essere registrato anche all'interno del tenant di Azure AD per un singolo utente. A causa delle attuali restrizioni di registrazione del dispositivo, un dispositivo può essere registrato solo in un singolo tenant. A causa di questa limitazione, nell'app Microsoft Authenticator può essere abilitato per l'accesso tramite telefono un solo account aziendale o dell'istituto di istruzione.

### <a name="intune-mobile-application-management"></a>Gestione di applicazioni per dispositivi mobili Intune 

Gli utenti finali soggetti a un criterio che richiede la gestione delle applicazioni mobili (MAM) non possono registrare le credenziali senza password nell'app Microsoft Authenticator. 

> [!NOTE]
> La registrazione del dispositivo non corrisponde alla gestione dei dispositivi o "MDM". Associa solo un ID dispositivo e un ID utente nella directory di Azure AD.  

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sull'accesso senza password](concept-authentication-passwordless.md)

[Informazioni sulla registrazione dei dispositivi](../devices/overview.md#getting-devices-in-azure-ad)

[Informazioni su Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
